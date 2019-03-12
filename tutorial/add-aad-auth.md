<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="af9bd-101">В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="af9bd-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="af9bd-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="af9bd-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="af9bd-103">На этом этапе вы интегрируете драгоценный камень [omniauth – OAuth2](https://github.com/omniauth/omniauth-oauth2) в приложение и создаете особую стратегию omniauth.</span><span class="sxs-lookup"><span data-stu-id="af9bd-103">In this step you will integrate the [omniauth-oauth2](https://github.com/omniauth/omniauth-oauth2) gem into the application, and create a custom OmniAuth strategy.</span></span>

<span data-ttu-id="af9bd-104">Сначала создайте отдельный файл для хранения идентификатора и секрета приложения.</span><span class="sxs-lookup"><span data-stu-id="af9bd-104">First, create a separate file to hold your app ID and secret.</span></span> <span data-ttu-id="af9bd-105">Создайте `oauth_environment_variables.rb` в `./config` папке новый файл и добавьте приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="af9bd-105">Create a new file called `oauth_environment_variables.rb` in the `./config` folder, and add the following code.</span></span>

```ruby
ENV['AZURE_APP_ID'] = 'YOUR_APP_ID_HERE'
ENV['AZURE_APP_SECRET'] = 'YOUR_APP_SECRET_HERE'
ENV['AZURE_SCOPES'] = 'openid profile email offline_access user.read calendars.read'
```

<span data-ttu-id="af9bd-106">Замените `YOUR_APP_ID_HERE` идентификатором приложения на портале регистрации приложений и замените `YOUR_APP_SECRET_HERE` созданным паролем.</span><span class="sxs-lookup"><span data-stu-id="af9bd-106">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_SECRET_HERE` with the password you generated.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="af9bd-107">Если вы используете систему управления версиями (например, Git), то в дальнейшем будет полезно исключить `oauth_environment_variables.rb` файл из системы управления версиями, чтобы избежать непреднамеренного утечки идентификатора и пароля приложения.</span><span class="sxs-lookup"><span data-stu-id="af9bd-107">If you're using source control such as git, now would be a good time to exclude the `oauth_environment_variables.rb` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

<span data-ttu-id="af9bd-108">Теперь добавьте код для загрузки этого файла, если он присутствует.</span><span class="sxs-lookup"><span data-stu-id="af9bd-108">Now add code to load this file if it's present.</span></span> <span data-ttu-id="af9bd-109">Откройте `./config/environment.rb` файл и добавьте приведенный ниже код перед `Rails.application.initialize!` строкой.</span><span class="sxs-lookup"><span data-stu-id="af9bd-109">Open the `./config/environment.rb` file and add the following code before the `Rails.application.initialize!` line.</span></span>

```ruby
# Load OAuth settings
oauth_environment_variables = File.join(Rails.root, 'config', 'oauth_environment_variables.rb')
load(oauth_environment_variables) if File.exist?(oauth_environment_variables)
```

## <a name="setup-omniauth"></a><span data-ttu-id="af9bd-110">Настройка OmniAuth</span><span class="sxs-lookup"><span data-stu-id="af9bd-110">Setup OmniAuth</span></span>

<span data-ttu-id="af9bd-111">Вы уже установили `omniauth-oauth2` драгоценный камень, но чтобы он работал с конечными точками Azure OAuth, необходимо [создать стратегию OAuth2](https://github.com/omniauth/omniauth-oauth2#creating-an-oauth2-strategy).</span><span class="sxs-lookup"><span data-stu-id="af9bd-111">You've already installed the `omniauth-oauth2` gem, but in order to make it work with the Azure OAuth endpoints, you need to [create an OAuth2 strategy](https://github.com/omniauth/omniauth-oauth2#creating-an-oauth2-strategy).</span></span> <span data-ttu-id="af9bd-112">Это класс Ruby, определяющий параметры для создания запросов OAuth для поставщика Azure.</span><span class="sxs-lookup"><span data-stu-id="af9bd-112">This is a Ruby class that defines the parameters for making OAuth requests to the Azure provider.</span></span>

<span data-ttu-id="af9bd-113">Создайте `microsoft_graph_auth.rb` в `./lib` папке новый файл и добавьте приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="af9bd-113">Create a new file called `microsoft_graph_auth.rb` in the `./lib` folder, and add the following code.</span></span>

```ruby
require 'omniauth-oauth2'

