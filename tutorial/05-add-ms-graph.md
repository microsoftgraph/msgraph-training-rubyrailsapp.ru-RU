<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="44545-101">В этом упражнении вы добавите Microsoft Graph в приложение.</span><span class="sxs-lookup"><span data-stu-id="44545-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="44545-102">Для этого приложения вы будете использовать драгоценный камень [хттпарти](https://github.com/jnunemaker/httparty) для совершения звонков в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="44545-102">For this application, you will use the [httparty](https://github.com/jnunemaker/httparty) gem to make calls to Microsoft Graph.</span></span>

## <a name="create-a-graph-helper"></a><span data-ttu-id="44545-103">Создание модуля поддержки Graph</span><span class="sxs-lookup"><span data-stu-id="44545-103">Create a Graph helper</span></span>

1. <span data-ttu-id="44545-104">Создайте вспомогательный объект для управления всеми вызовами API.</span><span class="sxs-lookup"><span data-stu-id="44545-104">Create a helper to manage all of your API calls.</span></span> <span data-ttu-id="44545-105">Выполните следующую команду в командной панели CLI, чтобы создать помощника.</span><span class="sxs-lookup"><span data-stu-id="44545-105">Run the following command in your CLI to generate the helper.</span></span>

    ```Shell
    rails generate helper Graph
    ```

1. <span data-ttu-id="44545-106">Откройте **./апп/хелперс/graph_helper. RB** и замените его содержимое приведенным ниже содержимым.</span><span class="sxs-lookup"><span data-stu-id="44545-106">Open **./app/helpers/graph_helper.rb** and replace the contents with the following.</span></span>

    ```ruby
    require 'httparty'

    # Graph API helper methods
    module GraphHelper
      GRAPH_HOST = 'https://graph.microsoft.com'.freeze

      def make_api_call(endpoint, token, params = nil)
        headers = {
          Authorization: "Bearer #{token}"
        }

        query = params || {}

        HTTParty.get "#{GRAPH_HOST}#{endpoint}",
                     headers: headers,
                     query: query
      end
    end
    ```

<span data-ttu-id="44545-107">Уделите время, чтобы узнать, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="44545-107">Take a moment to review what this code does.</span></span> <span data-ttu-id="44545-108">Он выполняет простой запрос GET через `httparty` драгоценный камень через драгоценный камень в запрашиваемую конечную точку.</span><span class="sxs-lookup"><span data-stu-id="44545-108">It makes a simple GET request via the `httparty` gem to the requested endpoint.</span></span> <span data-ttu-id="44545-109">Он отправляет маркер доступа в `Authorization` заголовке и включает все переданные параметры запроса.</span><span class="sxs-lookup"><span data-stu-id="44545-109">It sends the access token in the `Authorization` header, and it includes any query parameters that are passed.</span></span>

<span data-ttu-id="44545-110">Например, чтобы использовать `make_api_call` метод для получения `https://graph.microsoft.com/v1.0/me?$select=displayName`, можно вызвать его следующим образом:</span><span class="sxs-lookup"><span data-stu-id="44545-110">For example, to use the `make_api_call` method to do a GET to `https://graph.microsoft.com/v1.0/me?$select=displayName`, you could call it like so:</span></span>

```ruby
make_api_call `/v1.0/me`, access_token, { '$select': 'displayName' }
```

<span data-ttu-id="44545-111">Вы создадите это позднее при реализации дополнительных функций Microsoft Graph в приложении.</span><span class="sxs-lookup"><span data-stu-id="44545-111">You'll build on this later as you implement more Microsoft Graph features into the app.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="44545-112">Получение событий календаря из Outlook</span><span class="sxs-lookup"><span data-stu-id="44545-112">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="44545-113">Чтобы добавить новый контроллер, в интерфейсе командной строки выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="44545-113">In your CLI, run the following command to add a new controller.</span></span>

    ```Shell
    rails generate controller Calendar index
    ```

1. <span data-ttu-id="44545-114">Добавьте новый маршрут в **./конфиг/раутес.РБ**.</span><span class="sxs-lookup"><span data-stu-id="44545-114">Add the new route to **./config/routes.rb**.</span></span>

    ```ruby
    get 'calendar', to: 'calendar#index'
    ```

