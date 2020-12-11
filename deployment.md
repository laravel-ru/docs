# Развертывание

- [Вступление](#introduction)
- [Требования к серверу](#server-requirements)
- [Конфигурация сервера](#server-configuration)
    - [Nginx](#nginx)
- [Оптимизация](#optimization)
    - [Оптимизация автозагрузчика](#autoloader-optimization)
    - [Оптимизация загрузки конфигурации](#optimizing-configuration-loading)
    - [Оптимизация загрузки маршрута](#optimizing-route-loading)
    - [Оптимизация загрузки представления](#optimizing-view-loading)
- [Режим отладки](#debug-mode)
- [Деплой с помощью Forge / Vapor](#deploying-with-forge-or-vapor)

<a name="introduction"></a>
## Вступление

Когда Вы будете готовы развернуть приложение Laravel в продакшене, Вы можете сделать несколько важных вещей, чтобы убедиться, что Ваше приложение работает как можно более эффективно. В этом документе мы рассмотрим несколько отличных отправных точек, чтобы убедиться, что Ваше приложение Laravel развернуто правильно.

<a name="server-requirements"></a>
## Требования к серверу

The Laravel framework has a few system requirements. You should ensure that your web server has the following minimum PHP version and extensions:

<div class="content-list" markdown="1">
- PHP >= 7.3
- BCMath PHP Extension
- Ctype PHP Extension
- Fileinfo PHP Extension
- JSON PHP Extension
- Mbstring PHP Extension
- OpenSSL PHP Extension
- PDO PHP Extension
- Tokenizer PHP Extension
- XML PHP Extension
</div>

<a name="server-configuration"></a>
## Конфигурация сервера

<a name="nginx"></a>
### Nginx

Если Вы развертываете свое приложение на сервере, на котором работает Nginx, Вы можете использовать следующий файл конфигурации в качестве отправной точки для настройки Вашего веб-сервера. Скорее всего, этот файл нужно будет настроить в зависимости от конфигурации Вашего сервера. **Если Вам нужна помощь в управлении Вашим сервером, рассмотрите возможность использования собственной службы управления и развертывания серверов Laravel, такой как [Laravel Forge](https://forge.laravel.com).**

Убедитесь, что, как и в конфигурации ниже, ваш веб-сервер направляет все запросы в файл `public/index.php` вашего приложения. Вы никогда не должны пытаться переместить файл `index.php` в корень вашего проекта, так как обслуживание приложения из корня проекта откроет доступ ко многим конфиденциальным файлам конфигурации в общедоступный Интернет:

    server {
        listen 80;
        server_name example.com;
        root /srv/example.com/public;

        add_header X-Frame-Options "SAMEORIGIN";
        add_header X-XSS-Protection "1; mode=block";
        add_header X-Content-Type-Options "nosniff";

        index index.php;

        charset utf-8;

        location / {
            try_files $uri $uri/ /index.php?$query_string;
        }

        location = /favicon.ico { access_log off; log_not_found off; }
        location = /robots.txt  { access_log off; log_not_found off; }

        error_page 404 /index.php;

        location ~ \.php$ {
            fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
            fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
            include fastcgi_params;
        }

        location ~ /\.(?!well-known).* {
            deny all;
        }
    }

<a name="optimization"></a>
## Оптимизация

<a name="autoloader-optimization"></a>
### Оптимизация автозагрузчика

При развертывании в продакшене, убедитесь, что Вы оптимизируете карту автозагрузчика классов Composer, чтобы Composer мог быстро найти нужный файл для загрузки для данного класса:

    composer install --optimize-autoloader --no-dev

> {tip} Помимо оптимизации автозагрузчика, Вы всегда должны обязательно включать файл `composer.lock` в репозиторий системы управления версиями Вашего проекта. Зависимости Вашего проекта могут быть установлены намного быстрее, если присутствует файл `composer.lock`.

<a name="optimizing-configuration-loading"></a>
### Оптимизация загрузки конфигурации

При развертывании Вашего приложения в производственной среде Вы должны убедиться, что Вы выполнили Artisan-команду `config:cache` в процессе развертывания:

    php artisan config:cache

Эта команда объединит все файлы конфигурации Laravel в один кешированный файл, что значительно сокращает количество обращений, которые фреймворк должен совершить к файловой системе при загрузке значений Вашей конфигурации.

> {note} Если Вы выполняете команду `config:cache` в процессе деплоя, Вы должны быть уверены, что вызываете только функцию `env` из Ваших файлов конфигурации. После кэширования конфигурации файл `.env` не будет загружен, и все вызовы функции `env` для переменных `.env` вернут `null`.

<a name="optimizing-route-loading"></a>
### Оптимизация загрузки маршрута

Если Вы создаете большое приложение с множеством маршрутов, Вы должны убедиться, что Вы используете Artisan-команду `route:cache` во время процесса деплоя:

    php artisan route:cache

Эта команда сокращает все Ваши регистрации маршрутов до одного вызова метода в кэшированном файле, повышая производительность регистрации маршрута при регистрации сотен маршрутов.

<a name="optimizing-view-loading"></a>
### Оптимизация загрузки представления

При развертывании Вашего приложения в производственной среде Вы должны убедиться, что Вы выполнили Artisan-команду `view: cache` в процессе развертывания:

    php artisan view:cache

Эта команда предварительно компилирует все Ваши представления Blade, чтобы они не компилировались по запросу, повышая производительность каждого запроса, возвращающего представление.

<a name="debug-mode"></a>
## Режим отладки

The debug option in your config/app.php configuration file determines how much information about an error is actually displayed to the user. By default, this option is set to respect the value of the APP_DEBUG environment variable, which is stored in your .env file.

**In your production environment, this value should always be `false`. If the `APP_DEBUG` variable is set to `true` in production, you risk exposing sensitive configuration values to your application's end users.**

<a name="deploying-with-forge-or-vapor"></a>
## Деплой с помощью Forge / Vapor

<a name="laravel-forge"></a>
#### Laravel Forge

Если Вы не совсем готовы управлять конфигурацией своего сервера или Вам неудобно настраивать все различные службы, необходимые для запуска надежного приложения Laravel, [Laravel Forge](https://forge.laravel.com) - замечательная альтернатива.

Laravel Forge может создавать серверы у различных поставщиков инфраструктуры, таких как DigitalOcean, Linode, AWS и других. Кроме того, Forge устанавливает и управляет всеми инструментами, необходимыми для создания надежных приложений Laravel, таких как Nginx, MySQL, Redis, Memcached, Beanstalk и других.

<a name="laravel-vapor"></a>
#### Laravel Vapor

Если Вам нужна полностью бессерверная платформа развертывания с автоматическим масштабированием, настроенная для Laravel, ознакомьтесь с [Laravel Vapor](https://vapor.laravel.com). Laravel Vapor - это платформа для бессерверного развертывания Laravel, работающая на AWS. Запустите свою инфраструктуру Laravel на Vapor и влюбитесь в масштабируемую простоту бессерверной архитектуры. Laravel Vapor настроен создателями Laravel для безупречной работы с фреймворком, поэтому Вы можете продолжать писать свои приложения Laravel точно так же, как Вы привыкли.