module OmniAuth
  module Strategies
    # Implements an OmniAuth strategy to get a Microsoft Graph
    # compatible token from Azure AD
    class MicrosoftGraphAuth < OmniAuth::Strategies::OAuth2
      option :name, :microsoft_graph_auth

      DEFAULT_SCOPE = 'openid email profile User.Read'.freeze

      # Configure the Azure v2 endpoints
      option  :client_options,
              site:          'https://login.microsoftonline.com',
              authorize_url: '/common/oauth2/v2.0/authorize',
              token_url:     '/common/oauth2/v2.0/token'

      # Send the scope parameter during authorize
      option :authorize_options, [:scope]

      # Unique ID for the user is the id field
      uid { raw_info['id'] }

      # Get additional information after token is retrieved
      extra do
        {
          'raw_info' => raw_info
        }
      end

      def raw_info
        # Get user profile information from the /me endpoint
        @raw_info ||= access_token.get('https://graph.microsoft.com/v1.0/me').parsed
      end

      def authorize_params
        super.tap do |params|
          params['scope'.to_sym] = request.params['scope'] if request.params['scope']
          params[:scope] ||= DEFAULT_SCOPE
        end
      end

      # Override callback URL
      # OmniAuth by default passes the entire URL of the callback, including
      # query parameters. Azure fails validation because that doesn't match the
      # registered callback.
      def callback_url
        options[:redirect_uri] || (full_host + script_name + callback_path)
      end
    end
  end
end
```

<span data-ttu-id="af9bd-114">Уделите время, чтобы узнать, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="af9bd-114">Take a moment to review what this code does.</span></span>

- <span data-ttu-id="af9bd-115">Настройка `client_options` конечных точек Azure v2.</span><span class="sxs-lookup"><span data-stu-id="af9bd-115">It sets the `client_options` to specify the Azure v2 endpoints.</span></span>
- <span data-ttu-id="af9bd-116">Он указывает, что `scope` параметр должен быть отправлен на этапе авторизации.</span><span class="sxs-lookup"><span data-stu-id="af9bd-116">It specifies that the `scope` parameter should be sent during the authorize phase.</span></span>
- <span data-ttu-id="af9bd-117">Он сопоставляет `id` свойство пользователя с уникальным идентификатором пользователя.</span><span class="sxs-lookup"><span data-stu-id="af9bd-117">It maps the `id` property of the user as the unique ID for the user.</span></span>
- <span data-ttu-id="af9bd-118">Он использует маркер доступа для извлечения профиля пользователя из Microsoft Graph, чтобы заполнить `raw_info` хэш.</span><span class="sxs-lookup"><span data-stu-id="af9bd-118">It uses the access token to retrieve the user's profile from Microsoft Graph to fill in the `raw_info` hash.</span></span>
- <span data-ttu-id="af9bd-119">Он переопределяет URL-адрес обратного вызова, чтобы убедиться, что он соответствует зарегистрированному ответному вызову на портале регистрации приложений.</span><span class="sxs-lookup"><span data-stu-id="af9bd-119">It overrides the callback URL to ensure that it matches the registered callback in the app registration portal.</span></span>

<span data-ttu-id="af9bd-120">Теперь, когда мы определили стратегию, необходимо настроить OmniAuth для ее использования.</span><span class="sxs-lookup"><span data-stu-id="af9bd-120">Now that we've defined the strategy, we need to configure OmniAuth to use it.</span></span> <span data-ttu-id="af9bd-121">Создайте `omniauth_graph.rb` в `./config/initializers` папке новый файл и добавьте приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="af9bd-121">Create a new file called `omniauth_graph.rb` in the `./config/initializers` folder, and add the following code.</span></span>

```ruby
require 'microsoft_graph_auth'

Rails.application.config.middleware.use OmniAuth::Builder do
  provider :microsoft_graph_auth,
           ENV['AZURE_APP_ID'],
           ENV['AZURE_APP_SECRET'],
           scope: ENV['AZURE_SCOPES']
