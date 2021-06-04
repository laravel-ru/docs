# Сервис Контейнер

- [Введение](#introduction)
    - [Нулевое разрешение конфигурации](#zero-configuration-resolution)
    - [Когда использовать контейнер](#when-to-use-the-container)
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

<a name="zero-configuration-resolution"></a>
### Нулевое разрешение конфигурации

If a class has no dependencies or only depends on other concrete classes (not interfaces), the container does not need to be instructed on how to resolve that class. For example, you may place the following code in your `routes/web.php` file:

    <?php

    class Service
    {
        //
    }

    Route::get('/', function (Service $service) {
        die(get_class($service));
    });

In this example, hitting your application's `/` route will automatically resolve the `Service` class and inject it into your route's handler. This is game changing. It means you can develop your application and take advantage of dependency injection without worrying about bloated configuration files.

Thankfully, many of the classes you will be writing when building a Laravel application automatically receive their dependencies via the container, including [controllers](/docs/{{version}}/controllers), [event listeners](/docs/{{version}}/events), [middleware](/docs/{{version}}/middleware), and more. Additionally, you may type-hint dependencies in the `handle` method of [queued jobs](/docs/{{version}}/queues). Once you taste the power of automatic and zero configuration dependency injection it feels impossible to develop without it.

<a name="when-to-use-the-container"></a>
### Когда использовать контейнер

Thanks to zero configuration resolution, you will often type-hint dependencies on routes, controllers, event listeners, and elsewhere without ever manually interacting with the container. For example, you might type-hint the `Illuminate\Http\Request` object on your route definition so that you can easily access the current request. Even though we never have to interact with the container to write this code, it is managing the injection of these dependencies behind the scenes:

    use Illuminate\Http\Request;

    Route::get('/', function (Request $request) {
        // ...
    });

In many cases, thanks to automatic dependency injection and [facades](/docs/{{version}}/facades), you can build Laravel applications without **ever** manually binding or resolving anything from the container. **So, when would you ever manually interact with the container?** Let's examine two situations.

First, if you write a class that implements an interface and you wish to type-hint that interface on a route or class constructor, you must [tell the container how to resolve that interface](#binding-interfaces-to-implementations). Secondly, if you are [writing a Laravel package](/docs/{{version}}/packages) that you plan to share with other Laravel developers, you may need to bind your package's services into the container.

<a name="binding"></a>
## Привязка

<a name="binding-basics"></a>
### Основы привязки

<a name="simple-bindings"></a>
#### Простые привязки

Почти все Ваши привязки сервис контейнеров будут зарегистрированы в [сервис провайдерах](/docs/{{version}}/providers), поэтому в большинстве этих примеров будет продемонстрировано использование контейнера в этом контексте.

Внутри сервис провайдер у Вас всегда есть доступ к контейнеру через свойство `$this->app`. Мы можем зарегистрировать привязку, используя метод `bind`, передав имя класса или интерфейса, которые мы хотим зарегистрировать, вместе с замыканием, которое возвращает экземпляр класса:

    use App\Services\Transistor;
    use App\Services\PodcastParser;

    $this->app->bind(Transistor::class, function ($app) {
        return new Transistor($app->make(PodcastParser::class));
    });

Обратите внимание, что мы получаем сам контейнер в качестве аргумента для распознавателя. Затем мы можем использовать контейнер для разрешения подчиненных зависимостей объекта, который мы создаем.

Как уже упоминалось, Вы обычно будете взаимодействовать с контейнером внутри сервис провайдеров; однако, если Вы хотите взаимодействовать с контейнером вне сервис провайдера, Вы можете сделать это через `App` [фасад](/docs/{{version}}/facades):

    use App\Services\Transistor;
    use Illuminate\Support\Facades\App;

    App::bind(Transistor::class, function ($app) {
        // ...
    });

> {tip} Нет необходимости привязывать классы к контейнеру, если они не зависят от каких-либо интерфейсов. Контейнер не нужно указывать, как создавать эти объекты, поскольку он может автоматически разрешать эти объекты с помощью отражения.

<a name="binding-a-singleton"></a>
#### Связывание синглтона

Метод `singleton` связывает класс или интерфейс с контейнером, который должен разрешаться только один раз. Как только привязка `singleton` разрешена, тот же экземпляр объекта будет возвращен при последующих вызовах контейнера:

    use App\Services\Transistor;
    use App\Services\PodcastParser;

    $this->app->singleton(Transistor::class, function ($app) {
        return new Transistor($app->make(PodcastParser::class));
    });

<a name="binding-instances"></a>
#### Привязка экземпляров

Вы также можете привязать существующий экземпляр объекта к контейнеру, используя метод `instance`. Данный экземпляр всегда будет возвращаться при последующих вызовах контейнера:

    use App\Services\Transistor;
    use App\Services\PodcastParser;

    $service = new Transistor(new PodcastParser);

    $this->app->instance(Transistor::class, $service);

<a name="binding-interfaces-to-implementations"></a>
### Привязка интерфейсов к реализациям

Очень мощная функция контейнера служб - это его способность связывать интерфейс с заданной реализацией. Например, предположим, что у нас есть интерфейс `EventPusher` и реализация `RedisEventPusher`. После того, как мы закодировали нашу реализацию этого интерфейса `RedisEventPusher`, мы можем зарегистрировать его в сервисном контейнере следующим образом:

    use App\Contracts\EventPusher;
    use App\Services\RedisEventPusher;

    $this->app->bind(EventPusher::class, RedisEventPusher::class);

Этот оператор сообщает контейнеру, что он должен внедрить `RedisEventPusher`, когда классу требуется реализация `EventPusher`. Теперь мы можем указать интерфейс `EventPusher` в конструкторе класса, который разрешен контейнером. Помните, что контроллеры, прослушиватели событий, мидлвары и различные другие типы классов в приложениях Laravel всегда разрешаются с помощью контейнера:

    use App\Contracts\EventPusher;

    /**
     * Create a new class instance.
     *
     * @param  \App\Contracts\EventPusher  $pusher
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

Иногда класс может зависеть от массива экземпляров [tagged](#tagging). Используя метод `giveTagged`, Вы можете легко внедрить все привязки контейнера с этим тегом:

    $this->app->when(ReportAggregator::class)
        ->needs('$reports')
        ->giveTagged('reports');

Если вам нужно ввести значение из одного из файлов конфигурации вашего приложения, вы можете использовать метод `giveConfig`:

    $this->app->when(ReportAggregator::class)
        ->needs('$timezone')
        ->giveConfig('app.timezone');

<a name="binding-typed-variadics"></a>
### Привязка типизированных переменных

Иногда у вас может быть класс, который получает массив типизированных объектов с использованием аргумента конструктора с переменным числом аргументов:

    <?php

    use App\Models\Filter;
    use App\Services\Logger;

    class Firewall
    {
        /**
         * The logger instance.
         *
         * @var \App\Services\Logger
         */
        protected $logger;

        /**
         * The filter instances.
         *
         * @var array
         */
        protected $filters;

        /**
         * Create a new class instance.
         *
         * @param  \App\Services\Logger  $logger
         * @param  array  $filters
         * @return void
         */
        public function __construct(Logger $logger, Filter ...$filters)
        {
            $this->logger = $logger;
            $this->filters = $filters;
        }
    }

Используя контекстную привязку, Вы можете разрешить эту зависимость, предоставив методу `give` замыкание, которое возвращает массив разрешенных экземпляров `Filter`:

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

Иногда класс может иметь вариативную зависимость, указывающую на тип как данный класс (`Report ...$reports`). Используя методы `needs` и `giveTagged`, Вы можете легко внедрить все привязки контейнеров с этим [тегом](#tagging) для данной зависимости:

    $this->app->when(ReportAggregator::class)
        ->needs(Report::class)
        ->giveTagged('reports');

<a name="tagging"></a>
### Теги

Иногда может потребоваться разрешить все привязки определенной «категории». Например, возможно, Вы создаете анализатор отчетов, который получает массив из множества различных реализаций интерфейса `Report`. После регистрации реализаций `Report` Вы можете назначить им тег с помощью метода `tag`:

    $this->app->bind(CpuReport::class, function () {
        //
    });

    $this->app->bind(MemoryReport::class, function () {
        //
    });

    $this->app->tag([CpuReport::class, MemoryReport::class], 'reports');

После того, как сервисы были помечены, Вы можете легко разрешить их все с помощью метода контейнера `tagged`:

    $this->app->bind(ReportAnalyzer::class, function ($app) {
        return new ReportAnalyzer($app->tagged('reports'));
    });

<a name="extending-bindings"></a>
### Расширение привязок

Метод `extend` позволяет изменять разрешенные службы. Например, когда служба разрешена, Вы можете запустить дополнительный код для украшения или настройки службы. Метод `extend` принимает замыкание, которое должно возвращать измененную службу в качестве единственного аргумента. Замыкание получает разрешаемую службу и экземпляр контейнера:

    $this->app->extend(Service::class, function ($service, $app) {
        return new DecoratedService($service);
    });

<a name="resolving"></a>
## Разрешение

<a name="the-make-method"></a>
### Метод `make`

Вы можете использовать метод `make` для извлечения экземпляра класса из контейнера. Метод `make` принимает имя класса или интерфейса, который Вы хотите разрешить:

    use App\Services\Transistor;

    $transistor = $this->app->make(Transistor::class);

Если некоторые зависимости вашего класса не разрешаются через контейнер, вы можете внедрить их, передав их как ассоциативный массив в метод `makeWith`. Например, мы можем вручную передать аргумент конструктора `$id` требуемый сервисом `Transistor`:

    use App\Services\Transistor;

    $transistor = $this->app->makeWith(Transistor::class, ['id' => 1]);

Если Вы находитесь за пределами сервис провайдера в месте расположения Вашего кода, которое не имеет доступа к переменной `$app`, Вы можете использовать `App` [фасад](/docs/{{version}}/facades) чтобы разрешить экземпляр класса из контейнера:

    use App\Services\Transistor;
    use Illuminate\Support\Facades\App;

    $transistor = App::make(Transistor::class);

Если Вы хотите, чтобы сам экземпляр контейнера Laravel был внедрен в класс, который разрешается контейнером, Вы можете указать класс `Illuminate\Container\Container` в конструкторе Вашего класса:

    use Illuminate\Container\Container;

    /**
     * Create a new class instance.
     *
     * @param  \Illuminate\Container\Container  $container
     * @return void
     */
    public function __construct(Container $container)
    {
        $this->container = $container;
    }

<a name="automatic-injection"></a>
### Автоматическая Инъекция

В качестве альтернативы, что важно, Вы можете указать тип зависимости в конструкторе класса, который разрешается контейнером, включая [контроллеры](/docs/{{version}}/controllers), [прослушиватели событий](/docs/{{version}}/events), [мидлвары](/docs/{{version}}/middleware) и другие. Кроме того, Вы можете указать зависимости типа `handle` в методе [заданий в очереди](/docs/{{version}}/queues). На практике именно так контейнер должен разрешать большинство Ваших объектов.

Например, Вы можете указать репозиторий, определенный Вашим приложением, в конструкторе контроллера. Репозиторий будет автоматически разрешен и введен в класс:

    <?php

    namespace App\Http\Controllers;

    use App\Repositories\UserRepository;

    class UserController extends Controller
    {
        /**
         * The user repository instance.
         *
         * @var \App\Repositories\UserRepository
         */
        protected $users;

        /**
         * Create a new controller instance.
         *
         * @param  \App\Repositories\UserRepository  $users
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
         * @return \Illuminate\Http\Response
         */
        public function show($id)
        {
            //
        }
    }

<a name="container-events"></a>
## События контейнера

Сервисный контейнер запускает событие каждый раз, когда разрешает объект. Вы можете прослушать это событие с помощью метода `resolving`:

    use App\Services\Transistor;

    $this->app->resolving(Transistor::class, function ($transistor, $app) {
        // Вызывается, когда контейнер разрешает объекты типа "Transistor"...
    });

    $this->app->resolving(function ($object, $app) {
        // Вызывается, когда контейнер разрешает объект любого типа...
    });

Как видите, решаемый объект будет передан в обратный вызов, что позволит Вам установить любые дополнительные свойства объекта, прежде чем он будет передан его потребителю.

<a name="psr-11"></a>
## PSR-11

Сервисный контейнер Laravel реализует интерфейс [PSR-11](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-11-container.md). Поэтому Вы можете ввести подсказку к интерфейсу контейнера PSR-11, чтобы получить экземпляр контейнера Laravel:

    use App\Services\Transistor;
    use Psr\Container\ContainerInterface;

    Route::get('/', function (ContainerInterface $container) {
        $service = $container->get(Transistor::class);

        //
    });

Если данный идентификатор не может быть разрешен, создается исключение. Исключение будет экземпляром `Psr\Container\NotFoundExceptionInterface`, если идентификатор никогда не был привязан. Если идентификатор был привязан, но не удалось разрешить, будет брошен экземпляр `Psr\Container\ContainerExceptionInterface`.