1. <span data-ttu-id="44545-115">Добавьте новый метод в модуль поддержки графов, чтобы получить [список событий пользователя](/graph/api/user-list-events?view=graph-rest-1.0).</span><span class="sxs-lookup"><span data-stu-id="44545-115">Add a new method to the Graph helper to [list the user's events](/graph/api/user-list-events?view=graph-rest-1.0).</span></span> <span data-ttu-id="44545-116">Откройте **./апп/хелперс/graph_helper. RB** и добавьте в `GraphHelper` модуль следующий метод.</span><span class="sxs-lookup"><span data-stu-id="44545-116">Open **./app/helpers/graph_helper.rb** and add the following method to the `GraphHelper` module.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/helpers/graph_helper.rb" id="GetCalendarSnippet":::

    <span data-ttu-id="44545-117">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="44545-117">Consider what this code is doing.</span></span>

    - <span data-ttu-id="44545-118">URL-адрес, который будет вызываться — это `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="44545-118">The URL that will be called is `/v1.0/me/events`.</span></span>
    - <span data-ttu-id="44545-119">`$select` Параметр позволяет ограничить поля, возвращаемые для каждого события, только теми, которые используются нашим представлением.</span><span class="sxs-lookup"><span data-stu-id="44545-119">The `$select` parameter limits the fields returned for each events to just those our view will actually use.</span></span>
    - <span data-ttu-id="44545-120">`$orderby` Параметр сортирует результаты по дате и времени создания, начиная с самого последнего элемента.</span><span class="sxs-lookup"><span data-stu-id="44545-120">The `$orderby` parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>
    - <span data-ttu-id="44545-121">Для успешного ответа он возвращает массив элементов, содержащийся в `value` ключе.</span><span class="sxs-lookup"><span data-stu-id="44545-121">For a successful response, it returns the array of items contained in the `value` key.</span></span>

1. <span data-ttu-id="44545-122">Откройте **./апп/контроллерс/calendar_controller. RB** и замените все его содержимое на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="44545-122">Open **./app/controllers/calendar_controller.rb** and replace its entire contents with the following.</span></span>

    ```ruby
    # Calendar controller
    class CalendarController < ApplicationController
      include GraphHelper

      def index
        @events = get_calendar_events access_token || []
        render json: @events
      rescue RuntimeError => e
        @errors = [
          {
            message: 'Microsoft Graph returned an error getting events.',
            debug: e
          }
        ]
      end
    end
    ```

1. <span data-ttu-id="44545-123">Перезапустите сервер.</span><span class="sxs-lookup"><span data-stu-id="44545-123">Restart the server.</span></span> <span data-ttu-id="44545-124">Войдите и щелкните ссылку **Календарь** на панели навигации.</span><span class="sxs-lookup"><span data-stu-id="44545-124">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="44545-125">Если все работает, вы должны увидеть дамп событий JSON в календаре пользователя.</span><span class="sxs-lookup"><span data-stu-id="44545-125">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="44545-126">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="44545-126">Display the results</span></span>

<span data-ttu-id="44545-127">Теперь можно добавить HTML-код для отображения результатов более удобным для пользователя способом.</span><span class="sxs-lookup"><span data-stu-id="44545-127">Now you can add HTML to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="44545-128">Откройте **/АПП/виевс/календар/индекс.хтмл.ЕРБ** и замените его содержимое приведенным ниже.</span><span class="sxs-lookup"><span data-stu-id="44545-128">Open **./app/views/calendar/index.html.erb** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/app/views/calendar/index.html.erb" id="CalendarSnippet":::

    <span data-ttu-id="44545-129">Это приведет к перебору коллекции событий и добавлению строки таблицы для каждой из них.</span><span class="sxs-lookup"><span data-stu-id="44545-129">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="44545-130">Удалите `render json: @events` строку из `index` действия в файле **./АПП/контроллерс/calendar_controller. RB**.</span><span class="sxs-lookup"><span data-stu-id="44545-130">Remove the `render json: @events` line from the `index` action in **./app/controllers/calendar_controller.rb**.</span></span>

1. <span data-ttu-id="44545-131">Обновите страницу, после чего приложение должно отобразить таблицу событий.</span><span class="sxs-lookup"><span data-stu-id="44545-131">Refresh the page and the app should now render a table of events.</span></span>

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
