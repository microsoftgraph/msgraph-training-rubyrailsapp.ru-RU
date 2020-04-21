<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="40f74-101">В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="40f74-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="40f74-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="40f74-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="40f74-103">На этом этапе вы интегрируете драгоценный камень [omniauth – OAuth2](https://github.com/omniauth/omniauth-oauth2) в приложение и создаете особую стратегию omniauth.</span><span class="sxs-lookup"><span data-stu-id="40f74-103">In this step you will integrate the [omniauth-oauth2](https://github.com/omniauth/omniauth-oauth2) gem into the application, and create a custom OmniAuth strategy.</span></span>

1. <span data-ttu-id="40f74-104">Создайте отдельный файл для хранения идентификатора и секрета приложения.</span><span class="sxs-lookup"><span data-stu-id="40f74-104">Create a separate file to hold your app ID and secret.</span></span> <span data-ttu-id="40f74-105">Создайте новый файл, вызываемый `oauth_environment_variables.rb` в папке **/config** , и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="40f74-105">Create a new file called `oauth_environment_variables.rb` in the **./config** folder, and add the following code.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/config/oauth_environment_variables.rb.example":::

1. <span data-ttu-id="40f74-106">Замените `YOUR_APP_ID_HERE` идентификатором приложения на портале регистрации приложений и замените `YOUR_APP_SECRET_HERE` созданным паролем.</span><span class="sxs-lookup"><span data-stu-id="40f74-106">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_SECRET_HERE` with the password you generated.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="40f74-107">Если вы используете систему управления версиями (например, Git), то в дальнейшем будет полезно исключить `oauth_environment_variables.rb` файл из системы управления версиями, чтобы избежать непреднамеренного утечки идентификатора и пароля приложения.</span><span class="sxs-lookup"><span data-stu-id="40f74-107">If you're using source control such as git, now would be a good time to exclude the `oauth_environment_variables.rb` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

1. <span data-ttu-id="40f74-108">Откройте **./конфиг/енвиронмент.РБ** и добавьте приведенный ниже код перед `Rails.application.initialize!` строкой.</span><span class="sxs-lookup"><span data-stu-id="40f74-108">Open **./config/environment.rb** and add the following code before the `Rails.application.initialize!` line.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/config/environment.rb" id="LoadOAuthSettingsSnippet" highlight="4-6":::

## <a name="setup-omniauth"></a><span data-ttu-id="40f74-109">Настройка OmniAuth</span><span class="sxs-lookup"><span data-stu-id="40f74-109">Setup OmniAuth</span></span>

<span data-ttu-id="40f74-110">Вы уже установили `omniauth-oauth2` драгоценный камень, но чтобы он работал с конечными точками Azure OAuth, необходимо [создать стратегию OAuth2](https://github.com/omniauth/omniauth-oauth2#creating-an-oauth2-strategy).</span><span class="sxs-lookup"><span data-stu-id="40f74-110">You've already installed the `omniauth-oauth2` gem, but in order to make it work with the Azure OAuth endpoints, you need to [create an OAuth2 strategy](https://github.com/omniauth/omniauth-oauth2#creating-an-oauth2-strategy).</span></span> <span data-ttu-id="40f74-111">Это класс Ruby, определяющий параметры для создания запросов OAuth для поставщика Azure.</span><span class="sxs-lookup"><span data-stu-id="40f74-111">This is a Ruby class that defines the parameters for making OAuth requests to the Azure provider.</span></span>

1. <span data-ttu-id="40f74-112">Создайте новый файл, вызываемый `microsoft_graph_auth.rb` в папке **./lib**' \* \*, и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="40f74-112">Create a new file called `microsoft_graph_auth.rb` in the **./lib**\`\*\* folder, and add the following code.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/lib/microsoft_graph_auth.rb" id="AuthStrategySnippet":::

    <span data-ttu-id="40f74-113">Уделите время, чтобы узнать, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="40f74-113">Take a moment to review what this code does.</span></span>

    - <span data-ttu-id="40f74-114">Настройка конечных `client_options` точек платформы удостоверений (Майкрософт).</span><span class="sxs-lookup"><span data-stu-id="40f74-114">It sets the `client_options` to specify the Microsoft identity platform endpoints.</span></span>
    - <span data-ttu-id="40f74-115">Он указывает, что `scope` параметр должен быть отправлен на этапе авторизации.</span><span class="sxs-lookup"><span data-stu-id="40f74-115">It specifies that the `scope` parameter should be sent during the authorize phase.</span></span>
    - <span data-ttu-id="40f74-116">Он сопоставляет `id` свойство пользователя с уникальным идентификатором пользователя.</span><span class="sxs-lookup"><span data-stu-id="40f74-116">It maps the `id` property of the user as the unique ID for the user.</span></span>
    - <span data-ttu-id="40f74-117">Он использует маркер доступа для извлечения профиля пользователя из Microsoft Graph, чтобы заполнить `raw_info` хэш.</span><span class="sxs-lookup"><span data-stu-id="40f74-117">It uses the access token to retrieve the user's profile from Microsoft Graph to fill in the `raw_info` hash.</span></span>
    - <span data-ttu-id="40f74-118">Он переопределяет URL-адрес обратного вызова, чтобы убедиться, что он соответствует зарегистрированному ответному вызову на портале регистрации приложений.</span><span class="sxs-lookup"><span data-stu-id="40f74-118">It overrides the callback URL to ensure that it matches the registered callback in the app registration portal.</span></span>

1. <span data-ttu-id="40f74-119">Создайте новый файл, вызываемый `omniauth_graph.rb` в папке **./конфиг/инитиализерс** , и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="40f74-119">Create a new file called `omniauth_graph.rb` in the **./config/initializers** folder, and add the following code.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/config/initializers/omniauth_graph.rb" id="ConfigureOmniAuthSnippet":::

    <span data-ttu-id="40f74-120">Этот код будет выполняться при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="40f74-120">This code will execute when the app starts.</span></span> <span data-ttu-id="40f74-121">Он загружает промежуточный по OmniAuth с `microsoft_graph_auth` поставщиком, настроенным с использованием переменных среды, заданных в **oauth_environment_variables. RB**.</span><span class="sxs-lookup"><span data-stu-id="40f74-121">It loads up the OmniAuth middleware with the `microsoft_graph_auth` provider, configured with the environment variables set in **oauth_environment_variables.rb**.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="40f74-122">Реализация входа</span><span class="sxs-lookup"><span data-stu-id="40f74-122">Implement sign-in</span></span>

<span data-ttu-id="40f74-123">Теперь, когда настроено промежуточное по OmniAuth, вы можете перейти к добавлению входа в приложение.</span><span class="sxs-lookup"><span data-stu-id="40f74-123">Now that the OmniAuth middleware is configured, you can move on to adding sign-in to the app.</span></span>

1. <span data-ttu-id="40f74-124">Выполните следующую команду в интерфейсе командной строки, чтобы создать контроллер для входа и выхода.</span><span class="sxs-lookup"><span data-stu-id="40f74-124">Run the following command in your CLI to generate a controller for sign-in and sign-out.</span></span>

    ```Shell
    rails generate controller Auth
    ```

1. <span data-ttu-id="40f74-125">Open **./апп/контроллерс/auth_controller. RB**.</span><span class="sxs-lookup"><span data-stu-id="40f74-125">Open **./app/controllers/auth_controller.rb**.</span></span> <span data-ttu-id="40f74-126">Добавьте в `AuthController` класс метод обратного вызова.</span><span class="sxs-lookup"><span data-stu-id="40f74-126">Add a callback method to the `AuthController` class.</span></span> <span data-ttu-id="40f74-127">Этот метод будет вызываться по промежуточного слоя OmniAuth после завершения процесса OAuth.</span><span class="sxs-lookup"><span data-stu-id="40f74-127">This method will be called by the OmniAuth middleware once the OAuth flow is complete.</span></span>

    ```ruby
    def callback
      # Access the authentication hash for omniauth
      data = request.env['omniauth.auth']

      # Temporary for testing!
      render json: data.to_json
    end
    ```

    <span data-ttu-id="40f74-128">Теперь все это позволяет отобразить хэш, предоставленный OmniAuth.</span><span class="sxs-lookup"><span data-stu-id="40f74-128">For now all this does is render the hash provided by OmniAuth.</span></span> <span data-ttu-id="40f74-129">Вы будете использовать этот параметр, чтобы проверить, работает ли вход, прежде чем переходить к.</span><span class="sxs-lookup"><span data-stu-id="40f74-129">You'll use this to verify that sign-in is working before moving on.</span></span>

1. <span data-ttu-id="40f74-130">Добавьте маршруты в **./конфиг/раутес.РБ**.</span><span class="sxs-lookup"><span data-stu-id="40f74-130">Add the routes to **./config/routes.rb**.</span></span>

    ```ruby
    # Add route for OmniAuth callback
    match '/auth/:provider/callback', to: 'auth#callback', via: [:get, :post]
    ```

1. <span data-ttu-id="40f74-131">Запустите сервер и перейдите к `https://localhost:3000`.</span><span class="sxs-lookup"><span data-stu-id="40f74-131">Start the server and browse to `https://localhost:3000`.</span></span> <span data-ttu-id="40f74-132">Нажмите кнопку входа, и вы будете перенаправлены на `https://login.microsoftonline.com`.</span><span class="sxs-lookup"><span data-stu-id="40f74-132">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="40f74-133">Войдите с помощью учетной записи Майкрософт и согласия с запрошенными разрешениями.</span><span class="sxs-lookup"><span data-stu-id="40f74-133">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="40f74-134">Браузер перенаправляется в приложение, отображая хэш, созданный с помощью OmniAuth.</span><span class="sxs-lookup"><span data-stu-id="40f74-134">The browser redirects to the app, showing the hash generated by OmniAuth.</span></span>

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

