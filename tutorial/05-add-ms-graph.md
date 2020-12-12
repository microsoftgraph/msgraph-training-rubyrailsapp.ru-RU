<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="095e9-101">В этом упражнении вы включаете Microsoft Graph в приложение.</span><span class="sxs-lookup"><span data-stu-id="095e9-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="095e9-102">Для этого приложения вы будете использовать httparty-самосхему для вызова Microsoft Graph. [](https://github.com/jnunemaker/httparty)</span><span class="sxs-lookup"><span data-stu-id="095e9-102">For this application, you will use the [httparty](https://github.com/jnunemaker/httparty) gem to make calls to Microsoft Graph.</span></span>

## <a name="create-a-graph-helper"></a><span data-ttu-id="095e9-103">Создание помощника Graph</span><span class="sxs-lookup"><span data-stu-id="095e9-103">Create a Graph helper</span></span>

1. <span data-ttu-id="095e9-104">Создайте помощник для управления всеми вызовами API.</span><span class="sxs-lookup"><span data-stu-id="095e9-104">Create a helper to manage all of your API calls.</span></span> <span data-ttu-id="095e9-105">Чтобы создать помощник, запустите следующую команду в CLI.</span><span class="sxs-lookup"><span data-stu-id="095e9-105">Run the following command in your CLI to generate the helper.</span></span>

    ```Shell
    rails generate helper Graph
    ```

1. <span data-ttu-id="095e9-106">Откройте **./app/helpers/graph_helper.rb** и замените содержимое на следующее.</span><span class="sxs-lookup"><span data-stu-id="095e9-106">Open **./app/helpers/graph_helper.rb** and replace the contents with the following.</span></span>

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

<span data-ttu-id="095e9-107">Просмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="095e9-107">Take a moment to review what this code does.</span></span> <span data-ttu-id="095e9-108">Он делает простой запрос GET или POST с помощью самосхемы `httparty` в запрашиваемую конечную точку.</span><span class="sxs-lookup"><span data-stu-id="095e9-108">It makes a simple GET or POST request via the `httparty` gem to the requested endpoint.</span></span> <span data-ttu-id="095e9-109">Он отправляет маркер доступа в заголке и включает все переданные `Authorization` параметры запроса.</span><span class="sxs-lookup"><span data-stu-id="095e9-109">It sends the access token in the `Authorization` header, and it includes any query parameters that are passed.</span></span>

<span data-ttu-id="095e9-110">Например, чтобы использовать метод для `make_api_call` получения `https://graph.microsoft.com/v1.0/me?$select=displayName` get, можно вызвать его так:</span><span class="sxs-lookup"><span data-stu-id="095e9-110">For example, to use the `make_api_call` method to do a GET to `https://graph.microsoft.com/v1.0/me?$select=displayName`, you could call it like so:</span></span>

```ruby
make_api_call 'GET', '/v1.0/me', access_token, { '$select': 'displayName' }
```

<span data-ttu-id="095e9-111">Вы сможете реализации дополнительных функций Microsoft Graph в приложении позже.</span><span class="sxs-lookup"><span data-stu-id="095e9-111">You'll build on this later as you implement more Microsoft Graph features into the app.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="095e9-112">Получить события календаря из Outlook</span><span class="sxs-lookup"><span data-stu-id="095e9-112">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="095e9-113">В CLI запустите следующую команду, чтобы добавить новый контроллер.</span><span class="sxs-lookup"><span data-stu-id="095e9-113">In your CLI, run the following command to add a new controller.</span></span>

    ```Shell
    rails generate controller Calendar index new
    ```

1. <span data-ttu-id="095e9-114">Добавьте новый маршрут в **./config/routes.rb.**</span><span class="sxs-lookup"><span data-stu-id="095e9-114">Add the new route to **./config/routes.rb**.</span></span>

    ```ruby
    get 'calendar', :to => 'calendar#index'
    ```

