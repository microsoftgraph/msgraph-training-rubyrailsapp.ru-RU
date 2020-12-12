<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете использовать [Ruby on Rails](https://rubyonrails.org/) для создания веб-приложения.

1. Если вы еще не установили Rails, вы можете установить его из интерфейса командной строки (CLI) с помощью следующей команды.

    ```Shell
    gem install rails -v 6.0.3.4
    ```

1. Откройте CLI, перейдите в каталог, в котором у вас есть права на создание файлов, и запустите следующую команду, чтобы создать новое приложение Rails.

    ```Shell
    rails new graph-tutorial
    ```

1. Перейдите в этот новый каталог и введите следующую команду, чтобы запустить локальный веб-сервер.

    ```Shell
    rails server
    ```

1. Откройте браузер и перейдите по адресу `http://localhost:3000`. Если все работает, вы увидите "Yay! You're on Rails!" Сообщение. Если вы не видите это сообщение, проверьте руководство по [началу работы Rails.](http://guides.rubyonrails.org/)

## <a name="install-gems"></a>Установка самосхем

Прежде чем двигаться дальше, установите некоторые дополнительные самосхемы, которые вы будете использовать позже:

- [omniauth-oauth2](https://github.com/omniauth/omniauth-oauth2) для обработки потоков входов и маркеров OAuth.
- [omniauth-rails_csrf_protection](https://github.com/cookpad/omniauth-rails_csrf_protection) для добавления защиты CSRF в OmniAuth.
- [httparty](https://github.com/jnunemaker/httparty) для звонков в Microsoft Graph.
- [activerecord-session_store](https://github.com/rails/activerecord-session_store) для хранения сеансов в базе данных.

1. Откройте **./Gemfile** и добавьте следующие строки.

    :::code language="ruby" source="../demo/graph-tutorial/Gemfile" id="GemFileSnippet":::

1. В CLI запустите следующую команду.

    ```Shell
    bundle install
    ```

1. В CLI запустите следующие команды, чтобы настроить базу данных для хранения сеансов.

    ```Shell
    rails generate active_record:session_migration
    rake db:migrate
    ```

1. Создайте файл, который будет вызван в `session_store.rb` **каталоге ./config/initializers,** и добавьте следующий код.

    :::code language="ruby" source="../demo/graph-tutorial/config/initializers/session_store.rb" id="SessionStoreSnippet":::

## <a name="design-the-app"></a>Проектирование приложения

В этом разделе вы создадим базовый пользовательский интерфейс для приложения.

1. Откройте **./app/views/layouts/application.html.erb** и замените его содержимое на следующее.

    :::code language="html" source="../demo/graph-tutorial/app/views/layouts/application.html.erb" id="LayoutSnippet":::

    Этот код добавляет [Bootstrap](http://getbootstrap.com/) для простого стиля и [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) для некоторых простых значков. Он также определяет глобальный макет с панели nav.

1. Откройте **файл ./app/assets/stylesheets/application.css** и добавьте в конец файла следующее:

    :::code language="css" source="../demo/graph-tutorial/app/assets/stylesheets/application.css" id="CssSnippet":::

1. Создание контроллера домашней страницы с помощью следующей команды.

    ```Shell
    rails generate controller Home index
    ```

1. Настройте действие на контроллере в качестве `index` страницы по умолчанию для `Home` приложения. Откройте **./config/routes.rb** и замените его содержимое на следующее:

    ```ruby
    Rails.application.routes.draw do
      get 'home/index'
      root 'home#index'

      # Add future routes here

    end
    ```

1. Откройте **./app/view/home/index.html.erb** и замените его содержимое на следующее.

    :::code language="html" source="../demo/graph-tutorial/app/views/home/index.html.erb" id="HomeSnippet":::

1. Добавьте PNG-файл с **именемno-profile-photo.png** в **каталог ./app/assets/images.**

1. Сохраните все изменения и перезапустите сервер. Теперь приложение должно выглядеть совершенно иначе.

    ![Снимок экрана с измененной домашней страницей](./images/create-app-01.png)
