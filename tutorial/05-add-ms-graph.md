<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="af04c-101">В этом упражнении вы будете включать Graph Microsoft в приложение.</span><span class="sxs-lookup"><span data-stu-id="af04c-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="af04c-102">Для этого приложения вы будете использовать [драгоценный камень httparty](https://github.com/jnunemaker/httparty) для звонков в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="af04c-102">For this application, you will use the [httparty](https://github.com/jnunemaker/httparty) gem to make calls to Microsoft Graph.</span></span>

## <a name="create-a-graph-helper"></a><span data-ttu-id="af04c-103">Создание помощника Graph</span><span class="sxs-lookup"><span data-stu-id="af04c-103">Create a Graph helper</span></span>

1. <span data-ttu-id="af04c-104">Создайте помощника для управления всеми вызовами API.</span><span class="sxs-lookup"><span data-stu-id="af04c-104">Create a helper to manage all of your API calls.</span></span> <span data-ttu-id="af04c-105">Запустите следующую команду в CLI для создания помощника.</span><span class="sxs-lookup"><span data-stu-id="af04c-105">Run the following command in your CLI to generate the helper.</span></span>

    ```Shell
    rails generate helper Graph
    ```

1. <span data-ttu-id="af04c-106">Откройте **./app/helpers/graph_helper.rb** и замените содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="af04c-106">Open **./app/helpers/graph_helper.rb** and replace the contents with the following.</span></span>

    ```ruby
    require 'httparty'

    # Graph API helper methods
    module GraphHelper
      GRAPH_HOST = 'https://graph.microsoft.com'.freeze

      def make_api_call(method, endpoint, token, headers = nil, params = nil, payload = nil)
        headers ||= {}
        headers[:Authorization] = "Bearer #{token}"
        headers[:Accept] = 'application/json'

        params ||= {}

        case method.upcase
        when 'GET'
          HTTParty.get "#{GRAPH_HOST}#{endpoint}",
                       :headers => headers,
                       :query => params
        when 'POST'
          headers['Content-Type'] = 'application/json'
          HTTParty.post "#{GRAPH_HOST}#{endpoint}",
                        :headers => headers,
                        :query => params,
                        :body => payload ? payload.to_json : nil
        else
          raise "HTTP method #{method.upcase} not implemented"
        end
      end
    end
    ```

<span data-ttu-id="af04c-107">Найми минутку, чтобы просмотреть, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="af04c-107">Take a moment to review what this code does.</span></span> <span data-ttu-id="af04c-108">Он делает простой запрос GET или POST через драгоценный камень `httparty` в запрашиваемую конечную точку.</span><span class="sxs-lookup"><span data-stu-id="af04c-108">It makes a simple GET or POST request via the `httparty` gem to the requested endpoint.</span></span> <span data-ttu-id="af04c-109">Он отправляет маркер доступа в загонах и включает все параметры запроса, которые `Authorization` передаются.</span><span class="sxs-lookup"><span data-stu-id="af04c-109">It sends the access token in the `Authorization` header, and it includes any query parameters that are passed.</span></span>

<span data-ttu-id="af04c-110">Например, чтобы использовать метод get to, можно вызвать `make_api_call` `https://graph.microsoft.com/v1.0/me?$select=displayName` его так:</span><span class="sxs-lookup"><span data-stu-id="af04c-110">For example, to use the `make_api_call` method to do a GET to `https://graph.microsoft.com/v1.0/me?$select=displayName`, you could call it like so:</span></span>

```ruby
make_api_call 'GET', '/v1.0/me', access_token, {}, { '$select': 'displayName' }
```

<span data-ttu-id="af04c-111">Это будет реализовано позже по мере реализации дополнительных функций Microsoft Graph в приложении.</span><span class="sxs-lookup"><span data-stu-id="af04c-111">You'll build on this later as you implement more Microsoft Graph features into the app.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="af04c-112">Получение событий календаря из Outlook</span><span class="sxs-lookup"><span data-stu-id="af04c-112">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="af04c-113">В CLI запустите следующую команду, чтобы добавить новый контроллер.</span><span class="sxs-lookup"><span data-stu-id="af04c-113">In your CLI, run the following command to add a new controller.</span></span>

    ```Shell
    rails generate controller Calendar index new
    ```

1. <span data-ttu-id="af04c-114">Добавьте новый маршрут **в ./config/routes.rb**.</span><span class="sxs-lookup"><span data-stu-id="af04c-114">Add the new route to **./config/routes.rb**.</span></span>

    ```ruby
    get 'calendar', :to => 'calendar#index'
    ```

