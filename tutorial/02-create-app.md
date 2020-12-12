<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="b80a2-101">В этом упражнении вы будете использовать [Ruby on Rails](https://rubyonrails.org/) для создания веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="b80a2-101">In this exercise you will use [Ruby on Rails](https://rubyonrails.org/) to build a web app.</span></span>

1. <span data-ttu-id="b80a2-102">Если вы еще не установили Rails, вы можете установить его из интерфейса командной строки (CLI) с помощью следующей команды.</span><span class="sxs-lookup"><span data-stu-id="b80a2-102">If you don't already have Rails installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

    ```Shell
    gem install rails -v 6.0.3.4
    ```

1. <span data-ttu-id="b80a2-103">Откройте CLI, перейдите в каталог, в котором у вас есть права на создание файлов, и запустите следующую команду, чтобы создать новое приложение Rails.</span><span class="sxs-lookup"><span data-stu-id="b80a2-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Rails app.</span></span>

    ```Shell
    rails new graph-tutorial
    ```

1. <span data-ttu-id="b80a2-104">Перейдите в этот новый каталог и введите следующую команду, чтобы запустить локальный веб-сервер.</span><span class="sxs-lookup"><span data-stu-id="b80a2-104">Navigate to this new directory and enter the following command to start a local web server.</span></span>

    ```Shell
    rails server
    ```

1. <span data-ttu-id="b80a2-105">Откройте браузер и перейдите по адресу `http://localhost:3000`.</span><span class="sxs-lookup"><span data-stu-id="b80a2-105">Open your browser and navigate to `http://localhost:3000`.</span></span> <span data-ttu-id="b80a2-106">Если все работает, вы увидите "Yay!</span><span class="sxs-lookup"><span data-stu-id="b80a2-106">If everything is working, you will see a "Yay!</span></span> <span data-ttu-id="b80a2-107">You're on Rails!"</span><span class="sxs-lookup"><span data-stu-id="b80a2-107">You're on Rails!"</span></span> <span data-ttu-id="b80a2-108">Сообщение.</span><span class="sxs-lookup"><span data-stu-id="b80a2-108">message.</span></span> <span data-ttu-id="b80a2-109">Если вы не видите это сообщение, проверьте руководство по [началу работы Rails.](http://guides.rubyonrails.org/)</span><span class="sxs-lookup"><span data-stu-id="b80a2-109">If you don't see that message, check the [Rails getting started guide](http://guides.rubyonrails.org/).</span></span>

## <a name="install-gems"></a><span data-ttu-id="b80a2-110">Установка самосхем</span><span class="sxs-lookup"><span data-stu-id="b80a2-110">Install gems</span></span>

<span data-ttu-id="b80a2-111">Прежде чем двигаться дальше, установите некоторые дополнительные самосхемы, которые вы будете использовать позже:</span><span class="sxs-lookup"><span data-stu-id="b80a2-111">Before moving on, install some additional gems that you will use later:</span></span>

- <span data-ttu-id="b80a2-112">[omniauth-oauth2](https://github.com/omniauth/omniauth-oauth2) для обработки потоков входов и маркеров OAuth.</span><span class="sxs-lookup"><span data-stu-id="b80a2-112">[omniauth-oauth2](https://github.com/omniauth/omniauth-oauth2) for handling sign-in and OAuth token flows.</span></span>
- <span data-ttu-id="b80a2-113">[omniauth-rails_csrf_protection](https://github.com/cookpad/omniauth-rails_csrf_protection) для добавления защиты CSRF в OmniAuth.</span><span class="sxs-lookup"><span data-stu-id="b80a2-113">[omniauth-rails_csrf_protection](https://github.com/cookpad/omniauth-rails_csrf_protection) for adding CSRF protection to OmniAuth.</span></span>
- <span data-ttu-id="b80a2-114">[httparty](https://github.com/jnunemaker/httparty) для звонков в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="b80a2-114">[httparty](https://github.com/jnunemaker/httparty) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="b80a2-115">[activerecord-session_store](https://github.com/rails/activerecord-session_store) для хранения сеансов в базе данных.</span><span class="sxs-lookup"><span data-stu-id="b80a2-115">[activerecord-session_store](https://github.com/rails/activerecord-session_store) for storing sessions in the database.</span></span>

1. <span data-ttu-id="b80a2-116">Откройте **./Gemfile** и добавьте следующие строки.</span><span class="sxs-lookup"><span data-stu-id="b80a2-116">Open **./Gemfile** and add the following lines.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/Gemfile" id="GemFileSnippet":::

1. <span data-ttu-id="b80a2-117">В CLI запустите следующую команду.</span><span class="sxs-lookup"><span data-stu-id="b80a2-117">In your CLI, run the following command.</span></span>

    ```Shell
    bundle install
    ```

1. <span data-ttu-id="b80a2-118">В CLI запустите следующие команды, чтобы настроить базу данных для хранения сеансов.</span><span class="sxs-lookup"><span data-stu-id="b80a2-118">In your CLI, run the following commands to configure the database for storing sessions.</span></span>

    ```Shell
    rails generate active_record:session_migration
    rake db:migrate
    ```

1. <span data-ttu-id="b80a2-119">Создайте файл, который будет вызван в `session_store.rb` **каталоге ./config/initializers,** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="b80a2-119">Create a new file called `session_store.rb` in the **./config/initializers** directory, and add the following code.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/config/initializers/session_store.rb" id="SessionStoreSnippet":::

## <a name="design-the-app"></a><span data-ttu-id="b80a2-120">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="b80a2-120">Design the app</span></span>

<span data-ttu-id="b80a2-121">В этом разделе вы создадим базовый пользовательский интерфейс для приложения.</span><span class="sxs-lookup"><span data-stu-id="b80a2-121">In this section you'll create the basic UI for the app.</span></span>

1. <span data-ttu-id="b80a2-122">Откройте **./app/views/layouts/application.html.erb** и замените его содержимое на следующее.</span><span class="sxs-lookup"><span data-stu-id="b80a2-122">Open **./app/views/layouts/application.html.erb** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/app/views/layouts/application.html.erb" id="LayoutSnippet":::

    <span data-ttu-id="b80a2-123">Этот код добавляет [Bootstrap](http://getbootstrap.com/) для простого стиля и [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) для некоторых простых значков.</span><span class="sxs-lookup"><span data-stu-id="b80a2-123">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) for some simple icons.</span></span> <span data-ttu-id="b80a2-124">Он также определяет глобальный макет с панели nav.</span><span class="sxs-lookup"><span data-stu-id="b80a2-124">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="b80a2-125">Откройте **файл ./app/assets/stylesheets/application.css** и добавьте в конец файла следующее:</span><span class="sxs-lookup"><span data-stu-id="b80a2-125">Open **./app/assets/stylesheets/application.css** and add the following to the end of the file.</span></span>

    :::code language="css" source="../demo/graph-tutorial/app/assets/stylesheets/application.css" id="CssSnippet":::

1. <span data-ttu-id="b80a2-126">Создание контроллера домашней страницы с помощью следующей команды.</span><span class="sxs-lookup"><span data-stu-id="b80a2-126">Generate a home page controller with the following command.</span></span>

    ```Shell
    rails generate controller Home index
    ```

1. <span data-ttu-id="b80a2-127">Настройте действие на контроллере в качестве `index` страницы по умолчанию для `Home` приложения.</span><span class="sxs-lookup"><span data-stu-id="b80a2-127">Configure the `index` action on the `Home` controller as the default page for the app.</span></span> <span data-ttu-id="b80a2-128">Откройте **./config/routes.rb** и замените его содержимое на следующее:</span><span class="sxs-lookup"><span data-stu-id="b80a2-128">Open **./config/routes.rb** and replace its contents with the following</span></span>

    ```ruby
    Rails.application.routes.draw do
      get 'home/index'
      root 'home#index'

      # Add future routes here

    end
    ```

1. <span data-ttu-id="b80a2-129">Откройте **./app/view/home/index.html.erb** и замените его содержимое на следующее.</span><span class="sxs-lookup"><span data-stu-id="b80a2-129">Open **./app/view/home/index.html.erb** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/app/views/home/index.html.erb" id="HomeSnippet":::

1. <span data-ttu-id="b80a2-130">Добавьте PNG-файл с **именемno-profile-photo.png** в **каталог ./app/assets/images.**</span><span class="sxs-lookup"><span data-stu-id="b80a2-130">Add a PNG file named **no-profile-photo.png** in the **./app/assets/images** directory.</span></span>

1. <span data-ttu-id="b80a2-131">Сохраните все изменения и перезапустите сервер.</span><span class="sxs-lookup"><span data-stu-id="b80a2-131">Save all of your changes and restart the server.</span></span> <span data-ttu-id="b80a2-132">Теперь приложение должно выглядеть совершенно иначе.</span><span class="sxs-lookup"><span data-stu-id="b80a2-132">Now, the app should look very different.</span></span>

    ![Снимок экрана с измененной домашней страницей](./images/create-app-01.png)
