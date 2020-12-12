<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы расширим приложение из предыдущего упражнения, чтобы поддерживать проверку подлинности с помощью Azure AD. Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph. На этом этапе вы интегрируете в приложение самосъемку [omniauth-oauth2](https://github.com/omniauth/omniauth-oauth2) и создайте настраиваемую стратегию OmniAuth.

1. Создайте отдельный файл для с удержания ИД и секрета приложения. Создайте файл, который будет `oauth_environment_variables.rb` вызван в **папке ./config,** и добавьте следующий код.

    :::code language="ruby" source="../demo/graph-tutorial/config/oauth_environment_variables.rb.example":::

1. Замените код приложения на портале регистрации приложений `YOUR_APP_ID_HERE` и пароль, `YOUR_APP_SECRET_HERE` созданный вами.

    > [!IMPORTANT]
    > Если вы используете управление исходным кодом, например git, пришло бы время исключить файл из системы управления исходным кодом, чтобы не допустить случайной утечки вашего ИД приложения `oauth_environment_variables.rb` и пароля.

1. Откройте **./config/environment.rb** и добавьте следующий код перед `Rails.application.initialize!` строкой.

    :::code language="ruby" source="../demo/graph-tutorial/config/environment.rb" id="LoadOAuthSettingsSnippet" highlight="4-6":::

## <a name="setup-omniauth"></a>Setup OmniAuth

Вы уже установили этот набор, но чтобы он работал с конечными точками OAuth Azure, необходимо создать стратегию `omniauth-oauth2` [OAuth2.](https://github.com/omniauth/omniauth-oauth2#creating-an-oauth2-strategy) Это класс Ruby, который определяет параметры для создания запросов OAuth к поставщику Azure.

1. Создайте файл, который будет `microsoft_graph_auth.rb` называться **в папке ./lib**'** и добавьте следующий код.

    :::code language="ruby" source="../demo/graph-tutorial/lib/microsoft_graph_auth.rb" id="AuthStrategySnippet":::

    Просмотрите, что делает этот код.

    - Он задает конечные точки платформы `client_options` удостоверений Майкрософт.
    - Он указывает, что `scope` параметр должен быть отправлен на этапе авторизации.
    - Оно `id` сопокает свойство пользователя как уникальный ИД для пользователя.
    - Он использует маркер доступа для извлечения профиля пользователя из Microsoft Graph для заполнения `raw_info` hash.
    - Он переопределяет URL-адрес, чтобы убедиться, что он совпадает с зарегистрированным вызовом на портале регистрации приложения.

1. Создайте файл, который будет вызван `omniauth_graph.rb` в **папке ./config/initializers,** и добавьте следующий код.

    :::code language="ruby" source="../demo/graph-tutorial/config/initializers/omniauth_graph.rb" id="ConfigureOmniAuthSnippet":::

    Этот код будет выполняться при запускается приложение. Оно загружает по середине OmniAuth с поставщиком, настроенным с помощью переменных среды, задаваемых `microsoft_graph_auth` **в oauth_environment_variables.rb.**

## <a name="implement-sign-in"></a>Реализация входов

Теперь, когда по середине OmniAuth настроено, вы можете перейти к добавлению вход в приложение.

1. Чтобы создать контроллер для входов и выходов, запустите следующую команду в CLI.

    ```Shell
    rails generate controller Auth
    ```

1. Откройте **./app/controllers/auth_controller.rb**. Добавьте метод callback в `AuthController` класс. Этот метод будет вызван программой-посредником OmniAuth после завершения потока OAuth.

    ```ruby
    def callback
      # Access the authentication hash for omniauth
      data = request.env['omniauth.auth']

      # Temporary for testing!
      render json: data.to_json
    end
    ```

    На данный момент все это делает отрисовка hash, предоставленный OmniAuth. Вы будете использовать его, чтобы убедиться, что вход работает, прежде чем двигаться дальше.

1. Добавьте маршруты в **./config/routes.rb.**

    ```ruby
    # Add route for OmniAuth callback
    match '/auth/:provider/callback', :to => 'auth#callback', :via => [:get, :post]
    ```

1. Запустите сервер и перейдите к `https://localhost:3000` . Нажмите кнопку "Вход" и перенаправите вас на `https://login.microsoftonline.com` . Войдите в систему с помощью учетной записи Майкрософт и согласиться на запрашиваемую разрешения. Браузер перенаправляет пользователя в приложение с отображением hash, сгенерированного OmniAuth.

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

## <a name="storing-the-tokens"></a>Хранение маркеров

Теперь, когда вы можете получить маркеры, пора реализовать способ их хранения в приложении. Так как это пример приложения, для простоты вы храните их в сеансе. В реальных приложениях используется более надежное решение для безопасного хранения данных, например база данных.

1. Откройте **./app/controllers/application_controller.rb**. Добавьте приведенный ниже метод в класс `ApplicationController`.

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/application_controller.rb" id="SaveInSessionSnippet":::

    Метод принимает в качестве параметра hash OmniAuth и извлекает соответствующие биты информации, а затем сохраняет его в сеансе.

1. Добавьте в класс функции доступа для получения имени пользователя, адреса электронной почты и маркера доступа `ApplicationController` из сеанса.

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

1. Добавьте следующий код в `ApplicationController` класс, который будет запускаться перед обработкой какого-либо действия.

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/application_controller.rb" id="BeforeActionSnippet":::

    Этот метод задает переменные, которые макет (вapplication.htm **l.erb)** использует для показа сведений о пользователе на панели nav. Добавляя его сюда, вам не нужно добавлять этот код в каждом действии контроллера. Однако это также будет работать для действий в не оптимальных `AuthController` действиях.

1. Добавьте следующий код в класс `AuthController` **в ./app/controllers/auth_controller.rb,** чтобы пропустить действие перед.

    ```ruby
    skip_before_action :set_user
    ```

1. Обновите функцию в классе, чтобы сохранить маркеры в сеансе и `callback` `AuthController` перенаправить обратно на главную страницу. Замените имеющуюся функцию `callback` указанным ниже кодом.

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/auth_controller.rb" id="CallbackSnippet":::

## <a name="implement-sign-out"></a>Реализация выходов

Перед тестированием этой новой функции добавьте способ выйти из нее.

1. Добавьте в класс следующее `AuthController` действие.

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/auth_controller.rb" id="SignOutSnippet":::

1. Добавьте это действие в **./config/routes.rb.**

    ```ruby
    get 'auth/signout'
    ```

1. Перезапустите сервер и войдите в нее. Вы должны вернуться на home-страницу, но пользовательский интерфейс должен измениться, чтобы указать, что вы вписались.

    ![Снимок экрана с домашней страницей после входов](./images/add-aad-auth-01.png)

1. Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **"Выйти".** При **нажатии** кнопки "Выйти" сеанс сбрасывается и возвращается на домашней странице.

    ![Снимок экрана с выпадающим меню со ссылкой "Выйти"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Обновление маркеров

Если внимательно посмотреть на hash, созданный OmniAuth, вы заметите, что в hash есть два маркера: `token` и `refresh_token` . Значением `token` является маркер доступа, который отправляется в `Authorization` заголовке вызовов API. Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.

Однако этот маркер является кратковременной. Срок действия маркера истекает через час после его выпуска. Именно в этом случае `refresh_token` значение становится полезным. Маркер обновления позволяет приложению запрашивать новый маркер доступа, не требуя от пользователя повторного входить. Обновите код управления маркерами, чтобы реализовать обновление маркера.

1. Откройте **./app/controllers/application_controller.rb** и добавьте следующие утверждения `require` в верхней части:

    ```ruby
    require 'microsoft_graph_auth'
    require 'oauth2'
    ```

1. Добавьте приведенный ниже метод в класс `ApplicationController`.

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/application_controller.rb" id="RefreshTokensSnippet":::

    Этот метод использует самосхему [oauth2](https://github.com/oauth-xx/oauth2) (зависимость этого самосхемы) для обновления маркеров и обновляет `omniauth-oauth2` сеанс.

1. Замените `access_token` текущий метод на следующий.

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/application_controller.rb" id="AccessTokenSnippet":::

    Вместо того чтобы просто возвращать маркер из сеанса, он сначала проверяет, не истекает ли срок действия. В этом случае он обновется перед возвратом маркера.
