<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете включать Graph Microsoft в приложение. Для этого приложения вы будете использовать [драгоценный камень httparty](https://github.com/jnunemaker/httparty) для звонков в Microsoft Graph.

## <a name="create-a-graph-helper"></a>Создание помощника Graph

1. Создайте помощника для управления всеми вызовами API. Запустите следующую команду в CLI для создания помощника.

    ```Shell
    rails generate helper Graph
    ```

1. Откройте **./app/helpers/graph_helper.rb** и замените содержимое следующим.

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

Найми минутку, чтобы просмотреть, что делает этот код. Он делает простой запрос GET или POST через драгоценный камень `httparty` в запрашиваемую конечную точку. Он отправляет маркер доступа в загонах и включает все параметры запроса, которые `Authorization` передаются.

Например, чтобы использовать метод get to, можно вызвать `make_api_call` `https://graph.microsoft.com/v1.0/me?$select=displayName` его так:

```ruby
make_api_call 'GET', '/v1.0/me', access_token, {}, { '$select': 'displayName' }
```

Это будет реализовано позже по мере реализации дополнительных функций Microsoft Graph в приложении.

## <a name="get-calendar-events-from-outlook"></a>Получение событий календаря из Outlook

1. В CLI запустите следующую команду, чтобы добавить новый контроллер.

    ```Shell
    rails generate controller Calendar index new
    ```

1. Добавьте новый маршрут **в ./config/routes.rb**.

    ```ruby
    get 'calendar', :to => 'calendar#index'
    ```

1. Добавьте новый метод в помощник Graph, чтобы [получить представление календаря.](https://docs.microsoft.com/graph/api/calendar-list-calendarview?view=graph-rest-1.0) Откройте **./app/helpers/graph_helper.rb** и добавьте следующий метод в `GraphHelper` модуль.

    :::code language="ruby" source="../demo/graph-tutorial/app/helpers/graph_helper.rb" id="GetCalendarSnippet":::

    Давайте посмотрим, что делает этот код.

    - Вызывается URL-адрес `/v1.0/me/calendarview`.
        - Заготвка приводит к корректировке времени начала и окончания результатов в часовом поясе `Prefer: outlook.timezone` пользователя.
        - Параметры `startDateTime` `endDateTime` и параметров заданы начало и конец представления.
        - Параметр `$select` ограничивает поля, возвращаемые для каждого события, только теми, которые будут фактически использовать представление.
        - Параметр `$orderby` сортировать результаты по времени начала.
        - Параметр ограничивает результаты до `$top` 50 событий.
    - Для успешного ответа возвращается массив элементов, содержащихся в `value` ключе.

1. Добавьте новый метод в помощник Graph, чтобы найти идентификатор часового пояса [IANA](https://www.iana.org/time-zones) на основе имени Windows часового пояса. Это необходимо, так как microsoft Graph может Windows часовых поясов в качестве имен часовых поясов, а для класса Ruby **DateTime** требуются идентификаторы часового пояса IANA.

    :::code language="ruby" source="../demo/graph-tutorial/app/helpers/graph_helper.rb" id="ZoneMappingSnippet":::

1. Откройте **./app/controllers/calendar_controller.rb** и замените все содержимое на следующее.

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

1. Перезапустите сервер. Войдите и щелкните **ссылку Календарь** в панели nav. Если все работает надлежащим образом, в календаре пользователя должен появиться дамп событий в формате JSON.

## <a name="display-the-results"></a>Отображение результатов

Теперь вы можете добавить HTML для отображения результатов более удобным для пользователя способом.

1. Откройте **./app/views/calendar/index.html.erb** и замените его содержимое следующим.

    :::code language="html" source="../demo/graph-tutorial/app/views/calendar/index.html.erb" id="CalendarSnippet":::

    Это позволяет повторять коллекцию событий и добавить для каждого из них строку таблицы.

1. Удалите строку из действия `render json: @events` `index` в **./app/controllers/calendar_controller.rb**.

1. Обновите страницу, и приложение должно теперь отрисовка таблицы событий.

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