end
```

<span data-ttu-id="af9bd-122">Этот код будет выполняться при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="af9bd-122">This code will execute when the app starts.</span></span> <span data-ttu-id="af9bd-123">Он загружает промежуточный по OmniAuth с `microsoft_graph_auth` поставщиком, настроенным с использованием переменных среды, `oauth_environment_variables.rb`заданных в.</span><span class="sxs-lookup"><span data-stu-id="af9bd-123">It loads up the OmniAuth middleware with the `microsoft_graph_auth` provider, configured with the environment variables set in `oauth_environment_variables.rb`.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="af9bd-124">Реализация входа</span><span class="sxs-lookup"><span data-stu-id="af9bd-124">Implement sign-in</span></span>

<span data-ttu-id="af9bd-125">Теперь, когда настроено промежуточное по OmniAuth, вы можете перейти к добавлению входа в приложение.</span><span class="sxs-lookup"><span data-stu-id="af9bd-125">Now that the OmniAuth middleware is configured, you can move on to adding sign-in to the app.</span></span> <span data-ttu-id="af9bd-126">Выполните следующую команду в интерфейсе командной строки, чтобы создать контроллер для входа и выхода.</span><span class="sxs-lookup"><span data-stu-id="af9bd-126">Run the following command in your CLI to generate a controller for sign-in and sign-out.</span></span>

```Shell
rails generate controller Auth
```

<span data-ttu-id="af9bd-127">Откройте файл `./app/controllers/auth_controller.rb`.</span><span class="sxs-lookup"><span data-stu-id="af9bd-127">Open the `./app/controllers/auth_controller.rb` file.</span></span> <span data-ttu-id="af9bd-128">Добавьте приведенный ниже метод в класс `AuthController`.</span><span class="sxs-lookup"><span data-stu-id="af9bd-128">Add the following method to the `AuthController` class.</span></span>

```ruby
def signin
  redirect_to '/auth/microsoft_graph_auth'
end
```

<span data-ttu-id="af9bd-129">Весь этот метод выполняет перенаправление к маршруту, который ожидает, что OmniAuth вызывает нашу пользовательскую стратегию.</span><span class="sxs-lookup"><span data-stu-id="af9bd-129">All this method does is redirect to the route that OmniAuth expects to invoke our custom strategy.</span></span>

<span data-ttu-id="af9bd-130">Затем добавьте в `AuthController` класс метод обратного вызова.</span><span class="sxs-lookup"><span data-stu-id="af9bd-130">Next, add a callback method to the `AuthController` class.</span></span> <span data-ttu-id="af9bd-131">Этот метод будет вызываться по промежуточного слоя OmniAuth после завершения процесса OAuth.</span><span class="sxs-lookup"><span data-stu-id="af9bd-131">This method will be called by the OmniAuth middleware once the OAuth flow is complete.</span></span>

```ruby
def callback
  # Access the authentication hash for omniauth
  data = request.env['omniauth.auth']

  # Temporary for testing!
  render json: data.to_json
end
```

<span data-ttu-id="af9bd-132">Теперь все это позволяет отобразить хэш, предоставленный OmniAuth.</span><span class="sxs-lookup"><span data-stu-id="af9bd-132">For now all this does is render the hash provided by OmniAuth.</span></span> <span data-ttu-id="af9bd-133">Мы будем использовать эту проверку, чтобы убедиться, что наш вход работает перед переходом.</span><span class="sxs-lookup"><span data-stu-id="af9bd-133">We'll use this to verify that our sign-in is working before moving on.</span></span> <span data-ttu-id="af9bd-134">Перед тестированием необходимо добавить маршруты в `./config/routes.rb`.</span><span class="sxs-lookup"><span data-stu-id="af9bd-134">Before we test, we need to add the routes to `./config/routes.rb`.</span></span>

```ruby
get 'auth/signin'

