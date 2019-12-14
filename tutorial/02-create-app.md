<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете использовать для создания веб-приложения [транскрипцию на шинах](https://rubyonrails.org/) . Если вы еще не установили салазки, вы можете установить его из интерфейса командной строки (CLI) с помощью следующей команды.

```Shell
gem install rails
```

Откройте подсистему CLI, перейдите к каталогу, в котором у вас есть права на создание файлов, и выполните следующую команду, чтобы создать новое приложение с ограждениями.

```Shell
rails new graph-tutorial
```

С помощью салазок создается новый каталог с `graph-tutorial` именем и формированием шаблонов для приложения с ограждением. Перейдите к новому каталогу и введите следующую команду для запуска локального веб-сервера.

```Shell
rails server
```

Откройте браузер и перейдите по адресу `http://localhost:3000`. Если все работает, вы увидите "Яи! Вы находитесь на салазках! " Сообщение. Если вы не видите это сообщение, ознакомьтесь с [руководством по началу работы с салазками](http://guides.rubyonrails.org/).

Прежде чем переходить, установите несколько дополнительных драгоценных камней, которые будут использоваться позже:

- [omniauth — OAuth2](https://github.com/omniauth/omniauth-oauth2) для обработки входных потоков и маркеров OAuth.
- [omniauth — rails_csrf_protection](https://github.com/cookpad/omniauth-rails_csrf_protection) для добавления защиты Ксрф в omniauth.
- [хттпарти](https://github.com/jnunemaker/httparty) для совершения вызовов в Microsoft Graph.
- [нокогири](https://github.com/sparklemotion/nokogiri) , чтобы обрабатывать тексты электронной почты в формате HTML.
- [активерекорд — session_store](https://github.com/rails/activerecord-session_store) для хранения сеансов в базе данных.

Выполните следующие команды в командной панели CLI.

```Shell
bundle add omniauth-oauth2
bundle add omniauth-rails_csrf_protection
bundle add httparty
bundle add nokogiri
bundle add activerecord-session_store
rails generate active_record:session_migration
```

Последняя команда создает выходные данные, как показано ниже:

```Shell
create  db/migrate/20180618172216_add_sessions_table.rb
```

Откройте созданный файл и перейдите к следующей строке.

```ruby
class AddSessionsTable < ActiveRecord::Migration
```

Измените эту строку на следующий.

```ruby
class AddSessionsTable < ActiveRecord::Migration[5.2]
```

> [!NOTE]
> Предполагается, что вы используете салазки 5.2. x. Если вы используете другую версию, замените `5.2` ее версией.

Сохраните файл и выполните приведенную ниже команду.

```Shell
rake db:migrate
```

Наконец, настройте салазки для использования нового хранилища сеансов. Создайте `session_store.rb` в `./config/initializers` каталоге новый файл и добавьте приведенный ниже код.

```ruby
Rails.application.config.session_store :active_record_store, key: '_graph_app_session'
```

## <a name="design-the-app"></a>Проектирование приложения

Начните с обновления глобального макета для приложения. Откройте `./app/views/layouts/application.html.erb` и замените его содержимое приведенным ниже.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Ruby Graph Tutorial</title>
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/css/bootstrap.min.css"
      integrity="sha384-WskhaSGFgHYWDcbwN70/dfYBj47jz9qbsMId/iRN3ewGhXQFZCSftd1LZCfmhktB" crossorigin="anonymous">
    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.1.0/css/all.css"
      integrity="sha384-lKuwvrZot6UHsBSfcMvOkWwlCMgc0TaWr+30HWe3a4ltaBwTZhyTEggF5tJv8tbt" crossorigin="anonymous">
    <script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js"
      integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/js/bootstrap.min.js"
      integrity="sha384-smHYKdLADwkXOn1EmN1qk/HfnUcbVRZyYmZ4qpPea6sjB/pTJ0euyQp0Mk8ck+5T" crossorigin="anonymous"></script>
    <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>

  <body>
    <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
      <div class="container">
        <%= link_to "Ruby Graph Tutorial", root_path, class: "navbar-brand" %>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse"
          aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarCollapse">
          <ul class="navbar-nav mr-auto">
            <li class="nav-item">
              <%= link_to "Home", root_path, class: "nav-link#{' active' if controller.controller_name == 'home'}" %>
            </li>
            <% if @user_name %>
              <li class="nav-item" data-turbolinks="false">
                <a class="nav-link" href="#">Calendar</a>
              </li>
            <% end %>
          </ul>
          <ul class="navbar-nav justify-content-end">
            <li class="nav-item">
              <a class="nav-link" href="https://developer.microsoft.com/graph/docs/concepts/overview" target="_blank">
                <i class="fas fa-external-link-alt mr-1"></i>Docs
              </a>
            </li>
            <% if @user_name %>
              <li class="nav-item dropdown">
                <a class="nav-link dropdown-toggle" data-toggle="dropdown" href="#" role="button" aria-haspopup="true" aria-expanded="false">
                  <% if @user_avatar %>
                    <img src=<%= @user_avatar %> class="rounded-circle align-self-center mr-2" style="width: 32px;">
                  <% else %>
                    <i class="far fa-user-circle fa-lg rounded-circle align-self-center mr-2" style="width: 32px;"></i>
                  <% end %>
                </a>
                <div class="dropdown-menu dropdown-menu-right">
                  <h5 class="dropdown-item-text mb-0"><%= @user_name %></h5>
                  <p class="dropdown-item-text text-muted mb-0"><%= @user_email %></p>
                  <div class="dropdown-divider"></div>
                  <a href="#" class="dropdown-item">Sign Out</a>
                </div>
              </li>
            <% else %>
              <li class="nav-item">
                <a href="#" class="nav-link">Sign In</a>
              </li>
            <% end %>
          </ul>
        </div>
      </div>
    </nav>
    <main role="main" class="container">
      <% if @errors %>
        <% @errors.each do |error| %>
          <div class="alert alert-danger" role="alert">
            <p class="mb-3"><%= error[:message] %></p>
            <%if error[:debug] %>
              <pre class="alert-pre border bg-light p-2"><code><%= error[:debug] %></code></pre>
            <% end %>
          </div>
        <% end %>
      <% end %>
      <%= yield %>
    </main>
  </body>
</html>
```

В этом коде добавляется [Начальная](http://getbootstrap.com/) загрузка для простых стилей и [Шрифт Awesome](https://fontawesome.com/) для некоторых простых значков. Он также определяет глобальную структуру с помощью панели навигации.

Теперь откройте `./app/assets/stylesheets/application.css` и добавьте следующий элемент в конец файла.

```css
body {
  padding-top: 4.5rem;
}

.alert-pre {
  word-wrap: break-word;
  word-break: break-all;
  white-space: pre-wrap;
}
```

Теперь замените страницу по умолчанию новой. Создайте контроллер домашней страницы с помощью следующей команды.

```Shell
rails generate controller Home index
```

Затем настройте `index` действие на `Home` контроллере как страницу по умолчанию для приложения. Откройте `./config/routes.rb` и замените содержимое приведенным ниже

```ruby
Rails.application.routes.draw do
  get 'home/index'
  root 'home#index'

  # Add future routes here

end
```

Теперь откройте `./app/view/home/index.html.erb` файл и замените его содержимое приведенным ниже.

```html
<div class="jumbotron">
  <h1>Ruby Graph Tutorial</h1>
  <p class="lead">This sample app shows how to use the Microsoft Graph API to access Outlook and OneDrive data from Ruby</p>
  <% if @user_name %>
    <h4>Welcome <%= @user_name %>!</h4>
    <p>Use the navigation bar at the top of the page to get started.</p>
  <% else %>
    <a href="#" class="btn btn-primary btn-large">Click here to sign in</a>
  <% end %>
</div>
```

Сохраните все изменения и перезапустите сервер. Теперь приложение должно выглядеть по-другому.

![Снимок экрана с переработанной домашней страницей](./images/create-app-01.png)
