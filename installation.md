# Установка

- [Установка](#installation)
    - [Требования к серверу](#server-requirements)
    - [Установка Laravel](#installing-laravel)
    - [Конфигурация](#configuration)
- [Конфигурация веб-сервера](#web-server-configuration)
    - [Конфигурация каталога](#directory-configuration)
    - [Красивые URL-адреса](#pretty-urls)

<a name="installation"></a>
## Установка

<a name="server-requirements"></a>
### Требования к серверу

Фреймворк Laravel имеет несколько системных требований. Всем этим требованиям удовлетворяет виртуальная машина [Laravel Homestead](/docs/{{version}}/homestead), поэтому настоятельно рекомендуется использовать Homestead в качестве локальной среды разработки Laravel.

Однако, если Вы не используете Homestead, Вам необходимо убедиться, что Ваш сервер соответствует следующим требованиям:

<div class="content-list" markdown="1">
- PHP >= 7.3
- Расширение BCMath PHP
- Расширение Ctype PHP
- Расширение Fileinfo PHP
- Расширение JSON PHP
- Расширение Mbstring PHP
- Расширение OpenSSL PHP
- Расширение PDO PHP
- Расширение Tokenizer PHP
- Расширение XML PHP
</div>

<a name="installing-laravel"></a>
### Установка Laravel

Для управления зависимостями Laravel использует [Composer](https://getcomposer.org). Перед использованием Laravel убедитесь, что на компьютере установлен Composer.

<a name="via-laravel-installer"></a>
#### Через установщик Laravel

Сначала загрузите установщик Laravel с помощью Composer:

    composer global require laravel/installer

Убедитесь, что системный каталог bin поставщика Composer помещен в `$PATH`, чтобы исполняемый файл laravel можно было найти в системе. Этот каталог существует в различных местах и зависит от Вашей операционной системы; однако некоторые общие местоположения включают в себя:

<div class="content-list" markdown="1">
- macOS: `$HOME/.composer/vendor/bin`
- Windows: `%USERPROFILE%\AppData\Roaming\Composer\vendor\bin`
- GNU / Linux Distributions: `$HOME/.config/composer/vendor/bin` или `$HOME/.composer/vendor/bin`
</div>

Вы также можете найти глобальный путь установки composer, выполнив `composer global about` и посмотрев в первой строке.

После установки команда `laravel new` создаст новую установку Laravel в указанном Вами каталоге. Например, `laravel new blog` создаст каталог с именем `blog`, содержащий новую установку Laravel со всеми уже установленными зависимостями Laravel:

    laravel new blog

> {tip} Хотите создать проект Laravel с уже созданными для вас функциями входа, регистрации и другими функциями? Посмотрите [Laravel Jetstream](https://jetstream.laravel.com) или [Перевод Laravel Jetstream](https://jetstream.getlaravel.ru).

<a name="via-composer-create-project"></a>
#### Через Composer Создать-Проект

В качестве альтернативы Вы также можете установить Laravel, введя команду Composer `create-project` в Вашем терминале:

    composer create-project --prefer-dist laravel/laravel blog

<a name="local-development-server"></a>
#### Локальный сервер разработки

Если у Вас установлен PHP локально и Вы хотите использовать встроенный сервер разработки PHP для работы своего приложения, Вы можете использовать Artisan-команду `serve`. Эта команда запустит сервер разработки по адресу `http://localhost:8000`:

    php artisan serve

Более надежные варианты локальной разработки доступны через [Homestead](/docs/{{version}}/homestead) или [Valet](/docs/{{version}}/valet).

<a name="configuration"></a>
### Конфигурация

<a name="public-directory"></a>
#### Каталог public

После установки Laravel Вы должны настроить корневой каталог веб-сервера в папку `public`. Файл `index.php` в этом каталоге служит главным контроллером для всех HTTP-запросов, поступающих в приложение.

<a name="configuration-files"></a>
#### Файлы конфигурации

Все файлы конфигурации для фреймворка Laravel хранятся в каталоге `config`. Каждый вариант задокументирован, поэтому не стесняйтесь просматривать файлы и знакомиться с доступными Вам вариантами.

<a name="directory-permissions"></a>
#### Разрешения каталога

После установки Laravel Вам может потребоваться настроить некоторые разрешения. Каталоги `storage` и `bootstrap/cache` должны быть доступны для записи веб-сервером, иначе Laravel не будет запущен. Если Вы используете виртуальную машину [Homestead](/docs/{{version}}/homestead), эти разрешения уже должны быть установлены.

<a name="application-key"></a>
#### Ключ приложения

Следующее, что Вам нужно сделать после установки Laravel, - это установить для ключа приложения случайную строку. Если Вы установили Laravel через Composer или установщик Laravel, этот ключ уже был установлен для Вас с помощью команды `php artisan key:generate`.

Обычно эта строка должна состоять из 32 символов. Ключ может быть установлен в файле окружения `.env`. Если Вы не скопировали файл `.env.example` в новый файл с именем `.env`, Вам следует сделать это сейчас. **Если ключ приложения не установлен, Ваши пользовательские сеансы и другие зашифрованные данные не будут защищены!**

<a name="additional-configuration"></a>
#### Дополнительная конфигурация

Laravel почти не нуждается в другой конфигурации из коробки. Вы можете начать разработку! Однако Вы можете просмотреть файл `config/app.php` и его документацию. Он содержит несколько параметров, таких как `timezone` и `locale`, которые Вы можете изменить в соответствии с Вашим приложением.

Вы также можете настроить несколько дополнительных компонентов Laravel, например:

<div class="content-list" markdown="1">
- [Кэш](/docs/{{version}}/cache#configuration)
- [База данных](/docs/{{version}}/database#configuration)
- [Сессия](/docs/{{version}}/session#configuration)
</div>

<a name="web-server-configuration"></a>
## Конфигурация веб-сервера

<a name="directory-configuration"></a>
### Конфигурация каталога

Laravel всегда должен обслуживаться из корня «веб-каталога», настроенного для Вашего веб-сервера. Вы не должны пытаться обслуживать приложение Laravel из подкаталога «веб-каталога». Попытка сделать это может открыть доступ к конфиденциальным файлам, присутствующим в Вашем приложении.

<a name="pretty-urls"></a>
### Красивые URL-адреса

<a name="apache"></a>
#### Apache

Laravel включает файл `public/.htaccess`, который используется для предоставления URL-адресов без фронт-контроллера `index.php` в пути. Перед обслуживанием Laravel с Apache обязательно включите модуль `mod_rewrite`, чтобы файл `.htaccess` был обработан сервером.

Если файл `.htaccess`, поставляемый с Laravel, не работает с Вашей установкой Apache, попробуйте следующий вариант:

    Options +FollowSymLinks -Indexes
    RewriteEngine On

    RewriteCond %{HTTP:Authorization} .
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]

<a name="nginx"></a>
#### Nginx

Если Вы используете Nginx, следующая директива в конфигурации Вашего сайта будет направлять все запросы на фронт-контроллер `index.php`:

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

При использовании [Homestead](/docs/{{version}}/homestead) или [Valet](/docs/{{version}}/valet), красивые URL-адреса будут настроены автоматически.