## <a name="storing-the-tokens"></a><span data-ttu-id="40f74-135">Сохранение маркеров</span><span class="sxs-lookup"><span data-stu-id="40f74-135">Storing the tokens</span></span>

<span data-ttu-id="40f74-136">Теперь, когда вы можете получить маркеры, следует реализовать способ их хранения в приложении.</span><span class="sxs-lookup"><span data-stu-id="40f74-136">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="40f74-137">Так как это пример приложения, для простоты вы будете хранить их в сеансе.</span><span class="sxs-lookup"><span data-stu-id="40f74-137">Since this is a sample app, for simplicity's sake, you'll store them in the session.</span></span> <span data-ttu-id="40f74-138">Реальное приложение использует более надежное решение для безопасного хранения, например базу данных.</span><span class="sxs-lookup"><span data-stu-id="40f74-138">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

1. <span data-ttu-id="40f74-139">Open **./апп/контроллерс/application_controller. RB**.</span><span class="sxs-lookup"><span data-stu-id="40f74-139">Open **./app/controllers/application_controller.rb**.</span></span> <span data-ttu-id="40f74-140">Добавьте приведенный ниже метод в класс `ApplicationController`.</span><span class="sxs-lookup"><span data-stu-id="40f74-140">Add the following method to the `ApplicationController` class.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/application_controller.rb" id="SaveInSessionSnippet":::

    <span data-ttu-id="40f74-141">Метод принимает хэш OmniAuth в качестве параметра и извлекает релевантные биты информации, а затем сохраняет их в сеансе.</span><span class="sxs-lookup"><span data-stu-id="40f74-141">The method takes the OmniAuth hash as a parameter and extracts the relevant bits of information, then stores that in the session.</span></span>