# Add route for OmniAuth callback
match '/auth/:provider/callback', to: 'auth#callback', via: [:get, :post]
```

<span data-ttu-id="af9bd-135">Теперь обновите представления, чтобы использовать `signin` действие.</span><span class="sxs-lookup"><span data-stu-id="af9bd-135">Now update the views to use the `signin` action.</span></span> <span data-ttu-id="af9bd-136">Открыть `./app/views/layouts/application.html.erb`.</span><span class="sxs-lookup"><span data-stu-id="af9bd-136">Open `./app/views/layouts/application.html.erb`.</span></span> <span data-ttu-id="af9bd-137">Замените строку на `<a href="#" class="nav-link">Sign In</a>` приведенную ниже строку.</span><span class="sxs-lookup"><span data-stu-id="af9bd-137">Replace the line `<a href="#" class="nav-link">Sign In</a>` with the following.</span></span>

```html
<%= link_to "Sign In", {:controller => :auth, :action => :signin}, :class => "nav-link" %>
```

<span data-ttu-id="af9bd-138">Откройте `./app/views/home/index.html.erb` файл и замените `<a href="#" class="btn btn-primary btn-large">Click here to sign in</a>` строку приведенным ниже фрагментом.</span><span class="sxs-lookup"><span data-stu-id="af9bd-138">Open the `./app/views/home/index.html.erb` file and replace the `<a href="#" class="btn btn-primary btn-large">Click here to sign in</a>` line with the following.</span></span>

```html
<%= link_to "Click here to sign in", {:controller => :auth, :action => :signin}, :class => "btn btn-primary btn-large" %>
```

<span data-ttu-id="af9bd-139">Запустите сервер и перейдите к `https://localhost:3000`.</span><span class="sxs-lookup"><span data-stu-id="af9bd-139">Start the server and browse to `https://localhost:3000`.</span></span> <span data-ttu-id="af9bd-140">Нажмите кнопку входа, и вы будете перенаправлены на `https://login.microsoftonline.com`.</span><span class="sxs-lookup"><span data-stu-id="af9bd-140">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="af9bd-141">Войдите с помощью учетной записи Майкрософт и согласия с запрошенными разрешениями.</span><span class="sxs-lookup"><span data-stu-id="af9bd-141">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="af9bd-142">Браузер перенаправляется в приложение, отображая хэш, созданный с помощью OmniAuth.</span><span class="sxs-lookup"><span data-stu-id="af9bd-142">The browser redirects to the app, showing the hash generated by OmniAuth.</span></span>

```json
{
  "provider": "microsoft_graph_auth",
  "uid": "eb52b3b2-c4ac-4b4f-bacd-d5f7ece55df0",
  "info": {
    "name": null
  },
  "credentials": {
    "token": "eyJ0eXAi...",
    "refresh_token": "OAQABAAA...",
    "expires_at": 1529517383,
    "expires": true
  },
  "extra": {
    "raw_info": {
      "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#users/$entity",
      "id": "eb52b3b2-c4ac-4b4f-bacd-d5f7ece55df0",
      "businessPhones": [
        "+1 425 555 0109"
      ],
      "displayName": "Adele Vance",
      "givenName": "Adele",
      "jobTitle": "Retail Manager",
      "mail": "AdeleV@contoso.onmicrosoft.com",
      "mobilePhone": null,
      "officeLocation": "18/2111",
      "preferredLanguage": "en-US",
      "surname": "Vance",
      "userPrincipalName": "AdeleV@contoso.onmicrosoft.com"
    }
  }
}
```

## <a name="storing-the-tokens"></a><span data-ttu-id="af9bd-143">Сохранение маркеров</span><span class="sxs-lookup"><span data-stu-id="af9bd-143">Storing the tokens</span></span>

<span data-ttu-id="af9bd-144">Теперь, когда вы можете получить маркеры, следует реализовать способ их хранения в приложении.</span><span class="sxs-lookup"><span data-stu-id="af9bd-144">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="af9bd-145">Так как это пример приложения, для простоты вы будете хранить их в сеансе.</span><span class="sxs-lookup"><span data-stu-id="af9bd-145">Since this is a sample app, for simplicity's sake, you'll store them in the session.</span></span> <span data-ttu-id="af9bd-146">Реальное приложение использует более надежное решение для безопасного хранения, например базу данных.</span><span class="sxs-lookup"><span data-stu-id="af9bd-146">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

