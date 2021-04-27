<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="12296-101">В этом упражнении вы будете использовать [Ruby on Rails](https://rubyonrails.org/) для создания веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="12296-101">In this exercise you will use [Ruby on Rails](https://rubyonrails.org/) to build a web app.</span></span>

1. <span data-ttu-id="12296-102">Если у вас еще нет установленных рельсов, вы можете установить его из интерфейса командной строки (CLI) со следующей командной командой.</span><span class="sxs-lookup"><span data-stu-id="12296-102">If you don't already have Rails installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

    ```Shell
    gem install rails -v 6.1.3.1
    ```

1. <span data-ttu-id="12296-103">Откройте CLI, перейдите в каталог, в котором у вас есть права на создание файлов, и запустите следующую команду для создания нового приложения Rails.</span><span class="sxs-lookup"><span data-stu-id="12296-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Rails app.</span></span>

    ```Shell
    rails new graph-tutorial
    ```

1. <span data-ttu-id="12296-104">Перейдите к новому каталогу и введите следующую команду, чтобы запустить локальный веб-сервер.</span><span class="sxs-lookup"><span data-stu-id="12296-104">Navigate to this new directory and enter the following command to start a local web server.</span></span>

    ```Shell
    rails server
    ```

1. <span data-ttu-id="12296-105">Откройте браузер и перейдите по адресу `http://localhost:3000`.</span><span class="sxs-lookup"><span data-stu-id="12296-105">Open your browser and navigate to `http://localhost:3000`.</span></span> <span data-ttu-id="12296-106">Если все работает, вы увидите "Yay!</span><span class="sxs-lookup"><span data-stu-id="12296-106">If everything is working, you will see a "Yay!</span></span> <span data-ttu-id="12296-107">Вы на рельсах!</span><span class="sxs-lookup"><span data-stu-id="12296-107">You're on Rails!"</span></span> <span data-ttu-id="12296-108">Сообщение.</span><span class="sxs-lookup"><span data-stu-id="12296-108">message.</span></span> <span data-ttu-id="12296-109">Если вы не видите это сообщение, проверьте руководство по началу [работы Rails.](http://guides.rubyonrails.org/)</span><span class="sxs-lookup"><span data-stu-id="12296-109">If you don't see that message, check the [Rails getting started guide](http://guides.rubyonrails.org/).</span></span>

## <a name="install-gems"></a><span data-ttu-id="12296-110">Установка драгоценных камней</span><span class="sxs-lookup"><span data-stu-id="12296-110">Install gems</span></span>

<span data-ttu-id="12296-111">Прежде чем двигаться дальше, установите некоторые дополнительные драгоценные камни, которые вы будете использовать позже:</span><span class="sxs-lookup"><span data-stu-id="12296-111">Before moving on, install some additional gems that you will use later:</span></span>

- <span data-ttu-id="12296-112">[omniauth-oauth2 для](https://github.com/omniauth/omniauth-oauth2) обработки потоков маркеров входных и OAuth.</span><span class="sxs-lookup"><span data-stu-id="12296-112">[omniauth-oauth2](https://github.com/omniauth/omniauth-oauth2) for handling sign-in and OAuth token flows.</span></span>
- <span data-ttu-id="12296-113">[omniauth-rails_csrf_protection](https://github.com/cookpad/omniauth-rails_csrf_protection) для добавления защиты CSRF в OmniAuth.</span><span class="sxs-lookup"><span data-stu-id="12296-113">[omniauth-rails_csrf_protection](https://github.com/cookpad/omniauth-rails_csrf_protection) for adding CSRF protection to OmniAuth.</span></span>
- <span data-ttu-id="12296-114">[httparty](https://github.com/jnunemaker/httparty) для звонков в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="12296-114">[httparty](https://github.com/jnunemaker/httparty) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="12296-115">[activerecord-session_store](https://github.com/rails/activerecord-session_store) для хранения сеансов в базе данных.</span><span class="sxs-lookup"><span data-stu-id="12296-115">[activerecord-session_store](https://github.com/rails/activerecord-session_store) for storing sessions in the database.</span></span>

1. <span data-ttu-id="12296-116">Откройте **./Gemfile** и добавьте следующие строки.</span><span class="sxs-lookup"><span data-stu-id="12296-116">Open **./Gemfile** and add the following lines.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/Gemfile" id="GemFileSnippet":::

1. <span data-ttu-id="12296-117">В CLI запустите следующую команду.</span><span class="sxs-lookup"><span data-stu-id="12296-117">In your CLI, run the following command.</span></span>

    ```Shell
    bundle install
    ```

1. <span data-ttu-id="12296-118">В CLI запустите следующие команды, чтобы настроить базу данных для хранения сеансов.</span><span class="sxs-lookup"><span data-stu-id="12296-118">In your CLI, run the following commands to configure the database for storing sessions.</span></span>

    ```Shell
    rails generate active_record:session_migration
    rake db:migrate
    ```

1. <span data-ttu-id="12296-119">Создайте новый файл, называемый `session_store.rb` **в каталоге ./config/initializers,** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="12296-119">Create a new file called `session_store.rb` in the **./config/initializers** directory, and add the following code.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/config/initializers/session_store.rb" id="SessionStoreSnippet":::

## <a name="design-the-app"></a><span data-ttu-id="12296-120">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="12296-120">Design the app</span></span>

<span data-ttu-id="12296-121">В этом разделе вы создадим базовый пользовательский интерфейс для приложения.</span><span class="sxs-lookup"><span data-stu-id="12296-121">In this section you'll create the basic UI for the app.</span></span>

1. <span data-ttu-id="12296-122">Откройте **./app/views/layouts/application.html.erb** и замените его содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="12296-122">Open **./app/views/layouts/application.html.erb** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/app/views/layouts/application.html.erb" id="LayoutSnippet":::

    <span data-ttu-id="12296-123">Этот код добавляет [Bootstrap](http://getbootstrap.com/) для простого стиля и [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) для некоторых простых значков.</span><span class="sxs-lookup"><span data-stu-id="12296-123">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) for some simple icons.</span></span> <span data-ttu-id="12296-124">Он также определяет глобальную макет с панели nav.</span><span class="sxs-lookup"><span data-stu-id="12296-124">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="12296-125">Откройте **таблицы ./app/assets/stylesheets/application.css** и добавьте следующее в конец файла.</span><span class="sxs-lookup"><span data-stu-id="12296-125">Open **./app/assets/stylesheets/application.css** and add the following to the end of the file.</span></span>

    :::code language="css" source="../demo/graph-tutorial/app/assets/stylesheets/application.css" id="CssSnippet":::

1. <span data-ttu-id="12296-126">Создание контроллера домашней страницы со следующей командой.</span><span class="sxs-lookup"><span data-stu-id="12296-126">Generate a home page controller with the following command.</span></span>

    ```Shell
    rails generate controller Home index
    ```

1. <span data-ttu-id="12296-127">Настройте `index` действие на контроллере `Home` как страницу по умолчанию для приложения.</span><span class="sxs-lookup"><span data-stu-id="12296-127">Configure the `index` action on the `Home` controller as the default page for the app.</span></span> <span data-ttu-id="12296-128">Откройте **./config/routes.rb** и замените его содержимое следующим</span><span class="sxs-lookup"><span data-stu-id="12296-128">Open **./config/routes.rb** and replace its contents with the following</span></span>

    ```ruby
    Rails.application.routes.draw do
      get 'home/index'
      root 'home#index'

      # Add future routes here

    end
    ```

1. <span data-ttu-id="12296-129">Откройте **./app/view/home/index.html.erb** и замените его содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="12296-129">Open **./app/view/home/index.html.erb** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/app/views/home/index.html.erb" id="HomeSnippet":::

1. <span data-ttu-id="12296-130">Добавьте PNG-файл **с именемno-profile-photo.png** в **каталоге ./app/assets/images.**</span><span class="sxs-lookup"><span data-stu-id="12296-130">Add a PNG file named **no-profile-photo.png** in the **./app/assets/images** directory.</span></span>

1. <span data-ttu-id="12296-131">Сохраните все изменения и перезапустите сервер.</span><span class="sxs-lookup"><span data-stu-id="12296-131">Save all of your changes and restart the server.</span></span> <span data-ttu-id="12296-132">Теперь приложение должно выглядеть совсем по-другому.</span><span class="sxs-lookup"><span data-stu-id="12296-132">Now, the app should look very different.</span></span>

    ![Снимок экрана: обновленная домашняя страница](./images/create-app-01.png)