1. <span data-ttu-id="40f74-142">Добавьте в `ApplicationController` класс функции доступа, чтобы получить имя пользователя, адрес электронной почты и маркер доступа обратно из сеанса.</span><span class="sxs-lookup"><span data-stu-id="40f74-142">Add accessor functions to the `ApplicationController` class to retrieve the user name, email address, and access token back out of the session.</span></span>

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

1. <span data-ttu-id="40f74-143">Добавьте следующий код в `ApplicationController` класс, который будет выполняться до обработки какого-либо действия.</span><span class="sxs-lookup"><span data-stu-id="40f74-143">Add the following code to the `ApplicationController` class that will run before any action is processed.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/application_controller.rb" id="BeforeActionSnippet":::

    <span data-ttu-id="40f74-144">Этот метод задает переменные, используемые макетом (в **Application. HTML. ЕРБ**), для отображения сведений о пользователе в панели навигации.</span><span class="sxs-lookup"><span data-stu-id="40f74-144">This method sets the variables that the layout (in **application.html.erb**) uses to show the user's information in the nav bar.</span></span> <span data-ttu-id="40f74-145">Добавив его сюда, вам не нужно добавлять этот код в каждое действие с одним контроллером.</span><span class="sxs-lookup"><span data-stu-id="40f74-145">By adding it here, you don't have to add this code in every single controller action.</span></span> <span data-ttu-id="40f74-146">Однако это также будет выполняться для действий `AuthController`, которые не являются оптимальными.</span><span class="sxs-lookup"><span data-stu-id="40f74-146">However, this will also run for actions in the `AuthController`, which isn't optimal.</span></span>