<span data-ttu-id="af9bd-147">Откройте файл `./app/controllers/application_controller.rb`.</span><span class="sxs-lookup"><span data-stu-id="af9bd-147">Open the `./app/controllers/application_controller.rb` file.</span></span> <span data-ttu-id="af9bd-148">Вы добавите все методы управления маркерами здесь.</span><span class="sxs-lookup"><span data-stu-id="af9bd-148">You'll add all of our token management methods here.</span></span> <span data-ttu-id="af9bd-149">Так как все остальные контроллеры наследуют `ApplicationController` класс, они смогут использовать эти методы для доступа к маркерам.</span><span class="sxs-lookup"><span data-stu-id="af9bd-149">Because all of the other controllers inherit the `ApplicationController` class, they'll be able to use these methods to access the tokens.</span></span>

<span data-ttu-id="af9bd-150">Добавьте приведенный ниже метод в класс `ApplicationController`.</span><span class="sxs-lookup"><span data-stu-id="af9bd-150">Add the following method to the `ApplicationController` class.</span></span> <span data-ttu-id="af9bd-151">Метод принимает хэш OmniAuth в качестве параметра и извлекает релевантные биты информации, а затем сохраняет их в сеансе.</span><span class="sxs-lookup"><span data-stu-id="af9bd-151">The method takes the OmniAuth hash as a parameter and extracts the relevant bits of information, then stores that in the session.</span></span>

```ruby
def save_in_session(auth_hash)
  # Save the token info
  session[:graph_token_hash] = auth_hash.dig(:credentials)
  # Save the user's display name
  session[:user_name] = auth_hash.dig(:extra, :raw_info, :displayName)
  # Save the user's email address
  # Use the mail field first. If that's empty, fall back on
  # userPrincipalName
  session[:user_email] = auth_hash.dig(:extra, :raw_info, :mail) ||
                         auth_hash.dig(:extra, :raw_info, :userPrincipalName)
end
```

<span data-ttu-id="af9bd-152">Теперь добавьте функции доступа для получения имени пользователя, адреса электронной почты и маркера доступа обратно из сеанса.</span><span class="sxs-lookup"><span data-stu-id="af9bd-152">Now add accessor functions to retrieve the user name, email address, and access token back out of the session.</span></span>

```ruby
def user_name
  session[:user_name]
end

def user_email
  session[:user_email]
end

def access_token
  session[:graph_token_hash][:token]
end
```

<span data-ttu-id="af9bd-153">Наконец, добавьте код, который будет выполняться до обработки какого-либо действия.</span><span class="sxs-lookup"><span data-stu-id="af9bd-153">Finally, add some code that will run before any action is processed.</span></span>

```ruby
before_action :set_user

def set_user
  @user_name = user_name
  @user_email = user_email
end
```

<span data-ttu-id="af9bd-154">Этот метод задает переменные, используемые макетом (в `application.html.erb`) для отображения сведений о пользователе в панели навигации.</span><span class="sxs-lookup"><span data-stu-id="af9bd-154">This method sets the variables that the layout (in `application.html.erb`) uses to show the user's information in the nav bar.</span></span> <span data-ttu-id="af9bd-155">Добавив его сюда, вам не нужно добавлять этот код в каждое действие с одним контроллером.</span><span class="sxs-lookup"><span data-stu-id="af9bd-155">By adding it here, you don't have to add this code in every single controller action.</span></span> <span data-ttu-id="af9bd-156">Однако это также будет выполняться для действий `AuthController`, которые не являются оптимальными.</span><span class="sxs-lookup"><span data-stu-id="af9bd-156">However, this will also run for actions in the `AuthController`, which isn't optimal.</span></span> <span data-ttu-id="af9bd-157">Добавьте следующий код в `AuthController` класс, `./app/controllers/auth_controller.rb` чтобы пропустить действие before.</span><span class="sxs-lookup"><span data-stu-id="af9bd-157">Add the following code to the `AuthController` class in `./app/controllers/auth_controller.rb` to skip the before action.</span></span>

```ruby
skip_before_action :set_user
```

<span data-ttu-id="af9bd-158">Затем обновите `callback` функцию в `AuthController` классе для хранения маркеров в сеансе и перенаправление обратно на главную страницу.</span><span class="sxs-lookup"><span data-stu-id="af9bd-158">Then, update the `callback` function in the `AuthController` class to store the tokens in the session and redirect back to the main page.</span></span> <span data-ttu-id="af9bd-159">Замените имеющуюся функцию `callback` указанным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="af9bd-159">Replace the existing `callback` function with the following.</span></span>

