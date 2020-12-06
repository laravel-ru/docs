# Фасады

- [Введение](#introduction)
- [Когда использовать фасады](#when-to-use-facades)
    - [Фасады против Внедрения зависимостей](#facades-vs-dependency-injection)
    - [Фасады против Вспомогательных функций](#facades-vs-helper-functions)
- [Как работают фасады](#how-facades-work)
- [Фасады в реальном времени](#real-time-facades)
- [Справочник классов фасадов](#facade-class-reference)

<a name="introduction"></a>
## Введение

Фасады предоставляют «статический» интерфейс для классов, которые доступны в [сервисном контейнере приложения](/docs/{{version}}/container). Laravel поставляется с множеством фасадов, которые обеспечивают доступ почти ко всем функциям Laravel. Фасады Laravel служат «статическими прокси» для базовых классов в сервисном контейнере, обеспечивая преимущества краткого выразительного синтаксиса при сохранении большей тестируемости и гибкости, чем традиционные статические методы.

Все фасады Laravel определены в пространстве имён `Illuminate\Support\Facades`. Итак, мы можем легко получить доступ к такому фасаду:

    use Illuminate\Support\Facades\Cache;

    Route::get('/cache', function () {
        return Cache::get('key');
    });

В документации Laravel во многих примерах будут использоваться фасады для демонстрации различных функций фреймворка.

<a name="when-to-use-facades"></a>
## Когда использовать фасады

У фасадов много преимуществ. Они предоставляют краткий, запоминающийся синтаксис, который позволяет Вам использовать функции Laravel, не запоминая длинные имена классов, которые необходимо вводить или настраивать вручную. Более того, благодаря уникальному использованию динамических методов PHP их легко протестировать.

Однако при использовании фасадов необходимо соблюдать некоторую осторожность. Основная опасность фасадов - это расползание класса. Поскольку фасады настолько просты в использовании и не требуют инъекций, можно легко позволить Вашим классам продолжать расти и использовать множество фасадов в одном классе. При использовании внедрения зависимостей этот потенциал снижается за счет визуальной обратной связи, которую дает большой конструктор, что Ваш класс становится слишком большим. Итак, при использовании фасадов обратите особое внимание на размер вашего класса, чтобы объем его ответственности оставался узким.

> {tip} При создании стороннего пакета, который взаимодействует с Laravel, лучше внедрить [контракты Laravel](/docs/{{version}}/contracts) вместо использования фасадов. Поскольку пакеты создаются вне самого Laravel, у Вас не будет доступа к помощникам Laravel по тестированию фасадов.

<a name="facades-vs-dependency-injection"></a>
### Фасады против Внедрения зависимостей

Одним из основных преимуществ внедрения зависимостей является возможность поменять местами реализации внедренного класса. Это полезно во время тестирования, так как Вы можете вставить имитацию или заглушку и утверждать, что для заглушки были вызваны различные методы.

Как правило, невозможно издеваться над действительно статическим методом класса или заглушить его. Однако, поскольку фасады используют динамические методы для проксирования вызовов методов к объектам, разрешенным из контейнера службы, мы фактически можем тестировать фасады так же, как мы тестировали бы внедренный экземпляр класса. Например, учитывая следующий маршрут:

    use Illuminate\Support\Facades\Cache;

    Route::get('/cache', function () {
        return Cache::get('key');
    });

Мы можем написать следующий тест, чтобы убедиться, что метод `Cache::get` был вызван с ожидаемым аргументом:

    use Illuminate\Support\Facades\Cache;

    /**
     * Пример базового функционального теста.
     *
     * @return void
     */
    public function testBasicExample()
    {
        Cache::shouldReceive('get')
             ->with('key')
             ->andReturn('value');

        $response = $this->get('/cache');

        $response->assertSee('value');
    }

<a name="facades-vs-helper-functions"></a>
### Фасады против Вспомогательных функций

Помимо фасадов, Laravel включает в себя множество «вспомогательных» функций, которые могут выполнять общие задачи, такие как создание представлений, запуск событий, диспетчеризация заданий или отправка HTTP-ответов. Многие из этих вспомогательных функций выполняют ту же функцию, что и соответствующий фасад. Например, этот вызов фасада и вызов помощника эквивалентны:

    return View::make('profile');

    return view('profile');

Практической разницы между фасадами и вспомогательными функциями нет абсолютно никакой. При использовании вспомогательных функций Вы все равно можете тестировать их точно так же, как и соответствующий фасад. Например, учитывая следующий маршрут:

    Route::get('/cache', function () {
        return cache('key');
    });

Под капотом помощник `cache` будет вызывать метод `get` класса, лежащего в основе фасада `Cache`. Итак, даже если мы используем вспомогательную функцию, мы можем написать следующий тест, чтобы убедиться, что метод был вызван с ожидаемым аргументом:

    use Illuminate\Support\Facades\Cache;

    /**
     * Пример базового функционального теста.
     *
     * @return void
     */
    public function testBasicExample()
    {
        Cache::shouldReceive('get')
             ->with('key')
             ->andReturn('value');

        $response = $this->get('/cache');

        $response->assertSee('value');
    }

<a name="how-facades-work"></a>
## Как работают фасады

В приложении Laravel фасад - это класс, который предоставляет доступ к объекту из контейнера. Техника, которая выполняет эту работу, относится к классу `Facade`. Фасады Laravel и любые пользовательские фасады, которые Вы создаете, будут расширять базовый класс `Illuminate\Support\Facades\Facade`.

Базовый класс `Facade` использует магический метод `__callStatic()`, чтобы отложить вызовы Вашего фасада на объект, разрешенный из контейнера. В приведенном ниже примере выполняется вызов кэш-системы Laravel. Взглянув на этот код, можно предположить, что статический метод `get` вызывается в классе `Cache`:

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Support\Facades\Cache;

    class UserController extends Controller
    {
        /**
         * Показать профиль данного пользователя.
         *
         * @param  int  $id
         * @return Response
         */
        public function showProfile($id)
        {
            $user = Cache::get('user:'.$id);

            return view('profile', ['user' => $user]);
        }
    }

Обратите внимание, что в верхней части файла мы «импортируем» фасад `Cache`. Этот фасад служит прокси для доступа к базовой реализации интерфейса `Illuminate\Contracts\Cache\Factory`. Любые вызовы, которые мы делаем с использованием фасада, будут передаваться в базовый экземпляр службы кеширования Laravel.

Если мы посмотрим на этот класс `Illuminate\Support\Facades\Cache`, Вы увидите, что статического метода `get` не существует:

    class Cache extends Facade
    {
        /**
         * Получите зарегистрированное имя компонента.
         *
         * @return string
         */
        protected static function getFacadeAccessor() { return 'cache'; }
    }

Вместо этого фасад `Cache` расширяет базовый класс `Facade` и определяет метод `getFacadeAccessor()`. Задача этого метода - вернуть имя привязки контейнера службы. Когда пользователь ссылается на любой статический метод на фасаде `Cache`, Laravel разрешает привязку `cache` из [сервис контейнера](/docs/{{version}}/container) и запускает запрошенный метод (в данном случае `get`) против этого объекта.

<a name="real-time-facades"></a>
## Фасады в реальном времени

Используя фасады в реальном времени, Вы можете рассматривать любой класс в своем приложении, как если бы он был фасадом. Чтобы проиллюстрировать, как это можно использовать, давайте рассмотрим альтернативу. Например, предположим, что наша модель `Podcast` имеет метод `publish`. Однако, чтобы опубликовать подкаст, нам нужно внедрить экземпляр `Publisher`:

    <?php

    namespace App\Models;

    use App\Contracts\Publisher;
    use Illuminate\Database\Eloquent\Model;

    class Podcast extends Model
    {
        /**
         * Публикация подкаста.
         *
         * @param  Publisher  $publisher
         * @return void
         */
        public function publish(Publisher $publisher)
        {
            $this->update(['publishing' => now()]);

            $publisher->publish($this);
        }
    }

Внедрение реализации издателя в метод позволяет нам легко тестировать метод изолированно, поскольку мы можем имитировать внедренного издателя. Однако он требует от нас всегда передавать экземпляр издателя каждый раз, когда мы вызываем метод `publish`. Используя фасады в реальном времени, мы можем поддерживать такую же тестируемость, при этом не требуя явной передачи экземпляра `Publisher`. Чтобы сгенерировать фасад в реальном времени, добавьте к пространству имен импортируемого класса префикс `Facades`:

    <?php

    namespace App\Models;

    use Facades\App\Contracts\Publisher;
    use Illuminate\Database\Eloquent\Model;

    class Podcast extends Model
    {
        /**
         * Публикация подкаста.
         *
         * @return void
         */
        public function publish()
        {
            $this->update(['publishing' => now()]);

            Publisher::publish($this);
        }
    }

Когда используется фасад реального времени, реализация издателя будет разрешена из контейнера службы с использованием части интерфейса или имени класса, которая появляется после префикса `Facades`. При тестировании мы можем использовать встроенные помощники Laravel для тестирования фасадов, чтобы имитировать вызов этого метода:

    <?php

    namespace Tests\Feature;

    use App\Models\Podcast;
    use Facades\App\Contracts\Publisher;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Tests\TestCase;

    class PodcastTest extends TestCase
    {
        use RefreshDatabase;

        /**
         * Пример теста.
         *
         * @return void
         */
        public function test_podcast_can_be_published()
        {
            $podcast = Podcast::factory()->create();

            Publisher::shouldReceive('publish')->once()->with($podcast);

            $podcast->publish();
        }
    }

<a name="facade-class-reference"></a>
## Справочник классов фасадов

Ниже Вы найдете каждый фасад и его базовый класс. Это полезный инструмент для быстрого изучения документации API для данного корня фасада. Ключ [привязка сервисного контейнера](/docs/{{version}}/container) также включен, где это применимо.

Фасад  |  Класс  |  Привязка сервисного контейнера
------------- | ------------- | -------------
App  |  [Illuminate\Foundation\Application](https://laravel.com/api/{{version}}/Illuminate/Foundation/Application.html)  |  `app`
Artisan  |  [Illuminate\Contracts\Console\Kernel](https://laravel.com/api/{{version}}/Illuminate/Contracts/Console/Kernel.html)  |  `artisan`
Auth  |  [Illuminate\Auth\AuthManager](https://laravel.com/api/{{version}}/Illuminate/Auth/AuthManager.html)  |  `auth`
Auth (Instance)  |  [Illuminate\Contracts\Auth\Guard](https://laravel.com/api/{{version}}/Illuminate/Contracts/Auth/Guard.html)  |  `auth.driver`
Blade  |  [Illuminate\View\Compilers\BladeCompiler](https://laravel.com/api/{{version}}/Illuminate/View/Compilers/BladeCompiler.html)  |  `blade.compiler`
Broadcast  |  [Illuminate\Contracts\Broadcasting\Factory](https://laravel.com/api/{{version}}/Illuminate/Contracts/Broadcasting/Factory.html)  |  &nbsp;
Broadcast (Instance)  |  [Illuminate\Contracts\Broadcasting\Broadcaster](https://laravel.com/api/{{version}}/Illuminate/Contracts/Broadcasting/Broadcaster.html)  |  &nbsp;
Bus  |  [Illuminate\Contracts\Bus\Dispatcher](https://laravel.com/api/{{version}}/Illuminate/Contracts/Bus/Dispatcher.html)  |  &nbsp;
Cache  |  [Illuminate\Cache\CacheManager](https://laravel.com/api/{{version}}/Illuminate/Cache/CacheManager.html)  |  `cache`
Cache (Instance)  |  [Illuminate\Cache\Repository](https://laravel.com/api/{{version}}/Illuminate/Cache/Repository.html)  |  `cache.store`
Config  |  [Illuminate\Config\Repository](https://laravel.com/api/{{version}}/Illuminate/Config/Repository.html)  |  `config`
Cookie  |  [Illuminate\Cookie\CookieJar](https://laravel.com/api/{{version}}/Illuminate/Cookie/CookieJar.html)  |  `cookie`
Crypt  |  [Illuminate\Encryption\Encrypter](https://laravel.com/api/{{version}}/Illuminate/Encryption/Encrypter.html)  |  `encrypter`
DB  |  [Illuminate\Database\DatabaseManager](https://laravel.com/api/{{version}}/Illuminate/Database/DatabaseManager.html)  |  `db`
DB (Instance)  |  [Illuminate\Database\Connection](https://laravel.com/api/{{version}}/Illuminate/Database/Connection.html)  |  `db.connection`
Event  |  [Illuminate\Events\Dispatcher](https://laravel.com/api/{{version}}/Illuminate/Events/Dispatcher.html)  |  `events`
File  |  [Illuminate\Filesystem\Filesystem](https://laravel.com/api/{{version}}/Illuminate/Filesystem/Filesystem.html)  |  `files`
Gate  |  [Illuminate\Contracts\Auth\Access\Gate](https://laravel.com/api/{{version}}/Illuminate/Contracts/Auth/Access/Gate.html)  |  &nbsp;
Hash  |  [Illuminate\Contracts\Hashing\Hasher](https://laravel.com/api/{{version}}/Illuminate/Contracts/Hashing/Hasher.html)  |  `hash`
Http  |  [Illuminate\Http\Client\Factory](https://laravel.com/api/{{version}}/Illuminate/Http/Client/Factory.html)  |  &nbsp;
Lang  |  [Illuminate\Translation\Translator](https://laravel.com/api/{{version}}/Illuminate/Translation/Translator.html)  |  `translator`
Log  |  [Illuminate\Log\LogManager](https://laravel.com/api/{{version}}/Illuminate/Log/LogManager.html)  |  `log`
Mail  |  [Illuminate\Mail\Mailer](https://laravel.com/api/{{version}}/Illuminate/Mail/Mailer.html)  |  `mailer`
Notification  |  [Illuminate\Notifications\ChannelManager](https://laravel.com/api/{{version}}/Illuminate/Notifications/ChannelManager.html)  |  &nbsp;
Password  |  [Illuminate\Auth\Passwords\PasswordBrokerManager](https://laravel.com/api/{{version}}/Illuminate/Auth/Passwords/PasswordBrokerManager.html)  |  `auth.password`
Password (Instance)  |  [Illuminate\Auth\Passwords\PasswordBroker](https://laravel.com/api/{{version}}/Illuminate/Auth/Passwords/PasswordBroker.html)  |  `auth.password.broker`
Queue  |  [Illuminate\Queue\QueueManager](https://laravel.com/api/{{version}}/Illuminate/Queue/QueueManager.html)  |  `queue`
Queue (Instance)  |  [Illuminate\Contracts\Queue\Queue](https://laravel.com/api/{{version}}/Illuminate/Contracts/Queue/Queue.html)  |  `queue.connection`
Queue (Base Class)  |  [Illuminate\Queue\Queue](https://laravel.com/api/{{version}}/Illuminate/Queue/Queue.html)  |  &nbsp;
Redirect  |  [Illuminate\Routing\Redirector](https://laravel.com/api/{{version}}/Illuminate/Routing/Redirector.html)  |  `redirect`
Redis  |  [Illuminate\Redis\RedisManager](https://laravel.com/api/{{version}}/Illuminate/Redis/RedisManager.html)  |  `redis`
Redis (Instance)  |  [Illuminate\Redis\Connections\Connection](https://laravel.com/api/{{version}}/Illuminate/Redis/Connections/Connection.html)  |  `redis.connection`
Request  |  [Illuminate\Http\Request](https://laravel.com/api/{{version}}/Illuminate/Http/Request.html)  |  `request`
Response  |  [Illuminate\Contracts\Routing\ResponseFactory](https://laravel.com/api/{{version}}/Illuminate/Contracts/Routing/ResponseFactory.html)  |  &nbsp;
Response (Instance)  |  [Illuminate\Http\Response](https://laravel.com/api/{{version}}/Illuminate/Http/Response.html)  |  &nbsp;
Route  |  [Illuminate\Routing\Router](https://laravel.com/api/{{version}}/Illuminate/Routing/Router.html)  |  `router`
Schema  |  [Illuminate\Database\Schema\Builder](https://laravel.com/api/{{version}}/Illuminate/Database/Schema/Builder.html)  |  &nbsp;
Session  |  [Illuminate\Session\SessionManager](https://laravel.com/api/{{version}}/Illuminate/Session/SessionManager.html)  |  `session`
Session (Instance)  |  [Illuminate\Session\Store](https://laravel.com/api/{{version}}/Illuminate/Session/Store.html)  |  `session.store`
Storage  |  [Illuminate\Filesystem\FilesystemManager](https://laravel.com/api/{{version}}/Illuminate/Filesystem/FilesystemManager.html)  |  `filesystem`
Storage (Instance)  |  [Illuminate\Contracts\Filesystem\Filesystem](https://laravel.com/api/{{version}}/Illuminate/Contracts/Filesystem/Filesystem.html)  |  `filesystem.disk`
URL  |  [Illuminate\Routing\UrlGenerator](https://laravel.com/api/{{version}}/Illuminate/Routing/UrlGenerator.html)  |  `url`
Validator  |  [Illuminate\Validation\Factory](https://laravel.com/api/{{version}}/Illuminate/Validation/Factory.html)  |  `validator`
Validator (Instance)  |  [Illuminate\Validation\Validator](https://laravel.com/api/{{version}}/Illuminate/Validation/Validator.html)  |  &nbsp;
View  |  [Illuminate\View\Factory](https://laravel.com/api/{{version}}/Illuminate/View/Factory.html)  |  `view`
View (Instance)  |  [Illuminate\View\View](https://laravel.com/api/{{version}}/Illuminate/View/View.html)  |  &nbsp;
