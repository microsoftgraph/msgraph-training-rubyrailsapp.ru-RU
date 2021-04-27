<!-- markdownlint-disable MD002 MD041 -->

В этом руководстве рассказывается о создании веб-приложения Ruby on Rails, которое использует API microsoft Graph для получения сведений о календаре для пользователя.

> [!TIP]
> Если вы предпочитаете просто скачать завершенный учебник, вы можете скачать его двумя способами.
>
> - Скачайте [быстрый запуск Ruby,](https://developer.microsoft.com/graph/quick-start?platform=option-ruby) чтобы получить рабочий код за несколько минут.
> - Скачайте или [клонировать GitHub репозиторий](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp).

## <a name="prerequisites"></a>Необходимые условия

Перед началом этого учебного пособия на компьютере разработки должны быть установлены следующие средства.

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [SQLite3](https://sqlite.org/index.html)
- [Node.js](https://nodejs.org/en/)
- [Пряжа](https://yarnpkg.com/)

Вы также должны иметь личную учетную запись Майкрософт с почтовым ящиком на Outlook.com или учетную запись Microsoft work или school. Если у вас нет учетной записи Майкрософт, существует несколько вариантов получения бесплатной учетной записи:

- Вы можете [зарегистрироваться для новой личной учетной записи Майкрософт.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)
- Вы можете [зарегистрироваться в программе Office 365 разработчика,](https://developer.microsoft.com/office/dev-program) чтобы получить бесплатную Office 365 подписку.

> [!NOTE]
> Этот учебник был написан со следующими версиями необходимых инструментов. Действия в этом руководстве могут работать с другими версиями, но они не были проверены.
>
> - Версия Ruby 3.0.1
> - SQLite3 версии 3.35.5
> - Node.js версии 14.15.0
> - Пряжа версии 1.22.0

## <a name="feedback"></a>Отзывы

В репозитории [](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp)GitHub.