1. <span data-ttu-id="af04c-115">Добавьте новый метод в помощник Graph, чтобы [получить представление календаря.](https://docs.microsoft.com/graph/api/calendar-list-calendarview?view=graph-rest-1.0)</span><span class="sxs-lookup"><span data-stu-id="af04c-115">Add a new method to the Graph helper to [get a calendar view](https://docs.microsoft.com/graph/api/calendar-list-calendarview?view=graph-rest-1.0).</span></span> <span data-ttu-id="af04c-116">Откройте **./app/helpers/graph_helper.rb** и добавьте следующий метод в `GraphHelper` модуль.</span><span class="sxs-lookup"><span data-stu-id="af04c-116">Open **./app/helpers/graph_helper.rb** and add the following method to the `GraphHelper` module.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/helpers/graph_helper.rb" id="GetCalendarSnippet":::

    <span data-ttu-id="af04c-117">Давайте посмотрим, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="af04c-117">Consider what this code is doing.</span></span>

    - <span data-ttu-id="af04c-118">Вызывается URL-адрес `/v1.0/me/calendarview`.</span><span class="sxs-lookup"><span data-stu-id="af04c-118">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
        - <span data-ttu-id="af04c-119">Заготвка приводит к корректировке времени начала и окончания результатов в часовом поясе `Prefer: outlook.timezone` пользователя.</span><span class="sxs-lookup"><span data-stu-id="af04c-119">The `Prefer: outlook.timezone` header causes the start and end times in the results to be adjusted to the user's time zone.</span></span>
        - <span data-ttu-id="af04c-120">Параметры `startDateTime` `endDateTime` и параметров заданы начало и конец представления.</span><span class="sxs-lookup"><span data-stu-id="af04c-120">The `startDateTime` and `endDateTime` parameters set the start and end of the view.</span></span>
        - <span data-ttu-id="af04c-121">Параметр `$select` ограничивает поля, возвращаемые для каждого события, только теми, которые будут фактически использовать представление.</span><span class="sxs-lookup"><span data-stu-id="af04c-121">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
        - <span data-ttu-id="af04c-122">Параметр `$orderby` сортировать результаты по времени начала.</span><span class="sxs-lookup"><span data-stu-id="af04c-122">The `$orderby` parameter sorts the results by start time.</span></span>
        - <span data-ttu-id="af04c-123">Параметр ограничивает результаты до `$top` 50 событий.</span><span class="sxs-lookup"><span data-stu-id="af04c-123">The `$top` parameter limits the results to 50 events.</span></span>
    - <span data-ttu-id="af04c-124">Для успешного ответа возвращается массив элементов, содержащихся в `value` ключе.</span><span class="sxs-lookup"><span data-stu-id="af04c-124">For a successful response, it returns the array of items contained in the `value` key.</span></span>

1. <span data-ttu-id="af04c-125">Добавьте новый метод в помощник Graph, чтобы найти идентификатор часового пояса [IANA](https://www.iana.org/time-zones) на основе имени Windows часового пояса.</span><span class="sxs-lookup"><span data-stu-id="af04c-125">Add a new method to the Graph helper to lookup an [IANA time zone identifier](https://www.iana.org/time-zones) based on a Windows time zone name.</span></span> <span data-ttu-id="af04c-126">Это необходимо, так как microsoft Graph может Windows часовых поясов в качестве имен часовых поясов, а для класса Ruby **DateTime** требуются идентификаторы часового пояса IANA.</span><span class="sxs-lookup"><span data-stu-id="af04c-126">This is necessary because Microsoft Graph can return time zones as Windows time zone names, and the Ruby **DateTime** class requires IANA time zone identifiers.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/helpers/graph_helper.rb" id="ZoneMappingSnippet":::

1. <span data-ttu-id="af04c-127">Откройте **./app/controllers/calendar_controller.rb** и замените все содержимое на следующее.</span><span class="sxs-lookup"><span data-stu-id="af04c-127">Open **./app/controllers/calendar_controller.rb** and replace its entire contents with the following.</span></span>

    ```ruby
    # Calendar controller
    class CalendarController < ApplicationController
      include GraphHelper

      def index
        # Get the IANA identifier of the user's time zone
        time_zone = get_iana_from_windows(user_timezone)

        # Calculate the start and end of week in the user's time zone
        start_datetime = Date.today.beginning_of_week(:sunday).in_time_zone(time_zone).to_time
        end_datetime = start_datetime.advance(:days => 7)

        @events = get_calendar_view access_token, start_datetime, end_datetime, user_timezone || []
        render json: @events
      rescue RuntimeError => e
        @errors = [
          {
            :message => 'Microsoft Graph returned an error getting events.',
            :debug => e
          }
        ]
      end
    end
    ```

1. <span data-ttu-id="af04c-128">Перезапустите сервер.</span><span class="sxs-lookup"><span data-stu-id="af04c-128">Restart the server.</span></span> <span data-ttu-id="af04c-129">Войдите и щелкните **ссылку Календарь** в панели nav.</span><span class="sxs-lookup"><span data-stu-id="af04c-129">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="af04c-130">Если все работает надлежащим образом, в календаре пользователя должен появиться дамп событий в формате JSON.</span><span class="sxs-lookup"><span data-stu-id="af04c-130">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="af04c-131">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="af04c-131">Display the results</span></span>

<span data-ttu-id="af04c-132">Теперь вы можете добавить HTML для отображения результатов более удобным для пользователя способом.</span><span class="sxs-lookup"><span data-stu-id="af04c-132">Now you can add HTML to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="af04c-133">Откройте **./app/views/calendar/index.html.erb** и замените его содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="af04c-133">Open **./app/views/calendar/index.html.erb** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/app/views/calendar/index.html.erb" id="CalendarSnippet":::

    <span data-ttu-id="af04c-134">Это позволяет повторять коллекцию событий и добавить для каждого из них строку таблицы.</span><span class="sxs-lookup"><span data-stu-id="af04c-134">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="af04c-135">Удалите строку из действия `render json: @events` `index` в **./app/controllers/calendar_controller.rb**.</span><span class="sxs-lookup"><span data-stu-id="af04c-135">Remove the `render json: @events` line from the `index` action in **./app/controllers/calendar_controller.rb**.</span></span>

1. <span data-ttu-id="af04c-136">Обновите страницу, и приложение должно теперь отрисовка таблицы событий.</span><span class="sxs-lookup"><span data-stu-id="af04c-136">Refresh the page and the app should now render a table of events.</span></span>

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