1. <span data-ttu-id="40f74-147">Добавьте следующий код в `AuthController` класс в файле **./АПП/контроллерс/auth_controller. RB** , чтобы пропустить действие before.</span><span class="sxs-lookup"><span data-stu-id="40f74-147">Add the following code to the `AuthController` class in **./app/controllers/auth_controller.rb** to skip the before action.</span></span>

    ```ruby
    skip_before_action :set_user
    ```

1. <span data-ttu-id="40f74-148">Обновите `callback` функцию в `AuthController` классе для хранения маркеров в сеансе и перенаправление обратно на главную страницу.</span><span class="sxs-lookup"><span data-stu-id="40f74-148">Update the `callback` function in the `AuthController` class to store the tokens in the session and redirect back to the main page.</span></span> <span data-ttu-id="40f74-149">Замените имеющуюся функцию `callback` указанным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="40f74-149">Replace the existing `callback` function with the following.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/auth_controller.rb" id="CallbackSnippet":::

## <a name="implement-sign-out"></a><span data-ttu-id="40f74-150">Реализация выхода</span><span class="sxs-lookup"><span data-stu-id="40f74-150">Implement sign-out</span></span>

<span data-ttu-id="40f74-151">Перед тестированием новой функции добавьте способ выхода.</span><span class="sxs-lookup"><span data-stu-id="40f74-151">Before you test this new feature, add a way to sign out.</span></span>

1. <span data-ttu-id="40f74-152">Добавьте в `AuthController` класс следующее действие.</span><span class="sxs-lookup"><span data-stu-id="40f74-152">Add the following action to the `AuthController` class.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/auth_controller.rb" id="SignOutSnippet":::

1. <span data-ttu-id="40f74-153">Добавьте это действие в **./конфиг/раутес.РБ**.</span><span class="sxs-lookup"><span data-stu-id="40f74-153">Add this action to **./config/routes.rb**.</span></span>

    ```ruby
    get 'auth/signout'
    ```

1. <span data-ttu-id="40f74-154">Перезапустите сервер и пройдите процесс входа.</span><span class="sxs-lookup"><span data-stu-id="40f74-154">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="40f74-155">Необходимо вернуться на домашнюю страницу, но пользовательский интерфейс должен измениться, чтобы показать, что вы вошли в систему.</span><span class="sxs-lookup"><span data-stu-id="40f74-155">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Снимок экрана домашней страницы после входа](./images/add-aad-auth-01.png)

