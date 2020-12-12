# <a name="contributing-to-microsoft-graph-training-repositories"></a><span data-ttu-id="f2942-101">Участие в учебных репозиториях Microsoft Graph</span><span class="sxs-lookup"><span data-stu-id="f2942-101">Contributing to Microsoft Graph training repositories</span></span>

<span data-ttu-id="f2942-102">Благодарим вас за участие в этом проекте!</span><span class="sxs-lookup"><span data-stu-id="f2942-102">Thank you for contributing to this project!</span></span> <span data-ttu-id="f2942-103">Перед отправкой запроса на запрос на оттягку необходимо учесть следующее.</span><span class="sxs-lookup"><span data-stu-id="f2942-103">Before submitting your pull request, be sure to consider the following.</span></span>

## <a name="overview"></a><span data-ttu-id="f2942-104">Обзор</span><span class="sxs-lookup"><span data-stu-id="f2942-104">Overview</span></span>

<span data-ttu-id="f2942-105">Код в этом репозитории предназначен для трех целей:</span><span class="sxs-lookup"><span data-stu-id="f2942-105">The code in this repository serves three purposes:</span></span>

- <span data-ttu-id="f2942-106">Файлы Markdown в папке [учебника](/tutorial) публикуются в виде учебника на странице учебников [Microsoft Graph.](https://docs.microsoft.com/graph/tutorials)</span><span class="sxs-lookup"><span data-stu-id="f2942-106">The Markdown files in the [tutorial](/tutorial) folder are published as a tutorial on the [Microsoft Graph tutorials](https://docs.microsoft.com/graph/tutorials) page.</span></span>
- <span data-ttu-id="f2942-107">Пример проекта в папке [демонстрации](/demo) является источником краткого [запуска Microsoft Graph.\*](https://developer.microsoft.com/graph/quick-start) *\**</span><span class="sxs-lookup"><span data-stu-id="f2942-107">The sample project in the [demo](/demo) folder is the source for a [Microsoft Graph quick start](https://developer.microsoft.com/graph/quick-start).\**\** _</span></span>
- <span data-ttu-id="f2942-108">Пример проекта в папке демонстрации также можно скачать непосредственно с GitHub и должен запускаться как есть после простой настройки.</span><span class="sxs-lookup"><span data-stu-id="f2942-108">The sample project in the demo folder is also downloadable directly from GitHub and should run as-is after some simple configuration.</span></span>

> <span data-ttu-id="f2942-109">_*\**_ Не все учебные репозитории доступны как краткие (пока).</span><span class="sxs-lookup"><span data-stu-id="f2942-109">_*\**_ Not all training repositories are available as quick starts (yet).</span></span>

<span data-ttu-id="f2942-110">Это важно помнить, так как изменения в одном месте _may\* требуют изменений в другом, чтобы синхронизировать данные. По возможности файлы Markdown ссылаются непосредственно на файлы исходных кодов (с использованием пользовательского синтаксиса), чтобы обновление кода в источнике автоматически обновлялось в `:::code` Markdown.</span><span class="sxs-lookup"><span data-stu-id="f2942-110">This is important to keep in mind, because changes in one place _may\* require changes in another, to keep things in sync. Whereever possible, the Markdown files refer to the source code files directly (using a custom `:::code` syntax), so that updating code in source will automatically update the code in Markdown.</span></span>

## <a name="updating-code"></a><span data-ttu-id="f2942-111">Обновление кода</span><span class="sxs-lookup"><span data-stu-id="f2942-111">Updating code</span></span>

<span data-ttu-id="f2942-112">Синтаксис, используемый в Markdown, зависит от конкретных `:::code` комментариев в файле кода.</span><span class="sxs-lookup"><span data-stu-id="f2942-112">The `:::code` syntax used in Markdown depends on specific comments in the source code file.</span></span> <span data-ttu-id="f2942-113">Эти комментарии выглядят следующим образом:</span><span class="sxs-lookup"><span data-stu-id="f2942-113">These comments look like the following:</span></span>

```csharp
// <MySnippet>
Console.WriteLine("Hello World!");
// </MySnippet>
```

<span data-ttu-id="f2942-114">При обновлении кода между этими комментариями маркера файлы Markdown автоматически получают эти изменения при публикации на сайте документации Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="f2942-114">If you update code between these "marker" comments, the Markdown files will automatically get those changes when published to the Microsoft Graph documentation site.</span></span> <span data-ttu-id="f2942-115">Если код обновляется за пределами этих комментариев, скорее всего, потребуется обновить соответствующий Markdown.</span><span class="sxs-lookup"><span data-stu-id="f2942-115">If you update code outside of those comments, it's very likely that you'll need to update the corresponding Markdown.</span></span>

## <a name="adding-features"></a><span data-ttu-id="f2942-116">Добавление компонентов</span><span class="sxs-lookup"><span data-stu-id="f2942-116">Adding features</span></span>

<span data-ttu-id="f2942-117">Несмотря на то, что вам нравится, не отправляйте запросы на добавление новых функций в пример.</span><span class="sxs-lookup"><span data-stu-id="f2942-117">While the enthusiasm is appreciated, please don't send pull requests to add new features to the sample.</span></span> <span data-ttu-id="f2942-118">Поскольку этот репозиторий является в основном учебником по созданию первого приложения, набор функций ограничен по дизайну.</span><span class="sxs-lookup"><span data-stu-id="f2942-118">Because this repository is primarily a "build your first app" tutorial, the feature set is limited, by design.</span></span>

## <a name="submitting-pull-requests"></a><span data-ttu-id="f2942-119">Отправка запросов на оттягку</span><span class="sxs-lookup"><span data-stu-id="f2942-119">Submitting pull requests</span></span>

<span data-ttu-id="f2942-120">Отправьте все запросы на оттягивать в `master` филиал.</span><span class="sxs-lookup"><span data-stu-id="f2942-120">Please submit all pull requests to the `master` branch.</span></span>

## <a name="when-do-changes-get-published"></a><span data-ttu-id="f2942-121">Когда публикуются изменения?</span><span class="sxs-lookup"><span data-stu-id="f2942-121">When do changes get published?</span></span>

<span data-ttu-id="f2942-122">Публикация обновлений на сайте учебников [Microsoft Graph](https://docs.microsoft.com/graph/tutorials) не является автоматической.</span><span class="sxs-lookup"><span data-stu-id="f2942-122">Publishing of updates to the [Microsoft Graph tutorials](https://docs.microsoft.com/graph/tutorials) site is not automatic.</span></span> <span data-ttu-id="f2942-123">Изменения сначала должны быть повышены до ветви, а затем сборка должна быть инициирована `live` администраторами сайта.</span><span class="sxs-lookup"><span data-stu-id="f2942-123">Changes must first be promoted to the `live` branch, then a build must be triggered by the site admins.</span></span> <span data-ttu-id="f2942-124">Обычно это делается "по мере необходимости".</span><span class="sxs-lookup"><span data-stu-id="f2942-124">This is typically done on an "as-needed" basis.</span></span>

## <a name="code-of-conduct"></a><span data-ttu-id="f2942-125">Правила поведения</span><span class="sxs-lookup"><span data-stu-id="f2942-125">Code of conduct</span></span>

<span data-ttu-id="f2942-p106">Этот проект соответствует [правилам поведения Майкрософт, касающимся обращения с открытым кодом](https://opensource.microsoft.com/codeofconduct/). Читайте дополнительные сведения в [разделе вопросов и ответов по правилам поведения](https://opensource.microsoft.com/codeofconduct/faq/) или отправляйте новые вопросы и замечания по адресу [opencode@microsoft.com](mailto:opencode@microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="f2942-p106">This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.</span></span>
