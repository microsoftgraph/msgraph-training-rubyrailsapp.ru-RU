<!-- markdownlint-disable MD002 MD041 -->

В этом руководстве рассказывается, как создать веб-приложение Ruby on Rails, использующее API Microsoft Graph для получения сведений календаря для пользователя.

> [!TIP]
> Если вы предпочитаете просто скачать завершенный учебник, вы можете скачать его двумя способами.
>
> - Скачайте [быстрое начало работы Ruby,](https://developer.microsoft.com/graph/quick-start?platform=option-ruby) чтобы получить рабочий код в минутах.
> - Скачайте или клонировать [репозиторий GitHub.](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp)

## <a name="prerequisites"></a>Предварительные условия

Перед началом работы с этим учебником на компьютере разработчика должны быть установлены следующие средства.

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [SQLite3](https://sqlite.org/index.html)
- [Node.js](https://nodejs.org/en/)
- [Yarn](https://yarnpkg.com/)

У вас также должна быть личная учетная запись Майкрософт с почтовым Outlook.com или учетная запись Майкрософт для работы или учебного заведения. Если у вас нет учетной записи Майкрософт, существует несколько вариантов получения бесплатной учетной записи:

- Вы можете [зарегистрироваться для новой личной учетной записи Майкрософт.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)
- Вы можете зарегистрироваться в программе для разработчиков [Office 365,](https://developer.microsoft.com/office/dev-program) чтобы получить бесплатную подписку на Office 365.

> [!NOTE]
> Это руководство было написано со следующими версиями необходимых средств. Действия в этом руководстве могут работать с другими версиями, но не были протестированы.
>
> - Ruby версии 2.6.6.
> - SQLite3 версии 3.31.1
> - Node.js версии 14.15.0
> - Yarn версии 1.22.0

## <a name="feedback"></a>Отзывы

Поделитесь с этим учебником отзывами в [репозитории GitHub.](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp)