```ruby
def callback
  # Access the authentication hash for omniauth
  data = request.env['omniauth.auth']

  # Save the data in the session
  save_in_session data

  redirect_to root_url
end
```

## <a name="implement-sign-out"></a><span data-ttu-id="af9bd-160">Реализация выхода</span><span class="sxs-lookup"><span data-stu-id="af9bd-160">Implement sign-out</span></span>

<span data-ttu-id="af9bd-161">Перед тестированием новой функции добавьте способ выхода. Добавьте в `AuthController` класс следующее действие.</span><span class="sxs-lookup"><span data-stu-id="af9bd-161">Before you test this new feature, add a way to sign out. Add the following action to the `AuthController` class.</span></span>

```ruby
def signout
  reset_session
  redirect_to root_url
end
```

<span data-ttu-id="af9bd-162">Добавьте это действие в `./config/routes.rb`.</span><span class="sxs-lookup"><span data-stu-id="af9bd-162">Add this action to `./config/routes.rb`.</span></span>

```ruby
get 'auth/signout'
```

<span data-ttu-id="af9bd-163">Теперь обновите представление, чтобы использовать `signout` действие.</span><span class="sxs-lookup"><span data-stu-id="af9bd-163">Now update the view to use the `signout` action.</span></span> <span data-ttu-id="af9bd-164">Открыть `./app/views/layouts/application.html.erb`.</span><span class="sxs-lookup"><span data-stu-id="af9bd-164">Open `./app/views/layouts/application.html.erb`.</span></span> <span data-ttu-id="af9bd-165">Замените строку `<a href="#" class="dropdown-item">Sign Out</a>` следующим:</span><span class="sxs-lookup"><span data-stu-id="af9bd-165">Replace the line `<a href="#" class="dropdown-item">Sign Out</a>` with:</span></span>

```html
<%= link_to "Sign Out", {:controller => :auth, :action => :signout}, :class => "dropdown-item" %>
```

<span data-ttu-id="af9bd-166">ПереЗапустите сервер и пройдите процесс входа.</span><span class="sxs-lookup"><span data-stu-id="af9bd-166">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="af9bd-167">Необходимо вернуться на домашнюю страницу, но пользовательский интерфейс должен измениться, чтобы показать, что вы вошли в систему.</span><span class="sxs-lookup"><span data-stu-id="af9bd-167">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![Снимок экрана домашней страницы после входа](./images/add-aad-auth-01.png)

