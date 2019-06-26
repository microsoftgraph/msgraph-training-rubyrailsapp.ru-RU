<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="b82e7-101">В этом упражнении вы будете использовать для создания веб-приложения [транскрипцию на шинах](https://rubyonrails.org/) .</span><span class="sxs-lookup"><span data-stu-id="b82e7-101">In this exercise you will use [Ruby on Rails](https://rubyonrails.org/) to build a web app.</span></span> <span data-ttu-id="b82e7-102">Если вы еще не установили салазки, вы можете установить его из интерфейса командной строки (CLI) с помощью следующей команды.</span><span class="sxs-lookup"><span data-stu-id="b82e7-102">If you don't already have Rails installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

```Shell
gem install rails
```

<span data-ttu-id="b82e7-103">Откройте подсистему CLI, перейдите к каталогу, в котором у вас есть права на создание файлов, и выполните следующую команду, чтобы создать новое приложение с ограждениями.</span><span class="sxs-lookup"><span data-stu-id="b82e7-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Rails app.</span></span>

```Shell
rails new graph-tutorial
```

<span data-ttu-id="b82e7-104">С помощью салазок создается новый каталог с `graph-tutorial` именем и формированием шаблонов для приложения с ограждением.</span><span class="sxs-lookup"><span data-stu-id="b82e7-104">Rails creates a new directory called `graph-tutorial` and scaffolds a Rails app.</span></span> <span data-ttu-id="b82e7-105">Перейдите к новому каталогу и введите следующую команду для запуска локального веб-сервера.</span><span class="sxs-lookup"><span data-stu-id="b82e7-105">Navigate to this new directory and enter the following command to start a local web server.</span></span>

```Shell
rails server
```

<span data-ttu-id="b82e7-106">Откройте браузер и перейдите по адресу `http://localhost:3000`.</span><span class="sxs-lookup"><span data-stu-id="b82e7-106">Open your browser and navigate to `http://localhost:3000`.</span></span> <span data-ttu-id="b82e7-107">Если все работает, вы увидите "Яи!</span><span class="sxs-lookup"><span data-stu-id="b82e7-107">If everything is working, you will see a "Yay!</span></span> <span data-ttu-id="b82e7-108">Вы находитесь на салазках! "</span><span class="sxs-lookup"><span data-stu-id="b82e7-108">You're on Rails!"</span></span> <span data-ttu-id="b82e7-109">Сообщение.</span><span class="sxs-lookup"><span data-stu-id="b82e7-109">message.</span></span> <span data-ttu-id="b82e7-110">Если вы не видите это сообщение, ознакомьтесь с [руководством по началу работы](http://guides.rubyonrails.org/)с салазками.</span><span class="sxs-lookup"><span data-stu-id="b82e7-110">If you don't see that message, check the [Rails getting started guide](http://guides.rubyonrails.org/).</span></span>

<span data-ttu-id="b82e7-111">Прежде чем переходить, установите несколько дополнительных драгоценных камней, которые будут использоваться позже:</span><span class="sxs-lookup"><span data-stu-id="b82e7-111">Before moving on, install some additional gems that you will use later:</span></span>

- <span data-ttu-id="b82e7-112">[omniauth — OAuth2](https://github.com/omniauth/omniauth-oauth2) для обработки входных потоков и маркеров OAuth.</span><span class="sxs-lookup"><span data-stu-id="b82e7-112">[omniauth-oauth2](https://github.com/omniauth/omniauth-oauth2) for handling sign-in and OAuth token flows.</span></span>
- <span data-ttu-id="b82e7-113">[хттпарти](https://github.com/jnunemaker/httparty) для совершения вызовов в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="b82e7-113">[httparty](https://github.com/jnunemaker/httparty) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="b82e7-114">[нокогири](https://github.com/sparklemotion/nokogiri) , чтобы обрабатывать тексты электронной почты в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="b82e7-114">[nokogiri](https://github.com/sparklemotion/nokogiri) to process HTML bodies of email.</span></span>
- <span data-ttu-id="b82e7-115">[активерекорд — сессион_сторе](https://github.com/rails/activerecord-session_store) для хранения сеансов в базе данных.</span><span class="sxs-lookup"><span data-stu-id="b82e7-115">[activerecord-session_store](https://github.com/rails/activerecord-session_store) for storing sessions in the database.</span></span>

<span data-ttu-id="b82e7-116">Выполните следующие команды в командной панели CLI.</span><span class="sxs-lookup"><span data-stu-id="b82e7-116">Run the following commands in your CLI.</span></span>

```Shell
bundle add omniauth-oauth2
bundle add httparty
bundle add nokogiri
bundle add activerecord-session_store
rails generate active_record:session_migration
```

<span data-ttu-id="b82e7-117">Последняя команда создает выходные данные, как показано ниже:</span><span class="sxs-lookup"><span data-stu-id="b82e7-117">The last command generates output like the following:</span></span>

```Shell
create  db/migrate/20180618172216_add_sessions_table.rb
```

<span data-ttu-id="b82e7-118">Откройте созданный файл и перейдите к следующей строке.</span><span class="sxs-lookup"><span data-stu-id="b82e7-118">Open the file that was created and locate the following line.</span></span>

```ruby
class AddSessionsTable < ActiveRecord::Migration
```

<span data-ttu-id="b82e7-119">Измените эту строку на следующий.</span><span class="sxs-lookup"><span data-stu-id="b82e7-119">Change that line to the following.</span></span>

```ruby
class AddSessionsTable < ActiveRecord::Migration[5.2]
```

> [!NOTE]
> <span data-ttu-id="b82e7-120">Предполагается, что вы используете салазки 5.2. x.</span><span class="sxs-lookup"><span data-stu-id="b82e7-120">This assumes that you are using Rails 5.2.x.</span></span> <span data-ttu-id="b82e7-121">Если вы используете другую версию, замените `5.2` ее версией.</span><span class="sxs-lookup"><span data-stu-id="b82e7-121">If you are using a different version, replace `5.2` with your version.</span></span>

<span data-ttu-id="b82e7-122">Сохраните файл и выполните приведенную ниже команду.</span><span class="sxs-lookup"><span data-stu-id="b82e7-122">Save the file and run the following command.</span></span>

```Shell
rake db:migrate
```

<span data-ttu-id="b82e7-123">Наконец, настройте салазки для использования нового хранилища сеансов.</span><span class="sxs-lookup"><span data-stu-id="b82e7-123">Finally, configure Rails to use the new session store.</span></span> <span data-ttu-id="b82e7-124">Создайте `session_store.rb` в `./config/initializers` каталоге новый файл и добавьте приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="b82e7-124">Create a new file called `session_store.rb` in the `./config/initializers` directory, and add the following code.</span></span>

```ruby
Rails.application.config.session_store :active_record_store, key: '_graph_app_session'
```

## <a name="design-the-app"></a><span data-ttu-id="b82e7-125">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="b82e7-125">Design the app</span></span>

<span data-ttu-id="b82e7-126">Начните с обновления глобального макета для приложения.</span><span class="sxs-lookup"><span data-stu-id="b82e7-126">Start by updating the global layout for the app.</span></span> <span data-ttu-id="b82e7-127">Откройте `./app/views/layouts/application.html.erb` и замените его содержимое приведенным ниже.</span><span class="sxs-lookup"><span data-stu-id="b82e7-127">Open `./app/views/layouts/application.html.erb` and replace its contents with the following.</span></span>

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

<span data-ttu-id="b82e7-128">В этом коде [](http://getbootstrap.com/) добавляется начальная загрузка для простых стилей и [Шрифт Awesome](https://fontawesome.com/) для некоторых простых значков.</span><span class="sxs-lookup"><span data-stu-id="b82e7-128">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="b82e7-129">Он также определяет глобальную структуру с помощью панели навигации.</span><span class="sxs-lookup"><span data-stu-id="b82e7-129">It also defines a global layout with a nav bar.</span></span>

<span data-ttu-id="b82e7-130">Теперь откройте `./app/assets/stylesheets/application.css` и добавьте следующий элемент в конец файла.</span><span class="sxs-lookup"><span data-stu-id="b82e7-130">Now open `./app/assets/stylesheets/application.css` and add the following to the end of the file.</span></span>

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

<span data-ttu-id="b82e7-131">Теперь замените страницу по умолчанию новой.</span><span class="sxs-lookup"><span data-stu-id="b82e7-131">Now replace the default page with a new one.</span></span> <span data-ttu-id="b82e7-132">Создайте контроллер домашней страницы с помощью следующей команды.</span><span class="sxs-lookup"><span data-stu-id="b82e7-132">Generate a home page controller with the following command.</span></span>

```Shell
rails generate controller Home index
```

<span data-ttu-id="b82e7-133">Затем настройте `index` действие на `Home` контроллере как страницу по умолчанию для приложения.</span><span class="sxs-lookup"><span data-stu-id="b82e7-133">Then configure the `index` action on the `Home` controller as the default page for the app.</span></span> <span data-ttu-id="b82e7-134">Откройте `./config/routes.rb` и замените содержимое приведенным ниже</span><span class="sxs-lookup"><span data-stu-id="b82e7-134">Open `./config/routes.rb` and replace the contents with the following</span></span>

```ruby
Rails.application.routes.draw do
  get 'home/index'
  root 'home#index'

  # Add future routes here

end
```

<span data-ttu-id="b82e7-135">Теперь откройте `./app/view/home/index.html.erb` файл и замените его содержимое приведенным ниже.</span><span class="sxs-lookup"><span data-stu-id="b82e7-135">Now open the `./app/view/home/index.html.erb` file and replace its contents with the following.</span></span>

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

<span data-ttu-id="b82e7-136">Сохраните все изменения и перезапустите сервер.</span><span class="sxs-lookup"><span data-stu-id="b82e7-136">Save all of your changes and restart the server.</span></span> <span data-ttu-id="b82e7-137">Теперь приложение должно выглядеть по-другому.</span><span class="sxs-lookup"><span data-stu-id="b82e7-137">Now, the app should look very different.</span></span>

![Снимок экрана с переработанной домашней страницей](./images/create-app-01.png)
