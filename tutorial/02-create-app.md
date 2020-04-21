<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="5b61a-101">В этом упражнении вы будете использовать для создания веб-приложения [транскрипцию на шинах](https://rubyonrails.org/) .</span><span class="sxs-lookup"><span data-stu-id="5b61a-101">In this exercise you will use [Ruby on Rails](https://rubyonrails.org/) to build a web app.</span></span>

1. <span data-ttu-id="5b61a-102">Если вы еще не установили салазки, вы можете установить его из интерфейса командной строки (CLI) с помощью следующей команды.</span><span class="sxs-lookup"><span data-stu-id="5b61a-102">If you don't already have Rails installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

    ```Shell
    gem install rails -v 6.0.2.2
    ```

1. <span data-ttu-id="5b61a-103">Откройте подсистему CLI, перейдите к каталогу, в котором у вас есть права на создание файлов, и выполните следующую команду, чтобы создать новое приложение с ограждениями.</span><span class="sxs-lookup"><span data-stu-id="5b61a-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Rails app.</span></span>

    ```Shell
    rails new graph-tutorial
    ```

1. <span data-ttu-id="5b61a-104">Перейдите к новому каталогу и введите следующую команду для запуска локального веб-сервера.</span><span class="sxs-lookup"><span data-stu-id="5b61a-104">Navigate to this new directory and enter the following command to start a local web server.</span></span>

    ```Shell
    rails server
    ```

1. <span data-ttu-id="5b61a-105">Откройте браузер и перейдите по адресу `http://localhost:3000`.</span><span class="sxs-lookup"><span data-stu-id="5b61a-105">Open your browser and navigate to `http://localhost:3000`.</span></span> <span data-ttu-id="5b61a-106">Если все работает, вы увидите "Яи!</span><span class="sxs-lookup"><span data-stu-id="5b61a-106">If everything is working, you will see a "Yay!</span></span> <span data-ttu-id="5b61a-107">Вы находитесь на салазках! "</span><span class="sxs-lookup"><span data-stu-id="5b61a-107">You're on Rails!"</span></span> <span data-ttu-id="5b61a-108">Сообщение.</span><span class="sxs-lookup"><span data-stu-id="5b61a-108">message.</span></span> <span data-ttu-id="5b61a-109">Если вы не видите это сообщение, ознакомьтесь с [руководством по началу работы с салазками](http://guides.rubyonrails.org/).</span><span class="sxs-lookup"><span data-stu-id="5b61a-109">If you don't see that message, check the [Rails getting started guide](http://guides.rubyonrails.org/).</span></span>

## <a name="install-gems"></a><span data-ttu-id="5b61a-110">Установка драгоценных камней</span><span class="sxs-lookup"><span data-stu-id="5b61a-110">Install gems</span></span>

<span data-ttu-id="5b61a-111">Прежде чем переходить, установите несколько дополнительных драгоценных камней, которые будут использоваться позже:</span><span class="sxs-lookup"><span data-stu-id="5b61a-111">Before moving on, install some additional gems that you will use later:</span></span>

- <span data-ttu-id="5b61a-112">[omniauth — OAuth2](https://github.com/omniauth/omniauth-oauth2) для обработки входных потоков и маркеров OAuth.</span><span class="sxs-lookup"><span data-stu-id="5b61a-112">[omniauth-oauth2](https://github.com/omniauth/omniauth-oauth2) for handling sign-in and OAuth token flows.</span></span>
- <span data-ttu-id="5b61a-113">[omniauth — rails_csrf_protection](https://github.com/cookpad/omniauth-rails_csrf_protection) для добавления защиты Ксрф в omniauth.</span><span class="sxs-lookup"><span data-stu-id="5b61a-113">[omniauth-rails_csrf_protection](https://github.com/cookpad/omniauth-rails_csrf_protection) for adding CSRF protection to OmniAuth.</span></span>
- <span data-ttu-id="5b61a-114">[хттпарти](https://github.com/jnunemaker/httparty) для совершения вызовов в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="5b61a-114">[httparty](https://github.com/jnunemaker/httparty) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="5b61a-115">[активерекорд — session_store](https://github.com/rails/activerecord-session_store) для хранения сеансов в базе данных.</span><span class="sxs-lookup"><span data-stu-id="5b61a-115">[activerecord-session_store](https://github.com/rails/activerecord-session_store) for storing sessions in the database.</span></span>

1. <span data-ttu-id="5b61a-116">Откройте **./жемфиле** и добавьте следующие строки.</span><span class="sxs-lookup"><span data-stu-id="5b61a-116">Open **./Gemfile** and add the following lines.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/Gemfile" id="GemFileSnippet":::

1. <span data-ttu-id="5b61a-117">В интерфейсе командной строки выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="5b61a-117">In your CLI, run the following command.</span></span>

    ```Shell
    bundle install
    ```

1. <span data-ttu-id="5b61a-118">В интерфейсе командной строки выполните следующие команды, чтобы настроить базу данных для хранения сеансов.</span><span class="sxs-lookup"><span data-stu-id="5b61a-118">In your CLI, run the following commands to configure the database for storing sessions.</span></span>

    ```Shell
    rails generate active_record:session_migration
    rake db:migrate
    ```

1. <span data-ttu-id="5b61a-119">Создайте новый файл, вызываемый `session_store.rb` в каталоге **./конфиг/инитиализерс** , и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="5b61a-119">Create a new file called `session_store.rb` in the **./config/initializers** directory, and add the following code.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/config/initializers/session_store.rb" id="SessionStoreSnippet":::

## <a name="design-the-app"></a><span data-ttu-id="5b61a-120">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="5b61a-120">Design the app</span></span>

<span data-ttu-id="5b61a-121">В этом разделе вы создадите базовый пользовательский интерфейс для приложения.</span><span class="sxs-lookup"><span data-stu-id="5b61a-121">In this section you'll create the basic UI for the app.</span></span>

1. <span data-ttu-id="5b61a-122">Откройте **/АПП/виевс/лайаутс/аппликатион.хтмл.ЕРБ** и замените его содержимое приведенным ниже.</span><span class="sxs-lookup"><span data-stu-id="5b61a-122">Open **./app/views/layouts/application.html.erb** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/app/views/layouts/application.html.erb" id="LayoutSnippet":::

    <span data-ttu-id="5b61a-123">В этом коде добавляется [Начальная](http://getbootstrap.com/) загрузка для простых стилей и [Шрифт Awesome](https://fontawesome.com/) для некоторых простых значков.</span><span class="sxs-lookup"><span data-stu-id="5b61a-123">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="5b61a-124">Он также определяет глобальную структуру с помощью панели навигации.</span><span class="sxs-lookup"><span data-stu-id="5b61a-124">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="5b61a-125">Откройте файл **./АПП/Ассетс/стилешитс/аппликатион.КСС** и добавьте следующий элемент в конец файла.</span><span class="sxs-lookup"><span data-stu-id="5b61a-125">Open **./app/assets/stylesheets/application.css** and add the following to the end of the file.</span></span>

    :::code language="css" source="../demo/graph-tutorial/app/assets/stylesheets/application.css" id="CssSnippet":::

1. <span data-ttu-id="5b61a-126">Создайте контроллер домашней страницы с помощью следующей команды.</span><span class="sxs-lookup"><span data-stu-id="5b61a-126">Generate a home page controller with the following command.</span></span>

    ```Shell
    rails generate controller Home index
    ```

1. <span data-ttu-id="5b61a-127">Настройка `index` действия на `Home` контроллере в качестве страницы по умолчанию для приложения.</span><span class="sxs-lookup"><span data-stu-id="5b61a-127">Configure the `index` action on the `Home` controller as the default page for the app.</span></span> <span data-ttu-id="5b61a-128">Откройте **/конфиг/раутес.РБ** и замените его содержимое приведенным ниже</span><span class="sxs-lookup"><span data-stu-id="5b61a-128">Open **./config/routes.rb** and replace its contents with the following</span></span>

    ```ruby
    Rails.application.routes.draw do
      get 'home/index'
      root 'home#index'

      # Add future routes here

    end
    ```

1. <span data-ttu-id="5b61a-129">Откройте **/АПП/виев/Хоме/индекс.хтмл.ЕРБ** и замените его содержимое приведенным ниже.</span><span class="sxs-lookup"><span data-stu-id="5b61a-129">Open **./app/view/home/index.html.erb** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/app/views/home/index.html.erb" id="HomeSnippet":::

1. <span data-ttu-id="5b61a-130">Сохраните все изменения и перезапустите сервер.</span><span class="sxs-lookup"><span data-stu-id="5b61a-130">Save all of your changes and restart the server.</span></span> <span data-ttu-id="5b61a-131">Теперь приложение должно выглядеть по-другому.</span><span class="sxs-lookup"><span data-stu-id="5b61a-131">Now, the app should look very different.</span></span>

    ![Снимок экрана с переработанной домашней страницей](./images/create-app-01.png)