1. <span data-ttu-id="095e9-115">Добавьте новый метод во справку Graph, чтобы [получить представление календаря.](https://docs.microsoft.com/graph/api/calendar-list-calendarview?view=graph-rest-1.0)</span><span class="sxs-lookup"><span data-stu-id="095e9-115">Add a new method to the Graph helper to [get a calendar view](https://docs.microsoft.com/graph/api/calendar-list-calendarview?view=graph-rest-1.0).</span></span> <span data-ttu-id="095e9-116">Откройте **./app/helpers/graph_helper.rb** и добавьте в модуль следующий `GraphHelper` метод.</span><span class="sxs-lookup"><span data-stu-id="095e9-116">Open **./app/helpers/graph_helper.rb** and add the following method to the `GraphHelper` module.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/helpers/graph_helper.rb" id="GetCalendarSnippet":::

    <span data-ttu-id="095e9-117">Подумайте, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="095e9-117">Consider what this code is doing.</span></span>

    - <span data-ttu-id="095e9-118">Будет вызван URL-адрес `/v1.0/me/calendarview` .</span><span class="sxs-lookup"><span data-stu-id="095e9-118">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
        - <span data-ttu-id="095e9-119">Заголок приводит к корректировке времени начала и окончания результатов в часовом `Prefer: outlook.timezone` поясе пользователя.</span><span class="sxs-lookup"><span data-stu-id="095e9-119">The `Prefer: outlook.timezone` header causes the start and end times in the results to be adjusted to the user's time zone.</span></span>
        - <span data-ttu-id="095e9-120">Параметры `startDateTime` и начало и конец `endDateTime` представления.</span><span class="sxs-lookup"><span data-stu-id="095e9-120">The `startDateTime` and `endDateTime` parameters set the start and end of the view.</span></span>
        - <span data-ttu-id="095e9-121">Параметр `$select` ограничивает поля, возвращаемые для каждого события, только теми полями, которые будут фактически использовать представление.</span><span class="sxs-lookup"><span data-stu-id="095e9-121">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
        - <span data-ttu-id="095e9-122">Параметр `$orderby` сортировать результаты по времени начала.</span><span class="sxs-lookup"><span data-stu-id="095e9-122">The `$orderby` parameter sorts the results by start time.</span></span>
        - <span data-ttu-id="095e9-123">Этот `$top` параметр ограничивает результаты до 50 событий.</span><span class="sxs-lookup"><span data-stu-id="095e9-123">The `$top` parameter limits the results to 50 events.</span></span>
    - <span data-ttu-id="095e9-124">Для успешного отклика он возвращает массив элементов, содержащихся в `value` ключе.</span><span class="sxs-lookup"><span data-stu-id="095e9-124">For a successful response, it returns the array of items contained in the `value` key.</span></span>

1. <span data-ttu-id="095e9-125">Добавьте во помощник Graph новый метод для получения идентификатора часового пояса [IANA](https://www.iana.org/time-zones) на основе имени часового пояса Windows.</span><span class="sxs-lookup"><span data-stu-id="095e9-125">Add a new method to the Graph helper to lookup an [IANA time zone identifier](https://www.iana.org/time-zones) based on a Windows time zone name.</span></span> <span data-ttu-id="095e9-126">Это необходимо, так как Microsoft Graph может возвращать часовые пояса в качестве имен часовых поясов Windows, а для класса Ruby **DateTime** требуются идентификаторы часовых поясов IANA.</span><span class="sxs-lookup"><span data-stu-id="095e9-126">This is necessary because Microsoft Graph can return time zones as Windows time zone names, and the Ruby **DateTime** class requires IANA time zone identifiers.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/helpers/graph_helper.rb" id="ZoneMappingSnippet":::

1. <span data-ttu-id="095e9-127">Откройте **./app/controllers/calendar_controller.rb** и замените все его содержимое на следующее.</span><span class="sxs-lookup"><span data-stu-id="095e9-127">Open **./app/controllers/calendar_controller.rb** and replace its entire contents with the following.</span></span>

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

1. <span data-ttu-id="095e9-128">Перезапустите сервер.</span><span class="sxs-lookup"><span data-stu-id="095e9-128">Restart the server.</span></span> <span data-ttu-id="095e9-129">Войдите и щелкните **ссылку "Календарь"** на панели nav.</span><span class="sxs-lookup"><span data-stu-id="095e9-129">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="095e9-130">Если все работает, в календаре пользователя должен быть дамп событий JSON.</span><span class="sxs-lookup"><span data-stu-id="095e9-130">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="095e9-131">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="095e9-131">Display the results</span></span>

<span data-ttu-id="095e9-132">Теперь вы можете добавить HTML для более удобного отображения результатов.</span><span class="sxs-lookup"><span data-stu-id="095e9-132">Now you can add HTML to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="095e9-133">Откройте **./app/views/calendar/index.html.erb** и замените его содержимое на следующее.</span><span class="sxs-lookup"><span data-stu-id="095e9-133">Open **./app/views/calendar/index.html.erb** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/app/views/calendar/index.html.erb" id="CalendarSnippet":::

    <span data-ttu-id="095e9-134">При этом будет цикл через коллекцию событий и добавлена строка таблицы для каждого из них.</span><span class="sxs-lookup"><span data-stu-id="095e9-134">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="095e9-135">Удалите `render json: @events` строку из действия `index` в **./app/controllers/calendar_controller.rb**.</span><span class="sxs-lookup"><span data-stu-id="095e9-135">Remove the `render json: @events` line from the `index` action in **./app/controllers/calendar_controller.rb**.</span></span>

1. <span data-ttu-id="095e9-136">Обновите страницу, и приложение должно отрисовки таблицы событий.</span><span class="sxs-lookup"><span data-stu-id="095e9-136">Refresh the page and the app should now render a table of events.</span></span>

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
