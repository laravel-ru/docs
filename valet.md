# Laravel Valet

- [Введение](#introduction)
- [Установка](#installation)
    - [Обновление Valet](#upgrading-valet)
- [Обслуживание сайтов](#serving-sites)
    - [Команда "Park"](#the-park-command)
    - [Команда "Link"](#the-link-command)
    - [Защита сайтов с помощью TLS](#securing-sites)
- [Совместное использование сайтов](#sharing-sites)
    - [Совместное использование сайтов через Ngrok](#sharing-sites-via-ngrok)
    - [Совместное использование сайтов через Expose](#sharing-sites-via-expose)
    - [Совместное использование сайтов в Dашей локальной сети](#sharing-sites-on-your-local-network)
- [Переменные среды для конкретного сайта](#site-specific-environment-variables)
- [Прокси-сервисы](#proxying-services)
- [Пользовательские драйверы Valet](#custom-valet-drivers)
    - [Локальные драйверы](#local-drivers)
- [Другие команды Valet](#other-valet-commands)
- [Каталоги и файлы Valet](#valet-directories-and-files)

<a name="introduction"></a>
## Введение

Valet - это среда разработки Laravel для минималистов macOS. Laravel Valet настраивает Ваш Mac так, чтобы он всегда запускал [Nginx](https://www.nginx.com/) в фоновом режиме при запуске Вашего компьютера. Затем, используя [DnsMasq](https://en.wikipedia.org/wiki/Dnsmasq), Valet проксирует все запросы в домене `*.test`, чтобы указать на сайты, установленные на Вашем локальном компьютере.

Другими словами, Valet - это невероятно быстрая среда разработки Laravel, которая использует примерно 7 МБ ОЗУ. Valet не является полной заменой [Sail](/docs/{{version}}/sail) или [Homestead](/docs/{{version}}/homestead), но представляет собой отличную альтернативу, если Вам нужна гибкость, предпочитаете экстремальную скорость или работаете на машине с ограниченным объемом оперативной памяти.

Стандартная поддержка Valet включает, но не ограничивается:

<style>
    #valet-support > ul {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        line-height: 1.9;
    }
</style>

<div id="valet-support" markdown="1">
- [Laravel](https://laravel.com)
- [Lumen](https://lumen.laravel.com)
- [Bedrock](https://roots.io/bedrock/)
- [CakePHP 3](https://cakephp.org)
- [Concrete5](https://www.concrete5.org/)
- [Contao](https://contao.org/en/)
- [Craft](https://craftcms.com)
- [Drupal](https://www.drupal.org/)
- [ExpressionEngine](https://www.expressionengine.com/)
- [Jigsaw](https://jigsaw.tighten.co)
- [Joomla](https://www.joomla.org/)
- [Katana](https://github.com/themsaid/katana)
- [Kirby](https://getkirby.com/)
- [Magento](https://magento.com/)
- [OctoberCMS](https://octobercms.com/)
- [Sculpin](https://sculpin.io/)
- [Slim](https://www.slimframework.com)
- [Statamic](https://statamic.com)
- Static HTML
- [Symfony](https://symfony.com)
- [WordPress](https://wordpress.org)
- [Zend](https://framework.zend.com)
</div>

Однако Вы можете расширить Valet своими собственными [пользовательскими драйверами](#custom-valet-drivers).

<a name="installation"></a>
## Установка

> {note} Valet требует macOS и [Homebrew](https://brew.sh/). Перед установкой Вы должны убедиться, что никакие другие программы, такие как Apache или Nginx, не привязаны к порту 80 Вашего локального компьютера.

Для начала Вам необходимо убедиться, что Homebrew обновлен, используя команду `update`:

    brew update

Затем вы должны использовать Homebrew для установки PHP:

    brew install php

После установки PHP Вы готовы установить [менеджер пакетов Composer](https://getcomposer.org). Кроме того, Вы должны убедиться, что каталог `~/.composer/vendor/bin` находится в "PATH" Вашей системы. После установки Composer Вы можете установить Laravel Valet как глобальный пакет Composer:

    composer global require laravel/valet

Наконец, Вы можете выполнить команду Valet `install`. Это настроит и установит Valet и DnsMasq. Кроме того, демон Valet будет настроен на запуск при запуске Вашей системы:

    valet install

После установки Valet попробуйте выполнить пинг любого домена `*.test` на Вашем терминале с помощью такой команды, как `ping foobar.test`. Если Valet установлен правильно, Вы должны увидеть, что этот домен отвечает на `127.0.0.1`.

Valet автоматически запускает своего демона при каждой загрузке Вашей машины. После завершения первоначальной установки Valet больше не нужно запускать `valet start` или `valet install`.

<a name="php-versions"></a>
#### Версии PHP

Valet позволяет переключать версии PHP с помощью команды `valet use php@version`. Valet установит указанную версию PHP через Homebrew, если она еще не установлена:

    valet use php@7.2

    valet use php

> {note} Valet обслуживает только одну версию PHP за раз, даже если у Вас установлено несколько версий PHP.

<a name="database"></a>
#### База Данных

Если Вашему приложению нужна база данных, посетите [DBngin](https://dbngin.com). DBngin предоставляет бесплатный универсальный инструмент управления базами данных, который включает MySQL, PostgreSQL и Redis. После установки DBngin Вы можете подключиться к своей базе данных по адресу `127.0.0.1`, используя имя пользователя `root` и пустую строку для пароля.

<a name="resetting-your-installation"></a>
#### Сброс Вашей установки

Если у Вас возникли проблемы с правильной работой установки Valet, выполнение команды `composer global update`, за которой следует `valet install`, сбросит Вашу установку и может решить множество проблем. В редких случаях может потребоваться «полный сброс» Valet, выполнив `valet uninstall --force`, а затем `valet install`.

<a name="upgrading-valet"></a>
### Обновление Valet

Вы можете обновить установку Valet, выполнив команду `composer global update` в Вашем терминале. После обновления рекомендуется запустить команду `valet install`, чтобы Valet мог при необходимости выполнить дополнительные обновления Ваших файлов конфигурации.

<a name="serving-sites"></a>
## Обслуживание сайтов

После установки Valet Вы готовы приступить к обслуживанию своих приложений Laravel. Valet предоставляет две команды, которые помогут Вам обслуживать Ваши приложения: `park` и `link`.

<a name="the-park-command"></a>
### Команда `park`

Команда `park` регистрирует на Вашем компьютере каталог, содержащий Ваши приложения. После того, как каталог будет "припаркован" с помощью Valet, все каталоги в этом каталоге будут доступны в Вашем веб-браузере по адресу `http://<directory-name>.test`:

    cd ~/Sites

    valet park

Это все, что нужно сделать. Теперь любое приложение, которое Вы создаете в «припаркованном» каталоге, будет автоматически обслуживаться с использованием соглашения `http://<directory-name>.test`. Итак, если Ваш припаркованный каталог содержит каталог с именем «laravel», приложение в этом каталоге будет доступно по адресу `http://laravel.test`.

<a name="the-link-command"></a>
### Команда `link`

Команду `link` также можно использовать для обслуживания Ваших приложений Laravel. Эта команда полезна, если Вы хотите обслуживать один сайт в каталоге, а не весь каталог:

    cd ~/Sites/laravel

    valet link

После того, как приложение было связано с Valet с помощью команды `link`, Вы можете получить доступ к приложению, используя его имя каталога. Итак, сайт, на который была ссылка в приведенном выше примере, может быть доступен по адресу `http://laravel.test`.

Если Вы хотите обслуживать приложение на другом имени хоста, Вы можете передать имя хоста команде `link`. Например, Вы можете запустить следующую команду, чтобы сделать приложение доступным по адресу `http://application.test`:

    cd ~/Sites/laravel

    valet link application

Вы можете выполнить команду `links`, чтобы отобразить список всех Ваших связанных каталогов:

    valet links

Команда `unlink` может использоваться для уничтожения символической ссылки сайта:

    cd ~/Sites/laravel

    valet unlink

<a name="securing-sites"></a>
### Защита сайтов с помощью TLS

По умолчанию Valet обслуживает сайты через HTTP. Однако, если Вы хотите обслуживать сайт через зашифрованный TLS с использованием HTTP/2, Вы можете использовать команду `secure`. Например, если Ваш сайт обслуживается Valet в домене `laravel.test`, Вы должны выполнить следующую команду, чтобы защитить его:

    valet secure laravel

Чтобы "снять защиту" с сайта и вернуться к обслуживанию его трафика по обычному протоколу HTTP, используйте команду `unsecure`. Как и команда `secure`, эта команда принимает имя хоста, которое Вы хотите снять с защиты:

    valet unsecure laravel

<a name="sharing-sites"></a>
## Совместное использование сайтов

Valet даже включает команду для предоставления доступа к Вашим локальным сайтам всему миру, предоставляя простой способ протестировать Ваш сайт на мобильных устройствах или поделиться им с членами команды и клиентами.

<a name="sharing-sites-via-ngrok"></a>
### Совместное использование сайтов через Ngrok

Чтобы поделиться сайтом, перейдите в каталог сайта в Вашем терминале и выполните команду Valet `share`. Общедоступный URL-адрес будет вставлен в Ваш буфер обмена, и его можно будет вставить прямо в Ваш браузер или поделиться с Вашей командой:

    cd ~/Sites/laravel

    valet share

Чтобы прекратить совместное использование Вашего сайта, Вы можете нажать `Control + C`.

> {tip} You may pass additional Ngrok parameters to the share command, such as `valet share --region=eu`. For more information, consult the [ngrok documentation](https://ngrok.com/docs).

<a name="sharing-sites-via-expose"></a>
### Совместное использование сайтов через Expose

Если у Вас установлен [Expose](https://beyondco.de/docs/expose), Вы можете поделиться своим сайтом, перейдя в каталог сайта в Вашем терминале и запустив команду `expose`. Обратитесь к [документации Expose](https://beyondco.de/docs/expose/introduction) для получения информации о дополнительных параметрах командной строки, которые он поддерживает. После публикации сайта Expose отобразит URL-адрес для совместного использования, который Вы можете использовать на других своих устройствах или среди членов команды:

    cd ~/Sites/laravel

    valet expose

Чтобы прекратить совместное использование Вашего сайта, Вы можете нажать `Control + C`.

<a name="sharing-sites-on-your-local-network"></a>
### Совместное использование сайтов в Вашей локальной сети

Valet по умолчанию ограничивает входящий трафик внутренним интерфейсом `127.0.0.1`, чтобы Ваша машина разработки не подвергалась угрозам безопасности из Интернета.

Если Вы хотите разрешить другим устройствам в Вашей локальной сети доступ к сайтам Valet на Вашем компьютере через IP-адрес Вашего компьютера (например: `192.168.1.10/application.test`), Вам необходимо вручную отредактировать соответствующий файл конфигурации Nginx для этот сайт, чтобы снять ограничение на директиву `listen`. Вам следует удалить префикс `127.0.0.1:` в директиве `listen` для портов 80 и 443.

Если Вы не запускали `valet secure` в проекте, Вы можете открыть сетевой доступ для всех сайтов, не поддерживающих HTTPS, отредактировав файл `/usr/local/etc/nginx/valet/valet.conf`. Однако, если Вы обслуживаете сайт проекта через HTTPS (Вы запустили `valet secure` для сайта), Вам следует отредактировать файл `~/.config/valet/Nginx/app-name.test`.

После обновления конфигурации Nginx запустите команду `valet restart`, чтобы применить изменения конфигурации.

<a name="site-specific-environment-variables"></a>
## Переменные среды для конкретного сайта

Некоторые приложения, использующие другие платформы, могут зависеть от переменных среды сервера, но не позволяют настраивать эти переменные в Вашем проекте. Valet позволяет Вам настраивать переменные среды для конкретных сайтов, добавляя файл `.valet-env.php` в корень Вашего проекта. Этот файл должен возвращать массив пар переменных сайта / среды, который будет добавлен в глобальный массив `$_SERVER` для каждого сайта, указанного в массиве:

    <?php

    return [
        // Установите для $_SERVER['key'] значение "value" для сайта laravel.test...
        'laravel' => [
            'key' => 'value',
        ],

        // Установите для $_SERVER['key'] значение "value" для всех сайтов...
        '*' => [
            'key' => 'value',
        ],
    ];

<a name="proxying-services"></a>
## Прокси-услуги

Иногда Вам может потребоваться прокси-домен Valet для другой службы на Вашем локальном компьютере. Например, иногда Вам может потребоваться запустить Valet при одновременном запуске отдельного сайта в Docker; однако Valet и Docker не могут одновременно подключаться к порту 80.

Чтобы решить эту проблему, Вы можете использовать команду `proxy` для создания прокси. Например, Вы можете проксировать весь трафик с `http://elasticsearch.test` на `http://127.0.0.1:9200`:

```bash
valet proxy elasticsearch http://127.0.0.1:9200
```

Вы можете удалить прокси с помощью команды `unproxy`:

    valet unproxy elasticsearch

Вы можете использовать команду `proxies`, чтобы вывести список всех конфигураций сайта, которые проксируются:

    valet proxies

<a name="custom-valet-drivers"></a>
## Пользовательские драйверы Valet

Вы можете написать свой собственный «драйвер» Valet для обслуживания приложений PHP, работающих на платформе или CMS, которые изначально не поддерживаются Valet. Когда Вы устанавливаете Valet, создается каталог `~/.config/valet/Drivers`, содержащий файл `SampleValetDriver.php`. Этот файл содержит образец реализации драйвера, демонстрирующий, как написать собственный драйвер. Для написания драйвера необходимо реализовать только три метода: `serves`, `isStaticFile` и `frontControllerPath`.

Все три метода получают в качестве аргументов значения `$sitePath`, `$siteName` и `$uri`. `$sitePath` - это полный путь к сайту, обслуживаемому на Вашем компьютере, например, `/Users/Lisa/Sites/my-project`. `$siteName` - это часть домена "хост"/"имя сайта" (`my-project`). `$uri` - это URI входящего запроса (`/foo/bar`).

После того, как Вы закончите свой собственный драйвер Valet, поместите его в каталог `~/.config/valet/Drivers`, используя соглашение об именах `FrameworkValetDriver.php`. Например, если Вы пишете специальный драйвер для WordPress, Ваше имя файла должно быть `WordPressValetDriver.php`.

Давайте рассмотрим пример реализации каждого метода, который должен реализовать Ваш пользовательский драйвер Valet.

<a name="the-serves-method"></a>
#### Метод `serves`

Метод `serves` должен возвращать `true` если Ваш драйвер должен обрабатывать входящий запрос. В противном случае метод должен вернуть `false`. Итак, в рамках этого метода Вы должны попытаться определить, содержит ли данный `$sitePath` проект того типа, который Вы пытаетесь обслуживать.

Например, представим, что мы пишем `WordPressValetDriver`. Наш метод `serves` может выглядеть примерно так:

    /**
     * Определить, обслуживает ли драйвер запрос.
     *
     * @param  string  $sitePath
     * @param  string  $siteName
     * @param  string  $uri
     * @return bool
     */
    public function serves($sitePath, $siteName, $uri)
    {
        return is_dir($sitePath.'/wp-admin');
    }

<a name="the-isstaticfile-method"></a>
#### Метод `isStaticFile`

`isStaticFile` должен определять, относится ли входящий запрос к файлу, который является «статическим», таким как изображение или таблица стилей. Если файл статический, метод должен вернуть полный путь к статическому файлу на диске. Если входящий запрос не для статического файла, метод должен вернуть `false`:

    /**
     * Определить, относится ли входящий запрос к статическому файлу.
     *
     * @param  string  $sitePath
     * @param  string  $siteName
     * @param  string  $uri
     * @return string|false
     */
    public function isStaticFile($sitePath, $siteName, $uri)
    {
        if (file_exists($staticFilePath = $sitePath.'/public/'.$uri)) {
            return $staticFilePath;
        }

        return false;
    }

> {note} Метод `isStaticFile` будет вызываться только в том случае, если метод `serves` возвращает `true` для входящего запроса, а URI запроса не равен `/`.

<a name="the-frontcontrollerpath-method"></a>
#### Метод `frontControllerPath`

Метод `frontControllerPath` должен возвращать полный путь к «фронт-контроллеру» Вашего приложения, который обычно является файлом «index.php» или его эквивалентом:

    /**
     * Получить полностью разрешенный путь к фронт-контроллеру приложения.
     *
     * @param  string  $sitePath
     * @param  string  $siteName
     * @param  string  $uri
     * @return string
     */
    public function frontControllerPath($sitePath, $siteName, $uri)
    {
        return $sitePath.'/public/index.php';
    }

<a name="local-drivers"></a>
### Локальные драйверы

Если Вы хотите определить собственный драйвер Valet для отдельного приложения, создайте файл `LocalValetDriver.php` в корневом каталоге приложения. Ваш настраиваемый драйвер может расширять базовый класс `ValetDriver` или расширять существующий драйвер для конкретного приложения, такой как `LaravelValetDriver`:

    class LocalValetDriver extends LaravelValetDriver
    {
        /**
         * Определите, обслуживает ли драйвер запрос.
         *
         * @param  string  $sitePath
         * @param  string  $siteName
         * @param  string  $uri
         * @return bool
         */
        public function serves($sitePath, $siteName, $uri)
        {
            return true;
        }

        /**
         * Получить полностью разрешенный путь к фронт-контроллеру приложения.
         *
         * @param  string  $sitePath
         * @param  string  $siteName
         * @param  string  $uri
         * @return string
         */
        public function frontControllerPath($sitePath, $siteName, $uri)
        {
            return $sitePath.'/public_html/index.php';
        }
    }

<a name="other-valet-commands"></a>
## Другие Valet команды

Команда       | Описание
------------- | -------------
`valet forget` | Выполните эту команду из «припаркованного» каталога, чтобы удалить его из списка припаркованных каталогов.
`valet log` | Просмотреть список логов, которые ведутся службами Valet.
`valet paths` | Просмотреть все свои «припаркованные» пути.
`valet restart` | Перезапустить демон Valet.
`valet start` | Запустить демон Valet.
`valet stop` | Остановить демон Valet.
`valet trust` | Добавить файлы sudoers для Brew и Valet, чтобы команды Valet могли запускаться без запроса паролей.
`valet uninstall` | Удалить Valet: Показывает инструкции по удалению вручную; или передайте параметр `--force`, чтобы агрессивно удалить все Valet.

<a name="valet-directories-and-files"></a>
## Каталоги и файлы Valet

Следующая информация о каталогах и файлах может оказаться полезной при устранении неполадок в среде Valet:

#### `~/.config/valet`

Содержит всю конфигурацию Valet. Вы можете сохранить резервную копию этого каталога.

#### `~/.config/valet/dnsmasq.d/`

Этот каталог содержит конфигурацию DNSMasq.

#### `~/.config/valet/Drivers/`

Этот каталог содержит драйверы Valet. Драйверы определяют, как обслуживается конкретный фреймворк / CMS.

#### `~/.config/valet/Extensions/`

Этот каталог содержит настраиваемые расширения / команды Valet.

#### `~/.config/valet/Nginx/`

Этот каталог содержит все конфигурации сайта Valet Nginx. Эти файлы перестраиваются при запуске команд `install`, `secure` и `tld`.

#### `~/.config/valet/Sites/`

Этот каталог содержит все символические ссылки для Ваших [связанных проектов](#the-link-command).

#### `~/.config/valet/config.json`

Этот файл является основным файлом конфигурации Valet.

#### `~/.config/valet/valet.sock`

Этот файл представляет собой сокет PHP-FPM, используемый установкой Valet Nginx. Это будет существовать только в том случае, если PHP работает правильно.

#### `~/.config/valet/Log/fpm-php.www.log`

Этот файл является журналом пользователя для ошибок PHP.

#### `~/.config/valet/Log/nginx-error.log`

Этот файл представляет собой пользовательский журнал ошибок Nginx.

#### `/usr/local/var/log/php-fpm.log`

Этот файл представляет собой системный журнал ошибок PHP-FPM.

#### `/usr/local/var/log/nginx`

Этот каталог содержит журналы доступа и ошибок Nginx.

#### `/usr/local/etc/php/X.X/conf.d`

Этот каталог содержит файлы `*.ini` для различных настроек конфигурации PHP.

#### `/usr/local/etc/php/X.X/php-fpm.d/valet-fpm.conf`

Это файл конфигурации пула PHP-FPM.

#### `~/.composer/vendor/laravel/valet/cli/stubs/secure.valet.conf`

Этот файл является конфигурацией Nginx по умолчанию, используемой для создания сертификатов SSL для Ваших сайтов.
