# Laravel Homestead

- [Вступление](#introduction)
- [Установка и настройка](#installation-and-setup)
    - [Первые шаги](#first-steps)
    - [Настройка Homestead](#configuring-homestead)
    - [Запуск коробки Vagrant](#launching-the-vagrant-box)
    - [Установка в проект](#per-project-installation)
    - [Установка дополнительных функций](#installing-optional-features)
    - [Псевдонимы](#aliases)
- [Ежедневное использование](#daily-usage)
    - [Глобальный доступ к Homestead](#accessing-homestead-globally)
    - [Подключение через SSH](#connecting-via-ssh)
    - [Подключение к базам данных](#connecting-to-databases)
    - [Резервные копии базы данных](#database-backups)
    - [Снимки базы данных](#database-snapshots)
    - [Добавление дополнительных сайтов](#adding-additional-sites)
    - [Переменные среды](#environment-variables)
    - [Wildcard SSL](#wildcard-ssl)
    - [Настройка расписаний Cron](#configuring-cron-schedules)
    - [Настройка Mailhog](#configuring-mailhog)
    - [Настройка Minio](#configuring-minio)
    - [Порты](#ports)
    - [Совместное использование Вашего окружения](#sharing-your-environment)
    - [Несколько версий PHP](#multiple-php-versions)
    - [Веб-серверы](#web-servers)
    - [Почта](#mail)
    - [Laravel Dusk](#laravel-dusk)
- [Отладка и профилирование](#debugging-and-profiling)
    - [Отладка веб-запросов с помощью Xdebug](#debugging-web-requests)
    - [Отладка CLI-приложений](#debugging-cli-applications)
    - [Профилирование приложений с помощью Blackfire](#profiling-applications-with-blackfire)
- [Сетевые интерфейсы](#network-interfaces)
- [Расширение Homestead](#extending-homestead)
- [Обновление Homestead](#updating-homestead)
- [Настройки для конкретного поставщика](#provider-specific-settings)
    - [VirtualBox](#provider-specific-virtualbox)

<a name="introduction"></a>
## Вступление

Laravel стремится сделать весь процесс разработки PHP приятным, включая Вашу локальную среду разработки. [Vagrant](https://www.vagrantup.com) предоставляет простой и элегантный способ управления виртуальными машинами и их подготовки.

Laravel Homestead - это официальный предварительно упакованный пакет Vagrant, который предоставляет Вам прекрасную среду разработки, не требующую установки PHP, веб-сервера и любого другого серверного программного обеспечения на Вашем локальном компьютере. Больше не нужно беспокоиться о том, что можно загрязнить Вашу операционную систему! Коробки Vagrant полностью доступны. Если что-то пойдет не так, Вы можете уничтожить и воссоздать коробку за считанные минуты!

Homestead работает в любой системе Windows, Mac или Linux и включает Nginx, PHP, MySQL, PostgreSQL, Redis, Memcached, Node и все другие полезности, необходимые для разработки потрясающих приложений Laravel.

> {note} Если Вы используете Windows, Вам может потребоваться включить аппаратную виртуализацию (VT-x). Обычно это можно включить в BIOS. Если Вы используете Hyper-V в системе UEFI, Вам может потребоваться дополнительно отключить Hyper-V, чтобы получить доступ к VT-x.

<a name="included-software"></a>
### Включенное программное обеспечение

<style>
    #software-list > ul {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 5em; -moz-column-gap: 5em; -webkit-column-gap: 5em;
        line-height: 1.9;
    }
</style>

<div id="software-list" markdown="1">
- Ubuntu 18.04 (`master` ветка)
- Ubuntu 20.04 (`20.04` ветка)
- Git
- PHP 8.0
- PHP 7.4
- PHP 7.3
- PHP 7.2
- PHP 7.1
- PHP 7.0
- PHP 5.6
- Nginx
- MySQL
- lmm для снимков базы данных MySQL или MariaDB
- Sqlite3
- PostgreSQL (9.6, 10, 11, 12)
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
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
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
- Gearman
- Go
- Grafana
- InfluxDB
- MariaDB
- MinIO
- MongoDB
- MySQL 8
- Neo4j
- Oh My Zsh
- Open Resty
- PM2
- Python
- RabbitMQ
- Solr
- Webdriver & Laravel Dusk Utilities
</div>

<a name="installation-and-setup"></a>
## Установка и настройка

<a name="first-steps"></a>
### Первые шаги

Перед запуском среды Homestead необходимо установить [VirtualBox 6.x](https://www.virtualbox.org/wiki/Downloads), [VMWare](https://www.vmware.com), [Parallels](https://www.parallels.com/products/desktop/) или [Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v), а также [Vagrant](https://www.vagrantup.com/downloads.html). Все эти программные пакеты предоставляют простые в использовании визуальные установщики для всех популярных операционных систем.

Чтобы использовать поставщика VMware, Вам необходимо приобрести как VMware Fusion / Workstation, так и [плагин VMware Vagrant](https://www.vagrantup.com/vmware). Хотя это не бесплатно, VMware может обеспечить более быструю работу общих папок из коробки.

Чтобы использовать поставщика Parallels, вам необходимо установить [плагин Parallels Vagrant](https://github.com/Parallels/vagrant-parallels). Это бесплатно.

Из-за [ограничений Vagrant](https://www.vagrantup.com/docs/hyperv/limitations.html) провайдер Hyper-V игнорирует все сетевые настройки.

<a name="installing-the-homestead-vagrant-box"></a>
#### Установка Homestead Vagrant Box

После установки VirtualBox / VMware и Vagrant Вы должны добавить поле `laravel/homestead` в Вашу установку Vagrant, используя следующую команду в Вашем терминале. Загрузка коробки займет несколько минут, в зависимости от скорости Вашего интернет-соединения:

    vagrant box add laravel/homestead

Если эта команда не работает, убедитесь, что Ваша установка Vagrant обновлена.

> {note} Homestead периодически выдает для тестирования блоки "alpha" / "beta", которые могут мешать команде `vagrant box add`. Если у Вас возникли проблемы с запуском `vagrant box add`, вы можете запустить команду `vagrant up`, и правильный ящик будет загружен, когда Vagrant попытается запустить виртуальную машину.

<a name="installing-homestead"></a>
#### Установка Homestead

Вы можете установить Homestead, клонировав репозиторий на свой хост-компьютер. Рассмотрите возможность клонирования репозитория в папку `Homestead` в Вашем «домашнем» каталоге, так как поле Homestead будет служить хостом для всех Ваших проектов Laravel:

    git clone https://github.com/laravel/homestead.git ~/Homestead

Вам следует проверить версию Homestead с тегами, поскольку ветка `master` не всегда может быть стабильной. Вы можете найти последнюю стабильную версию на [странице выпуска GitHub](https://github.com/laravel/homestead/releases). Как вариант, Вы можете проверить ветку `release`, которая всегда содержит последнюю стабильную версию:

    cd ~/Homestead

    git checkout release

После клонирования репозитория Homestead запустите команду `bash init.sh` из каталога Homestead, чтобы создать файл конфигурации `Homestead.yaml`. Файл `Homestead.yaml` будет помещен в каталог Homestead:

    // Mac / Linux...
    bash init.sh

    // Windows...
    init.bat

<a name="configuring-homestead"></a>
### Настройка Homestead

<a name="setting-your-provider"></a>
#### Настройка вашего провайдера

Ключ `provider` в Вашем файле `Homestead.yaml` указывает, какой провайдер Vagrant следует использовать: `virtualbox`, `vmware_fusion`, `vmware_workstation`, `parallels` или `hyperv`. Вы можете выбрать поставщика, который предпочитаете:

    provider: virtualbox

<a name="configuring-shared-folders"></a>
#### Настройка общих папок

Свойство `folders` файла `Homestead.yaml` перечисляет все папки, которыми вы хотите поделиться со своей средой Homestead. При изменении файлов в этих папках они будут синхронизироваться между Вашим локальным компьютером и средой Homestead. Вы можете настроить столько общих папок, сколько необходимо:

    folders:
        - map: ~/code/project1
          to: /home/vagrant/project1

> {note} Пользователи Windows не должны использовать синтаксис пути `~/`, а вместо этого должны использовать полный путь к своему проекту, например `C:\Users\user\Code\project1`.

Вы всегда должны сопоставлять отдельные проекты с их собственным сопоставлением папок вместо сопоставления всей папки `~/code`. Когда Вы сопоставляете папку, виртуальная машина должна отслеживать все операции ввода-вывода диска для *каждого* файла в папке. Это приводит к проблемам с производительностью, если в папке много файлов.

    folders:
        - map: ~/code/project1
          to: /home/vagrant/project1

        - map: ~/code/project2
          to: /home/vagrant/project2

> {note} Вы никогда не должны монтировать `.` (текущий каталог) при использовании Homestead. Это приводит к тому, что Vagrant не отображает текущую папку в `/vagrant`, что нарушает работу дополнительных функций и приводит к неожиданным результатам во время подготовки.

Чтобы включить [NFS](https://www.vagrantup.com/docs/synced-folders/nfs.html), Вам нужно только добавить простой флаг в конфигурацию синхронизируемой папки:

    folders:
        - map: ~/code/project1
          to: /home/vagrant/project1
          type: "nfs"

> {note} При использовании NFS в Windows Вам следует подумать об установке плагина [vagrant-winnfsd](https://github.com/winnfsd/vagrant-winnfsd). Этот плагин будет поддерживать правильные права пользователя / группы для файлов и каталогов в поле Homestead.

Вы также можете передать любые параметры, поддерживаемые [Синхронизировавшие папки Vagrant](https://www.vagrantup.com/docs/synced-folders/basic_usage.html), указав их под ключом `options`:

    folders:
        - map: ~/code/project1
          to: /home/vagrant/project1
          type: "rsync"
          options:
              rsync__args: ["--verbose", "--archive", "--delete", "-zz"]
              rsync__exclude: ["node_modules"]

<a name="configuring-nginx-sites"></a>
#### Настройка сайтов Nginx

Не знаком с Nginx? Нет проблем. Свойство `sites` позволяет Вам легко сопоставить «домен» с папкой в Вашей среде Homestead. Пример конфигурации сайта включен в файл `Homestead.yaml`. Опять же, Вы можете добавить в среду Homestead столько сайтов, сколько необходимо. Homestead может служить удобной виртуализированной средой для каждого проекта Laravel, над которым Вы работаете:

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public

Если Вы измените свойство `sites` после подготовки поля Homestead, Вам следует повторно запустить `vagrant reload --provision`, чтобы обновить конфигурацию Nginx на виртуальной машине.

> {note} Скрипты Homestead построены так, чтобы быть максимально идемпотентными. Однако, если у Вас возникли проблемы при инициализации, Вам следует уничтожить и перестроить машину с помощью `vagrant destroy && vagrant up`.

<a name="enable-disable-services"></a>
#### Включение / отключение служб

По умолчанию Homestead запускает несколько сервисов; однако Вы можете настроить, какие службы будут включены или отключены во время подготовки. Например, Вы можете включить PostgreSQL и отключить MySQL:

    services:
        - enabled:
            - "postgresql@12-main"
        - disabled:
            - "mysql"

Указанные службы будут запускаться или останавливаться в зависимости от их порядка в директивах `enabled` b `disabled`.

<a name="hostname-resolution"></a>
#### Разрешение имени хоста

Homestead публикует имена хостов через `mDNS` для автоматического разрешения хоста. Если Вы установите `hostname: homestead` в Вашем файле `Homestead.yaml`, хост будет доступен по адресу `homestead.local`. Настольные дистрибутивы MacOS, iOS и Linux по умолчанию включают поддержку `mDNS`. Windows требует установки [Bonjour Print Services для Windows](https://support.apple.com/kb/DL999?viewlocale=en_US&locale=en_US).

Использование автоматических имен хостов лучше всего подходит для установок Homestead «на проект». Если Вы размещаете несколько сайтов на одном экземпляре Homestead, Вы можете добавить «домены» для своих веб-сайтов в файл `hosts` на Вашем компьютере. Файл `hosts` будет перенаправлять запросы для Ваших сайтов Homestead на Ваш компьютер Homestead. В Mac и Linux этот файл находится в `/etc/hosts`. В Windows он находится в `C:\Windows\System32\drivers\etc\hosts`. Строки, которые Вы добавляете в этот файл, будут выглядеть следующим образом:

    192.168.10.10  homestead.test

Убедитесь, что в списке указан IP-адрес, указанный в Вашем файле `Homestead.yaml`. После того, как Вы добавили домен в свой файл `hosts` и запустили окно Vagrant, Вы сможете получить доступ к сайту через свой веб-браузер:

    http://homestead.test

<a name="launching-the-vagrant-box"></a>
### Запуск коробки Vagrant

После того как Вы отредактировали файл `Homestead.yaml` по своему вкусу, запустите команду `vagrant up` из каталога Homestead. Vagrant загрузит виртуальную машину и автоматически настроит Ваши общие папки и сайты Nginx.

Чтобы уничтожить машину, Вы можете использовать команду `vagrant destroy --force`.

<a name="per-project-installation"></a>
### Установка в проект

Вместо того, чтобы устанавливать Homestead глобально и использовать один и тот же бокс Homestead для всех в=Ваших проектов, Вы можете вместо этого настроить экземпляр Homestead для каждого проекта, которым Вы управляете. Установка Homestead для каждого проекта может быть полезной, если Вы хотите отправить файл `Vagrantfile` вместе со своим проектом, позволяя другим, работающим над проектом `vagrant up`.

Чтобы установить Homestead прямо в свой проект, потребуйте его с помощью Composer:

    composer require laravel/homestead --dev

После установки Homestead используйте команду `make`, чтобы сгенерировать файлы `Vagrantfile` и `Homestead.yaml` в корне Вашего проекта. Команда `make` автоматически настроит директивы `sites` и `folders` в файле `Homestead.yaml`.

Mac / Linux:

    php vendor/bin/homestead make

Windows:

    vendor\\bin\\homestead make

Затем запустите команду `vagrant up` в Вашем терминале и войдите в свой проект по адресу `http://homestead.test` в Вашем браузере. Помните, что Вам все равно нужно будет добавить запись файла `/etc/hosts` для `homestead.test` или домена по вашему выбору, если вы не используете автоматическое [разрешение имени хоста](#hostname-resolution).

<a name="installing-optional-features"></a>
### Установка дополнительных функций

Дополнительное программное обеспечение устанавливается с помощью параметра «features» в файле конфигурации Homestead. Большинство функций можно включить или отключить с помощью логического значения, в то время как некоторые функции позволяют использовать несколько параметров конфигурации:

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
        - gearman: true
        - golang: true
        - grafana: true
        - influxdb: true
        - mariadb: true
        - minio: true
        - mongodb: true
        - mysql8: true
        - neo4j: true
        - ohmyzsh: true
        - openresty: true
        - pm2: true
        - python: true
        - rabbitmq: true
        - solr: true
        - webdriver: true

<a name="mariadb"></a>
#### MariaDB

Включение MariaDB удалит MySQL и установит MariaDB. MariaDB служит заменой MySQL, поэтому Вам все равно следует использовать драйвер базы данных mysql в конфигурации базы данных Вашего приложения.

<a name="mongodb"></a>
#### MongoDB

При установке MongoDB по умолчанию для имени пользователя базы данных будет установлено значение `homestead`, а для соответствующего пароля - `secret`.

<a name="elasticsearch"></a>
#### Elasticsearch

Вы можете указать поддерживаемую версию Elasticsearch, которая должна быть точным номером версии (major.minor.patch). При установке по умолчанию будет создан кластер с именем «homestead». Никогда не следует отдавать Elasticsearch больше половины памяти операционной системы, поэтому убедитесь, что на Вашем компьютере Homestead выделено как минимум вдвое больше памяти Elasticsearch.

> {tip} Ознакомьтесь с [документацией Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current), чтобы узнать, как настроить свою конфигурацию.

<a name="neo4j"></a>
#### Neo4j

При установке Neo4j по умолчанию в качестве имени пользователя базы данных будет указано `homestead`, а для соответствующего пароля -`secret`. Чтобы получить доступ к браузеру Neo4j, зайдите на сайт `http://homestead.test:7474` в своем браузере. Порты `7687` (Bolt), `7474` (HTTP) и `7473` (HTTPS) готовы обслуживать запросы от клиента Neo4j.

<a name="aliases"></a>
### Псевдонимы

Вы можете добавить псевдонимы Bash на свой компьютер Homestead, изменив файл `aliases` в каталоге Homestead:

    alias c='clear'
    alias ..='cd ..'

После того как Вы обновили файл `aliases`, Вы должны повторно подготовить машину Homestead с помощью команды `vagrant reload --provision`. Это обеспечит доступность Ваших новых псевдонимов на машине.

<a name="daily-usage"></a>
## Ежедневное использование

<a name="accessing-homestead-globally"></a>
### Глобальный доступ к Homestead

Иногда Вам может потребоваться `vagrant up` по Вашей машине Homestead из любой точки файловой системы. Вы можете сделать это в системах Mac / Linux, добавив функцию Bash в свой профиль Bash. В Windows Вы можете сделать это, добавив «пакетный» файл в Ваш `PATH`. Эти скрипты позволят Вам запускать любую команду Vagrant из любой точки Вашей системы и автоматически укажут эту команду на Вашу установку Homestead:

<a name="mac-linux"></a>
#### Mac / Linux

    function homestead() {
        ( cd ~/Homestead && vagrant $* )
    }

Убедитесь, что путь `~/Homestead` в функции соответствует месту Вашей фактической установки Homestead. После установки функции Вы можете запускать такие команды, как `homestead up` или `homestead ssh` из любого места в Вашей системе.

<a name="windows"></a>
#### Windows

Создайте командный файл `homestead.bat`, где угодно на Вашем компьютере со следующим содержимым:

    @echo off

    set cwd=%cd%
    set homesteadVagrant=C:\Homestead

    cd /d %homesteadVagrant% && vagrant %*
    cd /d %cwd%

    set cwd=
    set homesteadVagrant=

Не забудьте настроить пример пути `C:\Homestead` в скрипте на фактическое местоположение Вашей установки Homestead. После создания файла добавьте местоположение файла в Ваш `PATH`. Затем Вы можете запускать такие команды, как `homestead up` или `homestead ssh` из любой точки Вашей системы.

<a name="connecting-via-ssh"></a>
### Подключение через SSH

Вы можете подключиться к своей виртуальной машине по SSH, выполнив команду терминала `vagrant ssh` из каталога Homestead.

Но, поскольку Вам, вероятно, потребуется часто использовать SSH на Вашем компьютере Homestead, подумайте о добавлении «функции», описанной выше, на Ваш хост-компьютер, чтобы быстро использовать SSH в поле Homestead.

<a name="connecting-to-databases"></a>
### Подключение к базам данных

База данных `homestead` настроена как для MySQL, так и для PostgreSQL из коробки. Чтобы подключиться к базе данных MySQL или PostgreSQL из клиента базы данных Вашего хост-компьютера, Вы должны подключиться к `127.0.0.1` и порту `33060` (MySQL) или `54320` (PostgreSQL). Имя пользователя и пароль для обеих баз данных: `homestead` / `secret`.

> {note} Вы должны использовать эти нестандартные порты только при подключении к базам данных с Вашего хост-компьютера. Вы будете использовать порты 3306 и 5432 по умолчанию в файле конфигурации базы данных Laravel, поскольку Laravel работает _внутри_ виртуальной машины.

<a name="database-backups"></a>
### Резервные копии базы данных

Homestead может автоматически создавать резервную копию Вашей базы данных, когда Ваш Vagrant бокс уничтожается. Чтобы использовать эту функцию, Вы должны использовать Vagrant 2.1.0 или выше. Или, если Вы используете старую версию Vagrant, Вы должны установить плагин `vagrant-triggers`. Чтобы включить автоматическое резервное копирование базы данных, добавьте следующую строку в свой файл `Homestead.yaml` file:

    backup: true

После настройки Homestead будет экспортировать Ваши базы данных в каталоги `mysql_backup` и `postgres_backup` при выполнении команды `vagrant destroy`. Эти каталоги можно найти в папке, в которую Вы клонировали Homestead, или в корне Вашего проекта, если Вы используете метод [установка для каждого проекта](#per-project-installation).

<a name="database-snapshots"></a>
### Снимки базы данных

Homestead поддерживает замораживание состояния баз данных MySQL и MariaDB и переход между ними с помощью [Logical MySQL Manager](https://github.com/Lullabot/lmm). Например, представьте, что Вы работаете на сайте с многогигабайтной базой данных. Вы можете импортировать базу данных и сделать снимок. После некоторой работы и создания некоторого тестового содержимого локально Вы можете быстро восстановить исходное состояние.

Под капотом LMM использует функцию тонких снимков LVM с поддержкой копирования при записи. На практике это означает, что изменение одной строки в таблице приведет к записи на диск только внесенных Вами изменений, что значительно сэкономит время и дисковое пространство во время восстановления.

Поскольку `lmm` взаимодействует с LVM, он должен запускаться как `root`. Чтобы увидеть все доступные команды, запустите `sudo lmm` внутри Вашей Vagrant-коробки. Обычный рабочий процесс выглядит следующим образом:

1. Импортируйте базу данных в ветку lmm по умолчанию `master`
1. Сохраните снимок неизмененной базы данных с помощью `sudo lmm branch prod-YYYY-MM-DD`
1. Измените базу данных
1. Запустите `sudo lmm merge prod-YYYY-MM-DD`, чтобы отменить все изменения.
1. Запустите `sudo lmm delete <branch>`, чтобы удалить ненужные ветки.

<a name="adding-additional-sites"></a>
### Добавление дополнительных сайтов

После того, как Ваша среда Homestead подготовлена и запущена, Вы можете добавить дополнительные сайты Nginx для своих приложений Laravel. Вы можете запустить столько установок Laravel, сколько захотите, в одной среде Homestead. Чтобы добавить дополнительный сайт, добавьте его в файл `Homestead.yaml`:

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public
        - map: another.test
          to: /home/vagrant/project2/public

Если Vagrant не управляет Вашим файлом "hosts" автоматически, Вам может потребоваться добавить новый сайт в этот файл:

    192.168.10.10  homestead.test
    192.168.10.10  another.test

После добавления сайта запустите команду `vagrant reload --provision` из каталога Homestead.

<a name="site-types"></a>
#### Типы сайтов

Homestead поддерживает несколько типов сайтов, которые позволяют легко запускать проекты, не основанные на Laravel. Например, мы можем легко добавить приложение Symfony в Homestead, используя тип сайта `symfony2`:

    sites:
        - map: symfony2.test
          to: /home/vagrant/my-symfony-project/web
          type: "symfony2"

Доступные типы сайтов: `apache`, `apigility`, `expressive`, `laravel` (по умолчанию), `proxy`, `silverstripe`, `statamic`, `symfony2`, `symfony4` и `zf`.

<a name="site-parameters"></a>
#### Параметры сайта

Вы можете добавить дополнительные значения Nginx `fastcgi_param` на свой сайт с помощью директивы сайта `params`. Например, мы добавим параметр `FOO` со значением `BAR`:

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public
          params:
              - key: FOO
                value: BAR

<a name="environment-variables"></a>
### Переменные среды

Вы можете установить глобальные переменные окружения, добавив их в свой файл `Homestead.yaml`:

    variables:
        - key: APP_ENV
          value: local
        - key: FOO
          value: bar

После обновления `Homestead.yaml` не забудьте повторно подготовить машину, запустив `vagrant reload --provision`. Это обновит конфигурацию PHP-FPM для всех установленных версий PHP, а также обновит среду для пользователя `vagrant`.

<a name="wildcard-ssl"></a>
### Wildcard SSL

Homestead настраивает самоподписанный сертификат SSL для каждого сайта, определенного в разделе `sites:` Вашего файла `Homestead.yaml`. Если Вы хотите сгенерировать SSL-сертификат с подстановочными знаками для сайта, Вы можете добавить параметр `wildcard` в конфигурацию этого сайта. По умолчанию сайт будет использовать подстановочный сертификат *вместо* сертификата определенного домена:

    - map: foo.domain.test
      to: /home/vagrant/domain
      wildcard: "yes"

Если для параметра `use_wildcard` установлено значение `no`, то шаблонный сертификат будет сгенерирован, но не будет использоваться:

    - map: foo.domain.test
      to: /home/vagrant/domain
      wildcard: "yes"
      use_wildcard: "no"

<a name="configuring-cron-schedules"></a>
### Настройка расписаний Cron

Laravel предоставляет удобный способ [запланировать задания Cron](/docs/{{version}}/scheduling), запланировав выполнение единственной Artisan-команды `schedule:run` каждую минуту. Команда `schedule:run` проверяет расписание заданий, определенное в Вашем классе `App\Console\Kernel`, чтобы определить, какие задания следует запустить.

Если вы хотите, чтобы команда `schedule:run` запускалась для сайта Homestead, вы можете установить для параметра `schedule` значение `true` при определении сайта:

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public
          schedule: true

Задание Cron для сайта будет определено в папке `/etc/cron.d` виртуальной машины.

<a name="configuring-mailhog"></a>
### Настройка Mailhog

Mailhog позволяет Вам легко перехватывать исходящую электронную почту и исследовать ее, не отправляя ее получателям. Для начала обновите Ваш файл `.env`, чтобы использовать следующие настройки почты:

    MAIL_MAILER=smtp
    MAIL_HOST=localhost
    MAIL_PORT=1025
    MAIL_USERNAME=null
    MAIL_PASSWORD=null
    MAIL_ENCRYPTION=null

После настройки Mailhog Вы можете получить доступ к панели управления Mailhog по адресу `http://localhost:8025`.

<a name="configuring-minio"></a>
### Настройка Minio

Minio - это сервер хранения объектов с открытым исходным кодом и API, совместимый с Amazon S3. Чтобы установить Minio, обновите файл `Homestead.yaml`, указав следующую опцию конфигурации в разделе [функций](#installing-optional-features):

    minio: true

По умолчанию Minio доступен через порт 9600. Вы можете получить доступ к панели управления Minio, посетив `http://localhost:9600/`. Ключ доступа по умолчанию `homestead`, а секретный ключ по умолчанию - `secretkey`. При доступе к Minio Вы всегда должны использовать регион `us-east-1`.

Чтобы использовать Minio, Вам необходимо настроить конфигурацию диска S3 в файле конфигурации `config/filesystems.php`. Вам нужно будет добавить параметр `use_path_style_endpoint` в конфигурацию диска, а также изменить ключ `url` на `endpoint`:

    's3' => [
        'driver' => 's3',
        'key' => env('AWS_ACCESS_KEY_ID'),
        'secret' => env('AWS_SECRET_ACCESS_KEY'),
        'region' => env('AWS_DEFAULT_REGION'),
        'bucket' => env('AWS_BUCKET'),
        'endpoint' => env('AWS_URL'),
        'use_path_style_endpoint' => true,
    ]

Наконец, убедитесь, что в Вашем файле `.env` есть следующие параметры:

    AWS_ACCESS_KEY_ID=homestead
    AWS_SECRET_ACCESS_KEY=secretkey
    AWS_DEFAULT_REGION=us-east-1
    AWS_URL=http://localhost:9600

Чтобы подготовить корзины, добавьте директиву `buckets` в файл конфигурации Homestead:

    buckets:
        - name: your-bucket
          policy: public
        - name: your-private-bucket
          policy: none

Поддерживаемые значения `policy` включают в себя: `none`, `download`, `upload` и `public`.

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

При желании Вы можете перенаправить дополнительные порты в поле Vagrant, а также указать их протокол:

    ports:
        - send: 50000
          to: 5000
        - send: 7777
          to: 777
          protocol: udp

<a name="sharing-your-environment"></a>
### Совместное использование Вашего окружения

Иногда Вы можете поделиться тем, над чем Вы сейчас работаете, с коллегами или клиентом. У Vagrant есть встроенный способ поддержки этого с помощью `vagrant share`; однако это не сработает, если в Вашем файле `Homestead.yaml` настроено несколько сайтов.

Чтобы решить эту проблему, Homestead включает собственную команду `share`. Для начала подключитесь по SSH к Вашей машине Homestead через `vagrant ssh` и запустите `share homestead.test`. Это предоставит общий доступ к сайту `homestead.test` из Вашего файла конфигурации `Homestead.yaml`. Вы можете заменить любой из других настроенных Вами сайтов на `homestead.test`:

    share homestead.test

После выполнения команды Вы увидите экран Ngrok, который содержит журнал активности и общедоступные URL-адреса для общего сайта. Если Вы хотите указать настраиваемый регион, поддомен или другую опцию выполнения Ngrok, Вы можете добавить их в свою команду `share`:

    share homestead.test -region=eu -subdomain=laravel

> {note} Помните, что Vagrant по своей сути небезопасен, и Вы открываете свою виртуальную машину для доступа в Интернет при выполнении команды `share`.

<a name="multiple-php-versions"></a>
### Несколько версий PHP

В Homestead 6 появилась поддержка нескольких версий PHP на одной виртуальной машине. Вы можете указать, какую версию PHP использовать для данного сайта в файле `Homestead.yaml`. Доступные версии PHP: "5.6", "7.0", "7.1", "7.2", "7.3", and "7.4" (по умолчанию):

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public
          php: "7.1"

Кроме того, Вы можете использовать любую из поддерживаемых версий PHP через интерфейс командной строки:

    php5.6 artisan list
    php7.0 artisan list
    php7.1 artisan list
    php7.2 artisan list
    php7.3 artisan list
    php7.4 artisan list

Вы также можете обновить версию CLI по умолчанию, выполнив следующие команды на своей виртуальной машине Homestead:

    php56
    php70
    php71
    php72
    php73
    php74

<a name="web-servers"></a>
### Веб-серверы

По умолчанию Homestead использует веб-сервер Nginx. Однако он может установить Apache, если в качестве типа сайта указан `apache`. Хотя оба веб-сервера могут быть установлены одновременно, они не могут быть *запущены* одновременно. Для упрощения процесса переключения между веб-серверами доступна команда оболочки `flip`. Команда `flip` автоматически определяет, какой веб-сервер работает, выключает его, а затем запускает другой сервер. Чтобы использовать эту команду, подключитесь к компьютеру Homestead по SSH и запустите команду в терминале:

    flip

<a name="mail"></a>
### Почта

В Homestead есть агент пересылки почты Postfix, который по умолчанию прослушивает порт `1025`. Итак, Вы можете указать своему приложению использовать почтовый драйвер `smtp` для `localhost` на порту `1025`. Затем вся отправленная почта будет обработана Postfix и перехвачена Mailhog. Чтобы просмотреть отправленные сообщения электронной почты, откройте [http://localhost:8025](http://localhost:8025) в своем веб-браузере.

<a name="laravel-dusk"></a>
### Laravel Dusk

Чтобы запустить тесты [Laravel Dusk](/docs/{{version}}/dusk) в Homestead, Вы должны включить [`webdriver` feature](#installing-optional-features) в Вашей конфигурации Homestead:

      features:
          - webdriver: true

 Не забудьте после этого подготовить виртуальную машину Homestead, чтобы убедиться, что функция `webdriver` полностью установлена.

<a name="debugging-and-profiling"></a>
## Отладка и профилирование

<a name="debugging-web-requests"></a>
### Отладка веб-запросов с помощью Xdebug

Homestead включает поддержку пошаговой отладки с использованием [Xdebug](https://xdebug.org). Например, вы можете загрузить веб-страницу из браузера, и PHP подключится к вашей среде IDE, чтобы разрешить проверку и изменение выполняемого кода.

По умолчанию Xdebug уже запущен и готов принимать соединения. Если Вам нужно включить Xdebug в CLI, запустите команду `sudo phpenmod xdebug` в Вашем поле Vagrant. Затем следуйте инструкциям Вашей IDE, чтобы включить отладку. Наконец, настройте свой браузер на запуск Xdebug с расширением или [bookmarklet](https://www.jetbrains.com/phpstorm/marklets/).

> {note} Xdebug заставляет PHP работать значительно медленнее. Чтобы отключить Xdebug, запустите `sudo phpdismod xdebug` в Вашем окне Vagrant и перезапустите службу FPM.

<a name="debugging-cli-applications"></a>
### Отладка CLI-приложений

Чтобы отладить приложение PHP CLI, используйте псевдоним оболочки `xphp` внутри Вашего окна Vagrant:

    xphp path/to/script

<a name="autostarting-xdebug"></a>
#### Автозапуск Xdebug

При отладке функциональных тестов, которые отправляют запросы к веб-серверу, легче автоматически запускать отладку, чем изменять тесты для прохождения через настраиваемый заголовок или файл cookie для запуска отладки. Чтобы заставить Xdebug запускаться автоматически, измените `/etc/php/7.x/fpm/conf.d/20-xdebug.ini` внутри Вашего окна Vagrant и добавьте следующую конфигурацию:

    ; If Homestead.yaml contains a different subnet for the IP address, this address may be different...
    xdebug.remote_host = 192.168.10.1
    xdebug.remote_autostart = 1

<a name="profiling-applications-with-blackfire"></a>
### Профилирование приложений с помощью Blackfire

[Blackfire](https://blackfire.io/docs/introduction) - это сервис SaaS для профилирования веб-запросов и приложений CLI и написания утверждений о производительности. Он предлагает интерактивный пользовательский интерфейс, который отображает данные профиля в виде графиков вызовов и временных шкал. Он создан для использования в разработке, тестировании и производстве без дополнительных затрат для конечных пользователей. Он обеспечивает проверку производительности, качества и безопасности кода и параметров конфигурации `php.ini`.

[Blackfire Player](https://blackfire.io/docs/player/index) - это приложение с открытым исходным кодом для веб-сканирования, веб-тестирования и веб-скрейпинга, которое может работать совместно с Blackfire для создания сценариев профилирования сценариев.

Чтобы включить Blackfire, используйте параметр «features» в файле конфигурации Homestead:

    features:
        - blackfire:
            server_id: "server_id"
            server_token: "server_value"
            client_id: "client_id"
            client_token: "client_value"

Учетные данные сервера Blackfire и учетные данные клиента [требуется учетная запись пользователя](https://blackfire.io/signup). Blackfire предлагает различные варианты профилирования приложения, включая инструмент командной строки и расширение для браузера. Пожалуйста [просмотрите документацию Blackfire для получения дополнительной информации](https://blackfire.io/docs/cookbooks/index).

<a name="profiling-php-performance-using-xhgui"></a>
### Профилирование производительности PHP с помощью XHGui

[XHGui](https://www.github.com/perftools/xhgui) - это пользовательский интерфейс для изучения производительности ваших приложений PHP. Чтобы включить XHGui, добавьте `xhgui: 'true'` в конфигурацию Вашего сайта:

    sites:
        -
            map: your-site.test
            to: /home/vagrant/your-site/public
            type: "apache"
            xhgui: 'true'

Если сайт уже существует, не забудьте запустить `vagrant provision` после обновления Вашей конфигурации.

Чтобы профилировать веб-запрос, добавьте `xhgui=on` в качестве параметра запроса к запросу. XHGui автоматически прикрепит файл cookie к ответу, чтобы последующие запросы не нуждались в значении строки запроса. Вы можете просмотреть результаты своего профиля приложения, перейдя по адресу `http://your-site.test/xhgui`.

Чтобы профилировать запрос CLI с помощью XHGui, добавьте к команде префикс `XHGUI=on`:

    XHGUI=on path/to/script

Результаты профиля CLI можно просмотреть так же, как результаты профиля в Интернете.

Обратите внимание, что профилирование замедляет выполнение скрипта, и абсолютное время может быть в два раза больше, чем в реальных запросах. Поэтому всегда сравнивайте процентные улучшения, а не абсолютные числа. Также имейте в виду, что время выполнения включает любое время, затраченное на паузу в отладчике.

Поскольку профили производительности занимают много места на диске, они автоматически удаляются через несколько дней.

<a name="network-interfaces"></a>
## Сетевые интерфейсы

Свойство `networks` файла `Homestead.yaml` настраивает сетевые интерфейсы для Вашей среды Homestead. Вы можете настроить столько интерфейсов, сколько необходимо:

    networks:
        - type: "private_network"
          ip: "192.168.10.20"

Чтобы включить [bridged](https://www.vagrantup.com/docs/networking/public_network.html) интерфейс, настройте параметр `bridge` и измените тип сети на `public_network`:

    networks:
        - type: "public_network"
          ip: "192.168.10.20"
          bridge: "en1: Wi-Fi (AirPort)"

Чтобы включить [DHCP](https://www.vagrantup.com/docs/networking/public_network.html), просто удалите опцию `ip` из Вашей конфигурации:

    networks:
        - type: "public_network"
          bridge: "en1: Wi-Fi (AirPort)"

<a name="extending-homestead"></a>
## Расширение Homestead

Вы можете расширить Homestead, используя сценарий `after.sh` в корне Вашего каталога Homestead. В этот файл Вы можете добавить любые команды оболочки, которые необходимы для правильной настройки и настройки Вашей виртуальной машины.

При настройке Homestead Ubuntu может спросить Вас, хотите ли Вы сохранить исходную конфигурацию пакета или перезаписать ее новым файлом конфигурации. Чтобы избежать этого, Вы должны использовать следующую команду при установке пакетов, чтобы избежать перезаписи любой конфигурации, ранее написанной Homestead:

    sudo apt-get -y \
        -o Dpkg::Options::="--force-confdef" \
        -o Dpkg::Options::="--force-confold" \
        install your-package

<a name="user-customizations"></a>
### Пользовательские настройки

При использовании Homestead в команде Вы можете настроить Homestead, чтобы он лучше соответствовал Вашему личному стилю разработки. Вы можете создать файл `user-customizations.sh` в корне Вашего каталога Homestead (в том же каталоге, что и Ваш `Homestead.yaml`). В этом файле Вы можете сделать любую настройку, которую захотите; однако версия файла `user-customizations.sh` не должна контролироваться.

<a name="updating-homestead"></a>
## Обновление Homestead

Прежде чем начать обновление Homestead, убедитесь, что Вы удалили свою текущую виртуальную машину, выполнив следующую команду в каталоге Homestead:

    vagrant destroy

Затем Вам нужно обновить исходный код Homestead. Если Вы клонировали репозиторий, Вы можете выполнить следующие команды в том месте, где Вы изначально клонировали репозиторий:

    git fetch

    git pull origin release

Эти команды извлекают последний код Homestead из репозитория GitHub, извлекают последние теги, а затем проверяют последний выпуск с тегами. Вы можете найти последнюю стабильную версию выпуска на [странице выпусков GitHub](https://github.com/laravel/homestead/releases).

Если Вы установили Homestead через файл `composer.json` Вашего проекта, Вы должны убедиться, что Ваш файл `composer.json` содержит `"laravel/homestead": "^11"`, и обновите Ваши зависимости:

    composer update

Затем Вы должны обновить поле Vagrant с помощью команды `vagrant box update`:

    vagrant box update

Затем Вы должны запустить команду `bash init.sh` из каталога Homestead, чтобы обновить некоторые дополнительные файлы конфигурации. Вас спросят, хотите ли Вы перезаписать существующие файлы `Homestead.yaml`, `after.sh` и `aliases`:

    // Mac / Linux...
    bash init.sh

    // Windows...
    init.bat

Наконец, Вам нужно будет регенерировать Ваш Homestead box, чтобы использовать последнюю версию Vagrant:

    vagrant up

<a name="provider-specific-settings"></a>
## Настройки для конкретного поставщика

<a name="provider-specific-virtualbox"></a>
### VirtualBox

<a name="natdnshostresolver"></a>
#### `natdnshostresolver`

По умолчанию Homestead устанавливает для параметра `natdnshostresolver` значение `on`. Это позволяет Homestead использовать настройки DNS Вашей операционной системы. Если Вы хотите изменить это поведение, добавьте следующие строки в Ваш файл `Homestead.yaml`:

    provider: virtualbox
    natdnshostresolver: 'off'

<a name="symbolic-links-on-windows"></a>
#### Символические ссылки в Windows

Если символические ссылки не работают должным образом на Вашем компьютере с Windows, Вам может потребоваться добавить следующий блок в Ваш `Vagrantfile`:

    config.vm.provider "virtualbox" do |v|
        v.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]
    end
