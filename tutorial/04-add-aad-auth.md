<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD. Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph. На этом этапе вы интегрируете драгоценный камень [omniauth – OAuth2](https://github.com/omniauth/omniauth-oauth2) в приложение и создаете особую стратегию omniauth.

1. Создайте отдельный файл для хранения идентификатора и секрета приложения. Создайте новый файл, вызываемый `oauth_environment_variables.rb` в папке **/config** , и добавьте следующий код.

    :::code language="ruby" source="../demo/graph-tutorial/config/oauth_environment_variables.rb.example":::

1. Замените `YOUR_APP_ID_HERE` идентификатором приложения на портале регистрации приложений и замените `YOUR_APP_SECRET_HERE` созданным паролем.

    > [!IMPORTANT]
    > Если вы используете систему управления версиями (например, Git), то в дальнейшем будет полезно исключить `oauth_environment_variables.rb` файл из системы управления версиями, чтобы избежать непреднамеренного утечки идентификатора и пароля приложения.

1. Откройте **./конфиг/енвиронмент.РБ** и добавьте приведенный ниже код перед `Rails.application.initialize!` строкой.

    :::code language="ruby" source="../demo/graph-tutorial/config/environment.rb" id="LoadOAuthSettingsSnippet" highlight="4-6":::

## <a name="setup-omniauth"></a>Настройка OmniAuth

Вы уже установили `omniauth-oauth2` драгоценный камень, но чтобы он работал с конечными точками Azure OAuth, необходимо [создать стратегию OAuth2](https://github.com/omniauth/omniauth-oauth2#creating-an-oauth2-strategy). Это класс Ruby, определяющий параметры для создания запросов OAuth для поставщика Azure.

1. Создайте новый файл, вызываемый `microsoft_graph_auth.rb` в папке **./lib**' * *, и добавьте следующий код.

    :::code language="ruby" source="../demo/graph-tutorial/lib/microsoft_graph_auth.rb" id="AuthStrategySnippet":::

    Уделите время, чтобы узнать, что делает этот код.

    - Настройка конечных `client_options` точек платформы удостоверений (Майкрософт).
    - Он указывает, что `scope` параметр должен быть отправлен на этапе авторизации.
    - Он сопоставляет `id` свойство пользователя с уникальным идентификатором пользователя.
    - Он использует маркер доступа для извлечения профиля пользователя из Microsoft Graph, чтобы заполнить `raw_info` хэш.
    - Он переопределяет URL-адрес обратного вызова, чтобы убедиться, что он соответствует зарегистрированному ответному вызову на портале регистрации приложений.

1. Создайте новый файл, вызываемый `omniauth_graph.rb` в папке **./конфиг/инитиализерс** , и добавьте следующий код.

    :::code language="ruby" source="../demo/graph-tutorial/config/initializers/omniauth_graph.rb" id="ConfigureOmniAuthSnippet":::

    Этот код будет выполняться при запуске приложения. Он загружает промежуточный по OmniAuth с `microsoft_graph_auth` поставщиком, настроенным с использованием переменных среды, заданных в **oauth_environment_variables. RB**.

## <a name="implement-sign-in"></a>Реализация входа

Теперь, когда настроено промежуточное по OmniAuth, вы можете перейти к добавлению входа в приложение.

1. Выполните следующую команду в интерфейсе командной строки, чтобы создать контроллер для входа и выхода.

    ```Shell
    rails generate controller Auth
    ```

1. Open **./апп/контроллерс/auth_controller. RB**. Добавьте в `AuthController` класс метод обратного вызова. Этот метод будет вызываться по промежуточного слоя OmniAuth после завершения процесса OAuth.

    ```ruby
    def callback
      # Access the authentication hash for omniauth
      data = request.env['omniauth.auth']

      # Temporary for testing!
      render json: data.to_json
    end
    ```

    Теперь все это позволяет отобразить хэш, предоставленный OmniAuth. Вы будете использовать этот параметр, чтобы проверить, работает ли вход, прежде чем переходить к.

1. Добавьте маршруты в **./конфиг/раутес.РБ**.

    ```ruby
    # Add route for OmniAuth callback
    match '/auth/:provider/callback', to: 'auth#callback', via: [:get, :post]
    ```

1. Запустите сервер и перейдите к `https://localhost:3000`. Нажмите кнопку входа, и вы будете перенаправлены на `https://login.microsoftonline.com`. Войдите с помощью учетной записи Майкрософт и согласия с запрошенными разрешениями. Браузер перенаправляется в приложение, отображая хэш, созданный с помощью OmniAuth.

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

## <a name="storing-the-tokens"></a>Сохранение маркеров

Теперь, когда вы можете получить маркеры, следует реализовать способ их хранения в приложении. Так как это пример приложения, для простоты вы будете хранить их в сеансе. Реальное приложение использует более надежное решение для безопасного хранения, например базу данных.

1. Open **./апп/контроллерс/application_controller. RB**. Добавьте приведенный ниже метод в класс `ApplicationController`.

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/application_controller.rb" id="SaveInSessionSnippet":::

    Метод принимает хэш OmniAuth в качестве параметра и извлекает релевантные биты информации, а затем сохраняет их в сеансе.

1. Добавьте в `ApplicationController` класс функции доступа, чтобы получить имя пользователя, адрес электронной почты и маркер доступа обратно из сеанса.

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

1. Добавьте следующий код в `ApplicationController` класс, который будет выполняться до обработки какого-либо действия.

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/application_controller.rb" id="BeforeActionSnippet":::

    Этот метод задает переменные, используемые макетом (в **Application. HTML. ЕРБ**), для отображения сведений о пользователе в панели навигации. Добавив его сюда, вам не нужно добавлять этот код в каждое действие с одним контроллером. Однако это также будет выполняться для действий `AuthController`, которые не являются оптимальными.

1. Добавьте следующий код в `AuthController` класс в файле **./АПП/контроллерс/auth_controller. RB** , чтобы пропустить действие before.

    ```ruby
    skip_before_action :set_user
    ```

1. Обновите `callback` функцию в `AuthController` классе для хранения маркеров в сеансе и перенаправление обратно на главную страницу. Замените имеющуюся функцию `callback` указанным ниже кодом.

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/auth_controller.rb" id="CallbackSnippet":::

## <a name="implement-sign-out"></a>Реализация выхода

Перед тестированием новой функции добавьте способ выхода.

1. Добавьте в `AuthController` класс следующее действие.

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/auth_controller.rb" id="SignOutSnippet":::

1. Добавьте это действие в **./конфиг/раутес.РБ**.

    ```ruby
    get 'auth/signout'
    ```

1. Перезапустите сервер и пройдите процесс входа. Необходимо вернуться на домашнюю страницу, но пользовательский интерфейс должен измениться, чтобы показать, что вы вошли в систему.

    ![Снимок экрана домашней страницы после входа](./images/add-aad-auth-01.png)

1. Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **выхода** . При нажатии кнопки **выйти** сбрасывается сеанс и возвращается на домашнюю страницу.

    ![Снимок экрана с раскрывающимся меню со ссылкой "выйти"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Обновление маркеров

Если вы близки к хэшу, созданному с помощью OmniAuth, обратите внимание на два маркера в хэш `token` - `refresh_token`коде: и. Значение в `token` — это маркер доступа, который отправляется в `Authorization` заголовке вызовов API. Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.

Однако этот маркер кратковременно используется. Срок действия маркера истечет через час после его выдачи. В `refresh_token` этом случае значение становится полезным. Маркер обновления позволяет приложению запросить новый маркер доступа, не требуя от пользователя повторного входа. Обновите код управления маркером, чтобы реализовать обновление маркеров.

1. Откройте **./апп/контроллерс/application_controller. RB** и добавьте в начало `require` следующие операторы:

    ```ruby
    require 'microsoft_graph_auth'
    require 'oauth2'
    ```

1. Добавьте приведенный ниже метод в класс `ApplicationController`.

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/application_controller.rb" id="RefreshTokensSnippet":::

    Этот метод использует драгоценный камень [OAuth2](https://github.com/oauth-xx/oauth2) (зависимость от `omniauth-oauth2` драгоценного камень) для обновления маркеров и обновления сеанса.

1. Замените текущий `access_token` метод на приведенный ниже.

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/application_controller.rb" id="AccessTokenSnippet":::

    Вместо того чтобы просто возвратить маркер из сеанса, он сначала проверяет, является ли срок его действия близким к сроку действия. Если это так, то оно обновится перед возвратом маркера.