1. <span data-ttu-id="40f74-157">Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **выхода** .</span><span class="sxs-lookup"><span data-stu-id="40f74-157">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="40f74-158">При нажатии кнопки **выйти** сбрасывается сеанс и возвращается на домашнюю страницу.</span><span class="sxs-lookup"><span data-stu-id="40f74-158">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Снимок экрана с раскрывающимся меню со ссылкой "выйти"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="40f74-160">Обновление маркеров</span><span class="sxs-lookup"><span data-stu-id="40f74-160">Refreshing tokens</span></span>

<span data-ttu-id="40f74-161">Если вы близки к хэшу, созданному с помощью OmniAuth, обратите внимание на два маркера в хэш `token` - `refresh_token`коде: и.</span><span class="sxs-lookup"><span data-stu-id="40f74-161">If you look closely at the hash generated by OmniAuth, you'll notice there are two tokens in the hash: `token` and `refresh_token`.</span></span> <span data-ttu-id="40f74-162">Значение в `token` — это маркер доступа, который отправляется в `Authorization` заголовке вызовов API.</span><span class="sxs-lookup"><span data-stu-id="40f74-162">The value in `token` is the access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="40f74-163">Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="40f74-163">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="40f74-164">Однако этот маркер кратковременно используется.</span><span class="sxs-lookup"><span data-stu-id="40f74-164">However, this token is short-lived.</span></span> <span data-ttu-id="40f74-165">Срок действия маркера истечет через час после его выдачи.</span><span class="sxs-lookup"><span data-stu-id="40f74-165">The token expires an hour after it is issued.</span></span> <span data-ttu-id="40f74-166">В `refresh_token` этом случае значение становится полезным.</span><span class="sxs-lookup"><span data-stu-id="40f74-166">This is where the `refresh_token` value becomes useful.</span></span> <span data-ttu-id="40f74-167">Маркер обновления позволяет приложению запросить новый маркер доступа, не требуя от пользователя повторного входа.</span><span class="sxs-lookup"><span data-stu-id="40f74-167">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span> <span data-ttu-id="40f74-168">Обновите код управления маркером, чтобы реализовать обновление маркеров.</span><span class="sxs-lookup"><span data-stu-id="40f74-168">Update the token management code to implement token refresh.</span></span>

1. <span data-ttu-id="40f74-169">Откройте **./апп/контроллерс/application_controller. RB** и добавьте в начало `require` следующие операторы:</span><span class="sxs-lookup"><span data-stu-id="40f74-169">Open **./app/controllers/application_controller.rb** and add the following `require` statements at the top:</span></span>

    ```ruby
    require 'microsoft_graph_auth'
    require 'oauth2'
    ```

1. <span data-ttu-id="40f74-170">Добавьте приведенный ниже метод в класс `ApplicationController`.</span><span class="sxs-lookup"><span data-stu-id="40f74-170">Add the following method to the `ApplicationController` class.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/application_controller.rb" id="RefreshTokensSnippet":::

    <span data-ttu-id="40f74-171">Этот метод использует драгоценный камень [OAuth2](https://github.com/oauth-xx/oauth2) (зависимость от `omniauth-oauth2` драгоценного камень) для обновления маркеров и обновления сеанса.</span><span class="sxs-lookup"><span data-stu-id="40f74-171">This method uses the [oauth2](https://github.com/oauth-xx/oauth2) gem (a dependency of the `omniauth-oauth2` gem) to refresh the tokens, and updates the session.</span></span>

1. <span data-ttu-id="40f74-172">Замените текущий `access_token` метод на приведенный ниже.</span><span class="sxs-lookup"><span data-stu-id="40f74-172">Replace the current `access_token` method with the following.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/application_controller.rb" id="AccessTokenSnippet":::

    <span data-ttu-id="40f74-173">Вместо того чтобы просто возвратить маркер из сеанса, он сначала проверяет, является ли срок его действия близким к сроку действия.</span><span class="sxs-lookup"><span data-stu-id="40f74-173">Instead of just returning the token from the session, it will first check if it is close to expiration.</span></span> <span data-ttu-id="40f74-174">Если это так, то оно обновится перед возвратом маркера.</span><span class="sxs-lookup"><span data-stu-id="40f74-174">If it is, then it will refresh before returning the token.</span></span>
