# Установка

- [Познакомьтесь с Laravel](#meet-laravel)
    - [Почему именно Laravel?](#why-laravel)
- [Ваш первый проект на Laravel](#your-first-laravel-project)
    - [Начало работы в macOS](#getting-started-on-macos)
    - [Начало работы в Windows](#getting-started-on-windows)
    - [Начало работы в Linux](#getting-started-on-linux)
    - [Выберите свой Sail сервис](#choosing-your-sail-services)
    - [Установка через Composer](#installation-via-composer)
- [Начальная конфигурация](#initial-configuration)
    - [Конфигурация на основе среды](#environment-based-configuration)
    - [Конфигурация каталога](#directory-configuration)
- [Следующие шаги](#next-steps)
    - [Laravel - фреймворк фулл стек](#laravel-the-fullstack-framework)
    - [Laravel как бэкэнд API](#laravel-the-api-backend)

<a name="meet-laravel"></a>
## Познакомьтесь с Laravel

Laravel - это среда веб-приложений с выразительным элегантным синтаксисом. Веб-фреймворк обеспечивает структуру и отправную точку для создания Вашего приложения, позволяя Вам сосредоточиться на создании чего-то удивительного, пока мы не будем вдаваться в детали.

Laravel стремится обеспечить потрясающий опыт разработчика, предоставляя мощные функции, такие как тщательное внедрение зависимостей, выразительный уровень абстракции базы данных, очереди и запланированные задания, модульное и интеграционное тестирование и многое другое.

Если Вы новичок в PHP или веб-фреймворках или имеете многолетний опыт, Laravel - это фреймворк, который может расти вместе с Вами. Мы поможем Вам сделать первые шаги в качестве веб-разработчика или подскажем, как Вы выведете свой опыт на новый уровень. Нам не терпится увидеть, что Вы построите.

<a name="why-laravel"></a>
### Почему именно Laravel?

При создании веб-приложения Вам доступны различные инструменты и платформы. Однако мы считаем, что Laravel - лучший выбор для создания современных полнофункциональных веб-приложений.

#### Прогрессивный фреймворк

Нам нравится называть Laravel «прогрессивным» фреймворком. Под этим мы подразумеваем, что Laravel растет вместе с Вами. Если Вы только делаете первые шаги в веб-разработке, обширная библиотека документации, руководств и [видеоуроков](https://laracasts.com) Laravel поможет Вам изучить основы, не перегружая себя.

Если Вы старший разработчик, Laravel предоставляет Вам надежные инструменты для [внедрения зависимостей](/docs/{{version}}/container), [модульного тестирования](/docs/{{version}}/testing), [очередей](/docs/{{version}}/queues), [события в реальном времени](/docs/{{version}}/broadcasting) и другие. Laravel оптимизирован для создания профессиональных веб-приложений и готов обрабатывать корпоративные рабочие нагрузки.

#### Масштабируемый фреймворк

Laravel невероятно масштабируем. Благодаря удобному для масштабирования характеру PHP и встроенной поддержке Laravel быстрых распределенных систем кеширования, таких как Redis, горизонтальное масштабирование с помощью Laravel очень просто. Фактически, приложения Laravel легко масштабируются для обработки сотен миллионов запросов в месяц.

Требуется экстремальное масштабирование? Такие платформы, как [Laravel Vapor](https://vapor.laravel.com), позволяют запускать приложение Laravel в практически неограниченном масштабе с использованием новейшей бессерверной технологии AWS.

#### Сообщество фреймворка

Laravel объединяет лучшие пакеты в экосистеме PHP, чтобы предложить наиболее надежную и удобную для разработчиков структуру. Кроме того, тысячи талантливых разработчиков со всего мира [внесли свой вклад в фреймворк](https://github.com/laravel/framework). Кто знает, возможно, Вы даже станете участником Laravel.

<a name="your-first-laravel-project"></a>
## Ваш первый проект на Laravel

Мы хотим, чтобы начать работу с Laravel было как можно проще. Существует множество вариантов разработки и запуска проекта Laravel на Вашем собственном компьютере. Хотя Вы, возможно, захотите изучить эти варианты позже, Laravel предоставляет [Sail](/docs/{{version}}/sail), встроенное решение для запуска вашего проекта Laravel с использованием [Docker](https://www.docker.com).

Docker - это инструмент для запуска приложений и служб в небольших и легких «контейнерах», которые не мешают установленному на Вашем локальном компьютере программному обеспечению или конфигурации. Это означает, что Вам не нужно беспокоиться о настройке или настройке сложных инструментов разработки, таких как веб-серверы и базы данных на Вашем персональном компьютере. Для начала Вам нужно всего лишь установить [Docker Desktop](https://www.docker.com/products/docker-desktop).

Laravel Sail - это легкий интерфейс командной строки для взаимодействия с конфигурацией Docker по умолчанию в Laravel. Sail обеспечивает отличную отправную точку для создания приложения Laravel с использованием PHP, MySQL и Redis без предварительного опыта работы с Docker.

> {tip} Уже являетесь экспертом по Docker? Не волнуйтесь! Все в Sail можно настроить с помощью файла docker-compose.yml, включенного в Laravel.

<a name="getting-started-on-macos"></a>
### Начало работы в macOS

Если Вы разрабатываете на Mac и [Docker Desktop](https://www.docker.com/products/docker-desktop) уже установлен, Вы можете использовать простую команду терминала для создания нового проекта Laravel. Например, чтобы создать новое приложение Laravel в каталоге с именем «example-app», Вы можете запустить следующую команду в своем терминале:

```nothing
curl -s "https://laravel.build/example-app" | bash
```

Конечно, Вы можете изменить "example-app" в этом URL на что угодно. Каталог приложения Laravel будет создан в каталоге, из которого Вы выполняете команду.

После создания проекта Вы можете перейти в каталог приложения и запустить Laravel Sail. Laravel Sail предоставляет простой интерфейс командной строки для взаимодействия с конфигурацией Docker по умолчанию в Laravel:

```nothing
cd example-app

./vendor/bin/sail up
```

При первом запуске команды Sail `up` на Вашем компьютере будут созданы контейнеры приложений Sail. Это может занять несколько минут. **Не волнуйтесь, последующие попытки запустить Sail будут намного быстрее.**

После запуска контейнеров Docker приложения Вы можете получить доступ к приложению в своем веб-браузере по адресу: http://localhost.

> {tip} Чтобы продолжить изучение Laravel Sail, просмотрите его [полную документацию](/docs/{{version}}/sail).

<a name="getting-started-on-windows"></a>
### Начало работы в Windows

Прежде чем мы создадим новое приложение Laravel на Вашем компьютере с Windows, обязательно установите [Docker Desktop](https://www.docker.com/products/docker-desktop). Затем Вы должны убедиться, что подсистема Windows для Linux 2 (WSL2) установлена и включена. WSL позволяет запускать двоичные исполняемые файлы Linux изначально в Windows 10. Информацию о том, как установить и включить WSL2, можно найти в [документации по среде разработчика](https://docs.microsoft.com/en-us/windows/wsl/install-win10) Microsoft.

> {tip} После установки и включения WSL2 вы должны убедиться, что Docker Desktop [настроен на использование серверной части WSL2](https://docs.docker.com/docker-for-windows/wsl/).

Затем Вы готовы создать свой первый проект Laravel. Запустите [Терминал Windows](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701?rtc=1&activetab=pivot:overviewtab) и начните новый сеанс терминала для Вашей операционной системы WSL2 Linux. Затем Вы можете использовать простую команду терминала для создания нового проекта Laravel. Например, чтобы создать новое приложение Laravel в каталоге с именем «example-app», Вы можете запустить следующую команду в своем терминале:

```nothing
curl -s https://laravel.build/example-app | bash
```

Конечно, Вы можете изменить "example-app" в этом URL на что угодно. Каталог приложения Laravel будет создан в каталоге, из которого Вы выполняете команду.

После создания проекта Вы можете перейти в каталог приложения и запустить Laravel Sail. Laravel Sail предоставляет простой интерфейс командной строки для взаимодействия с конфигурацией Docker по умолчанию в Laravel:

```nothing
cd example-app

./vendor/bin/sail up
```

При первом запуске команды Sail `up` на Вашем компьютере будут созданы контейнеры приложений Sail. Это может занять несколько минут. **Не волнуйтесь, последующие попытки запустить Sail будут намного быстрее.**

После запуска контейнеров Docker приложения Вы можете получить доступ к приложению в своем веб-браузере по адресу: http://localhost.

> {tip} Чтобы продолжить изучение Laravel Sail, просмотрите его [полную документацию](/docs/{{version}}/sail).

#### Разработка в WSL2

Конечно, Вам потребуется иметь возможность изменять файлы приложения Laravel, которые были созданы в Вашей установке WSL2. Для этого мы рекомендуем использовать редактор Microsoft [Visual Studio Code](https://code.visualstudio.com) и его собственное расширение для [Remote Development](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack).

После установки этих инструментов Вы можете открыть любой проект Laravel, выполнив команду `code .` Из корневого каталога Вашего приложения с помощью Терминала Windows.

<a name="getting-started-on-linux"></a>
### Начало работы в Linux

Если Вы разрабатываете в Linux и [Docker](https://www.docker.com) уже установлен, Вы можете использовать простую команду терминала для создания нового проекта Laravel. Например, чтобы создать новое приложение Laravel в каталоге с именем «example-app», Вы можете запустить следующую команду в своем терминале:

```nothing
curl -s https://laravel.build/example-app | bash
```

Конечно, Вы можете изменить "example-app" в этом URL на что угодно. Каталог приложения Laravel будет создан в каталоге, из которого Вы выполняете команду.

После создания проекта Вы можете перейти в каталог приложения и запустить Laravel Sail. Laravel Sail предоставляет простой интерфейс командной строки для взаимодействия с конфигурацией Docker по умолчанию в Laravel:

```nothing
cd example-app

./vendor/bin/sail up
```

При первом запуске команды Sail `up` на Вашем компьютере будут созданы контейнеры приложений Sail. Это может занять несколько минут. **Не волнуйтесь, последующие попытки запустить Sail будут намного быстрее.**

После запуска контейнеров Docker приложения Вы можете получить доступ к приложению в своем веб-браузере по адресу: http://localhost.

> {tip} Чтобы узнать больше о Laravel Sail, просмотрите его [полную документацию](/docs/{{version}}/sail).

<a name="choosing-your-sail-services"></a>
### Choosing Your Sail Services

When creating a new Laravel application via Sail, you may use the `with` query string variable to choose which services should be configured in your new application's `docker-compose.yml` file. Available services include `mysql`, `pgsql`, `mariadb`, `redis`, `memcached`, `meilisearch`, `selenium`, and `mailhog`:

```nothing
curl -s "https://laravel.build/example-app?with=mysql,redis" | bash
```

If you do not specify which services you would like configured, a default stack of `mysql`, `redis`, `meilisearch`, `mailhog`, and `selenium` will be configured.

<a name="installation-via-composer"></a>
### Установка через Composer

Если на Вашем компьютере уже установлены PHP и Composer, Вы можете создать новый проект Laravel напрямую с помощью Composer. После того, как приложение было создано, Вы можете запустить локальный сервер разработки Laravel, используя команду Artisan CLI `serve`:

    composer create-project laravel/laravel example-app

    cd example-app

    php artisan serve

<a name="the-laravel-installer"></a>
#### Установщик Laravel

Или вы можете установить установщик Laravel как глобальную зависимость Composer:

```nothing
composer global require laravel/installer

laravel new example-app

cd example-app

php artisan serve
```

Обязательно поместите общесистемный каталог bin поставщика Composer в Ваш `$PATH`, чтобы исполняемый файл `laravel` мог быть обнаружен Вашей системой. Этот каталог существует в разных местах в зависимости от Вашей операционной системы; однако некоторые общие местоположения включают:

<div class="content-list" markdown="1">
- macOS: `$HOME/.composer/vendor/bin`
- Windows: `%USERPROFILE%\AppData\Roaming\Composer\vendor\bin`
- GNU / Linux Distributions: `$HOME/.config/composer/vendor/bin` or `$HOME/.composer/vendor/bin`
</div>

For convenience, the Laravel installer can also create a Git repository for your new project. To indicate that you want a Git repository to be created, pass the `--git` flag when creating a new project:

```bash
laravel new example-app --git
```

This command will initialize a new Git repository for your project and automatically commit the base Laravel skeleton. The `git` flag assumes you have properly installed and configured Git. You can also use the `--branch` flag to set the initial branch name:

```bash
laravel new example-app --git --branch="main"
```

Instead of using the `--git` flag, you may also use the `--github` flag to create a Git repository and also create a corresponding private repository on GitHub:

```bash
laravel new example-app --github
```

The created repository will then be available at `https://github.com/<your-account>/my-app.com`. The `github` flag assumes you have properly installed the [`gh` CLI tool](https://cli.github.com) and are authenticated with GitHub. Additionally, you should have `git` installed and properly configured. If needed, you can pass additional flags that supported by the GitHub CLI:

```bash
laravel new example-app --github="--public"
```

You may use the `--organization` flag to create the repository under a specific GitHub organization:

```bash
laravel new example-app --github="--public" --organization="laravel"
```

<a name="initial-configuration"></a>
## Начальная конфигурация

Все файлы конфигурации для фреймворка Laravel хранятся в каталоге `config`. Каждый параметр задокументирован, поэтому не стесняйтесь просматривать файлы и знакомиться с доступными Вам вариантами.

Laravel практически не требует дополнительной настройки из коробки. Вы можете начать разработку! Однако Вы можете просмотреть файл `config/app.php` и его документацию. Он содержит несколько параметров, таких как `timezone` и `locale`, которые Вы можете изменить в соответствии с Вашим приложением.

<a name="environment-based-configuration"></a>
### Конфигурация на основе среды

Поскольку многие значения параметров конфигурации Laravel могут различаться в зависимости от того, работает ли Ваше приложение на локальном компьютере или на продакшн веб-сервере, многие важные значения конфигурации определяются с помощью файла `.env`, который существует в корне Вашего приложения.

Ваш файл `.env` не должен быть привязан к системе контроля версий Вашего приложения, поскольку каждому разработчику / серверу, использующему Ваше приложение, может потребоваться другая конфигурация среды. Кроме того, это будет угрозой безопасности в случае, если злоумышленник получит доступ к Вашему репозиторию системы управления версиями, поскольку любые конфиденциальные учетные данные будут раскрыты.

> {tip} Для получения дополнительной информации о файле `.env` и основной конфигурации среды ознакомьтесь с полной [документацией по конфигурации](/docs/{{version}}/configuration#environment-configuration).

<a name="directory-configuration"></a>
### Конфигурация каталога

Laravel всегда должен обслуживаться из корня «веб-каталога», настроенного для вашего веб-сервера. Вы не должны пытаться обслуживать приложение Laravel из подкаталога «веб-каталога». Попытка сделать это может открыть доступ к конфиденциальным файлам, существующим в вашем приложении.

<a name="next-steps"></a>
## Следующие шаги

Теперь, когда Вы создали свой проект Laravel, Вам может быть интересно, чему научиться дальше. Во-первых, мы настоятельно рекомендуем ознакомиться с тем, как работает Laravel, прочитав следующую документацию:

<div class="content-list" markdown="1">
- [Жизненный цикл запроса](/docs/{{version}}/lifecycle)
- [Конфигурация](/docs/{{version}}/configuration)
- [Структура каталогов](/docs/{{version}}/structure)
- [Сервис контейнер](/docs/{{version}}/container)
- [Фасады](/docs/{{version}}/facades)
</div>

То, как Вы хотите использовать Laravel, также будет определять следующие шаги на Вашем пути. Существует множество способов использования Laravel, и мы рассмотрим два основных варианта использования фреймворка ниже.

<a name="laravel-the-fullstack-framework"></a>
### Laravel - Фул-Стек фреймворк

Laravel может служить полноценным фреймворком. Под "фреймворком полного стека" мы подразумеваем, что вы собираетесь использовать Laravel для маршрутизации запросов к вашему приложению и рендеринга интерфейса через [шаблоны Blade](/docs/{{version}}/blade) или с помощью гибридного одностраничного приложения, такие технологии, как [Inertia.js](https://inertiajs.com). Это наиболее распространенный способ использования фреймворка Laravel.

Если Вы планируете использовать Laravel именно так, Вы можете ознакомиться с нашей документацией по [маршрутизации](/docs/{{version}}/routing), [представлениям](/docs/{{version}}/views) или [Eloquent ORM](/docs/{{version}}/eloquent). Кроме того, Вам может быть интересно узнать о таких пакетах сообщества, как [Livewire](https://laravel-livewire.com) и [Inertia.js](https://inertiajs.com). Эти пакеты позволяют использовать Laravel в качестве фреймворка полного стека, обладая при этом многими преимуществами пользовательского интерфейса, предоставляемыми одностраничными приложениями JavaScript.

Если Вы используете Laravel в качестве фреймворка полного стека, мы также настоятельно рекомендуем Вам научиться компилировать CSS и JavaScript Вашего приложения с помощью [Laravel Mix](/docs/{{version}}/mix).

> {tip} Если Вы хотите начать разработку своего приложения, воспользуйтесь одним из наших официальных [стартовых наборов приложений](/docs/{{version}}/starter-kits).

<a name="laravel-the-api-backend"></a>
### Laravel как бэкэнд API

Laravel также может служить серверной частью API для одностраничного приложения JavaScript или мобильного приложения. Например, Вы можете использовать Laravel в качестве серверной части API для своего приложения [Next.js](https://nextjs.org). В этом контексте Вы можете использовать Laravel для обеспечения [аутентификации](/docs/{{version}}/sanctum) и хранения / извлечения данных для Вашего приложения, а также пользуясь преимуществами мощных сервисов Laravel, таких как очереди, электронная почта, уведомления и другое.

Если Вы планируете использовать Laravel именно так, Вы можете ознакомиться с нашей документацией по [маршрутизации](/docs/{{version}}/routing), [Laravel Sanctum](/docs/{{version}}/sanctum) и [Eloquent ORM](/docs/{{version}}/eloquent).
