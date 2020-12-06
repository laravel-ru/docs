# Контейнер служб

- [Введение](#introduction)
- [Привязка](#binding)
    - [Основы привязки](#binding-basics)
    - [Привязка интерфейсов к реализациям](#binding-interfaces-to-implementations)
    - [Контекстная привязка](#contextual-binding)
    - [Связывание примитивов](#binding-primitives)
    - [Привязка типизированных переменных](#binding-typed-variadics)
    - [Теги](#tagging)
    - [Расширение привязок](#extending-bindings)
- [Разрешение](#resolving)
    - [Метод создания](#the-make-method)
    - [Автоматическая инъекция](#automatic-injection)
- [События контейнера](#container-events)
- [PSR-11](#psr-11)

<a name="introduction"></a>
## Введение

Сервисный контейнер Laravel - это мощный инструмент для управления зависимостями классов и выполнения внедрения зависимостей. Внедрение зависимостей - это причудливая фраза, которая по существу означает следующее: зависимости классов «вводятся» в класс через конструктор или, в некоторых случаях, методы «установки».

Давайте посмотрим на простой пример:

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Repositories\UserRepository;
    use App\Models\User;

    class UserController extends Controller
    {
        /**
         * The user repository implementation.
         *
         * @var UserRepository
         */
        protected $users;

        /**
         * Create a new controller instance.
         *
         * @param  UserRepository  $users
         * @return void
         */
        public function __construct(UserRepository $users)
        {
            $this->users = $users;
        }

        /**
         * Show the profile for the given user.
         *
         * @param  int  $id
         * @return Response
         */
        public function show($id)
        {
            $user = $this->users->find($id);

            return view('user.profile', ['user' => $user]);
        }
    }

В этом примере `UserController` необходимо получить пользователей из источника данных. Итак, мы **внедрим** службу, которая может получать пользователей. В этом контексте наш `UserRepository`, скорее всего, использует [Eloquent](/docs/{{version}}/eloquent) для извлечения информации о пользователе из базы данных. Однако, поскольку репозиторий внедрен, мы можем легко заменить его другой реализацией. Мы также можем легко "имитировать" или создать фиктивную реализацию `UserRepository` при тестировании нашего приложения.

Глубокое понимание сервис контейнера Laravel необходимо для создания мощного, большого приложения, а также для внесения вклада в само ядро Laravel.

<a name="binding"></a>
## Привязка

<a name="binding-basics"></a>
### Основы привязки

Почти все Ваши привязки контейнеров служб будут зарегистрированы в [сервис провайдерах](/docs/{{version}}/providers), поэтому в большинстве этих примеров будет продемонстрировано использование контейнера в этом контексте.

> {tip} Нет необходимости привязывать классы к контейнеру, если они не зависят от каких-либо интерфейсов. Контейнеру не нужно указывать, как создавать эти объекты, поскольку он может автоматически разрешать эти объекты с помощью отражения.

<a name="simple-bindings"></a>
#### Простые привязки

Внутри сервис провайдера у Вас всегда есть доступ к контейнеру через свойство `$this->app`. Мы можем зарегистрировать привязку, используя метод `bind`, передав имя класса или интерфейса, который мы хотим зарегистрировать, вместе с `Closure`, возвращающим экземпляр класса:

    $this->app->bind('HelpSpot\API', function ($app) {
        return new \HelpSpot\API($app->make('HttpClient'));
    });

Обратите внимание, что мы получаем сам контейнер в качестве аргумента для распознавателя. Затем мы можем использовать контейнер для разрешения подчиненных зависимостей объекта, который мы создаем.

<a name="binding-a-singleton"></a>
#### Связывание синглтона

Метод `singleton` связывает класс или интерфейс с контейнером, который должен разрешаться только один раз. Как только привязка `singleton` разрешена, тот же экземпляр объекта будет возвращен при последующих вызовах контейнера:

    $this->app->singleton('HelpSpot\API', function ($app) {
        return new \HelpSpot\API($app->make('HttpClient'));
    });

<a name="binding-instances"></a>
#### Привязка экземпляров

Вы также можете привязать существующий экземпляр объекта к контейнеру, используя метод `instance`. Данный экземпляр всегда будет возвращаться при последующих вызовах контейнера:

    $api = new \HelpSpot\API(new HttpClient);

    $this->app->instance('HelpSpot\API', $api);

<a name="binding-interfaces-to-implementations"></a>
### Привязка интерфейсов к реализациям

Очень мощная функция контейнера служб - это его способность связывать интерфейс с заданной реализацией. Например, предположим, что у нас есть интерфейс `EventPusher` и реализация `RedisEventPusher`. После того, как мы закодировали нашу реализацию этого интерфейса `RedisEventPusher`, мы можем зарегистрировать его в сервисном контейнере следующим образом:

    $this->app->bind(
        'App\Contracts\EventPusher',
        'App\Services\RedisEventPusher'
    );

Этот оператор сообщает контейнеру, что он должен внедрить `RedisEventPusher`, когда классу требуется реализация `EventPusher`. Теперь мы можем указать интерфейс `EventPusher` в конструкторе или в любом другом месте, где зависимости внедряются контейнером службы:

    use App\Contracts\EventPusher;

    /**
     * Create a new class instance.
     *
     * @param  EventPusher  $pusher
     * @return void
     */
    public function __construct(EventPusher $pusher)
    {
        $this->pusher = $pusher;
    }

<a name="contextual-binding"></a>
### Контекстная привязка

Иногда у Вас может быть два класса, которые используют один и тот же интерфейс, но Вы хотите внедрить разные реализации в каждый класс. Например, два контроллера могут зависеть от различных реализаций `Illuminate\Contracts\Filesystem\Filesystem` [контракта](/docs/{{version}}/contracts). Laravel предоставляет простой и понятный интерфейс для определения этого поведения:

    use App\Http\Controllers\PhotoController;
    use App\Http\Controllers\UploadController;
    use App\Http\Controllers\VideoController;
    use Illuminate\Contracts\Filesystem\Filesystem;
    use Illuminate\Support\Facades\Storage;

    $this->app->when(PhotoController::class)
              ->needs(Filesystem::class)
              ->give(function () {
                  return Storage::disk('local');
              });

    $this->app->when([VideoController::class, UploadController::class])
              ->needs(Filesystem::class)
              ->give(function () {
                  return Storage::disk('s3');
              });

<a name="binding-primitives"></a>
### Связывание примитивов

Иногда у Вас может быть класс, который получает некоторые внедренные классы, но также требует внедренного примитивного значения, такого как целое число. Вы можете легко использовать контекстную привязку, чтобы ввести любое значение, которое может понадобиться Вашему классу:

    $this->app->when('App\Http\Controllers\UserController')
              ->needs('$variableName')
              ->give($value);

Иногда класс может зависеть от массива помеченных экземпляров. Используя метод `giveTagged`, Вы можете легко внедрить все привязки контейнера с этим тегом:

    $this->app->when(ReportAggregator::class)
        ->needs('$reports')
        ->giveTagged('reports');

<a name="binding-typed-variadics"></a>
### Привязка типизированных переменных

Иногда у Вас может быть класс, который получает массив типизированных объектов с использованием аргумента вариативного конструктора:

    class Firewall
    {
        protected $logger;
        protected $filters;

        public function __construct(Logger $logger, Filter ...$filters)
        {
            $this->logger = $logger;
            $this->filters = $filters;
        }
    }

Используя контекстную привязку, Вы можете разрешить эту зависимость, предоставив методу `give` метод с замыканием, который возвращает массив разрешенных экземпляров `Filter`:

    $this->app->when(Firewall::class)
              ->needs(Filter::class)
              ->give(function ($app) {
                    return [
                        $app->make(NullFilter::class),
                        $app->make(ProfanityFilter::class),
                        $app->make(TooLongFilter::class),
                    ];
              });

Для удобства Вы также можете просто предоставить массив имен классов, которые будут разрешены контейнером всякий раз, когда `Firewall` потребуются экземпляры `Filter`:

    $this->app->when(Firewall::class)
              ->needs(Filter::class)
              ->give([
                  NullFilter::class,
                  ProfanityFilter::class,
                  TooLongFilter::class,
              ]);

<a name="variadic-tag-dependencies"></a>
#### Вариативные зависимости тегов

Иногда класс может иметь вариативную зависимость, указывающую на тип как данный класс (`Report ...$reports`). Используя методы `needs` и `giveTagged`, Вы можете легко внедрить все привязки контейнеров с этим тегом для данной зависимости:

    $this->app->when(ReportAggregator::class)
        ->needs(Report::class)
        ->giveTagged('reports');

<a name="tagging"></a>
### Теги

Иногда может потребоваться разрешить все привязки определенной «категории». Например, возможно, Вы создаете агрегатор отчетов, который получает массив множества различных реализаций интерфейса `Report`. После регистрации реализаций `Report` Вы можете назначить им тег с помощью метода `tag`:

    $this->app->bind('SpeedReport', function () {
        //
    });

    $this->app->bind('MemoryReport', function () {
        //
    });

    $this->app->tag(['SpeedReport', 'MemoryReport'], 'reports');

После того, как сервисы были помечены, Вы можете легко разрешить их все с помощью метода `tagged`:

    $this->app->bind('ReportAggregator', function ($app) {
        return new ReportAggregator($app->tagged('reports'));
    });

<a name="extending-bindings"></a>
### Расширение привязок

Метод `extend` позволяет изменять разрешенные службы. Например, когда служба разрешена, Вы можете запустить дополнительный код для украшения или настройки службы. Метод `extend` принимает замыкание, которое должно возвращать измененную службу в качестве единственного аргумента замыкания получает разрешаемую службу и экземпляр контейнера:

    $this->app->extend(Service::class, function ($service, $app) {
        return new DecoratedService($service);
    });

<a name="resolving"></a>
## Разрешение

<a name="the-make-method"></a>
#### Метод `make`

Вы можете использовать метод `make` для извлечения экземпляра класса из контейнера. Метод `make` принимает имя класса или интерфейса, который Вы хотите разрешить:

    $api = $this->app->make('HelpSpot\API');

Если Вы находитесь в месте расположения кода, у которого нет доступа к переменной `$app`, Вы можете использовать глобальный помощник `resolve`:

    $api = resolve('HelpSpot\API');

Если некоторые зависимости Вашего класса не могут быть разрешены через контейнер, Вы можете ввести их, передав их как ассоциативный массив в метод `makeWith`:

    $api = $this->app->makeWith('HelpSpot\API', ['id' => 1]);

<a name="automatic-injection"></a>
#### Автоматическая инъекция

В качестве альтернативы, что важно, Вы можете «указать тип» зависимости в конструкторе класса, который разрешается контейнером, включая [контроллеры](/docs/{{version}}/controllers), [прослушиватели событий](/docs/{{version}}/events), [middleware](/docs/{{version}}/middleware) и др. Кроме того, Вы можете указать зависимости типа `handle` в методе [заданий в очереди](/docs/{{version}}/queues). На практике именно так контейнер должен разрешать большинство Ваших объектов.

Например, Вы можете указать репозиторий, определенный Вашим приложением, в конструкторе контроллера. Репозиторий будет автоматически разрешен и введен в класс:

    <?php

    namespace App\Http\Controllers;

    use App\Models\Users\Repository as UserRepository;

    class UserController extends Controller
    {
        /**
         * The user repository instance.
         */
        protected $users;

        /**
         * Create a new controller instance.
         *
         * @param  UserRepository  $users
         * @return void
         */
        public function __construct(UserRepository $users)
        {
            $this->users = $users;
        }

        /**
         * Show the user with the given ID.
         *
         * @param  int  $id
         * @return Response
         */
        public function show($id)
        {
            //
        }
    }

<a name="container-events"></a>
## События контейнера

Сервисный контейнер запускает событие каждый раз, когда разрешает объект. Вы можете прослушать это событие с помощью метода `resolving`:

    $this->app->resolving(function ($object, $app) {
        // Вызывается, когда контейнер разрешает объект любого типа...
    });

    $this->app->resolving(\HelpSpot\API::class, function ($api, $app) {
        // Вызывается, когда контейнер разрешает объекты типа "HelpSpot\API"...
    });

Как видите, решаемый объект будет передан в обратный вызов, что позволит Вам установить любые дополнительные свойства объекта, прежде чем он будет передан его потребителю.

<a name="psr-11"></a>
## PSR-11

Сервисный контейнер Laravel реализует интерфейс [PSR-11](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-11-container.md). Поэтому Вы можете ввести подсказку к интерфейсу контейнера PSR-11, чтобы получить экземпляр контейнера Laravel:

    use Psr\Container\ContainerInterface;

    Route::get('/', function (ContainerInterface $container) {
        $service = $container->get('Service');

        //
    });

Если данный идентификатор не может быть разрешен, создается исключение. Исключение будет экземпляром `Psr\Container\NotFoundExceptionInterface`, если идентификатор никогда не был привязан. Если идентификатор был привязан, но не удалось разрешить, будет брошен экземпляр `Psr\Container\ContainerExceptionInterface`.
