# Laravel Homestead

- [Вступление](#introduction)
- [Установка и настройка](#installation-and-setup)
    - [Первые шаги](#first-steps)
    - [Настройка Homestead](#configuring-homestead)
    - [Настройка сайтов Nginx](#configuring-nginx-sites)
    - [Настройка сервисов](#configuring-services)
    - [Запуск коробки Vagrant](#launching-the-vagrant-box)
    - [Установка в проект](#per-project-installation)
    - [Установка дополнительных функций](#installing-optional-features)
    - [Псевдонимы](#aliases)
- [Обновление Homestead](#updating-homestead)
- [Ежедневное использование](#daily-usage)
    - [Подключение через SSH](#connecting-via-ssh)
    - [Добавление дополнительных сайтов](#adding-additional-sites)
    - [Переменные среды](#environment-variables)
    - [Порты](#ports)
    - [Версии PHP](#php-versions)
    - [Подключение к базам данных](#connecting-to-databases)
    - [Резервные копии базы данных](#database-backups)
    - [Снимки базы данных](#database-snapshots)
    - [Настройка расписаний Cron](#configuring-cron-schedules)
    - [Настройка MailHog](#configuring-mailhog)
    - [Настройка Minio](#configuring-minio)
    - [Laravel Dusk](#laravel-dusk)
    - [Совместное использование Вашего окружения](#sharing-your-environment)
- [Отладка и профилирование](#debugging-and-profiling)
    - [Отладка веб-запросов с помощью Xdebug](#debugging-web-requests)
    - [Отладка CLI-приложений](#debugging-cli-applications)
    - [Профилирование приложений с помощью Blackfire](#profiling-applications-with-blackfire)
- [Сетевые интерфейсы](#network-interfaces)
- [Расширение Homestead](#extending-homestead)
- [Настройки для конкретного провайдера](#provider-specific-settings)
    - [VirtualBox](#provider-specific-virtualbox)

<a name="introduction"></a>
## Вступление

Laravel стремится сделать весь процесс разработки PHP приятным, включая Вашу локальную среду разработки. Laravel Homestead - это официальный предварительно упакованный пакет Vagrant, который предоставляет Вам прекрасную среду разработки, не требуя установки PHP, веб-сервера и любого другого серверного программного обеспечения на Вашем локальном компьютере.

[Vagrant](https://www.vagrantup.com) предоставляет простой и элегантный способ управления виртуальными машинами и их подготовки. Бродячие ящики полностью одноразовые. Если что-то пойдет не так, Вы можете уничтожить и воссоздать коробку за считанные минуты!

Homestead работает в любой системе Windows, macOS или Linux и включает Nginx, PHP, MySQL, PostgreSQL, Redis, Memcached, Node и все другое программное обеспечение, необходимое для разработки потрясающих приложений Laravel.

> {note} Если Вы используете Windows, Вам может потребоваться включить аппаратную виртуализацию (VT-x). Обычно это можно включить в BIOS. Если Вы используете Hyper-V в системе UEFI, Вам может потребоваться дополнительно отключить Hyper-V, чтобы получить доступ к VT-x.

<a name="included-software"></a>
### Включенное программное обеспечение

<style>
    #software-list > ul {
        column-count: 2; -moz-column-count: 2; -webkit-column-count: 2;
        column-gap: 5em; -moz-column-gap: 5em; -webkit-column-gap: 5em;
        line-height: 1.9;
    }
</style>

<div id="software-list" markdown="1">
- Ubuntu 20.04
- Git
- PHP 8.0
- PHP 7.4
- PHP 7.3
- PHP 7.2
- PHP 7.1
- PHP 7.0
- PHP 5.6
- Nginx
- MySQL (8.0)
- lmm
- Sqlite3
- PostgreSQL (9.6, 10, 11, 12, 13)
- Composer
- Node (С Yarn, Bower, Grunt и Gulp)
- Redis
- Memcached
- Beanstalkd
- Mailhog
- avahi
- ngrok
- Xdebug
- XHProf / Tideways / XHGui
- wp-cli
</div>

<a name="optional-software"></a>
### Дополнительное программное обеспечение

<style>
    #software-list > ul {
        column-count: 2; -moz-column-count: 2; -webkit-column-count: 2;
        column-gap: 5em; -moz-column-gap: 5em; -webkit-column-gap: 5em;
        line-height: 1.9;
    }
</style>

<div id="software-list" markdown="1">
- Apache
- Blackfire
- Cassandra
- Chronograf
- CouchDB
- Crystal & Lucky Framework
- Docker
- Elasticsearch
- EventStoreDB
- Gearman
- Go
- Grafana
- InfluxDB
- MariaDB
- Meilisearch
- MinIO
- MongoDB
- Neo4j
- Oh My Zsh
- Open Resty
- PM2
- Python
- R
- RabbitMQ
- RVM (Ruby Version Manager)
- Solr
- TimescaleDB
- Trader <small>(PHP extension)</small>
- Webdriver & Laravel Dusk Utilities
</div>

<a name="installation-and-setup"></a>
## Установка и настройка

<a name="first-steps"></a>
### Первые шаги

Перед запуском среды Homestead Вы должны установить [Vagrant](https://www.vagrantup.com/downloads.html), а также одного из следующих поддерживаемых поставщиков:

- [VirtualBox 6.1.x](https://www.virtualbox.org/wiki/Downloads)
- [Parallels](https://www.parallels.com/products/desktop/)

Все эти программные пакеты предоставляют простые в использовании визуальные установщики для всех популярных операционных систем.

Чтобы использовать поставщика Parallels, вам необходимо установить [плагин Parallels Vagrant](https://github.com/Parallels/vagrant-parallels). Это бесплатно.

<a name="installing-homestead"></a>
#### Установка Homestead

Вы можете установить Homestead, клонировав репозиторий Homestead на свой хост-компьютер. Рассмотрите возможность клонирования репозитория в папку `Homestead` в вашем «домашнем» каталоге, поскольку виртуальная машина Homestead будет служить хостом для всех ваших приложений Laravel. В этой документации мы будем называть этот каталог вашим «каталогом Homestead»:

```bash
git clone https://github.com/laravel/homestead.git ~/Homestead
```

После клонирования репозитория Laravel Homestead Вы должны проверить ветку `release`. Эта ветка всегда содержит последний стабильный выпуск Homestead:

    cd ~/Homestead

    git checkout release

Затем выполните команду `bash init.sh` из каталога Homestead, чтобы создать файл конфигурации `Homestead.yaml`. В файле `Homestead.yaml` Вы настраиваете все параметры для установки Homestead. Этот файл будет помещен в каталог Homestead:

    // macOS / Linux...
    bash init.sh

    // Windows...
    init.bat

<a name="configuring-homestead"></a>
### Настройка Homestead

<a name="setting-your-provider"></a>
#### Настройка вашего провайдера

Ключ `provider` в вашем файле `Homestead.yaml` указывает, какой провайдер Vagrant следует использовать: `virtualbox` или `parallels`:

    provider: virtualbox

<a name="configuring-shared-folders"></a>
#### Настройка общих папок

Свойство `folders` файла `Homestead.yaml` перечисляет все папки, которыми Вы хотите поделиться со своей средой Homestead. При изменении файлов в этих папках они будут синхронизироваться между Вашим локальным компьютером и виртуальной средой Homestead. Вы можете настроить столько общих папок, сколько необходимо:

```yaml
folders:
    - map: ~/code/project1
      to: /home/vagrant/project1
```

> {note} Пользователи Windows не должны использовать синтаксис пути `~/`, а вместо этого должны использовать полный путь к своему проекту, например `C:\Users\user\Code\project1`.

Вы всегда должны сопоставлять отдельные приложения с их собственным сопоставлением папок вместо сопоставления одного большого каталога, содержащего все Ваши приложения. При сопоставлении папки виртуальная машина должна отслеживать все операции ввода-вывода на диске для *каждого* файла в папке. У Вас может снизиться производительность, если в папке много файлов:

```yaml
folders:
    - map: ~/code/project1
      to: /home/vagrant/project1
    - map: ~/code/project2
      to: /home/vagrant/project2
```

> {note} Вы никогда не должны монтировать `.` (текущий каталог) при использовании Homestead. Это приводит к тому, что Vagrant не отображает текущую папку в `/vagrant`, что нарушает работу дополнительных функций и приводит к неожиданным результатам во время подготовки.

Чтобы включить [NFS](https://www.vagrantup.com/docs/synced-folders/nfs.html), Вы можете добавить параметр `type` в сопоставление папок:

    folders:
        - map: ~/code/project1
          to: /home/vagrant/project1
          type: "nfs"

> {note} При использовании NFS в Windows Вам следует подумать об установке плагина [vagrant-winnfsd](https://github.com/winnfsd/vagrant-winnfsd). Этот плагин будет поддерживать правильные права пользователя / группы для файлов и каталогов на виртуальной машине Homestead.

Вы также можете передать любые параметры, поддерживаемые [Синхронизировавшие папки Vagrant](https://www.vagrantup.com/docs/synced-folders/basic_usage.html), указав их под ключом `options`:

    folders:
        - map: ~/code/project1
          to: /home/vagrant/project1
          type: "rsync"
          options:
              rsync__args: ["--verbose", "--archive", "--delete", "-zz"]
              rsync__exclude: ["node_modules"]

<a name="configuring-nginx-sites"></a>
### Настройка сайтов Nginx

Не знаком с Nginx? Без проблем. Свойство `sites` вашего файла `Homestead.yaml` позволяет вам легко сопоставить "домен" с папкой в вашей среде Homestead. Пример конфигурации сайта включен в файл `Homestead.yaml`. Опять же, вы можете добавить столько сайтов в среду Homestead, сколько необходимо. Homestead может служить удобной виртуализированной средой для каждого приложения Laravel, над которым вы работаете:

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public

Если Вы измените свойство `sites` после подготовки виртуальной машины Homestead, Вы должны выполнить команду `vagrant reload --provision` в своем терминале, чтобы обновить конфигурацию Nginx на виртуальной машине.

> {note} Скрипты Homestead построены так, чтобы быть максимально идемпотентными. Однако, если у Вас возникли проблемы во время подготовки, Вы должны уничтожить и перестроить машину, выполнив команду `vagrant destroy && vagrant up`.

<a name="hostname-resolution"></a>
#### Разрешение имени хоста

Homestead публикует имена хостов, используя `mDNS` для автоматического разрешения хостов. Если Вы установите `hostname: homestead` в Вашем файле `Homestead.yaml`, хост будет доступен по адресу `homestead.local`. Настольные дистрибутивы macOS, iOS и Linux по умолчанию включают поддержку `mDNS`. Если Вы используете Windows, Вы должны установить [Bonjour Print Services для Windows](https://support.apple.com/kb/DL999?viewlocale=en_US&locale=en_US).

Использование автоматических имен хостов лучше всего подходит для [установок для каждого проекта](#per-project-installation) Homestead. Если Вы размещаете несколько сайтов на одном экземпляре Homestead, Вы можете добавить «домены» для своих веб-сайтов в файл `hosts` на Вашем компьютере. Файл `hosts` будет перенаправлять запросы для Ваших сайтов Homestead на Вашу виртуальную машину Homestead. В macOS и Linux этот файл находится в `/etc/hosts`. В Windows он находится в `C:\Windows\System32\drivers\etc\hosts`. Строки, которые Вы добавляете в этот файл, будут выглядеть следующим образом:

    192.168.10.10  homestead.test

Убедитесь, что в списке указан IP-адрес, указанный в Вашем файле `Homestead.yaml`. После того, как Вы добавили домен в свой файл `hosts` и запустили окно Vagrant, Вы сможете получить доступ к сайту через свой веб-браузер:

```bash
http://homestead.test
```

<a name="configuring-services"></a>
### Настройка сервисов

По умолчанию Homestead запускает несколько сервисов; однако Вы можете настроить, какие службы будут включены или отключены во время подготовки. Например, Вы можете включить PostgreSQL и отключить MySQL, изменив параметр `services` в файле `Homestead.yaml`:

```yaml
services:
    - enabled:
        - "postgresql@12-main"
    - disabled:
        - "mysql"
```

Указанные службы будут запускаться или останавливаться в зависимости от их порядка в директивах `enabled` и `disabled`.

<a name="launching-the-vagrant-box"></a>
### Запуск коробки Vagrant

После того как Вы отредактировали файл `Homestead.yaml` по своему вкусу, запустите команду `vagrant up` из каталога Homestead. Vagrant загрузит виртуальную машину и автоматически настроит Ваши общие папки и сайты Nginx.

Чтобы уничтожить машину, Вы можете использовать команду `vagrant destroy --force`.

<a name="per-project-installation"></a>
### Установка в проект

Вместо того, чтобы устанавливать Homestead глобально и использовать одну и ту же виртуальную машину Homestead для всех Ваших проектов, Вы можете вместо этого настроить экземпляр Homestead для каждого проекта, которым Вы управляете. Установка Homestead для каждого проекта может быть полезной, если Вы хотите отправить файл `Vagrantfile` вместе с Вашим проектом, позволяя другим работающим над проектом `vagrant up` сразу после клонирования репозитория проекта.

Вы можете установить Homestead в свой проект с помощью диспетчера пакетов Composer:

```bash
composer require laravel/homestead --dev
```

После установки Homestead вызовите команду Homestead `make`, чтобы сгенерировать файлы `Vagrantfile` и `Homestead.yaml` для Вашего проекта. Эти файлы будут помещены в корень Вашего проекта. Команда `make` автоматически настроит директивы `sites` и `folders` в файле `Homestead.yaml`:

    // macOS / Linux...
    php vendor/bin/homestead make

    // Windows...
    vendor\\bin\\homestead make

Затем запустите команду `vagrant up` в Вашем терминале и войдите в свой проект по адресу `http://homestead.test` в Вашем браузере. Помните, что Вам все равно нужно будет добавить запись файла `/etc/hosts` для `homestead.test` или домена по вашему выбору, если вы не используете автоматическое [разрешение имени хоста](#hostname-resolution).

<a name="installing-optional-features"></a>
### Установка дополнительных функций

Дополнительное программное обеспечение устанавливается с помощью опции `features` в Вашем файле `Homestead.yaml`. Большинство функций можно включить или отключить с помощью логического значения, в то время как некоторые функции допускают несколько параметров конфигурации:

    features:
        - blackfire:
            server_id: "server_id"
            server_token: "server_value"
            client_id: "client_id"
            client_token: "client_value"
        - cassandra: true
        - chronograf: true
        - couchdb: true
        - crystal: true
        - docker: true
        - elasticsearch:
            version: 7.9.0
        - eventstore: true
            version: 21.2.0
        - gearman: true
        - golang: true
        - grafana: true
        - influxdb: true
        - mariadb: true
        - meilisearch: true
        - minio: true
        - mongodb: true
        - neo4j: true
        - ohmyzsh: true
        - openresty: true
        - pm2: true
        - python: true
        - r-base: true
        - rabbitmq: true
        - rvm: true
        - solr: true
        - timescaledb: true
        - trader: true
        - webdriver: true

<a name="elasticsearch"></a>
#### Elasticsearch

Вы можете указать поддерживаемую версию Elasticsearch, которая должна быть точным номером версии (major.minor.patch). При установке по умолчанию будет создан кластер с именем «homestead». Никогда не следует отдавать Elasticsearch больше половины памяти операционной системы, поэтому убедитесь, что на Вашей виртуальной машине Homestead выделено как минимум вдвое больше памяти Elasticsearch.

> {tip} Ознакомьтесь с [документацией Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current), чтобы узнать, как настроить свою конфигурацию.

<a name="mariadb"></a>
#### MariaDB

Включение MariaDB удалит MySQL и установит MariaDB. MariaDB обычно служит заменой MySQL, поэтому Вам все равно следует использовать драйвер базы данных `mysql` в конфигурации базы данных Вашего приложения.

<a name="mongodb"></a>
#### MongoDB

При установке MongoDB по умолчанию для имени пользователя базы данных будет установлено значение `homestead`, а для соответствующего пароля - `secret`.

<a name="neo4j"></a>
#### Neo4j

При установке Neo4j по умолчанию для имени пользователя базы данных будет задано `homestead`, а для соответствующего пароля `secret`. Чтобы получить доступ к браузеру Neo4j, зайдите на сайт `http://homestead.test:7474` в своем браузере. Порты `7687` (Bolt), `7474` (HTTP), and `7473` (HTTPS) готовы обслуживать запросы от клиента Neo4j.

<a name="aliases"></a>
### Псевдонимы

Вы можете добавить псевдонимы Bash на свою виртуальную машину Homestead, изменив файл `aliases` в каталоге Homestead:

    alias c='clear'
    alias ..='cd ..'

После того, как Вы обновили файл `aliases`, Вы должны повторно подготовить виртуальную машину Homestead с помощью команды `vagrant reload --provision`. Это обеспечит доступность Ваших новых псевдонимов на машине.

<a name="updating-homestead"></a>
## Обновление Homestead

Прежде чем начать обновление Homestead, Вы должны убедиться, что удалили текущую виртуальную машину, выполнив следующую команду в каталоге Homestead:

    vagrant destroy

Затем Вам нужно обновить исходный код Homestead. Если Вы клонировали репозиторий, Вы можете выполнить следующие команды в том месте, где Вы изначально клонировали репозиторий:

    git fetch

    git pull origin release

Эти команды извлекают последний код Homestead из репозитория GitHub, извлекают последние теги, а затем проверяют последний выпуск с тегами. Вы можете найти последнюю стабильную версию выпуска на [странице выпусков GitHub](https://github.com/laravel/homestead/releases) Homestead.

Если вы установили Homestead через файл `composer.json` вашего проекта, вы должны убедиться, что ваш файл `composer.json` содержит `"laravel/homestead": "^12"` и обновите ваши зависимости:

    composer update

Затем Вы должны обновить поле Vagrant с помощью команды `vagrant box update`:

    vagrant box update

После обновления окна Vagrant Вы должны запустить команду `bash init.sh` из каталога Homestead, чтобы обновить дополнительные файлы конфигурации Homestead. Вас спросят, хотите ли Вы перезаписать существующие файлы `Homestead.yaml`, `after.sh` и `aliases`:

    // macOS / Linux...
    bash init.sh

    // Windows...
    init.bat

Наконец, вам нужно будет регенерировать вашу виртуальную машину Homestead, чтобы использовать последнюю версию Vagrant:

    vagrant up

<a name="daily-usage"></a>
## Ежедневное использование

<a name="connecting-via-ssh"></a>
### Подключение через SSH

Вы можете подключиться к своей виртуальной машине по SSH, выполнив команду терминала `vagrant ssh` из каталога Homestead.

<a name="adding-additional-sites"></a>
### Добавление дополнительных сайтов

После того, как Ваша среда Homestead подготовлена и запущена, Вы можете добавить дополнительные сайты Nginx для других Ваших проектов Laravel. Вы можете запускать столько проектов Laravel, сколько хотите, в одной среде Homestead. Чтобы добавить дополнительный сайт, добавьте его в свой файл `Homestead.yaml`.

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public
        - map: another.test
          to: /home/vagrant/project2/public

> {note} Вы должны убедиться, что Вы настроили [сопоставление папок](#configuring-shared-folders) для каталога проекта, прежде чем добавлять сайт.

Если Vagrant не управляет Вашим файлом «hosts» автоматически, Вам может потребоваться также добавить новый сайт в этот файл. В macOS и Linux этот файл находится в `/etc/hosts`. В Windows он находится в `C:\Windows\System32\drivers\etc\hosts`:

    192.168.10.10  homestead.test
    192.168.10.10  another.test

После добавления сайта выполните команду терминала `vagrant reload --provision` из каталога Homestead.

<a name="site-types"></a>
#### Типы сайтов

Homestead поддерживает несколько «типов» сайтов, которые позволяют легко запускать проекты, не основанные на Laravel. Например, мы можем легко добавить приложение Statamic в Homestead, используя тип сайта `statamic`:

```yaml
sites:
    - map: statamic.test
      to: /home/vagrant/my-symfony-project/web
      type: "statamic"
```

Доступные типы сайтов: `apache`, `apigility`, `expressive`, `laravel` (по умолчанию), `proxy`, `silverstripe`, `statamic`, `symfony2`, `symfony4` и `zf`.

<a name="site-parameters"></a>
#### Параметры сайта

Вы можете добавить дополнительные значения Nginx `fastcgi_param` на свой сайт с помощью директивы сайта `params`:

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public
          params:
              - key: FOO
                value: BAR

<a name="environment-variables"></a>
### Переменные среды

Вы можете определить глобальные переменные среды, добавив их в свой файл `Homestead.yaml`:

    variables:
        - key: APP_ENV
          value: local
        - key: FOO
          value: bar

После обновления файла `Homestead.yaml` не забудьте повторно подготовить машину, выполнив команду `vagrant reload --provision`. Это обновит конфигурацию PHP-FPM для всех установленных версий PHP, а также обновит среду для пользователя `vagrant`.

<a name="ports"></a>
### Порты

По умолчанию в среду Homestead перенаправляются следующие порты:

<div class="content-list" markdown="1">
- **SSH:** 2222 &rarr; перенаправляет на 22
- **ngrok UI:** 4040 &rarr; перенаправляет на 4040
- **HTTP:** 8000 &rarr; перенаправляет на 80
- **HTTPS:** 44300 &rarr; перенаправляет на 443
- **MySQL:** 33060 &rarr; перенаправляет на 3306
- **PostgreSQL:** 54320 &rarr; перенаправляет на 5432
- **MongoDB:** 27017 &rarr; перенаправляет на 27017
- **Mailhog:** 8025 &rarr; перенаправляет на 8025
- **Minio:** 9600 &rarr; перенаправляет на 9600
</div>

<a name="forwarding-additional-ports"></a>
#### Перенаправление дополнительных портов

Если хотите, Вы можете перенаправить дополнительные порты в поле Vagrant, указав конфигурационную запись `ports` в Вашем файле `Homestead.yaml`. После обновления файла `Homestead.yaml` не забудьте повторно подготовить машину, выполнив команду `vagrant reload --provision`:

    ports:
        - send: 50000
          to: 5000
        - send: 7777
          to: 777
          protocol: udp

<a name="php-versions"></a>
### Версии PHP

В Homestead 6 появилась поддержка запуска нескольких версий PHP на одной виртуальной машине. Вы можете указать, какую версию PHP использовать для данного сайта в файле `Homestead.yaml`. Доступные версии PHP: "5.6", "7.0", "7.1", "7.2", "7.3", "7.4" и "8.0" (по умолчанию):

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public
          php: "7.1"

[На вашей виртуальной машине Homestead](#connecting-via-ssh) Вы можете использовать любую из поддерживаемых версий PHP через интерфейс командной строки:

    php5.6 artisan list
    php7.0 artisan list
    php7.1 artisan list
    php7.2 artisan list
    php7.3 artisan list
    php7.4 artisan list
    php8.0 artisan list

Вы можете изменить версию PHP по умолчанию, используемую CLI, выполнив следующие команды на своей виртуальной машине Homestead:

    php56
    php70
    php71
    php72
    php73
    php74
    php80

<a name="connecting-to-databases"></a>
### Подключение к базам данных

База данных `homestead` настроена как для MySQL, так и для PostgreSQL из коробки. Чтобы подключиться к базе данных MySQL или PostgreSQL из клиента базы данных хоста, Вы должны подключиться к `127.0.0.1` через порт `33060` (MySQL) или `54320` (PostgreSQL). Имя пользователя и пароль для обеих баз данных: `homestead` / `secret`.

> {note} Вы должны использовать эти нестандартные порты только при подключении к базам данных с Вашего хост-компьютера. Вы будете использовать порты 3306 и 5432 по умолчанию в файле конфигурации Вашего приложения Laravel `database`, поскольку Laravel работает _внутри_ виртуальной машины.

<a name="database-backups"></a>
### Резервные копии базы данных

Homestead может автоматически создавать резервную копию Вашей базы данных, когда Ваша виртуальная машина Homestead будет уничтожена. Чтобы использовать эту функцию, Вы должны использовать Vagrant 2.1.0 или выше. Или, если Вы используете старую версию Vagrant, Вы должны установить плагин `vagrant-triggers`. Чтобы включить автоматическое резервное копирование базы данных, добавьте следующую строку в Ваш файл `Homestead.yaml`:

    backup: true

После настройки Homestead будет экспортировать Ваши базы данных в каталоги `mysql_backup` и `postgres_backup` при выполнении команды `vagrant destroy`. Эти каталоги можно найти в папке, в которую Вы установили Homestead, или в корне Вашего проекта, если Вы используете метод [установка для каждого проекта](#per-project-installation).

<a name="database-snapshots"></a>
### Снимки базы данных

Homestead поддерживает замораживание состояния баз данных MySQL и MariaDB и переход между ними с помощью [Logical MySQL Manager](https://github.com/Lullabot/lmm). Например, представьте, что Вы работаете на сайте с многогигабайтной базой данных. Вы можете импортировать базу данных и сделать снимок. После некоторой работы и создания некоторого тестового содержимого локально Вы можете быстро восстановить исходное состояние.

Под капотом LMM использует функцию тонких снимков LVM с поддержкой копирования при записи. На практике это означает, что изменение одной строки в таблице приведет к записи только внесенных Вами изменений на диск, что значительно сэкономит время и дисковое пространство во время восстановления.

Поскольку LMM взаимодействует с LVM, он должен запускаться как `root`. Чтобы увидеть все доступные команды, запустите команду `sudo lmm` в поле Vagrant. Обычный рабочий процесс выглядит следующим образом:

- Импортируйте базу данных в ветку lmm по умолчанию `master`.
- Сохраните снимок неизмененной базы данных с помощью `sudo lmm branch prod-YYYY-MM-DD`.
- Измените базу данных.
- Запустите `sudo lmm merge prod-YYYY-MM-DD`, чтобы отменить все изменения.
- Запустите `sudo lmm delete <branch>`, чтобы удалить ненужные ветки.

<a name="configuring-cron-schedules"></a>
### Настройка расписаний Cron

Laravel предоставляет удобный способ [запланировать задания cron](/docs/{{version}}/scheduling), запланировав выполнение одной Artisan-команды `schedule:run` каждую минуту. Команда `schedule:run` проверяет расписание заданий, определенное в Вашем классе `App\Console\Kernel`, чтобы определить, какие запланированные задачи нужно запустить.

Если Вы хотите, чтобы команда `schedule:run` запускалась для сайта Homestead, Вы можете установить для параметра `schedule` значение `true` при определении сайта:

```yaml
sites:
    - map: homestead.test
      to: /home/vagrant/project1/public
      schedule: true
```

Задание cron для сайта будет определено в каталоге `/etc/cron.d` виртуальной машины Homestead.

<a name="configuring-mailhog"></a>
### Настройка MailHog

[MailHog](https://github.com/mailhog/MailHog) позволяет Вам перехватывать исходящую электронную почту и проверять ее, не отправляя ее получателям. Для начала обновите файл `.env` Вашего приложения, чтобы использовать следующие настройки почты:

    MAIL_MAILER=smtp
    MAIL_HOST=localhost
    MAIL_PORT=1025
    MAIL_USERNAME=null
    MAIL_PASSWORD=null
    MAIL_ENCRYPTION=null

После настройки MailHog Вы можете получить доступ к панели управления MailHog по адресу `http://localhost:8025`.

<a name="configuring-minio"></a>
### Настройка Minio

[Minio](https://github.com/minio/minio) - сервер хранения объектов с открытым исходным кодом и API, совместимый с Amazon S3. Чтобы установить Minio, обновите файл `Homestead.yaml`, указав следующую опцию конфигурации в разделе [функции](#installing-optional-features):

    minio: true

По умолчанию Minio доступен через порт 9600. Вы можете получить доступ к панели управления Minio, посетив `http://localhost:9600`. Ключ доступа по умолчанию - `homestead`, а секретный ключ по умолчанию - `secretkey`. При доступе к Minio Вы всегда должны использовать регион `us-east-1`.

Чтобы использовать Minio, вам нужно будет настроить конфигурацию диска S3 в файле конфигурации вашего приложения `config/filesystems.php`. Вам нужно будет добавить параметр `use_path_style_endpoint` в конфигурацию диска, а также изменить ключ `url` на `endpoint`:

    's3' => [
        'driver' => 's3',
        'key' => env('AWS_ACCESS_KEY_ID'),
        'secret' => env('AWS_SECRET_ACCESS_KEY'),
        'region' => env('AWS_DEFAULT_REGION'),
        'bucket' => env('AWS_BUCKET'),
        'endpoint' => env('AWS_URL'),
        'use_path_style_endpoint' => true,
    ]

Наконец, убедитесь, что Ваш файл `.env` имеет следующие параметры:

```bash
AWS_ACCESS_KEY_ID=homestead
AWS_SECRET_ACCESS_KEY=secretkey
AWS_DEFAULT_REGION=us-east-1
AWS_URL=http://localhost:9600
```

Чтобы подготовить ведра "S3" на базе Minio, добавьте директиву `buckets` в файл `Homestead.yaml`. После определения Ваших корзин Вы должны выполнить команду `vagrant reload --provision` в вашем терминале:

```yaml
buckets:
    - name: your-bucket
      policy: public
    - name: your-private-bucket
      policy: none
```

Supported `policy` values include: `none`, `download`, `upload`, and `public`.

<a name="laravel-dusk"></a>
### Laravel Dusk

Чтобы запустить тесты [Laravel Dusk](/docs/{{version}}/dusk) в Homestead, Вы должны включить [ функцию `webdriver`](#installing-optional-features) в Вашей конфигурации Homestead:

```yaml
features:
    - webdriver: true
```

После включения функции `webdriver` Вы должны выполнить команду `vagrant reload --provision` в Вашем терминале.

<a name="sharing-your-environment"></a>
### Совместное использование Вашей среды

Иногда Вы можете поделиться тем, над чем Вы сейчас работаете, с коллегами или клиентом. Vagrant имеет встроенную поддержку для этого с помощью команды `vagrant share`; однако это не сработает, если в Вашем файле `Homestead.yaml` настроено несколько сайтов.

Чтобы решить эту проблему, Homestead включает собственную команду `share`. Для начала [SSH в Вашу виртуальную машину Homestead](#connecting-via-ssh) через `vagrant ssh` и выполните команду `share homestead.test`. Эта команда предоставит общий доступ к сайту `homestead.test` из Вашего файла конфигурации `Homestead.yaml`. Вы можете заменить любой из других настроенных Вами сайтов на `homestead.test`:

    share homestead.test

После выполнения команды Вы увидите экран Ngrok, который содержит журнал активности и общедоступные URL-адреса для общего сайта. Если Вы хотите указать настраиваемый регион, поддомен или другую опцию выполнения Ngrok, Вы можете добавить их в свою команду `share`:

    share homestead.test -region=eu -subdomain=laravel

> {note} Помните, что Vagrant по своей сути небезопасен, и Вы открываете свою виртуальную машину в Интернете, выполняя команду `share`.

<a name="debugging-and-profiling"></a>
## Отладка и профилирование

<a name="debugging-web-requests"></a>
### Отладка веб-запросов с помощью Xdebug

Homestead включает поддержку пошаговой отладки с использованием [Xdebug](https://xdebug.org). Например, Вы можете получить доступ к странице в своем браузере, и PHP подключится к Вашей IDE, чтобы разрешить проверку и изменение выполняемого кода.

По умолчанию Xdebug уже запущен и готов принимать соединения. Если Вам нужно включить Xdebug в CLI, выполните команду `sudo phpenmod xdebug` на своей виртуальной машине Homestead. Затем следуйте инструкциям Вашей IDE, чтобы включить отладку. Наконец, настройте свой браузер на запуск Xdebug с расширением или [bookmarklet](https://www.jetbrains.com/phpstorm/marklets/).

> {note} Xdebug заставляет PHP работать значительно медленнее. Чтобы отключить Xdebug, запустите `sudo phpdismod xdebug` на своей виртуальной машине Homestead и перезапустите службу FPM.

<a name="autostarting-xdebug"></a>
#### Автозапуск Xdebug

При отладке функциональных тестов, которые отправляют запросы к веб-серверу, проще автоматически запускать отладку, чем изменять тесты для прохождения через настраиваемый заголовок или файл cookie для запуска отладки. Чтобы заставить Xdebug запускаться автоматически, измените файл `/etc/php/7.x/fpm/conf.d/20-xdebug.ini` внутри Вашей виртуальной машины Homestead и добавьте следующую конфигурацию:

```ini
; Если Homestead.yaml содержит другую подсеть для IP-адреса, этот адрес может быть другим...
xdebug.remote_host = 192.168.10.1
xdebug.remote_autostart = 1
```

<a name="debugging-cli-applications"></a>
### Отладка приложений CLI

Чтобы отладить приложение PHP CLI, используйте псевдоним оболочки `xphp` внутри Вашей виртуальной машины Homestead:

    xphp /path/to/script

<a name="profiling-applications-with-blackfire"></a>
### Профилирование приложений с помощью Blackfire

[Blackfire](https://blackfire.io/docs/introduction) - это сервис для профилирования веб-запросов и приложений CLI. Он предлагает интерактивный пользовательский интерфейс, который отображает данные профиля в виде графиков вызовов и временных шкал. Он создан для использования в разработке, тестировании и производстве без дополнительных затрат для конечных пользователей. Кроме того, Blackfire обеспечивает проверку производительности, качества и безопасности кода и параметров конфигурации `php.ini`.

[Blackfire Player](https://blackfire.io/docs/player/index) - это приложение с открытым исходным кодом для веб-сканирования, веб-тестирования и веб-скрапинга, которое может работать вместе с Blackfire для создания сценариев профилирования сценариев.

Чтобы включить Blackfire, используйте параметр «features» в файле конфигурации Homestead:

```yaml
features:
    - blackfire:
        server_id: "server_id"
        server_token: "server_value"
        client_id: "client_id"
        client_token: "client_value"
```

Учетные данные сервера Blackfire и учетные данные клиента [требуется учетная запись Blackfire](https://blackfire.io/signup). Blackfire предлагает различные варианты профилирования приложения, включая инструмент командной строки и расширение для браузера. Пожалуйста [просмотрите документацию Blackfire для получения дополнительной информации](https://blackfire.io/docs/cookbooks/index).

<a name="network-interfaces"></a>
## Сетевые интерфейсы

Свойство `networks` файла `Homestead.yaml` настраивает сетевые интерфейсы для Вашей виртуальной машины Homestead. Вы можете настроить столько интерфейсов, сколько необходимо:

```yaml
networks:
    - type: "private_network"
      ip: "192.168.10.20"
```

Чтобы включить интерфейс [bridged](https://www.vagrantup.com/docs/networking/public_network.html), настройте параметр `bridge` для сети и измените тип сети на `public_network`:

```yaml
networks:
    - type: "public_network"
      ip: "192.168.10.20"
      bridge: "en1: Wi-Fi (AirPort)"
```

Чтобы включить [DHCP](https://www.vagrantup.com/docs/networking/public_network.html), просто удалите опцию `ip` из Вашей конфигурации:

```yaml
networks:
    - type: "public_network"
      bridge: "en1: Wi-Fi (AirPort)"
```

<a name="extending-homestead"></a>
## Расширение Homestead

Вы можете расширить Homestead, используя сценарий `after.sh` в корне Вашего каталога Homestead. В этот файл Вы можете добавить любые команды оболочки, которые необходимы для правильной настройки и настройки Вашей виртуальной машины.

При настройке Homestead Ubuntu может спросить Вас, хотите ли Вы сохранить исходную конфигурацию пакета или перезаписать ее новым файлом конфигурации. Чтобы избежать этого, Вы должны использовать следующую команду при установке пакетов, чтобы избежать перезаписи любой конфигурации, ранее написанной Homestead:

    sudo apt-get -y \
        -o Dpkg::Options::="--force-confdef" \
        -o Dpkg::Options::="--force-confold" \
        install package-name

<a name="user-customizations"></a>
### Пользовательские настройки

При использовании Homestead со своей командой Вы можете настроить Homestead, чтобы он лучше соответствовал Вашему личному стилю разработки. Для этого Вы можете создать файл `user-customizations.sh` в корне Вашего каталога Homestead (тот же каталог, где находится Ваш файл `Homestead.yaml`). В этом файле Вы можете сделать любую настройку, которую захотите; однако версия файла `user-customizations.sh` не должна контролироваться.

<a name="provider-specific-settings"></a>
## Настройки для конкретного провайдера

<a name="provider-specific-virtualbox"></a>
### VirtualBox

<a name="natdnshostresolver"></a>
#### `natdnshostresolver`

По умолчанию Homestead устанавливает для параметра `natdnshostresolver` значение `on`. Это позволяет Homestead использовать настройки DNS Вашей операционной системы. Если Вы хотите изменить это поведение, добавьте следующие параметры конфигурации в Ваш файл `Homestead.yaml`:

```yaml
provider: virtualbox
natdnshostresolver: 'off'
```

<a name="symbolic-links-on-windows"></a>
#### Символические ссылки в Windows

Если символические ссылки не работают должным образом на Вашем компьютере с Windows, Вам может потребоваться добавить следующий блок в Ваш `Vagrantfile`:

```ruby
config.vm.provider "virtualbox" do |v|
    v.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]
end
```
