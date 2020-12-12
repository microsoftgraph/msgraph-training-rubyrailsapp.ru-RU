<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы включаете Microsoft Graph в приложение. Для этого приложения вы будете использовать httparty-самосхему для вызова Microsoft Graph. [](https://github.com/jnunemaker/httparty)

## <a name="create-a-graph-helper"></a>Создание помощника Graph

1. Создайте помощник для управления всеми вызовами API. Чтобы создать помощник, запустите следующую команду в CLI.

    ```Shell
    rails generate helper Graph
    ```

1. Откройте **./app/helpers/graph_helper.rb** и замените содержимое на следующее.

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

Просмотрите, что делает этот код. Он делает простой запрос GET или POST с помощью самосхемы `httparty` в запрашиваемую конечную точку. Он отправляет маркер доступа в заголке и включает все переданные `Authorization` параметры запроса.

Например, чтобы использовать метод для `make_api_call` получения `https://graph.microsoft.com/v1.0/me?$select=displayName` get, можно вызвать его так:

```ruby
make_api_call 'GET', '/v1.0/me', access_token, { '$select': 'displayName' }
```

Вы сможете реализации дополнительных функций Microsoft Graph в приложении позже.

## <a name="get-calendar-events-from-outlook"></a>Получить события календаря из Outlook

1. В CLI запустите следующую команду, чтобы добавить новый контроллер.

    ```Shell
    rails generate controller Calendar index new
    ```

1. Добавьте новый маршрут в **./config/routes.rb.**

    ```ruby
    get 'calendar', :to => 'calendar#index'
    ```

1. Добавьте новый метод во справку Graph, чтобы [получить представление календаря.](https://docs.microsoft.com/graph/api/calendar-list-calendarview?view=graph-rest-1.0) Откройте **./app/helpers/graph_helper.rb** и добавьте в модуль следующий `GraphHelper` метод.

    :::code language="ruby" source="../demo/graph-tutorial/app/helpers/graph_helper.rb" id="GetCalendarSnippet":::

    Подумайте, что делает этот код.

    - Будет вызван URL-адрес `/v1.0/me/calendarview` .
        - Заголок приводит к корректировке времени начала и окончания результатов в часовом `Prefer: outlook.timezone` поясе пользователя.
        - Параметры `startDateTime` и начало и конец `endDateTime` представления.
        - Параметр `$select` ограничивает поля, возвращаемые для каждого события, только теми полями, которые будут фактически использовать представление.
        - Параметр `$orderby` сортировать результаты по времени начала.
        - Этот `$top` параметр ограничивает результаты до 50 событий.
    - Для успешного отклика он возвращает массив элементов, содержащихся в `value` ключе.

1. Добавьте во помощник Graph новый метод для получения идентификатора часового пояса [IANA](https://www.iana.org/time-zones) на основе имени часового пояса Windows. Это необходимо, так как Microsoft Graph может возвращать часовые пояса в качестве имен часовых поясов Windows, а для класса Ruby **DateTime** требуются идентификаторы часовых поясов IANA.

    :::code language="ruby" source="../demo/graph-tutorial/app/helpers/graph_helper.rb" id="ZoneMappingSnippet":::

1. Откройте **./app/controllers/calendar_controller.rb** и замените все его содержимое на следующее.

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

1. Перезапустите сервер. Войдите и щелкните **ссылку "Календарь"** на панели nav. Если все работает, в календаре пользователя должен быть дамп событий JSON.

## <a name="display-the-results"></a>Отображение результатов

Теперь вы можете добавить HTML для более удобного отображения результатов.

1. Откройте **./app/views/calendar/index.html.erb** и замените его содержимое на следующее.

    :::code language="html" source="../demo/graph-tutorial/app/views/calendar/index.html.erb" id="CalendarSnippet":::

    При этом будет цикл через коллекцию событий и добавлена строка таблицы для каждого из них.

1. Удалите `render json: @events` строку из действия `index` в **./app/controllers/calendar_controller.rb**.

1. Обновите страницу, и приложение должно отрисовки таблицы событий.

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
