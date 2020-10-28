- Проблемы большого фронтенда:
  - фронтенд большой, и несколько команд его пилят
  - разделение ответственности (кто за что отвечает)
  - долгие часы CI/CD и интеграционных тестов, чтоб собрать монолитный фронтенд
  - А как было здорово лет 5 тому назад:
    - никаких билдов, влепил jquery плагин на страницу и поехали
    - сейчас с билдами боишься лишний скрипт подключить. Такое ощущение, что атрофировалось способность что-то подключать руками.
  - Принцип: Разделяй и властвую
- Что такое микрофронтенды?
  - Сперва взглянем на бекендерский аналог – microservices
  - Микросервисы это широко распространенный архитектурный паттерн для разработки бэкендов. Микросервисные системы объединяют небольшие независимо разворачиваемые сервисы, которые запускаются в разных процессах (машинах) и комуницируют по сети.
  - Микросервисы преследуют низкую связность компонентов системы между собой, что дает высокую автономность командам:
    - разделенные кодовой базы и ответственности
    - независимое развертывание
    - лучшая ориентация на клиента
  - Microfrontends – аналогичный архитектурный паттерн для фронтенда
    - Как и микросервисы, они нацелены на обеспечение высокой автономии команд
    - Но зрелость методов и технологий гораздо ниже, чем у микросервисов.
  - Микрофронтенды – это архитектурный стиль, в котором независимо доставляемые клиентские компоненты в браузер объединяются в единое целое.
  - A micro-frontend represents a business domain that is autonomous, is independently deliverable, and is owned by a team.
  - Microfrontends are modelled to match organization structure.
- Что дают микрофронтенды?
  - Упрощение координации задач - команды более сфокусированы на своих предметных областях, четкая зона ответственности.
  - Возможность независимого развертывания - позволяет командам быть более автономными
  - Сокращение цикла поставки - более быстрая сборка и тесты. Как нового функционала, так и исправления ошибок.
  - Снижение сложности - отдельные части меньше и легче для понимания, чем большой сложный монолит.
  - Изоляция ошибок - может быть проще изолировать сбои в отдельных частях приложения, пока остальная часть приложения работает.
  - Фокус на предметной области и эффективность
- Технические требования к микрофронтендам
  - автономность команд и независимый деплой микрофронтендов (наиболее важный аспект)
    - Проблемы, которые решают микрофронтенды, в основном являются организационными, а не техническими. Такая проблема - это способность разных частей организации действовать независимо. Иметь возможность выпускать свой код в продакшен без одобрения остальных.
  - сокращение времени сборки
    - На сборку и автотесты больших интерфейсных приложений могут уходить часы, что значительно удлиняет цикл обратной связи с разработчиками. Разделив приложение на более мелкие части, мы можем радикально ускорить сборку и тесты.
  - переиспользование общего кода
    - для фронтенда важным ограничением является объем данных загруженных по сети, и чем он меньше тем лучше. Частая болячка микрофронтендов дублирование библиотек и кода фреймворков в их бандлах. В связи с этим мы максимально должны стараться переиспользовать общие модули и компоненты. Крайне важно придерживаться semantic versioning – BREAKING.FEATURE.FIX. И иметь механизм в микрофронтендах для анонсирования необходимых версий модулей и их загрузку в рантайме.
    - Болячки микрофронтендов
  - безопасное выполнение микрофронтендов
    - Микрофронтендов выполняются в браузере и работают в одном потоке. Модель DOM, которую можно рассматривать как замену хранилища данных, является общей, что означает, что они обращаются к одному и тому же хранилищу данных и изменяют его. И ошибка в одном микрофронтенде, может положить все клиентское приложение.
  - SSR – возможность генерации HTML-код страницы для поисковиков
  - Возможность управлять одним микрофронтендом из другого – к примеру, в sidebar'e показывать пункты меню, которые задаются в другом микросервисе
