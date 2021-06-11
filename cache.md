# Кэш

- [Введение](#introduction)
- [Конфигурация](#configuration)
    - [Требования к драйверам](#driver-prerequisites)
- [Использование кеша](#cache-usage)
    - [Получение экземпляра кеша](#obtaining-a-cache-instance)
    - [Получение элементов из кеша](#retrieving-items-from-the-cache)
    - [Хранение элементов в кэше](#storing-items-in-the-cache)
    - [Удаление элементов из кеша](#removing-items-from-the-cache)
    - [Помощник кеша](#the-cache-helper)
- [Теги кеша](#cache-tags)
    - [Хранение элементов кэша с тегами](#storing-tagged-cache-items)
    - [Доступ к элементам кэша с тегами](#accessing-tagged-cache-items)
    - [Удаление элементов кеша с тегами](#removing-tagged-cache-items)
- [Атомарные блокировки](#atomic-locks)
    - [Требования к драйверам](#lock-driver-prerequisites)
    - [Управление блокировками](#managing-locks)
    - [Управление блокировками между процессами](#managing-locks-across-processes)
- [Добавление собственных драйверов кэша](#adding-custom-cache-drivers)
    - [Написание драйвера](#writing-the-driver)
    - [Регистрация драйвера](#registering-the-driver)
- [События](#events)

<a name="introduction"></a>
## Введение

Некоторые задачи извлечения или обработки данных, выполняемые вашим приложением, могут потребовать больших ресурсов ЦП или занять несколько секунд. В этом случае извлеченные данные обычно кэшируют на какое-то время, чтобы их можно было быстро извлечь при последующих запросах тех же данных. Кэшированные данные обычно хранятся в очень быстром хранилище данных, таком как [Memcached](https://memcached.org) или [Redis](https://redis.io).

К счастью, Laravel предоставляет выразительный унифицированный API для различных механизмов кеширования, что позволяет вам воспользоваться их невероятно быстрым извлечением данных и ускорить работу вашего веб-приложения.

<a name="configuration"></a>
## Конфигурация

Файл конфигурации кеша вашего приложения находится в `config/cache.php`. В этом файле вы можете указать, какой драйвер кеша вы хотите использовать по умолчанию во всем приложении. Laravel поддерживает популярные серверы кеширования, такие как [Memcached](https://memcached.org), [Redis](https://redis.io), [DynamoDB](https://aws.amazon.com/dynamodb) и реляционные базы данных из коробки. Кроме того, доступен драйвер кеширования на основе файлов, а драйверы кеш-памяти `array` и `null` предоставляют удобные механизмы кэширования для ваших автоматических тестов.

Файл конфигурации кеша также содержит различные другие параметры, которые задокументированы в файле, поэтому обязательно прочтите эти параметры. По умолчанию Laravel настроен на использование драйвера кеширования файлов `file`, который хранит сериализованные кэшированные объекты в файловой системе сервера. Для более крупных приложений рекомендуется использовать более надежный драйвер, например Memcached или Redis. Вы даже можете настроить несколько конфигураций кеша для одного и того же драйвера.

<a name="driver-prerequisites"></a>
### Требования к драйверам

<a name="prerequisites-database"></a>
#### База данных

При использовании драйвера кэша `database` вам нужно будет настроить таблицу для хранения элементов кеша. Вы найдете пример объявления `Schema` в таблице ниже:

    Schema::create('cache', function ($table) {
        $table->string('key')->unique();
        $table->text('value');
        $table->integer('expiration');
    });

> {tip} Вы также можете использовать Artisan-команду `php artisan cache:table` для генерации миграции с правильной схемой.

<a name="memcached"></a>
#### Memcached

Для использования драйвера Memcached требуется установить [пакет Memcached PECL](https://pecl.php.net/package/memcached). Вы можете перечислить все ваши серверы Memcached в файле конфигурации `config/cache.php`. Этот файл уже содержит запись `memcached.servers` для начала:

    'memcached' => [
        'servers' => [
            [
                'host' => env('MEMCACHED_HOST', '127.0.0.1'),
                'port' => env('MEMCACHED_PORT', 11211),
                'weight' => 100,
            ],
        ],
    ],

Если необходимо, вы можете установить опцию `host` на путь сокета UNIX. Если вы это сделаете, опция `port` должна быть установлена в `0`:

    'memcached' => [
        [
            'host' => '/var/run/memcached/memcached.sock',
            'port' => 0,
            'weight' => 100
        ],
    ],

<a name="redis"></a>
#### Redis

Перед использованием кеша Redis с Laravel вам нужно будет либо установить расширение PHP PhpRedis через PECL, либо установить пакет `predis/predis` (~1.0) через Composer. [Laravel Sail](/docs/{{version}}/sail) уже включает это расширение. Кроме того, на официальных платформах развертывания Laravel, таких как [Laravel Forge](https://forge.laravel.com) и [Laravel Vapor](https://vapor.laravel.com), по умолчанию установлено расширение PhpRedis.

Для получения дополнительной информации о настройке Redis обратитесь к его [странице документации](/docs/{{version}}/redis#configuration).

<a name="dynamodb"></a>
#### DynamoDB

Перед использованием драйвера кеширования [DynamoDB](https://aws.amazon.com/dynamodb) необходимо создать таблицу DynamoDB для хранения всех кэшированных данных. Обычно эта таблица должна называться `cache`. Однако вы должны назвать таблицу на основе значения значения конфигурации `stores.dynamodb.table` в файле конфигурации `cache` вашего приложения.

Эта таблица также должна иметь строковый ключ раздела с именем, которое соответствует значению элемента конфигурации `stores.dynamodb.key` в файле конфигурации `cache` вашего приложения. По умолчанию ключ раздела должен называться `key`.

<a name="cache-usage"></a>
## Использование кеша

<a name="obtaining-a-cache-instance"></a>
### Получение экземпляра кеша

Чтобы получить экземпляр хранилища кеша, вы можете использовать фасад `Cache`, который мы будем использовать в этой документации. Фасад `Cache` обеспечивает удобный и краткий доступ к базовым реализациям контрактов кеширования Laravel:

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Support\Facades\Cache;

    class UserController extends Controller
    {
        /**
         * Show a list of all users of the application.
         *
         * @return Response
         */
        public function index()
        {
            $value = Cache::get('key');

            //
        }
    }

<a name="accessing-multiple-cache-stores"></a>
#### Доступ к нескольким кеш-хранилищам

Используя фасад `Cache`, вы можете получить доступ к различным хранилищам кеша с помощью метода `store`. Ключ, переданный методу `store`, должен соответствовать одному из хранилищ, перечисленных в массиве конфигурации `stores` в вашем конфигурационном файле `cache`:

    $value = Cache::store('file')->get('foo');

    Cache::store('redis')->put('bar', 'baz', 600); // 10 Minutes

<a name="retrieving-items-from-the-cache"></a>
### Получение элементов из кеша

Метод `get` фасада `Cache` используется для извлечения элементов из кеша. Если элемент не существует в кеше, будет возвращено значение `null`. При желании вы можете передать второй аргумент методу `get`, указав значение по умолчанию, которое вы хотите вернуть, если элемент не существует:

    $value = Cache::get('key');

    $value = Cache::get('key', 'default');

Вы даже можете передать замыкание в качестве значения по умолчанию. Результат замыкания будет возвращен, если указанный элемент не существует в кеше. Передача замыкания позволяет отложить получение значений по умолчанию из базы данных или другой внешней службы:

    $value = Cache::get('key', function () {
        return DB::table(...)->get();
    });

<a name="checking-for-item-existence"></a>
#### Проверка существования элемента

Метод `has` может использоваться для определения того, существует ли элемент в кэше. Этот метод также вернет `false`, если элемент существует, но его значение равно `null`:

    if (Cache::has('key')) {
        //
    }

<a name="incrementing-decrementing-values"></a>
#### Увеличение / уменьшение значений

Методы `increment` и `decrement` могут использоваться для корректировки значения целочисленных элементов в кэше. Оба эти метода принимают необязательный второй аргумент, указывающий величину увеличения или уменьшения значения элемента:

    Cache::increment('key');
    Cache::increment('key', $amount);
    Cache::decrement('key');
    Cache::decrement('key', $amount);

<a name="retrieve-store"></a>
#### Получить и сохранить

Иногда вы можете захотеть получить элемент из кеша, но также сохранить значение по умолчанию, если запрошенный элемент не существует. Например, вы можете захотеть получить всех пользователей из кеша или, если они не существуют, извлечь их из базы данных и добавить их в кеш. Вы можете сделать это с помощью метода `Cache::remember`:

    $value = Cache::remember('users', $seconds, function () {
        return DB::table('users')->get();
    });

Если элемент не существует в кеше, замыкание, переданное методу `remember`, будет выполнено, и его результат будет помещен в кеш.

Вы можете использовать метод `rememberForever`, чтобы получить элемент из кеша или сохранить его навсегда, если он не существует:

    $value = Cache::rememberForever('users', function () {
        return DB::table('users')->get();
    });

<a name="retrieve-delete"></a>
#### Получить и удалить

Если вам нужно получить элемент из кеша, а затем удалить этот элемент, вы можете использовать метод `pull`. Как и метод `get`, `null` будет возвращен, если элемент не существует в кеше:

    $value = Cache::pull('key');

<a name="storing-items-in-the-cache"></a>
### Хранение элементов в кэше

Вы можете использовать метод `put` на фасаде `Cache` для хранения элементов в кеше:

    Cache::put('key', 'value', $seconds = 10);

Если время хранения не передается методу `put`, элемент будет храниться бесконечно:

    Cache::put('key', 'value');

Вместо того, чтобы передавать количество секунд в виде целого числа, вы также можете передать экземпляр `DateTime`, представляющий желаемое время истечения кэшированного элемента:

    Cache::put('key', 'value', now()->addMinutes(10));

<a name="store-if-not-present"></a>
#### Сохранить при отсутствии

Метод `add` добавит элемент в кеш, только если он еще не существует в хранилище кеша. Метод вернет `true`, если элемент действительно добавлен в кеш. В противном случае метод вернет `false`. Метод `add` - это атомарная операция:

    Cache::add('key', 'value', $seconds);

<a name="storing-items-forever"></a>
#### Сохранение элементов навсегда

Метод `forever` может использоваться для постоянного хранения элемента в кеше. Поскольку срок действия этих элементов не истекает, их необходимо вручную удалить из кеша с помощью метода `forget`:

    Cache::forever('key', 'value');

> {tip} Если вы используете драйвер Memcached, элементы, которые хранятся «навсегда», могут быть удалены, когда кэш достигнет предельного размера.

<a name="removing-items-from-the-cache"></a>
### Удаление элементов из кеша

Вы можете удалить элементы из кеша с помощью метода `forget`:

    Cache::forget('key');

Вы также можете удалить элементы, указав нулевое или отрицательное количество секунд истечения срока действия:

    Cache::put('key', 'value', 0);

    Cache::put('key', 'value', -5);

Вы можете очистить весь кеш, используя метод `flush`:

    Cache::flush();

> {note} Очистка кеша не учитывает ваш настроенный «префикс» кеша и удаляет все записи из кеша. Внимательно учтите это при очистке кеша, который используется другими приложениями.

<a name="the-cache-helper"></a>
### Помощник кеша

Помимо использования фасада `Cache`, вы также можете использовать глобальную функцию `cache` для извлечения и хранения данных через кеш. Когда функция `cache` вызывается с одним строковым аргументом, она возвращает значение данного ключа:

    $value = cache('key');

Если вы предоставите массив пар ключ / значение и срок действия функции, она сохранит значения в кеше в течение указанного времени:

    cache(['key' => 'value'], $seconds);

    cache(['key' => 'value'], now()->addMinutes(10));

Когда функция `cache` вызывается без каких-либо аргументов, она возвращает экземпляр реализации `Illuminate\Contracts\Cache\Factory`, позволяя вам вызывать другие методы кеширования:

    cache()->remember('users', $seconds, function () {
        return DB::table('users')->get();
    });

> {tip} При тестировании вызова глобальной функции `cache` вы можете использовать метод `Cache::shouldReceive` так же, как если бы вы [тестировали фасад](/docs/{{version}}/mocking#mocking-facades).

<a name="cache-tags"></a>
## Теги кеша

> {note} Теги кеширования не поддерживаются при использовании драйверов кеширования `file`, `dynamodb` или `database`. Более того, при использовании нескольких тегов с кешами, которые хранятся «навсегда», производительность будет лучше с таким драйвером, как `memcached`, , который автоматически очищает устаревшие записи.

<a name="storing-tagged-cache-items"></a>
### Хранение элементов кэша с тегами

Теги кэша позволяют помечать связанные элементы в кеше, а затем сбрасывать все кэшированные значения, которым был назначен данный тег. Вы можете получить доступ к тегированному кешу, передав упорядоченный массив имен тегов. Например, давайте обратимся к тегированному кешу и `put` значение в кеш:

    Cache::tags(['people', 'artists'])->put('John', $john, $seconds);

    Cache::tags(['people', 'authors'])->put('Anne', $anne, $seconds);

<a name="accessing-tagged-cache-items"></a>
### Доступ к элементам кэша с тегами

Чтобы получить помеченный элемент кеша, передайте тот же упорядоченный список тегов методу `tags`, а затем вызовите метод `get` с ключом, который вы хотите получить:

    $john = Cache::tags(['people', 'artists'])->get('John');

    $anne = Cache::tags(['people', 'authors'])->get('Anne');

<a name="removing-tagged-cache-items"></a>
### Удаление элементов кеша с тегами

Вы можете удалить все элементы, которым назначен тег или список тегов. Например, этот оператор удалит все кеши, помеченные как `people`, `authors` или и то, и другое. Таким образом, и `Anne` и `John` будут удалены из кеша:

    Cache::tags(['people', 'authors'])->flush();

Напротив, этот оператор удалит только кешированные значения, помеченные как `authors`, поэтому будет удалена `Anne`, но не `John`:

    Cache::tags('authors')->flush();

<a name="atomic-locks"></a>
## Атомарные блокировки

> {note} Чтобы использовать эту функцию, ваше приложение должно использовать драйвер кэша `memcached`, `redis`, `dynamodb`, `database`, `file` или `array` в качестве драйвера кэша по умолчанию для вашего приложения. Кроме того, все серверы должны взаимодействовать с одним и тем же сервером центрального кеша.

<a name="lock-driver-prerequisites"></a>
### Требования к драйверам

<a name="atomic-locks-prerequisites-database"></a>
#### База данных

При использовании драйвера кеша `database` вам нужно будет настроить таблицу, в которой будут храниться блокировки кеша вашего приложения. Вы найдете пример объявления `Schema` в таблице ниже:

    Schema::create('cache_locks', function ($table) {
        $table->string('key')->primary();
        $table->string('owner');
        $table->integer('expiration');
    });

<a name="managing-locks"></a>
### Управление блокировками

Атомарные блокировки позволяют манипулировать распределенными блокировками, не беспокоясь об условиях гонки. Например, [Laravel Forge](https://forge.laravel.com) использует атомарные блокировки, чтобы гарантировать, что на сервере одновременно выполняется только одна удаленная задача. Вы можете создавать и управлять блокировками, используя метод `Cache::lock`:

    use Illuminate\Support\Facades\Cache;

    $lock = Cache::lock('foo', 10);

    if ($lock->get()) {
        // Lock acquired for 10 seconds...

        $lock->release();
    }

Метод `get` также принимает замыкание. После выполнения замыкания Laravel автоматически снимает блокировку:

    Cache::lock('foo')->get(function () {
        // Lock acquired indefinitely and automatically released...
    });

Если блокировка недоступна в тот момент, когда вы ее запрашиваете, вы можете указать Laravel подождать определенное количество секунд. Если блокировка не может быть получена в течение указанного срока, будет сгенерировано исключение `Illuminate\Contracts\Cache\LockTimeoutException`:

    use Illuminate\Contracts\Cache\LockTimeoutException;

    $lock = Cache::lock('foo', 10);

    try {
        $lock->block(5);

        // Lock acquired after waiting a maximum of 5 seconds...
    } catch (LockTimeoutException $e) {
        // Unable to acquire lock...
    } finally {
        optional($lock)->release();
    }

Приведенный выше пример можно упростить, передав замыкание методу `block`. Когда замыкание передается этому методу, Laravel будет пытаться получить блокировку на указанное количество секунд и автоматически снимет блокировку после того, как замыкание будет выполнено:

    Cache::lock('foo', 10)->block(5, function () {
        // Lock acquired after waiting a maximum of 5 seconds...
    });

<a name="managing-locks-across-processes"></a>
### Управление блокировками между процессами

Иногда вам может потребоваться установить блокировку в одном процессе и снять ее в другом процессе. Например, вы можете получить блокировку во время веб-запроса и захотите снять блокировку в конце задания в очереди, которое запускается этим запросом. В этом сценарии вы должны передать «токен владельца» с областью действия блокировки в задание в очереди, чтобы задание могло повторно создать экземпляр блокировки с использованием данного токена.

В приведенном ниже примере мы отправим задание в очередь, если блокировка будет успешно получена. Кроме того, мы передадим токен владельца блокировки заданию в очереди с помощью метода блокировки `owner`:

    $podcast = Podcast::find($id);

    $lock = Cache::lock('processing', 120);

    if ($result = $lock->get()) {
        ProcessPodcast::dispatch($podcast, $lock->owner());
    }

В рамках задания `ProcessPodcast` нашего приложения мы можем восстановить и снять блокировку, используя токен владельца:

    Cache::restoreLock('processing', $this->owner)->release();

Если вы хотите снять блокировку, не уважая ее текущего владельца, вы можете использовать метод `forceRelease`:

    Cache::lock('processing')->forceRelease();

<a name="adding-custom-cache-drivers"></a>
## Добавление собственных драйверов кэша

<a name="writing-the-driver"></a>
### Написание драйвера

Чтобы создать наш собственный драйвер кеша, нам сначала нужно реализовать [контракт](/docs/{{version}}/contracts) `Illuminate\Contracts\Cache\Store`. Итак, реализация кеша MongoDB может выглядеть примерно так:

    <?php

    namespace App\Extensions;

    use Illuminate\Contracts\Cache\Store;

    class MongoStore implements Store
    {
        public function get($key) {}
        public function many(array $keys) {}
        public function put($key, $value, $seconds) {}
        public function putMany(array $values, $seconds) {}
        public function increment($key, $value = 1) {}
        public function decrement($key, $value = 1) {}
        public function forever($key, $value) {}
        public function forget($key) {}
        public function flush() {}
        public function getPrefix() {}
    }

Нам просто нужно реализовать каждый из этих методов, используя соединение MongoDB. Пример реализации каждого из этих методов можно найти в `Illuminate\Cache\MemcachedStore` в [исходном коде фреймворка Laravel](https://github.com/laravel/framework). Как только наша реализация будет завершена, мы можем закончить нашу настраиваемую регистрацию драйвера, вызвав метод `extend` фасада `Cache`:

    Cache::extend('mongo', function ($app) {
        return Cache::repository(new MongoStore);
    });

> {tip} Если вам интересно, где разместить собственный код драйвера кеша, вы можете создать пространство имен `Extensions` в своем каталоге `app`. Однако имейте в виду, что Laravel не имеет жесткой структуры приложения, и вы можете организовать свое приложение в соответствии со своими предпочтениями.

<a name="registering-the-driver"></a>
### Регистрация драйвера

Чтобы зарегистрировать настраиваемый драйвер кеша в Laravel, мы будем использовать метод `extend` на фасаде `Cache`. Поскольку другие поставщики услуг могут попытаться прочитать кэшированные значения в рамках своего метода загрузки `boot`, мы зарегистрируем наш настраиваемый драйвер в обратном вызове `booting`. Используя обратный вызов `booting`, мы можем гарантировать, что пользовательский драйвер зарегистрирован непосредственно перед вызовом метода `boot` у сервис провайдера нашего приложения, но после того, как метод `register` вызывается у всех сервис провайдеров. Мы зарегистрируем наш обратный вызов `booting` в методе `register` класса `App\Providers\AppServiceProvider` нашего приложения:

    <?php

    namespace App\Providers;

    use App\Extensions\MongoStore;
    use Illuminate\Support\Facades\Cache;
    use Illuminate\Support\ServiceProvider;

    class CacheServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
         *
         * @return void
         */
        public function register()
        {
            $this->app->booting(function () {
                 Cache::extend('mongo', function ($app) {
                     return Cache::repository(new MongoStore);
                 });
             });
        }

        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            //
        }
    }

Первым аргументом, передаваемым методу `extend`, является имя драйвера. Это будет соответствовать вашей опции `driver` в файле конфигурации `config/cache.php`. Второй аргумент - это замыкание, которое должно возвращать экземпляр `Illuminate\Cache\Repository`. Замыкание будет передано экземпляру `$app`, который является экземпляром [сервис контейнера](/docs/{{version}}/container).

После регистрации расширения обновите параметр `driver` в файле конфигурации `config/cache.php`, указав имя вашего расширения.

<a name="events"></a>
## События

Чтобы выполнить код для каждой операции с кешем, вы можете прослушивать [события](/docs/{{version}}/events), запускаемые кешем. Как правило, вы должны поместить эти прослушиватели событий в класс приложения `App\Providers\EventServiceProvider`:

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        'Illuminate\Cache\Events\CacheHit' => [
            'App\Listeners\LogCacheHit',
        ],

        'Illuminate\Cache\Events\CacheMissed' => [
            'App\Listeners\LogCacheMissed',
        ],

        'Illuminate\Cache\Events\KeyForgotten' => [
            'App\Listeners\LogKeyForgotten',
        ],

        'Illuminate\Cache\Events\KeyWritten' => [
            'App\Listeners\LogKeyWritten',
        ],
    ];
