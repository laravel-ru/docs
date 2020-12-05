# Руководство по обновлению

- [Обновление до 8.0 с 7.x](#upgrade-8.0)

<a name="high-impact-changes"></a>
## Изменения, оказывающие большое влияние

<div class="content-list" markdown="1">
- [Модели фабрик](#model-factories)
- [Метод очереди `retryAfter`](#queue-retry-after-method)
- [Свойство очереди `timeoutAt`](#queue-timeout-at-property)
- [Очередь `allOnQueue` и `allOnConnection`](#queue-allOnQueue-allOnConnection)
- [Пагинация по умолчанию](#pagination-defaults)
- [Пространства имен Сидов и Фабрик](#seeder-factory-namespaces)
</div>

<a name="medium-impact-changes"></a>
## Изменения, оказывающие среднее влияние

<div class="content-list" markdown="1">
- [Требуется PHP 7.3.0](#php-7.3.0-required)
- [Пакетная поддержка таблицы невыполненных заданий](#failed-jobs-table-batch-support)
- [Обновления режима обслуживания](#maintenance-mode-updates)
- [Параметр `php artisan down --message`](#artisan-down-message)
- [Метод `assertExactJson`](#assert-exact-json-method)
</div>

<a name="upgrade-8.0"></a>
## Обновление до 8.0 с 7.x

<a name="estimated-upgrade-time-15-minutes"></a>
#### Приблизительное время обновления: 15 минут

> {note} Мы стараемся задокументировать все возможные критические изменения. Поскольку некоторые из этих критических изменений находятся в малоизвестных частях фреймворка, только часть этих изменений может повлиять на Ваше приложение.

<a name="php-7.3.0-required"></a>
### Требуется PHP 7.3.0

**Вероятность воздействия: средняя**

Новая минимальная версия PHP теперь 7.3.0.

<a name="updating-dependencies"></a>
### Обновление зависимостей

Обновите следующие зависимости в Вашем файле `composer.json`:

<div class="content-list" markdown="1">
- `guzzlehttp/guzzle` на `^7.0.1`
- `facade/ignition` на `^2.3.6`
- `laravel/framework` на `^8.0`
- `laravel/ui` на `^3.0`
- `nunomaduro/collision` на `^5.0`
- `phpunit/phpunit` на `^9.0`
</div>

Следующие основные пакеты имеют новые основные выпуски для поддержки Laravel 8. Если возможно, Вы должны прочитать соответствующие руководства по обновлению перед обновлением:

<div class="content-list" markdown="1">
- [Horizon v5.0](https://github.com/laravel/horizon/blob/master/UPGRADE.md)
- [Passport v10.0](https://github.com/laravel/passport/blob/master/UPGRADE.md)
- [Socialite v5.0](https://github.com/laravel/socialite/blob/master/UPGRADE.md)
- [Telescope v4.0](https://github.com/laravel/telescope/blob/master/UPGRADE.md)
</div>

Кроме того, установщик Laravel был обновлен для поддержки `composer create-project` и Laravel Jetstream. Любой установщик старше 4.0 перестанет работать после октября 2020 года. Вам следует как можно скорее обновить глобальный установщик до `^4.0`.

Наконец, изучите любые другие сторонние пакеты, используемые Вашим приложением, и убедитесь, что Вы используете правильную версию для поддержки Laravel 8.

<a name="collections"></a>
### Коллекции

<a name="the-isset-method"></a>
#### Метод `isset`

**Вероятность воздействия: Низкая**

Чтобы соответствовать типичному поведению PHP, метод `offsetExists` в `Illuminate\Support\Collection` был обновлен и теперь использует `isset` вместо `array_key_exists`. Это может привести к изменению поведения при работе с элементами коллекции, имеющими значение `null`:

    $collection = collect([null]);

    // Laravel 7.x - true
    isset($collection[0]);

    // Laravel 8.x - false
    isset($collection[0]);

<a name="database"></a>
### База данных

<a name="seeder-factory-namespaces"></a>
#### Пространства имен Сидов и Фабрик

**Вероятность воздействия: Высокая**

Сиды и фабрики теперь имеют пространство имен. Чтобы учесть эти изменения, добавьте пространство имен `Database\Seeders` в Ваши классы сидов. Кроме того, предыдущий каталог `database/seeds` должен быть переименован в `database/seeders`:

    <?php

    namespace Database\Seeders;

    use App\Models\User;
    use Illuminate\Database\Seeder;

    class DatabaseSeeder extends Seeder
    {
        /**
         * Seed the application's database.
         *
         * @return void
         */
        public function run()
        {
            ...
        }
    }

Если Вы решили использовать пакет `laravel/legacy-factories`, никаких изменений в Ваших фабричных классах не требуется. Однако, если Вы обновляете свои фабрики, Вы должны добавить к этим классам пространство имен `Database\Factories`.

Затем в Вашем файле `composer.json` удалите блок `classmap` из раздела `autoload` и добавьте новые сопоставления каталогов классов с пространством имен:

    "autoload": {
        "psr-4": {
            "App\\": "app/",
            "Database\\Factories\\": "database/factories/",
            "Database\\Seeders\\": "database/seeders/"
        }
    },

<a name="eloquent"></a>
### Eloquent

<a name="model-factories"></a>
#### Модель Фабрик

**Вероятность воздействия: Высокая**

Функция Laravel [фабрики моделей](/docs/{{version}}/database-testing#creating-factories) была полностью переписана для поддержки классов и несовместима с фабриками стиля Laravel 7.x. Однако, чтобы упростить процесс обновления, был создан новый пакет `laravel/legacy-factories`, чтобы продолжать использовать Ваши существующие фабрики с Laravel 8.x. Вы можете установить этот пакет через Composer:

    composer require laravel/legacy-factories

<a name="the-castable-interface"></a>
#### Интерфейс `Castable`

**Вероятность воздействия: Низкая**

Метод `castUsing` интерфейса `Castable` был обновлен, чтобы принимать массив аргументов. Если Вы реализуете этот интерфейс, Вам следует соответствующим образом обновить свою реализацию:

    public static function castUsing(array $arguments);

<a name="increment-decrement-events"></a>
#### События увеличения / уменьшения

**Вероятность воздействия: Низкая**

Соответствующие события модели, связанные с «обновлением» и «сохранением», теперь будут отправляться при выполнении методов `increment` или `decrement` на экземплярах модели Eloquent.

<a name="events"></a>
### События

<a name="the-dispatcher-contract"></a>
#### Контракт `Dispatcher`

**Вероятность воздействия: Низкая**

Метод `listen` контракта `Illuminate\Contracts\Events\Dispatcher` был обновлен, чтобы сделать свойство `$listener` необязательным. Это изменение было внесено для поддержки автоматического определения обрабатываемых типов событий через отражение. Если Вы реализуете этот интерфейс вручную, Вам следует соответствующим образом обновить свою реализацию:

    public function listen($events, $listener = null);

<a name="framework"></a>
### Фреймворк

<a name="maintenance-mode-updates"></a>
#### Обновления режима обслуживания

**Вероятность воздействия: Необязательная**

Функция [режим обслуживания](/docs/{{version}}/configuration#maintenance-mode) Laravel была улучшена в Laravel 8.x. Теперь поддерживается предварительная визуализация шаблона режима обслуживания, что исключает вероятность того, что конечные пользователи столкнутся с ошибками в режиме обслуживания. Однако для поддержки этого в Ваш файл `public/index.php` необходимо добавить следующие строки. Эти строки следует разместить непосредственно под существующим определением константы `LARAVEL_START`:

    define('LARAVEL_START', microtime(true));

    if (file_exists(__DIR__.'/../storage/framework/maintenance.php')) {
        require __DIR__.'/../storage/framework/maintenance.php';
    }

<a name="artisan-down-message"></a>
#### Параметр `php artisan down --message`

**Вероятность воздействия: Средняя**

Опция `--message` команды `php artisan down` была удалена. В качестве альтернативы рассмотрите возможность [предварительной обработки представлений в режиме обслуживания](/docs/{{version}}/configuration#maintenance-mode) с выбранным Вами сообщением.

<a name="php-artisan-serve-no-reload-option"></a>
#### Параметр `php artisan serve --no-reload`

**Вероятность воздействия: Низкая**

В команду `php artisan serve` добавлен параметр `--no-reload`. Это даст указание встроенному серверу не перезагружать сервер при обнаружении изменений файла среды. Эта опция в первую очередь полезна при запуске тестов Laravel Dusk в среде CI.

<a name="manager-app-property"></a>
#### Свойство менеджера `$app`

**Вероятность воздействия: Низкая**

Ранее устаревшее свойство `$app` класса `Illuminate\Support\Manager` было удалено. Если Вы полагались на это свойство, Вам следует использовать вместо него свойство `$container`.

<a name="the-elixir-helper"></a>
#### Помощник `elixir`

**Вероятность воздействия: Низкая**

Ранее устаревший помощник `elixir` был удален. Приложениям, все еще использующим этот метод, рекомендуется перейти на [Laravel Mix](https://github.com/JeffreyWay/laravel-mix).

<a name="mail"></a>
### Почта

<a name="the-sendnow-method"></a>
#### Метод `sendNow`

**Вероятность воздействия: Низкая**

Ранее устаревший метод `sendNow` был удален. Вместо этого используйте метод `send`.

<a name="pagination"></a>
### Пагинация

<a name="pagination-defaults"></a>
#### Пагинация по умолчанию

**Вероятность воздействия: Высокая**

Пагинатор теперь использует [CSS-фреймворк Tailwind](https://tailwindcss.com) для своего стиля по умолчанию. Чтобы продолжать использовать Bootstrap, Вы должны добавить следующий вызов метода к методу `boot` Вашего приложения `AppServiceProvider`:

    use Illuminate\Pagination\Paginator;

    Paginator::useBootstrap();

<a name="queue"></a>
### Очередь

<a name="queue-retry-after-method"></a>
#### Метод `retryAfter`

**Вероятность воздействия: Высокая**

Для согласованности с другими функциями Laravel метод `retryAfter` и свойство `retryAfter` заданий в очереди, почтовых программ, уведомлений и слушателей были переименованы в `backoff`. Вам следует обновить имя этого метода / свойства в соответствующих классах Вашего приложения.

<a name="queue-timeout-at-property"></a>
#### Свойство `timeoutAt`

**Вероятность воздействия: Высокая**

Свойство `timeoutAt` заданий в очереди, уведомлений и слушателей переименовано в `retryUntil`. Вам следует обновить имя этого свойства в соответствующих классах Вашего приложения.

<a name="queue-allOnQueue-allOnConnection"></a>
#### Методы `allOnQueue()` / `allOnConnection()`

**Вероятность воздействия: Высокая**

Для согласованности с другими методами диспетчеризации были удалены методы `allOnQueue()` и `allOnConnection()`, используемые с цепочкой заданий. Вместо этого Вы можете использовать методы `onQueue()` и `onConnection()`. Эти методы следует вызывать перед вызовом метода `dispatch`:

    ProcessPodcast::withChain([
        new OptimizePodcast,
        new ReleasePodcast
    ])->onConnection('redis')->onQueue('podcasts')->dispatch();

Обратите внимание, что это изменение влияет только на код, использующий метод `withChain`. `allOnQueue()` и `allOnConnection()` по-прежнему доступны при использовании глобального помощника `dispatch()`.

<a name="failed-jobs-table-batch-support"></a>
#### Пакетная поддержка таблицы невыполненных заданий

**Вероятность воздействия: Необязательная**

Если Вы планируете использовать функции [пакетирование заданий](/docs/{{version}}/queues#job-batching) Laravel 8.x, Ваша таблица базы данных `failed_jobs` должна быть обновлена. Во-первых, в Вашу таблицу должен быть добавлен новый столбец `uuid`:

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::table('failed_jobs', function (Blueprint $table) {
        $table->string('uuid')->after('id')->nullable()->unique();
    });

Затем параметр конфигурации `failed.driver` в Вашем конфигурационном файле `queue` должен быть обновлен до `database-uuids`.

Кроме того, Вы можете создать UUID для существующих неудачных заданий:

    DB::table('failed_jobs')->whereNull('uuid')->cursor()->each(function ($job) {
        DB::table('failed_jobs')
            ->where('id', $job->id)
            ->update(['uuid' => (string) Illuminate\Support\Str::uuid()]);
    });

<a name="routing"></a>
### Маршрутизация

<a name="automatic-controller-namespace-prefixing"></a>
#### Автоматическое префикс пространства имен контроллера

**Вероятность воздействия: Необязательная**

В предыдущих выпусках Laravel класс `RouteServiceProvider` содержал свойство `$namespace` со значением `App\Http\Controllers`. Это значение этого свойства использовалось для автоматического префикса объявлений маршрута контроллера и генерации URL маршрута контроллера, например, при вызове вспомогательной функции `action`.

В Laravel 8 для этого свойства по умолчанию установлено значение `null`. Это позволяет объявлениям маршрутов Вашего контроллера использовать стандартный вызываемый синтаксис PHP, который обеспечивает лучшую поддержку перехода к классу контроллера во многих IDE:

    use App\Http\Controllers\UserController;

    // Использование вызываемого синтаксиса PHP...
    Route::get('/users', [UserController::class, 'index']);

    // Использование строкового синтаксиса...
    Route::get('/users', 'App\Http\Controllers\UserController@index');

В большинстве случаев это не повлияет на приложения, которые обновляются, потому что Ваш `RouteServiceProvider` все еще будет содержать свойство `$namespace` с его предыдущим значением. Однако, если Вы обновите свое приложение, создав новый проект Laravel, Вы можете столкнуться с этим как критическое изменение.

Если Вы хотите продолжить использование исходной маршрутизации контроллера с автоматическим префиксом, Вы можете просто установить значение свойства `$namespace` в своем `RouteServiceProvider` и обновить регистрации маршрута в методе `boot`, чтобы использовать свойство пространства имен `$namespace`:

    class RouteServiceProvider extends ServiceProvider
    {
        /**
         * The path to the "home" route for your application.
         *
         * This is used by Laravel authentication to redirect users after login.
         *
         * @var string
         */
        public const HOME = '/home';

        /**
         * If specified, this namespace is automatically applied to your controller routes.
         *
         * In addition, it is set as the URL generator's root namespace.
         *
         * @var string
         */
        protected $namespace = 'App\Http\Controllers';

        /**
         * Define your route model bindings, pattern filters, etc.
         *
         * @return void
         */
        public function boot()
        {
            $this->configureRateLimiting();

            $this->routes(function () {
                Route::middleware('web')
                    ->namespace($this->namespace)
                    ->group(base_path('routes/web.php'));

                Route::prefix('api')
                    ->middleware('api')
                    ->namespace($this->namespace)
                    ->group(base_path('routes/api.php'));
            });
        }

        /**
         * Configure the rate limiters for the application.
         *
         * @return void
         */
        protected function configureRateLimiting()
        {
            RateLimiter::for('api', function (Request $request) {
                return Limit::perMinute(60);
            });
        }
    }

<a name="scheduling"></a>
### Планирование

<a name="the-cron-expression-library"></a>
#### Библиотека `cron-expression`

**Вероятность воздействия: Низкая**

Зависимость Laravel от `dragonmantank/cron-expression` была обновлена с `2.x` до `3.x`. Это не должно вызывать каких-либо критических изменений в Вашем приложении, если Вы не взаимодействуете напрямую с библиотекой `cron-expression`. Если Вы напрямую взаимодействуете с этой библиотекой, просмотрите ее [журнал изменений](https://github.com/dragonmantank/cron-expression/blob/master/CHANGELOG.md).

<a name="session"></a>
### Сессия

<a name="the-session-contract"></a>
#### Контракт `Session`

**Вероятность воздействия: Низкая**

Контракт `Illuminate\Contracts\Session\Session` получил новый метод `pull`. Если Вы реализуете этот контракт вручную, Вам следует соответствующим образом обновить свою реализацию

    /**
     * Get the value of a given key and then forget it.
     *
     * @param  string  $key
     * @param  mixed  $default
     * @return mixed
     */
    public function pull($key, $default = null);

<a name="testing"></a>
### Тестирование

<a name="decode-response-json-method"></a>
#### Метод `decodeResponseJson`

**Вероятность воздействия: Низкая**

Метод `decodeResponseJson`, принадлежащий классу `Illuminate\Testing\TestResponse` , больше не принимает никаких аргументов. Пожалуйста, подумайте об использовании вместо этого метода `json`.

<a name="assert-exact-json-method"></a>
#### Метод `assertExactJson`

**Вероятность воздействия: Средняя**

Метод `assertExactJson` теперь требует, чтобы числовые ключи сравниваемых массивов совпадали и располагались в том же порядке. Если Вы хотите сравнить JSON с массивом, не требуя, чтобы массивы с числовыми ключами имели одинаковый порядок, Вы можете вместо этого использовать метод `assertSimilarJson`.

<a name="validation"></a>
### Валидация

<a name="database-rule-connections"></a>
### Правила подключения базы данных

**Вероятность воздействия: Низкая**

Правила `unique` и `exists` теперь будут учитывать указанное имя соединения (доступное через метод `getConnectionName` модели) моделей Eloquent при выполнении запросов.

<a name="miscellaneous"></a>
### Разное

Мы также рекомендуем Вам просматривать изменения в `laravel/laravel` [репозитории GitHub](https://github.com/laravel/laravel). Хотя многие из этих изменений не требуются, Вы можете захотеть синхронизировать эти файлы с Вашим приложением. Некоторые из этих изменений будут рассмотрены в этом руководстве по обновлению, но другие, такие как изменения файлов конфигурации или комментарии, не будут. Вы можете легко просмотреть изменения с помощью [инструмента сравнения GitHub](https://github.com/laravel/laravel/compare/7.x...master) и выбрать, какие обновления важны для Вас.
