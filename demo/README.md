# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="33682-101">Выполнение завершенного проекта</span><span class="sxs-lookup"><span data-stu-id="33682-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="33682-102">Необходимые компоненты</span><span class="sxs-lookup"><span data-stu-id="33682-102">Prerequisites</span></span>

<span data-ttu-id="33682-103">Чтобы запустить завершенный проект в этой папке, вам потребуются следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="33682-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="33682-104">Установленный на компьютере разработчика [Ruby](https://www.ruby-lang.org/en/downloads/) .</span><span class="sxs-lookup"><span data-stu-id="33682-104">[Ruby](https://www.ruby-lang.org/en/downloads/) installed on your development machine.</span></span> <span data-ttu-id="33682-105">Если вы не используете транскрипцию, перейдите к предыдущей ссылке для получения вариантов загрузки.</span><span class="sxs-lookup"><span data-stu-id="33682-105">If you do not have Ruby, visit the previous link for download options.</span></span> <span data-ttu-id="33682-106">(**Примечание:** это руководство написано с помощью Ruby версии 2.6.5.</span><span class="sxs-lookup"><span data-stu-id="33682-106">(**Note:** This tutorial was written with Ruby version 2.6.5.</span></span> <span data-ttu-id="33682-107">Действия, описанные в этом руководстве, могут работать с другими версиями, но не тестировались.</span><span class="sxs-lookup"><span data-stu-id="33682-107">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="33682-108">Личная учетная запись Майкрософт с почтовым ящиком на Outlook.com или рабочей или учебной учетной записью Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="33682-108">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="33682-109">Если у вас нет учетной записи Майкрософт, у вас есть несколько вариантов для получения бесплатной учетной записи:</span><span class="sxs-lookup"><span data-stu-id="33682-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="33682-110">Вы можете [зарегистрироваться для создания новой личной учетной записи Майкрософт](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="33682-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="33682-111">Вы можете [зарегистрироваться в программе для разработчиков office 365](https://developer.microsoft.com/office/dev-program) , чтобы получить бесплатную подписку на Office 365.</span><span class="sxs-lookup"><span data-stu-id="33682-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="33682-112">Регистрация веб-приложения с помощью центра администрирования Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="33682-112">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="33682-113">Откройте браузер и перейдите к [Центру администрирования Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="33682-113">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="33682-114">Войдите с помощью **личной учетной записи** (т.е. учетной записи Microsoft) или **рабочей (учебной) учетной записи**.</span><span class="sxs-lookup"><span data-stu-id="33682-114">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="33682-115">Выберите **Azure Active Directory** на панели навигации слева, затем выберите **Регистрация приложений** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="33682-115">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="33682-116">Снимок экрана с регистрациями приложений</span><span class="sxs-lookup"><span data-stu-id="33682-116">A screenshot of the App registrations</span></span> ](/tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="33682-117">Выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="33682-117">Select **New registration**.</span></span> <span data-ttu-id="33682-118">На странице**Зарегистрировать приложение** задайте необходимые значения следующим образом.</span><span class="sxs-lookup"><span data-stu-id="33682-118">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="33682-119">Введите **имя** `Ruby Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="33682-119">Set **Name** to `Ruby Graph Tutorial`.</span></span>
    - <span data-ttu-id="33682-120">Введите **поддерживаемые типы учетных записей** для **учетных записей в любом каталоге организаций и личных учетных записей Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="33682-120">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="33682-121">В разделе **URI адрес перенаправления** введите значение в первом раскрывающемся списке `Web` и задайте значение `http://localhost:3000/auth/microsoft_graph_auth/callback`.</span><span class="sxs-lookup"><span data-stu-id="33682-121">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `http://localhost:3000/auth/microsoft_graph_auth/callback`.</span></span>

    ![Снимок страницы "регистрация приложения"](/tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="33682-123">Нажмите кнопку **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="33682-123">Choose **Register**.</span></span> <span data-ttu-id="33682-124">На странице **Tutorial "Диаграмма Ruby** " СКОПИРУЙТЕ значение **идентификатора Application (Client)** и сохраните его, он понадобится на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="33682-124">On the **Ruby Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Снимок экрана с ИДЕНТИФИКАТОРом приложения для новой регистрации приложения](/tutorial/images/aad-application-id.png)

1. <span data-ttu-id="33682-126">Выберите **Сертификаты и секреты** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="33682-126">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="33682-127">Нажмите кнопку **Новый секрет клиента**.</span><span class="sxs-lookup"><span data-stu-id="33682-127">Select the **New client secret** button.</span></span> <span data-ttu-id="33682-128">Введите значение в **Описание** и выберите один из вариантов для **Срок действия** и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="33682-128">Enter a value in **Description** and select one of the options for **Expires** and choose **Add**.</span></span>

    ![Снимок экрана: диалоговое окно добавления секрета клиента](/tutorial/images/aad-new-client-secret.png)

1. <span data-ttu-id="33682-130">Скопируйте значение секрета клиента, а затем покиньте эту страницу.</span><span class="sxs-lookup"><span data-stu-id="33682-130">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="33682-131">Оно вам понадобится на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="33682-131">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="33682-132">Это секрет клиента, он никогда не отображается еще раз, поэтому убедитесь, что вы скопировали его.</span><span class="sxs-lookup"><span data-stu-id="33682-132">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Снимок экрана с недавно добавленным секретом клиента](/tutorial/images/aad-copy-client-secret.png)

## <a name="configure-the-sample"></a><span data-ttu-id="33682-134">Настройка примера</span><span class="sxs-lookup"><span data-stu-id="33682-134">Configure the sample</span></span>

1. <span data-ttu-id="33682-135">Переименуйте `./config/oauth_environment_variables.rb.example` файл в `oauth_environment_variables.rb`.</span><span class="sxs-lookup"><span data-stu-id="33682-135">Rename the `./config/oauth_environment_variables.rb.example` file to `oauth_environment_variables.rb`.</span></span>
1. <span data-ttu-id="33682-136">Измените `oauth_environment_variables.rb` файл и внесите следующие изменения.</span><span class="sxs-lookup"><span data-stu-id="33682-136">Edit the `oauth_environment_variables.rb` file and make the following changes.</span></span>
    1. <span data-ttu-id="33682-137">Замените `YOUR_APP_ID_HERE` **идентификатором приложения** , полученным на портале регистрации приложений.</span><span class="sxs-lookup"><span data-stu-id="33682-137">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
    1. <span data-ttu-id="33682-138">Замените `YOUR APP PASSWORD HERE` паролем, полученным на портале регистрации приложений.</span><span class="sxs-lookup"><span data-stu-id="33682-138">Replace `YOUR APP PASSWORD HERE` with the password you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="33682-139">В интерфейсе командной строки (CLI) перейдите к этому каталогу и выполните следующую команду, чтобы установить требования.</span><span class="sxs-lookup"><span data-stu-id="33682-139">In your command-line interface (CLI), navigate to this directory and run the following command to install requirements.</span></span>

    ```Shell
    bundle install
    ```

1. <span data-ttu-id="33682-140">В интерфейсе командной строки выполните следующую команду, чтобы инициализировать базу данных приложения.</span><span class="sxs-lookup"><span data-stu-id="33682-140">In your CLI, run the following command to initialize the app's database.</span></span>

    ```Shell
    rake db:migrate
    ```

## <a name="run-the-sample"></a><span data-ttu-id="33682-141">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="33682-141">Run the sample</span></span>

1. <span data-ttu-id="33682-142">Выполните следующую команду в командной панели CLI, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="33682-142">Run the following command in your CLI to start the application.</span></span>

    ```Shell
    rails server
    ```

1. <span data-ttu-id="33682-143">Откройте браузер и перейдите по адресу `http://localhost:3000`.</span><span class="sxs-lookup"><span data-stu-id="33682-143">Open a browser and browse to `http://localhost:3000`.</span></span>
