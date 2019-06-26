<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы добавите Microsoft Graph в приложение. Для этого приложения вы будете использовать драгоценный камень [хттпарти](https://github.com/jnunemaker/httparty) для совершения звонков в Microsoft Graph.

## <a name="create-a-graph-helper"></a>Создание модуля поддержки Graph

Создайте вспомогательный объект для управления всеми вызовами API. Выполните следующую команду в командной панели CLI, чтобы создать помощника.

```Shell
rails generate helper Graph
```

Откройте созданный `./app/helpers/graph_helper.rb` файл и замените его содержимое приведенным ниже.

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

Уделите время, чтобы узнать, что делает этот код. Он выполняет простой запрос GET через `httparty` драгоценный камень через драгоценный камень в запрашиваемую конечную точку. Он отправляет маркер доступа в `Authorization` заголовке и включает все переданные параметры запроса.

Например, чтобы использовать `make_api_call` метод для получения `https://graph.microsoft.com/v1.0/me?$select=displayName`, можно вызвать его следующим образом:

```ruby
make_api_call `/v1.0/me`, access_token, { '$select': 'displayName' }
```

Вы создадите это позднее при реализации дополнительных функций Microsoft Graph в приложении.

## <a name="get-calendar-events-from-outlook"></a>Получение событий календаря из Outlook

Для начала добавим возможность просмотра событий в календаре пользователя. Чтобы добавить новый контроллер, в интерфейсе командной строки выполните следующую команду.

```Shell
rails generate controller Calendar index
```

Теперь, когда у нас есть доступный маршрут, обновите ссылку на **Календарь** в панели `./app/view/layouts/application.html.erb` навигации, чтобы использовать ее. Замените строку на `<a class="nav-link" href="#">Calendar</a>` приведенную ниже строку.

```html
<%= link_to "Calendar", {:controller => :calendar, :action => :index}, class: "nav-link#{' active' if controller.controller_name == 'calendar'}" %>
```

Добавьте новый метод в модуль поддержки графов, чтобы получить [список событий пользователя](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_list_events). Откройте `./app/helpers/graph_helper.rb` следующий метод в `GraphHelper` модуле и добавьте к нему следующий метод.

```ruby
def get_calendar_events(token)
  get_events_url = '/v1.0/me/events'

  query = {
    '$select': 'subject,organizer,start,end',
    '$orderby': 'createdDateTime DESC'
  }

  response = make_api_call get_events_url, token, query

  raise response.parsed_response.to_s || "Request returned #{response.code}" unless response.code == 200
  response.parsed_response['value']
end
```

Рассмотрите, что делает этот код.

- URL-адрес, который будет вызываться — это `/v1.0/me/events`.
- `$select` Параметр позволяет ограничить поля, возвращаемые для каждого события, только теми, которые используются нашим представлением.
- `$orderby` Параметр сортирует результаты по дате и времени создания, начиная с самого последнего элемента.
- Для успешного ответа он возвращает массив элементов, содержащийся в `value` ключе.

Теперь вы можете протестировать это. Откройте `./app/controllers/calendar_controller.rb` и обновите `index` действие, чтобы вызвать этот метод и отобразить результаты.

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

Перезапустите сервер. Войдите и щелкните ссылку **Календарь** на панели навигации. Если все работает, вы должны увидеть дамп событий JSON в календаре пользователя.

## <a name="display-the-results"></a>Отображение результатов

Теперь вы можете добавить HTML и CSS для отображения результатов более удобным для пользователя способом.

Откройте `./app/views/calendar/index.html.erb` и замените его содержимое приведенным ниже.

```html
<h1>Calendar</h1>
<table class="table">
  <thead>
    <tr>
      <th scope="col">Organizer</th>
      <th scope="col">Subject</th>
      <th scope="col">Start</th>
      <th scope="col">End</th>
    </tr>
  </thead>
  <tbody>
    <% @events.each do |event| %>
      <tr>
        <td><%= event['organizer']['emailAddress']['name'] %></td>
        <td><%= event['subject'] %></td>
        <td><%= event['start']['dateTime'].to_time(:utc).localtime.strftime('%-m/%-d/%y %l:%M %p') %></td>
        <td><%= event['end']['dateTime'].to_time(:utc).localtime.strftime('%-m/%-d/%y %l:%M %p') %></td>
      </tr>
    <% end %>
  </tbody>
</table>
```

Это приведет к перебору коллекции событий и добавлению строки таблицы для каждой из них. Удаление `render json: @events` строки из `index` действия в `./app/controllers/calendar_controller.rb` и приложение теперь отображает таблицу событий.

![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