- Существующие решения
  - Server-Side Fragment Composition (на сервере)
    - Также известно под словами transclusion, Server Side Includes. Применяется на бэкенде. Когда веб-сервер собирает html-страницу из разных кусков (сервисов) в единую html страницу. Очень древняя технология.
    - Nginx SSI
    - Zalando Tailor – is a layout service that uses streams to compose a web page from fragment services.
    - слабо-применима к современным SPA-приложениям
  - Iframes (на клиенте)
    - transclusion который работает на клиенте. Который позволяет собирать html страницу из других html страниц. Появился как стандарт в HTML 4.01 (1998 год).
    - zoid (ифреймы могут общаться через postMessage)
    - боль с модальными окнами, и управлением лейаута
    - проблема с SEO для поисковиков
    - проблема с перформансом (одни и те же библиотеки загружаются несколько раз)
  - Web Components (на клиенте)
    - Гораздо более новым веб-стандартом являются веб-компоненты (2011-2013). Они позволяют определять и регистрировать динамические настраиваемые элементы в инкапсулированной области.
    - Самая большая проблема с SSR, т.к web components сильно завязаны на DOM API
    - Поможет инкапсулировать компонент, но не поможет сделать большое SPA приложение
    - По мне чутка поумневший iframe не более того.
  - next.js Multi Zones (на сервере) (Linked Pages & SPAs)
    - Подход при котором load-балансировщик согласно адреса страницы отдает то или иное SPA-приложение.
    - При таком подходе переход между приложениями будет "дорогим" для пользователя. Но роутинг внутри SPA-приложения будет дешевым и быстрым. Так же разработчики вольны выбирать любые средства разработки, для них разработка практически никак не усложняется.
  - single-spa (на клиенте)
    - Один из самых популярных фреймворков на данный момент для SPA. Это приложение-оболочка, которое включает приложения, разработанные с использованием других фреймворков. Он ведет себя как тонкий слой оркестровки, который согласно URL запускает тот или иной микрофронтенд, "выключая" предыдущий.
    - погружаемся в systemjs и мапперы, что-то костылим с подгрузкой ассетов css, fonts, images
    - проблема с переиспользованием кода, хотя lazy loading по роуту страницы позволяет сократить первоначальную загрузку
  - Module Federation
    - Новая киллер фича в Webpack 5. Анонсирована Zack Jackson в [декабре 2019 года](https://levelup.gitconnected.com/micro-frontend-architecture-dynamic-import-chunks-from-another-webpack-bundle-at-runtime-1132d8cb6051). Позволяет подключать модули из другой webpack-сборки, которая расположена на другом хосте. Если в systemjs вы вынуждены вручную собирать import maps, то с Module Federation это происходит автоматически под капотом. 
  - свой велосипед
    - обычно дорого – R&D, тесты, документация, bus factor
    - нужно опенсорсить, чтоб сократить косты на R&D
    - хорошо если на systemjs, еще лучше если на Module Federation
  - Таблица соответствия подходов тех. требованиям
- Module Federation
  - Zack Jackson author of module federation (ссылка на твиттер, ссылка на сайт).
  - Анонсирована в декабре 2019 как сторонний плагин; зарелизили в октябре 2020 как core-плагин
  - Webpack plugin that imports chunks from other Webpack bundles at runtime. To put it plainly, I want to merge two Webpack manifests at runtime and have them work together as if it was compiled as one SPA from the start.
  - Many webpack builds to act as one when in the browser, without context as build time
  - Все что может сбандлить Webpack (css, images, fonts) может быть зашарено между микрофронтендами.
  - Отдельные единицы развертывания могут иметь общие зависимости и зависеть друг от друга. Композиция выполняется во время выполнения, что облегчает независимое развертывание.
  - Объединенные модули могут иметь большое влияние на микрофронтенды, поскольку они позволяют разработчикам писать приложения, как если бы они были монолитными, тогда как на практике они распределяются по нескольким проектам.
  - Module federation нацелен на то, чтобы микрофронты могли легко совместно использовать общий код. Дублирование кода предотвращается за счет упрощения совместного использования.
  - module federation в webpack 5 (вот она рыба моей мечты)
  - The goals
    - No page refreshes when routing to another MFE, multiple apps should route as one SPA would
    - Don’t redownload vendor code that is already provided by another Webpack build on the page. (Don’t bundle multiple copies of the same dependency)
    - Each MFE should be completely standalone and have no centralized dependency. I don’t want to share code by managing Webpack externals, or commons vendor chunks.
    - Frontend resources should have the ability to be evergreen, not requiring a consumer to re-install.
    - I should not need to redeploy the whole fleet because of a change to a shared component or something managed by another team (such as navigation, I don’t want to redeploy my all whenever they push a new update)
    - Orchestration should be completely managed in user-land, allowing dynamic adaptations based on what JavaScript bundles are loaded on a page. There should be no remote logic or calls required beyond adding static JavaScript like the bundles themselves.
- Демо
  - как работает (неглубоко без кишков)
  - кто реализует и можно ли доверять
  - где посмотреть больше демок
  - будут ли проблемы со сборкой, хэшами, ошибки в рантайме
- Стратегии использования Module Federation
  - State management. У каждого приложения должно быть своим. Глобальный стейт будет нарушать инкапсуляцию микросервисов. Если два микрофронтенда имеют много чего общего в стейте – скорее всего их следует объединить в один микрофронтенд.
  - тонкий шелл (уровне интеграции). Всё что является общим для многих микрофронтендов, можно разместить на уровне интеграции. Например, хедер, футер меню.
  - толстый шелл
  - и еще 100500 других
- Минусы Module Federation
  - это вебпак (ссылку на твит про ангела и демона) Ежики плачут, но продолжают есть кактус.
  - все еще новая технология. Но точно будет развиваться и набирать популярность.
  - не все паттерны и стратегии еще выработаны. Нам дали мяч, а вот с правилами игры пока не все ясно. Ждем адаптацию фреймворков, таких как next.js
  - SSR есть, но он tricky и я до него еще не добрался
- Что дальше:
  - React 17
  - Ждем Next.js 11, но уже сейчас можно клеить микрофронтенды.
  - Выход с докладом на весну 2021: apollo federation, webpack module federation, russian federation – как сделать так, чтобы все федерации работали как часы?!