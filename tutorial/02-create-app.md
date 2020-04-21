<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете использовать для создания веб-приложения [транскрипцию на шинах](https://rubyonrails.org/) .

1. Если вы еще не установили салазки, вы можете установить его из интерфейса командной строки (CLI) с помощью следующей команды.

    ```Shell
    gem install rails -v 6.0.2.2
    ```

1. Откройте подсистему CLI, перейдите к каталогу, в котором у вас есть права на создание файлов, и выполните следующую команду, чтобы создать новое приложение с ограждениями.

    ```Shell
    rails new graph-tutorial
    ```

1. Перейдите к новому каталогу и введите следующую команду для запуска локального веб-сервера.

    ```Shell
    rails server
    ```

1. Откройте браузер и перейдите по адресу `http://localhost:3000`. Если все работает, вы увидите "Яи! Вы находитесь на салазках! " Сообщение. Если вы не видите это сообщение, ознакомьтесь с [руководством по началу работы с салазками](http://guides.rubyonrails.org/).

## <a name="install-gems"></a>Установка драгоценных камней

Прежде чем переходить, установите несколько дополнительных драгоценных камней, которые будут использоваться позже:

- [omniauth — OAuth2](https://github.com/omniauth/omniauth-oauth2) для обработки входных потоков и маркеров OAuth.
- [omniauth — rails_csrf_protection](https://github.com/cookpad/omniauth-rails_csrf_protection) для добавления защиты Ксрф в omniauth.
- [хттпарти](https://github.com/jnunemaker/httparty) для совершения вызовов в Microsoft Graph.
- [активерекорд — session_store](https://github.com/rails/activerecord-session_store) для хранения сеансов в базе данных.

1. Откройте **./жемфиле** и добавьте следующие строки.

    :::code language="ruby" source="../demo/graph-tutorial/Gemfile" id="GemFileSnippet":::

1. В интерфейсе командной строки выполните следующую команду.

    ```Shell
    bundle install
    ```

1. В интерфейсе командной строки выполните следующие команды, чтобы настроить базу данных для хранения сеансов.

    ```Shell
    rails generate active_record:session_migration
    rake db:migrate
    ```

1. Создайте новый файл, вызываемый `session_store.rb` в каталоге **./конфиг/инитиализерс** , и добавьте следующий код.

    :::code language="ruby" source="../demo/graph-tutorial/config/initializers/session_store.rb" id="SessionStoreSnippet":::

## <a name="design-the-app"></a>Проектирование приложения

В этом разделе вы создадите базовый пользовательский интерфейс для приложения.

1. Откройте **/АПП/виевс/лайаутс/аппликатион.хтмл.ЕРБ** и замените его содержимое приведенным ниже.

    :::code language="html" source="../demo/graph-tutorial/app/views/layouts/application.html.erb" id="LayoutSnippet":::

    В этом коде добавляется [Начальная](http://getbootstrap.com/) загрузка для простых стилей и [Шрифт Awesome](https://fontawesome.com/) для некоторых простых значков. Он также определяет глобальную структуру с помощью панели навигации.

1. Откройте файл **./АПП/Ассетс/стилешитс/аппликатион.КСС** и добавьте следующий элемент в конец файла.

    :::code language="css" source="../demo/graph-tutorial/app/assets/stylesheets/application.css" id="CssSnippet":::

1. Создайте контроллер домашней страницы с помощью следующей команды.

    ```Shell
    rails generate controller Home index
    ```

1. Настройка `index` действия на `Home` контроллере в качестве страницы по умолчанию для приложения. Откройте **/конфиг/раутес.РБ** и замените его содержимое приведенным ниже

    ```ruby
    Rails.application.routes.draw do
      get 'home/index'
      root 'home#index'

      # Add future routes here

    end
    ```

1. Откройте **/АПП/виев/Хоме/индекс.хтмл.ЕРБ** и замените его содержимое приведенным ниже.

    :::code language="html" source="../demo/graph-tutorial/app/views/home/index.html.erb" id="HomeSnippet":::

1. Сохраните все изменения и перезапустите сервер. Теперь приложение должно выглядеть по-другому.

    ![Снимок экрана с переработанной домашней страницей](./images/create-app-01.png)
