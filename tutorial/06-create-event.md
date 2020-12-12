<!-- markdownlint-disable MD002 MD041 -->

В этом разделе вы добавим возможность создания событий в календаре пользователя.

1. Откройте **./app/helpers/graph_helper.rb** и добавьте следующий метод в **класс Graph.**

    :::code language="ruby" source="../demo/graph-tutorial/app/helpers/graph_helper.rb" id="CreateEventSnippet":::

1. Откройте **./app/controllers/calendar_controller** и добавьте следующий маршрут в класс **CalendarController.**

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/calendar_controller.rb" id="CreateEventRouteSnippet":::

1. Откройте **./config/routes.rb** и добавьте новый маршрут.

    ```ruby
    post 'calendar/new', :to => 'calendar#create'
    ```

1. Откройте **./app/views/calendar/new.html.erb** и замените его содержимое на следующее.

    :::code language="html" source="../demo/graph-tutorial/app/views/calendar/new.html.erb" id="NewEventFormSnippet":::

1. Сохраните изменения и обновите приложение. На странице **"Календарь"** выберите кнопку **"Новое** событие". Заполните форму и выберите **"Создать",** чтобы создать новое событие.

    ![Снимок экрана с новой формой события](images/create-event-01.png)
