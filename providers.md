# Сервис Провайдеры

- [Введение](#introduction)
- [Написание Сервис Провайдеров](#writing-service-providers)
    - [Метод регистрации](#the-register-method)
    - [Метод загрузки](#the-boot-method)
- [Регистрация провайдеров](#registering-providers)
- [Отложенные провайдеры](#deferred-providers)

<a name="introduction"></a>
## Введение

Сервис Провайдеры - это центральное место при начальной загрузке всех приложений Laravel. Ваше собственное приложение, а также все основные службы Laravel загружаются через сервис провайдеров.

Но что мы подразумеваем под «самозагрузкой»? В общем, мы имеем в виду **регистрацию** вещей, включая регистрацию привязок сервисных контейнеров, прослушивателей событий, мидлваров и даже маршрутов. Сервис Провайдеры - это центральное место для настройки Вашего приложения.

Если Вы откроете файл `config/app.php`, включенный в Laravel, Вы увидите массив провайдеров `providers`. Это все классы поставщиков услуг, которые будут загружены для Вашего приложения. По умолчанию в этом массиве перечислены основные поставщики услуг Laravel. Эти поставщики загружают основные компоненты Laravel, такие как почтовая программа, очередь, кеш и другие. Многие из этих поставщиков являются «отложенными» поставщиками, то есть они не будут загружаться при каждом запросе, а только тогда, когда предоставляемые ими услуги действительно необходимы.

В этом обзоре Вы узнаете, как писать собственные сервис провайдеры и регистрировать их в приложении Laravel.

> {tip} Если Вы хотите узнать больше о том, как Laravel обрабатывает запросы и работает внутри, ознакомьтесь с нашей документацией Laravel про [жизненный цикл запроса](/docs/{{version}}/lifecycle).

<a name="writing-service-providers"></a>
## Написание Сервис Провайдеров

Все сервис провайдеры расширяют класс `Illuminate\Support\ServiceProvider`. Большинство сервис провайдеров содержат метод `register` и `boot`. В методе `register` Вы должны **привязывать вещи только к [сервисному контейнеру](/docs/{{version}}/container)**. Вы никогда не должны пытаться регистрировать какие-либо прослушиватели событий, маршруты или любую другую функциональность в методе `register`.

Интерфейс командной строки Artisan может сгенерировать нового провайдера с помощью команды `make:provider`:

    php artisan make:provider RiakServiceProvider

<a name="the-register-method"></a>
### Метод регистрации

Как упоминалось ранее, в методе `register` Вы должны привязывать вещи только к [сервисному контейнеру](/docs/{{version}}/container). Вы никогда не должны пытаться регистрировать какие-либо прослушиватели событий, маршруты или любую другую функциональность в методе `register`. В противном случае Вы можете случайно воспользоваться сервисом, предоставляемой сервис провайдером, но еще не загруженной.

Давайте посмотрим на основного сервис провайдера. В любом из методов Вашего сервис провайдера у Вас всегда есть доступ к свойству `$app`, которое обеспечивает доступ к контейнеру службы:

    <?php

    namespace App\Providers;

    use App\Services\Riak\Connection;
    use Illuminate\Support\ServiceProvider;

    class RiakServiceProvider extends ServiceProvider
    {
        /**
         * Регистрация других сервисов приложения.
         *
         * @return void
         */
        public function register()
        {
            $this->app->singleton(Connection::class, function ($app) {
                return new Connection(config('riak'));
            });
        }
    }

Этот сервис провайдер определяет только метод `register` и использует этот метод для определения реализации `App\Services\Riak\Connection` в контейнере службы. Если Вы еще не знакомы с сервис контейнером Laravel, ознакомьтесь с [его документацией](/docs/{{version}}/container).

<a name="the-bindings-and-singletons-properties"></a>
#### Свойства `bindings` и `singletons`

Если Ваш сервис провайдер регистрирует много простых привязок, Вы можете использовать свойства `bindings` и `singletons` вместо ручной регистрации каждой привязки контейнера. Когда сервис провайдер загружается платформой, он автоматически проверяет эти свойства и регистрирует их привязки:

    <?php

    namespace App\Providers;

    use App\Contracts\DowntimeNotifier;
    use App\Contracts\ServerProvider;
    use App\Services\DigitalOceanServerProvider;
    use App\Services\PingdomDowntimeNotifier;
    use App\Services\ServerToolsProvider;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Все привязки контейнеров, которые необходимо зарегистрировать.
         *
         * @var array
         */
        public $bindings = [
            ServerProvider::class => DigitalOceanServerProvider::class,
        ];

        /**
         * Все одиночные контейнеры, которые необходимо зарегистрировать.
         *
         * @var array
         */
        public $singletons = [
            DowntimeNotifier::class => PingdomDowntimeNotifier::class,
            ServerProvider::class => ServerToolsProvider::class,
        ];
    }

<a name="the-boot-method"></a>
### Метод загрузки

Итак, что, если нам нужно зарегистрировать [представление composer](/docs/{{version}}/views#view-composers) у нашего сервис провайдера? Это должно быть сделано методом `boot`. **Этот метод вызывается после регистрации всех других сервис провайдеров**, что означает, что у Вас есть доступ ко всем другим сервисам, которые были зарегистрированы фреймворком:

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\View;
    use Illuminate\Support\ServiceProvider;

    class ComposerServiceProvider extends ServiceProvider
    {
        /**
         * Загрузка других сервисов приложения.
         *
         * @return void
         */
        public function boot()
        {
            View::composer('view', function () {
                //
            });
        }
    }

<a name="boot-method-dependency-injection"></a>
#### Внедрение зависимости метода загрузки (DI)

Вы можете указать зависимости для метода загрузки `boot` Вашего сервис провайдера. [Сервисный контейнер](/docs/{{version}}/container) автоматически вставит все необходимые Вам зависимости:

    use Illuminate\Contracts\Routing\ResponseFactory;

    /**
     * Bootstrap any application services.
     *
     * @param  \Illuminate\Contracts\Routing\ResponseFactory
     * @return void
     */
    public function boot(ResponseFactory $response)
    {
        $response->macro('serialized', function ($value) {
            //
        });
    }

<a name="registering-providers"></a>
## Регистрация провайдеров

Все сервис провайдеры зарегистрированы в файле конфигурации `config/app.php`. Этот файл содержит массив провайдеров `providers`, в котором Вы можете перечислить имена классов Ваших сервис провайдеры. По умолчанию в этом массиве перечислены основные сервис провайдеры Laravel. Эти провайдеры загружают основные компоненты Laravel, такие как почтовая программа, очередь, кеш и другие.

Чтобы зарегистрировать своего провайдера, добавьте его в массив:

    'providers' => [
        // Другие Сервис Провайдеры

        App\Providers\ComposerServiceProvider::class,
    ],

<a name="deferred-providers"></a>
## Отложенные провайдеры

Если Ваш провайдер **только** регистрирует привязки в [сервис контейнере](/docs/{{version}}/container), Вы можете отложить его регистрацию до тех пор, пока одна из зарегистрированных привязок не понадобится. Отсрочка загрузки такого провайдера улучшит производительность Вашего приложения, поскольку оно не загружается из файловой системы при каждом запросе.

Laravel компилирует и хранит список всех сервисов, предоставляемых отложенными сервис провайдерами, вместе с именем своего класса сервис провайдера. Затем, только когда Вы пытаетесь разрешить одну из этих служб, Laravel загружает сервис провайдера.

Чтобы отложить загрузку провайдера, реализуйте интерфейс `\Illuminate\Contracts\Support\DeferrableProvider` и определите метод `provides`. Метод `provides` должен возвращать привязки сервисного контейнера, зарегистрированные провайдером:

    <?php

    namespace App\Providers;

    use App\Services\Riak\Connection;
    use Illuminate\Contracts\Support\DeferrableProvider;
    use Illuminate\Support\ServiceProvider;

    class RiakServiceProvider extends ServiceProvider implements DeferrableProvider
    {
        /**
         * Зарегистрируйте любые сервисы приложения.
         *
         * @return void
         */
        public function register()
        {
            $this->app->singleton(Connection::class, function ($app) {
                return new Connection($app['config']['riak']);
            });
        }

        /**
         * Получите сервисы, предоставляемые провайдером.
         *
         * @return array
         */
        public function provides()
        {
            return [Connection::class];
        }
    }
