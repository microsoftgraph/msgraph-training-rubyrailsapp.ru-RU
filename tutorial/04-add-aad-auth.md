<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="88c52-101">В этом упражнении вы расширим приложение из предыдущего упражнения, чтобы поддерживать проверку подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="88c52-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="88c52-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="88c52-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="88c52-103">На этом этапе вы интегрируете в приложение самосъемку [omniauth-oauth2](https://github.com/omniauth/omniauth-oauth2) и создайте настраиваемую стратегию OmniAuth.</span><span class="sxs-lookup"><span data-stu-id="88c52-103">In this step you will integrate the [omniauth-oauth2](https://github.com/omniauth/omniauth-oauth2) gem into the application, and create a custom OmniAuth strategy.</span></span>

1. <span data-ttu-id="88c52-104">Создайте отдельный файл для с удержания ИД и секрета приложения.</span><span class="sxs-lookup"><span data-stu-id="88c52-104">Create a separate file to hold your app ID and secret.</span></span> <span data-ttu-id="88c52-105">Создайте файл, который будет `oauth_environment_variables.rb` вызван в **папке ./config,** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="88c52-105">Create a new file called `oauth_environment_variables.rb` in the **./config** folder, and add the following code.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/config/oauth_environment_variables.rb.example":::

1. <span data-ttu-id="88c52-106">Замените код приложения на портале регистрации приложений `YOUR_APP_ID_HERE` и пароль, `YOUR_APP_SECRET_HERE` созданный вами.</span><span class="sxs-lookup"><span data-stu-id="88c52-106">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_SECRET_HERE` with the password you generated.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="88c52-107">Если вы используете управление исходным кодом, например git, пришло бы время исключить файл из системы управления исходным кодом, чтобы не допустить случайной утечки вашего ИД приложения `oauth_environment_variables.rb` и пароля.</span><span class="sxs-lookup"><span data-stu-id="88c52-107">If you're using source control such as git, now would be a good time to exclude the `oauth_environment_variables.rb` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

1. <span data-ttu-id="88c52-108">Откройте **./config/environment.rb** и добавьте следующий код перед `Rails.application.initialize!` строкой.</span><span class="sxs-lookup"><span data-stu-id="88c52-108">Open **./config/environment.rb** and add the following code before the `Rails.application.initialize!` line.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/config/environment.rb" id="LoadOAuthSettingsSnippet" highlight="4-6":::

## <a name="setup-omniauth"></a><span data-ttu-id="88c52-109">Setup OmniAuth</span><span class="sxs-lookup"><span data-stu-id="88c52-109">Setup OmniAuth</span></span>

<span data-ttu-id="88c52-110">Вы уже установили этот набор, но чтобы он работал с конечными точками OAuth Azure, необходимо создать стратегию `omniauth-oauth2` [OAuth2.](https://github.com/omniauth/omniauth-oauth2#creating-an-oauth2-strategy)</span><span class="sxs-lookup"><span data-stu-id="88c52-110">You've already installed the `omniauth-oauth2` gem, but in order to make it work with the Azure OAuth endpoints, you need to [create an OAuth2 strategy](https://github.com/omniauth/omniauth-oauth2#creating-an-oauth2-strategy).</span></span> <span data-ttu-id="88c52-111">Это класс Ruby, который определяет параметры для создания запросов OAuth к поставщику Azure.</span><span class="sxs-lookup"><span data-stu-id="88c52-111">This is a Ruby class that defines the parameters for making OAuth requests to the Azure provider.</span></span>

1. <span data-ttu-id="88c52-112">Создайте файл, который будет `microsoft_graph_auth.rb` называться **в папке ./lib**'\*\* и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="88c52-112">Create a new file called `microsoft_graph_auth.rb` in the **./lib**\`\*\* folder, and add the following code.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/lib/microsoft_graph_auth.rb" id="AuthStrategySnippet":::

    <span data-ttu-id="88c52-113">Просмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="88c52-113">Take a moment to review what this code does.</span></span>

    - <span data-ttu-id="88c52-114">Он задает конечные точки платформы `client_options` удостоверений Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="88c52-114">It sets the `client_options` to specify the Microsoft identity platform endpoints.</span></span>
    - <span data-ttu-id="88c52-115">Он указывает, что `scope` параметр должен быть отправлен на этапе авторизации.</span><span class="sxs-lookup"><span data-stu-id="88c52-115">It specifies that the `scope` parameter should be sent during the authorize phase.</span></span>
    - <span data-ttu-id="88c52-116">Оно `id` сопокает свойство пользователя как уникальный ИД для пользователя.</span><span class="sxs-lookup"><span data-stu-id="88c52-116">It maps the `id` property of the user as the unique ID for the user.</span></span>
    - <span data-ttu-id="88c52-117">Он использует маркер доступа для извлечения профиля пользователя из Microsoft Graph для заполнения `raw_info` hash.</span><span class="sxs-lookup"><span data-stu-id="88c52-117">It uses the access token to retrieve the user's profile from Microsoft Graph to fill in the `raw_info` hash.</span></span>
    - <span data-ttu-id="88c52-118">Он переопределяет URL-адрес, чтобы убедиться, что он совпадает с зарегистрированным вызовом на портале регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="88c52-118">It overrides the callback URL to ensure that it matches the registered callback in the app registration portal.</span></span>

1. <span data-ttu-id="88c52-119">Создайте файл, который будет вызван `omniauth_graph.rb` в **папке ./config/initializers,** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="88c52-119">Create a new file called `omniauth_graph.rb` in the **./config/initializers** folder, and add the following code.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/config/initializers/omniauth_graph.rb" id="ConfigureOmniAuthSnippet":::

    <span data-ttu-id="88c52-120">Этот код будет выполняться при запускается приложение.</span><span class="sxs-lookup"><span data-stu-id="88c52-120">This code will execute when the app starts.</span></span> <span data-ttu-id="88c52-121">Оно загружает по середине OmniAuth с поставщиком, настроенным с помощью переменных среды, задаваемых `microsoft_graph_auth` **в oauth_environment_variables.rb.**</span><span class="sxs-lookup"><span data-stu-id="88c52-121">It loads up the OmniAuth middleware with the `microsoft_graph_auth` provider, configured with the environment variables set in **oauth_environment_variables.rb**.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="88c52-122">Реализация входов</span><span class="sxs-lookup"><span data-stu-id="88c52-122">Implement sign-in</span></span>

<span data-ttu-id="88c52-123">Теперь, когда по середине OmniAuth настроено, вы можете перейти к добавлению вход в приложение.</span><span class="sxs-lookup"><span data-stu-id="88c52-123">Now that the OmniAuth middleware is configured, you can move on to adding sign-in to the app.</span></span>

1. <span data-ttu-id="88c52-124">Чтобы создать контроллер для входов и выходов, запустите следующую команду в CLI.</span><span class="sxs-lookup"><span data-stu-id="88c52-124">Run the following command in your CLI to generate a controller for sign-in and sign-out.</span></span>

    ```Shell
    rails generate controller Auth
    ```

1. <span data-ttu-id="88c52-125">Откройте **./app/controllers/auth_controller.rb**.</span><span class="sxs-lookup"><span data-stu-id="88c52-125">Open **./app/controllers/auth_controller.rb**.</span></span> <span data-ttu-id="88c52-126">Добавьте метод callback в `AuthController` класс.</span><span class="sxs-lookup"><span data-stu-id="88c52-126">Add a callback method to the `AuthController` class.</span></span> <span data-ttu-id="88c52-127">Этот метод будет вызван программой-посредником OmniAuth после завершения потока OAuth.</span><span class="sxs-lookup"><span data-stu-id="88c52-127">This method will be called by the OmniAuth middleware once the OAuth flow is complete.</span></span>

    ```ruby
    def callback
      # Access the authentication hash for omniauth
      data = request.env['omniauth.auth']

      # Temporary for testing!
      render json: data.to_json
    end
    ```

    <span data-ttu-id="88c52-128">На данный момент все это делает отрисовка hash, предоставленный OmniAuth.</span><span class="sxs-lookup"><span data-stu-id="88c52-128">For now all this does is render the hash provided by OmniAuth.</span></span> <span data-ttu-id="88c52-129">Вы будете использовать его, чтобы убедиться, что вход работает, прежде чем двигаться дальше.</span><span class="sxs-lookup"><span data-stu-id="88c52-129">You'll use this to verify that sign-in is working before moving on.</span></span>

1. <span data-ttu-id="88c52-130">Добавьте маршруты в **./config/routes.rb.**</span><span class="sxs-lookup"><span data-stu-id="88c52-130">Add the routes to **./config/routes.rb**.</span></span>

    ```ruby
    # Add route for OmniAuth callback
    match '/auth/:provider/callback', :to => 'auth#callback', :via => [:get, :post]
    ```

1. <span data-ttu-id="88c52-131">Запустите сервер и перейдите к `https://localhost:3000` .</span><span class="sxs-lookup"><span data-stu-id="88c52-131">Start the server and browse to `https://localhost:3000`.</span></span> <span data-ttu-id="88c52-132">Нажмите кнопку "Вход" и перенаправите вас на `https://login.microsoftonline.com` .</span><span class="sxs-lookup"><span data-stu-id="88c52-132">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="88c52-133">Войдите в систему с помощью учетной записи Майкрософт и согласиться на запрашиваемую разрешения.</span><span class="sxs-lookup"><span data-stu-id="88c52-133">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="88c52-134">Браузер перенаправляет пользователя в приложение с отображением hash, сгенерированного OmniAuth.</span><span class="sxs-lookup"><span data-stu-id="88c52-134">The browser redirects to the app, showing the hash generated by OmniAuth.</span></span>

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
          "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#users(displayName,mail,mailboxSettings,userPrincipalName)/$entity",
          "displayName": "Lynne Robbins",
          "mail": "LynneR@contoso.OnMicrosoft.com",
          "userPrincipalName": "LynneR@contoso.OnMicrosoft.com",
          "id": "d294e784-840e-4f9f-bb1e-95c0a75f2f18@2d18179c-4386-4cbd-8891-7fd867c4f62e",
          "mailboxSettings": {
            "archiveFolder": "AAMkAGI2...",
            "timeZone": "Pacific Standard Time",
            "delegateMeetingMessageDeliveryOptions": "sendToDelegateOnly",
            "dateFormat": "M/d/yyyy",
            "timeFormat": "h:mm tt",
            "automaticRepliesSetting": {
              "status": "disabled",
              "externalAudience": "all",
              "internalReplyMessage": "",
              "externalReplyMessage": "",
              "scheduledStartDateTime": {
                "dateTime": "2020-12-09T17:00:00.0000000",
                "timeZone": "UTC"
              },
              "scheduledEndDateTime": {
                "dateTime": "2020-12-10T17:00:00.0000000",
                "timeZone": "UTC"
              }
            },
            "language": {
              "locale": "en-US",
              "displayName": "English (United States)"
            },
            "workingHours": {
              "daysOfWeek": [
                "monday",
                "tuesday",
                "wednesday",
                "thursday",
                "friday"
              ],
              "startTime": "08:00:00.0000000",
              "endTime": "17:00:00.0000000",
              "timeZone": {
                "name": "Pacific Standard Time"
              }
            }
          }
        }
      }
    }
    ```

## <a name="storing-the-tokens"></a><span data-ttu-id="88c52-135">Хранение маркеров</span><span class="sxs-lookup"><span data-stu-id="88c52-135">Storing the tokens</span></span>

<span data-ttu-id="88c52-136">Теперь, когда вы можете получить маркеры, пора реализовать способ их хранения в приложении.</span><span class="sxs-lookup"><span data-stu-id="88c52-136">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="88c52-137">Так как это пример приложения, для простоты вы храните их в сеансе.</span><span class="sxs-lookup"><span data-stu-id="88c52-137">Since this is a sample app, for simplicity's sake, you'll store them in the session.</span></span> <span data-ttu-id="88c52-138">В реальных приложениях используется более надежное решение для безопасного хранения данных, например база данных.</span><span class="sxs-lookup"><span data-stu-id="88c52-138">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

1. <span data-ttu-id="88c52-139">Откройте **./app/controllers/application_controller.rb**.</span><span class="sxs-lookup"><span data-stu-id="88c52-139">Open **./app/controllers/application_controller.rb**.</span></span> <span data-ttu-id="88c52-140">Добавьте приведенный ниже метод в класс `ApplicationController`.</span><span class="sxs-lookup"><span data-stu-id="88c52-140">Add the following method to the `ApplicationController` class.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/application_controller.rb" id="SaveInSessionSnippet":::

    <span data-ttu-id="88c52-141">Метод принимает в качестве параметра hash OmniAuth и извлекает соответствующие биты информации, а затем сохраняет его в сеансе.</span><span class="sxs-lookup"><span data-stu-id="88c52-141">The method takes the OmniAuth hash as a parameter and extracts the relevant bits of information, then stores that in the session.</span></span>

1. <span data-ttu-id="88c52-142">Добавьте в класс функции доступа для получения имени пользователя, адреса электронной почты и маркера доступа `ApplicationController` из сеанса.</span><span class="sxs-lookup"><span data-stu-id="88c52-142">Add accessor functions to the `ApplicationController` class to retrieve the user name, email address, and access token back out of the session.</span></span>

    ```ruby
    def user_name
      session[:user_name]
    end

    def user_email
      session[:user_email]
    end

    def user_timezone
      session[:user_timezone]
    end

    def access_token
      session[:graph_token_hash][:token]
    end
    ```

1. <span data-ttu-id="88c52-143">Добавьте следующий код в `ApplicationController` класс, который будет запускаться перед обработкой какого-либо действия.</span><span class="sxs-lookup"><span data-stu-id="88c52-143">Add the following code to the `ApplicationController` class that will run before any action is processed.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/application_controller.rb" id="BeforeActionSnippet":::

    <span data-ttu-id="88c52-144">Этот метод задает переменные, которые макет (вapplication.htm **l.erb)** использует для показа сведений о пользователе на панели nav.</span><span class="sxs-lookup"><span data-stu-id="88c52-144">This method sets the variables that the layout (in **application.html.erb**) uses to show the user's information in the nav bar.</span></span> <span data-ttu-id="88c52-145">Добавляя его сюда, вам не нужно добавлять этот код в каждом действии контроллера.</span><span class="sxs-lookup"><span data-stu-id="88c52-145">By adding it here, you don't have to add this code in every single controller action.</span></span> <span data-ttu-id="88c52-146">Однако это также будет работать для действий в не оптимальных `AuthController` действиях.</span><span class="sxs-lookup"><span data-stu-id="88c52-146">However, this will also run for actions in the `AuthController`, which isn't optimal.</span></span>

1. <span data-ttu-id="88c52-147">Добавьте следующий код в класс `AuthController` **в ./app/controllers/auth_controller.rb,** чтобы пропустить действие перед.</span><span class="sxs-lookup"><span data-stu-id="88c52-147">Add the following code to the `AuthController` class in **./app/controllers/auth_controller.rb** to skip the before action.</span></span>

    ```ruby
    skip_before_action :set_user
    ```

1. <span data-ttu-id="88c52-148">Обновите функцию в классе, чтобы сохранить маркеры в сеансе и `callback` `AuthController` перенаправить обратно на главную страницу.</span><span class="sxs-lookup"><span data-stu-id="88c52-148">Update the `callback` function in the `AuthController` class to store the tokens in the session and redirect back to the main page.</span></span> <span data-ttu-id="88c52-149">Замените имеющуюся функцию `callback` указанным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="88c52-149">Replace the existing `callback` function with the following.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/auth_controller.rb" id="CallbackSnippet":::

## <a name="implement-sign-out"></a><span data-ttu-id="88c52-150">Реализация выходов</span><span class="sxs-lookup"><span data-stu-id="88c52-150">Implement sign-out</span></span>

<span data-ttu-id="88c52-151">Перед тестированием этой новой функции добавьте способ выйти из нее.</span><span class="sxs-lookup"><span data-stu-id="88c52-151">Before you test this new feature, add a way to sign out.</span></span>

1. <span data-ttu-id="88c52-152">Добавьте в класс следующее `AuthController` действие.</span><span class="sxs-lookup"><span data-stu-id="88c52-152">Add the following action to the `AuthController` class.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/auth_controller.rb" id="SignOutSnippet":::

1. <span data-ttu-id="88c52-153">Добавьте это действие в **./config/routes.rb.**</span><span class="sxs-lookup"><span data-stu-id="88c52-153">Add this action to **./config/routes.rb**.</span></span>

    ```ruby
    get 'auth/signout'
    ```

1. <span data-ttu-id="88c52-154">Перезапустите сервер и войдите в нее.</span><span class="sxs-lookup"><span data-stu-id="88c52-154">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="88c52-155">Вы должны вернуться на home-страницу, но пользовательский интерфейс должен измениться, чтобы указать, что вы вписались.</span><span class="sxs-lookup"><span data-stu-id="88c52-155">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Снимок экрана с домашней страницей после входов](./images/add-aad-auth-01.png)

1. <span data-ttu-id="88c52-157">Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **"Выйти".**</span><span class="sxs-lookup"><span data-stu-id="88c52-157">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="88c52-158">При **нажатии** кнопки "Выйти" сеанс сбрасывается и возвращается на домашней странице.</span><span class="sxs-lookup"><span data-stu-id="88c52-158">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Снимок экрана с выпадающим меню со ссылкой "Выйти"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="88c52-160">Обновление маркеров</span><span class="sxs-lookup"><span data-stu-id="88c52-160">Refreshing tokens</span></span>

<span data-ttu-id="88c52-161">Если внимательно посмотреть на hash, созданный OmniAuth, вы заметите, что в hash есть два маркера: `token` и `refresh_token` .</span><span class="sxs-lookup"><span data-stu-id="88c52-161">If you look closely at the hash generated by OmniAuth, you'll notice there are two tokens in the hash: `token` and `refresh_token`.</span></span> <span data-ttu-id="88c52-162">Значением `token` является маркер доступа, который отправляется в `Authorization` заголовке вызовов API.</span><span class="sxs-lookup"><span data-stu-id="88c52-162">The value in `token` is the access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="88c52-163">Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="88c52-163">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="88c52-164">Однако этот маркер является кратковременной.</span><span class="sxs-lookup"><span data-stu-id="88c52-164">However, this token is short-lived.</span></span> <span data-ttu-id="88c52-165">Срок действия маркера истекает через час после его выпуска.</span><span class="sxs-lookup"><span data-stu-id="88c52-165">The token expires an hour after it is issued.</span></span> <span data-ttu-id="88c52-166">Именно в этом случае `refresh_token` значение становится полезным.</span><span class="sxs-lookup"><span data-stu-id="88c52-166">This is where the `refresh_token` value becomes useful.</span></span> <span data-ttu-id="88c52-167">Маркер обновления позволяет приложению запрашивать новый маркер доступа, не требуя от пользователя повторного входить.</span><span class="sxs-lookup"><span data-stu-id="88c52-167">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span> <span data-ttu-id="88c52-168">Обновите код управления маркерами, чтобы реализовать обновление маркера.</span><span class="sxs-lookup"><span data-stu-id="88c52-168">Update the token management code to implement token refresh.</span></span>

1. <span data-ttu-id="88c52-169">Откройте **./app/controllers/application_controller.rb** и добавьте следующие утверждения `require` в верхней части:</span><span class="sxs-lookup"><span data-stu-id="88c52-169">Open **./app/controllers/application_controller.rb** and add the following `require` statements at the top:</span></span>

    ```ruby
    require 'microsoft_graph_auth'
    require 'oauth2'
    ```

1. <span data-ttu-id="88c52-170">Добавьте приведенный ниже метод в класс `ApplicationController`.</span><span class="sxs-lookup"><span data-stu-id="88c52-170">Add the following method to the `ApplicationController` class.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/application_controller.rb" id="RefreshTokensSnippet":::

    <span data-ttu-id="88c52-171">Этот метод использует самосхему [oauth2](https://github.com/oauth-xx/oauth2) (зависимость этого самосхемы) для обновления маркеров и обновляет `omniauth-oauth2` сеанс.</span><span class="sxs-lookup"><span data-stu-id="88c52-171">This method uses the [oauth2](https://github.com/oauth-xx/oauth2) gem (a dependency of the `omniauth-oauth2` gem) to refresh the tokens, and updates the session.</span></span>

1. <span data-ttu-id="88c52-172">Замените `access_token` текущий метод на следующий.</span><span class="sxs-lookup"><span data-stu-id="88c52-172">Replace the current `access_token` method with the following.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/application_controller.rb" id="AccessTokenSnippet":::

    <span data-ttu-id="88c52-173">Вместо того чтобы просто возвращать маркер из сеанса, он сначала проверяет, не истекает ли срок действия.</span><span class="sxs-lookup"><span data-stu-id="88c52-173">Instead of just returning the token from the session, it will first check if it is close to expiration.</span></span> <span data-ttu-id="88c52-174">В этом случае он обновется перед возвратом маркера.</span><span class="sxs-lookup"><span data-stu-id="88c52-174">If it is, then it will refresh before returning the token.</span></span>
