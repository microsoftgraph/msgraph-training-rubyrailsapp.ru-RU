<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="06610-101">В этом руководстве рассказывается, как создать веб-приложение Ruby on Rails, использующее API Microsoft Graph для получения сведений календаря для пользователя.</span><span class="sxs-lookup"><span data-stu-id="06610-101">This tutorial teaches you how to build a Ruby on Rails web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="06610-102">Если вы предпочитаете просто скачать завершенный учебник, вы можете скачать его двумя способами.</span><span class="sxs-lookup"><span data-stu-id="06610-102">If you prefer to just download the completed tutorial, you can download it in two ways.</span></span>
>
> - <span data-ttu-id="06610-103">Скачайте [быстрое начало работы Ruby,](https://developer.microsoft.com/graph/quick-start?platform=option-ruby) чтобы получить рабочий код в минутах.</span><span class="sxs-lookup"><span data-stu-id="06610-103">Download the [Ruby quick start](https://developer.microsoft.com/graph/quick-start?platform=option-ruby) to get working code in minutes.</span></span>
> - <span data-ttu-id="06610-104">Скачайте или клонировать [репозиторий GitHub.](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp)</span><span class="sxs-lookup"><span data-stu-id="06610-104">Download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="06610-105">Предварительные условия</span><span class="sxs-lookup"><span data-stu-id="06610-105">Prerequisites</span></span>

<span data-ttu-id="06610-106">Перед началом работы с этим учебником на компьютере разработчика должны быть установлены следующие средства.</span><span class="sxs-lookup"><span data-stu-id="06610-106">Before you start this tutorial, you should have the following tools installed on your development machine.</span></span>

- [<span data-ttu-id="06610-107">Ruby</span><span class="sxs-lookup"><span data-stu-id="06610-107">Ruby</span></span>](https://www.ruby-lang.org/en/downloads/)
- [<span data-ttu-id="06610-108">SQLite3</span><span class="sxs-lookup"><span data-stu-id="06610-108">SQLite3</span></span>](https://sqlite.org/index.html)
- [<span data-ttu-id="06610-109">Node.js</span><span class="sxs-lookup"><span data-stu-id="06610-109">Node.js</span></span>](https://nodejs.org/en/)
- [<span data-ttu-id="06610-110">Yarn</span><span class="sxs-lookup"><span data-stu-id="06610-110">Yarn</span></span>](https://yarnpkg.com/)

<span data-ttu-id="06610-111">У вас также должна быть личная учетная запись Майкрософт с почтовым Outlook.com или учетная запись Майкрософт для работы или учебного заведения.</span><span class="sxs-lookup"><span data-stu-id="06610-111">You should also have either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span> <span data-ttu-id="06610-112">Если у вас нет учетной записи Майкрософт, существует несколько вариантов получения бесплатной учетной записи:</span><span class="sxs-lookup"><span data-stu-id="06610-112">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="06610-113">Вы можете [зарегистрироваться для новой личной учетной записи Майкрософт.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)</span><span class="sxs-lookup"><span data-stu-id="06610-113">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="06610-114">Вы можете зарегистрироваться в программе для разработчиков [Office 365,](https://developer.microsoft.com/office/dev-program) чтобы получить бесплатную подписку на Office 365.</span><span class="sxs-lookup"><span data-stu-id="06610-114">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

> [!NOTE]
> <span data-ttu-id="06610-115">Это руководство было написано со следующими версиями необходимых средств.</span><span class="sxs-lookup"><span data-stu-id="06610-115">This tutorial was written with the following versions of the required tools.</span></span> <span data-ttu-id="06610-116">Действия в этом руководстве могут работать с другими версиями, но не были протестированы.</span><span class="sxs-lookup"><span data-stu-id="06610-116">The steps in this guide may work with other versions, but that has not been tested.</span></span>
>
> - <span data-ttu-id="06610-117">Ruby версии 2.6.6.</span><span class="sxs-lookup"><span data-stu-id="06610-117">Ruby version 2.6.6.</span></span>
> - <span data-ttu-id="06610-118">SQLite3 версии 3.31.1</span><span class="sxs-lookup"><span data-stu-id="06610-118">SQLite3 version 3.31.1</span></span>
> - <span data-ttu-id="06610-119">Node.js версии 14.15.0</span><span class="sxs-lookup"><span data-stu-id="06610-119">Node.js version 14.15.0</span></span>
> - <span data-ttu-id="06610-120">Yarn версии 1.22.0</span><span class="sxs-lookup"><span data-stu-id="06610-120">Yarn version 1.22.0</span></span>

## <a name="feedback"></a><span data-ttu-id="06610-121">Отзывы</span><span class="sxs-lookup"><span data-stu-id="06610-121">Feedback</span></span>

<span data-ttu-id="06610-122">Поделитесь с этим учебником отзывами в [репозитории GitHub.](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp)</span><span class="sxs-lookup"><span data-stu-id="06610-122">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp).</span></span>
