<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e52a6-101">В этом разделе вы добавим возможность создания событий в календаре пользователя.</span><span class="sxs-lookup"><span data-stu-id="e52a6-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="e52a6-102">Откройте **./app/helpers/graph_helper.rb** и добавьте следующий метод в **класс Graph.**</span><span class="sxs-lookup"><span data-stu-id="e52a6-102">Open **./app/helpers/graph_helper.rb** and add the following method to the **Graph** class.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/helpers/graph_helper.rb" id="CreateEventSnippet":::

1. <span data-ttu-id="e52a6-103">Откройте **./app/controllers/calendar_controller** и добавьте следующий маршрут в класс **CalendarController.**</span><span class="sxs-lookup"><span data-stu-id="e52a6-103">Open **./app/controllers/calendar_controller** and add the following route to the **CalendarController** class.</span></span>

    :::code language="ruby" source="../demo/graph-tutorial/app/controllers/calendar_controller.rb" id="CreateEventRouteSnippet":::

1. <span data-ttu-id="e52a6-104">Откройте **./config/routes.rb** и добавьте новый маршрут.</span><span class="sxs-lookup"><span data-stu-id="e52a6-104">Open **./config/routes.rb** and add the new route.</span></span>

    ```ruby
    post 'calendar/new', :to => 'calendar#create'
    ```

1. <span data-ttu-id="e52a6-105">Откройте **./app/views/calendar/new.html.erb** и замените его содержимое на следующее.</span><span class="sxs-lookup"><span data-stu-id="e52a6-105">Open **./app/views/calendar/new.html.erb** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/app/views/calendar/new.html.erb" id="NewEventFormSnippet":::

1. <span data-ttu-id="e52a6-106">Сохраните изменения и обновите приложение.</span><span class="sxs-lookup"><span data-stu-id="e52a6-106">Save your changes and refresh the app.</span></span> <span data-ttu-id="e52a6-107">На странице **"Календарь"** выберите кнопку **"Новое** событие".</span><span class="sxs-lookup"><span data-stu-id="e52a6-107">On the **Calendar** page, select the **New event** button.</span></span> <span data-ttu-id="e52a6-108">Заполните форму и выберите **"Создать",** чтобы создать новое событие.</span><span class="sxs-lookup"><span data-stu-id="e52a6-108">Fill in the form and select **Create** to create a new event.</span></span>

    ![Снимок экрана с новой формой события](images/create-event-01.png)
