<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="edd11-101">В этом руководстве рассказывается, как создать веб-приложение Ruby on Салазокs, которое использует API Microsoft Graph для получения сведений о календаре для пользователя.</span><span class="sxs-lookup"><span data-stu-id="edd11-101">This tutorial teaches you how to build a Ruby on Rails web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="edd11-102">Если вы хотите просто скачать заполненный учебник, можно скачать его двумя способами.</span><span class="sxs-lookup"><span data-stu-id="edd11-102">If you prefer to just download the completed tutorial, you can download it in two ways.</span></span>
>
> - <span data-ttu-id="edd11-103">Скачайте [Краткое руководство Ruby](https://developer.microsoft.com/graph/quick-start?platform=option-ruby) , чтобы получить рабочий код в минутах.</span><span class="sxs-lookup"><span data-stu-id="edd11-103">Download the [Ruby quick start](https://developer.microsoft.com/graph/quick-start?platform=option-ruby) to get working code in minutes.</span></span>
> - <span data-ttu-id="edd11-104">Скачайте или скопируйте [репозиторий GitHub](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp).</span><span class="sxs-lookup"><span data-stu-id="edd11-104">Download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="edd11-105">Необходимые компоненты</span><span class="sxs-lookup"><span data-stu-id="edd11-105">Prerequisites</span></span>

<span data-ttu-id="edd11-106">Прежде чем приступить к работе с этим руководством, на компьютере для разработки должен быть установлен [Ruby](https://www.ruby-lang.org/en/downloads/) .</span><span class="sxs-lookup"><span data-stu-id="edd11-106">Before you start this tutorial, you should have [Ruby](https://www.ruby-lang.org/en/downloads/) installed on your development machine.</span></span> <span data-ttu-id="edd11-107">Если вы не используете транскрипцию, перейдите к предыдущей ссылке для получения вариантов загрузки.</span><span class="sxs-lookup"><span data-stu-id="edd11-107">If you do not have Ruby, visit the previous link for download options.</span></span>

> [!NOTE]
> <span data-ttu-id="edd11-108">Это руководство было написано с помощью Ruby версии 2.4.4.</span><span class="sxs-lookup"><span data-stu-id="edd11-108">This tutorial was written with Ruby version 2.4.4.</span></span> <span data-ttu-id="edd11-109">Действия, описанные в этом руководстве, могут работать с другими версиями, но не тестировались.</span><span class="sxs-lookup"><span data-stu-id="edd11-109">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="edd11-110">Отзывы</span><span class="sxs-lookup"><span data-stu-id="edd11-110">Feedback</span></span>

<span data-ttu-id="edd11-111">Сообщите о нем в [репозиторий GitHub](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp).</span><span class="sxs-lookup"><span data-stu-id="edd11-111">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp).</span></span>