<span data-ttu-id="af9bd-169">Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **выхода** .</span><span class="sxs-lookup"><span data-stu-id="af9bd-169">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="af9bd-170">При нажатии кнопки **выйти** сбрасывается сеанс и возвращается на домашнюю страницу.</span><span class="sxs-lookup"><span data-stu-id="af9bd-170">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![Снимок экрана с раскрывающимся меню со ссылкой "выйти"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="af9bd-172">Обновление маркеров</span><span class="sxs-lookup"><span data-stu-id="af9bd-172">Refreshing tokens</span></span>

<span data-ttu-id="af9bd-173">Если вы близки к хэшу, созданному с помощью OmniAuth, обратите внимание на два маркера в хэш `token` - `refresh_token`коде: и.</span><span class="sxs-lookup"><span data-stu-id="af9bd-173">If you look closely at the hash generated by OmniAuth, you'll notice there are two tokens in the hash: `token` and `refresh_token`.</span></span> <span data-ttu-id="af9bd-174">Значение в `token` — это маркер доступа, который отправляется в `Authorization` заголовке вызовов API.</span><span class="sxs-lookup"><span data-stu-id="af9bd-174">The value in `token` is the access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="af9bd-175">Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="af9bd-175">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="af9bd-176">Однако этот маркер кратковременно используется.</span><span class="sxs-lookup"><span data-stu-id="af9bd-176">However, this token is short-lived.</span></span> <span data-ttu-id="af9bd-177">Срок действия маркера истечет через час после его выдачи.</span><span class="sxs-lookup"><span data-stu-id="af9bd-177">The token expires an hour after it is issued.</span></span> <span data-ttu-id="af9bd-178">В `refresh_token` этом случае значение становится полезным.</span><span class="sxs-lookup"><span data-stu-id="af9bd-178">This is where the `refresh_token` value becomes useful.</span></span> <span data-ttu-id="af9bd-179">Маркер обновления позволяет приложению запросить новый маркер доступа, не требуя от пользователя повторного входа.</span><span class="sxs-lookup"><span data-stu-id="af9bd-179">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span> <span data-ttu-id="af9bd-180">Обновите код управления маркером, чтобы реализовать обновление маркеров.</span><span class="sxs-lookup"><span data-stu-id="af9bd-180">Update the token management code to implement token refresh.</span></span>

<span data-ttu-id="af9bd-181">Откройте `./app/controllers/application_controller.rb` и добавьте следующие `require` операторы в верхней части:</span><span class="sxs-lookup"><span data-stu-id="af9bd-181">Open `./app/controllers/application_controller.rb` and add the following `require` statements at the top:</span></span>

```ruby
require 'microsoft_graph_auth'
require 'oauth2'
```

<span data-ttu-id="af9bd-182">Затем добавьте указанный ниже метод в `ApplicationController` класс.</span><span class="sxs-lookup"><span data-stu-id="af9bd-182">Then add the following method to the `ApplicationController` class.</span></span>

```ruby
def refresh_tokens(token_hash)
  oauth_strategy = OmniAuth::Strategies::MicrosoftGraphAuth.new(
    nil, ENV['AZURE_APP_ID'], ENV['AZURE_APP_SECRET']
  )

  token = OAuth2::AccessToken.new(
    oauth_strategy.client, token_hash[:token],
    refresh_token: token_hash[:refresh_token]
  )

  # Refresh the tokens
  new_tokens = token.refresh!.to_hash.slice(:access_token, :refresh_token, :expires_at)

  # Rename token key
  new_tokens[:token] = new_tokens.delete :access_token

  # Store the new hash
  session[:graph_token_hash] = new_tokens
end
```

<span data-ttu-id="af9bd-183">Этот метод использует драгоценный камень [OAuth2](https://github.com/oauth-xx/oauth2) (зависимость от `omniauth-oauth2` драгоценного камень) для обновления маркеров и обновления сеанса.</span><span class="sxs-lookup"><span data-stu-id="af9bd-183">This method uses the [oauth2](https://github.com/oauth-xx/oauth2) gem (a dependency of the `omniauth-oauth2` gem) to refresh the tokens, and updates the session.</span></span>

<span data-ttu-id="af9bd-184">Теперь поместите этот метод для использования.</span><span class="sxs-lookup"><span data-stu-id="af9bd-184">Now put this method to use.</span></span> <span data-ttu-id="af9bd-185">Для этого сделайте `access_token` метод доступа в `ApplicationController` классе более многоразрядным.</span><span class="sxs-lookup"><span data-stu-id="af9bd-185">To do that, make the `access_token` accessor in the `ApplicationController` class a bit smarter.</span></span> <span data-ttu-id="af9bd-186">Вместо того чтобы просто возвратить маркер из сеанса, он сначала проверяет, является ли срок его действия близким к сроку действия.</span><span class="sxs-lookup"><span data-stu-id="af9bd-186">Instead of just returning the token from the session, it will first check if it is close to expiration.</span></span> <span data-ttu-id="af9bd-187">Если это так, то оно обновится перед возвратом маркера.</span><span class="sxs-lookup"><span data-stu-id="af9bd-187">If it is, then it will refresh before returning the token.</span></span> <span data-ttu-id="af9bd-188">Замените текущий `access_token` метод на приведенный ниже.</span><span class="sxs-lookup"><span data-stu-id="af9bd-188">Replace the current `access_token` method with the following.</span></span>

```ruby
def access_token
  token_hash = session[:graph_token_hash]

  # Get the expiry time - 5 minutes
  expiry = Time.at(token_hash[:expires_at] - 300)

  if Time.now > expiry
    # Token expired, refresh
    new_hash = refresh_tokens token_hash
    new_hash[:token]
  else
    token_hash[:token]
  end
end
```