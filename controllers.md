# Контроллеры

- [Введение](#introduction)
- [Написание контроллеров](#writing-controllers)
    - [Базовые контроллеры](#basic-controllers)
    - [Контроллеры одного действия](#single-action-controllers)
- [Мидлвар в контроллере](#controller-middleware)
- [Контроллеры ресурсов](#resource-controllers)
    - [Частичные маршруты ресурсов](#restful-partial-resource-routes)
    - [Вложенные ресурсы](#restful-nested-resources)
    - [Именование маршрутов ресурсов](#restful-naming-resource-routes)
    - [Именование параметров маршрута ресурса](#restful-naming-resource-route-parameters)
    - [Обзор маршрутов ресурсов](#restful-scoping-resource-routes)
    - [Локализация URI ресурсов](#restful-localizing-resource-uris)
    - [Дополнительные контроллеры ресурсов](#restful-supplementing-resource-controllers)
- [Внедрение зависимостей и контроллеры](#dependency-injection-and-controllers)

<a name="introduction"></a>
## Введение

Вместо того, чтобы определять всю логику обработки запросов как замыкания в файлах маршрутов, Вы можете захотеть организовать это поведение с помощью классов «контроллеров». Контроллеры могут сгруппировать связанную логику обработки запросов в один класс. Например, класс `UserController` может обрабатывать все входящие запросы, относящиеся к пользователям, включая отображение, создание, обновление и удаление пользователей. По умолчанию контроллеры хранятся в каталоге `app/Http/Controllers`.

<a name="writing-controllers"></a>
## Написание контроллеров

<a name="basic-controllers"></a>
### Базовые контроллеры

Давайте посмотрим на пример базового контроллера. Обратите внимание, что контроллер расширяет базовый класс контроллера, включенный в Laravel: `App\Http\Controllers\Controller`:

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Models\User;

    class UserController extends Controller
    {
        /**
         * Показать профиль для данного пользователя.
         *
         * @param  int  $id
         * @return \Illuminate\View\View
         */
        public function show($id)
        {
            return view('user.profile', [
                'user' => User::findOrFail($id)
            ]);
        }
    }

Вы можете определить маршрут к этому методу контроллера следующим образом:

    use App\Http\Controllers\UserController;

    Route::get('/user/{id}', [UserController::class, 'show']);

Когда входящий запрос совпадает с указанным URI маршрута, будет вызван метод `show` в классе `App\Http\Controllers\UserController` и параметры маршрута будут переданы методу.

> {tip} Контроллеры **не требуются** для расширения базового класса. Однако у Вас не будет доступа к удобным функциям, таким как методы `middleware` и `authorize`.

<a name="single-action-controllers"></a>
### Контроллеры одного действия

Если действие контроллера особенно сложно, Вам может показаться удобным выделить целый класс контроллера для этого единственного действия. Для этого Вы можете определить один метод `__invoke` в контроллере:

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Models\User;

    class ProvisionServer extends Controller
    {
        /**
         * Подготовьте новый веб-сервер.
         *
         * @return \Illuminate\Http\Response
         */
        public function __invoke()
        {
            // ...
        }
    }

При регистрации маршрутов для контроллеров одиночного действия Вам не нужно указывать метод контроллера. Вместо этого Вы можете просто передать маршрутизатору имя контроллера:

    use App\Http\Controllers\ProvisionServer;

    Route::post('/server', ProvisionServer::class);

Вы можете сгенерировать вызываемый контроллер, используя параметр `--invokable` Artisan-команды `make:controller`:

    php artisan make:controller ProvisionServer --invokable

> {tip} Заглушки контроллера можно настроить с помощью [публикация заглушки](/docs/{{version}}/artisan#stub-customization).

<a name="controller-middleware"></a>
## Мидлвар в контроллере

[Мидлвар](/docs/{{version}}/middleware) может быть назначен маршрутам контроллера в Ваших файлах маршрутов:

    Route::get('profile', [UserController::class, 'show'])->middleware('auth');

Или Вам может быть удобно указать мидлвар в конструкторе Вашего контроллера. Используя метод `middleware` в конструкторе Вашего контроллера, Вы можете назначить мидлвар действиям контроллера:

    class UserController extends Controller
    {
        /**
         * Создать новый экземпляр контроллера.
         *
         * @return void
         */
        public function __construct()
        {
            $this->middleware('auth');
            $this->middleware('log')->only('index');
            $this->middleware('subscribed')->except('store');
        }
    }

Контроллеры также позволяют регистрировать мидлвар с помощью замыкания. Это обеспечивает удобный способ определения встроенного мидлвара для одного контроллера без определения всего класса мидлвара:

    $this->middleware(function ($request, $next) {
        return $next($request);
    });

<a name="resource-controllers"></a>
## Контроллеры ресурсов

Если Вы думаете о каждой модели Eloquent в Вашем приложении как о «ресурсе», то для каждого ресурса в Вашем приложении обычно выполняются одни и те же наборы действий. Например, представьте, что Ваше приложение содержит модель `Photo` и модель `Movie`. Вполне вероятно, что пользователи могут создавать, читать, обновлять или удалять эти ресурсы.

Из-за этого распространенного варианта использования маршрутизация ресурсов Laravel назначает типичные маршруты создания, чтения, обновления и удаления («CRUD») контроллеру с помощью одной строки кода. Для начала мы можем использовать параметр `--resource` в Artisan-команде `make:controller`, чтобы быстро создать контроллер для обработки этих действий:

    php artisan make:controller PhotoController --resource

Эта команда сгенерирует контроллер в `app/Http/Controllers/PhotoController.php`. Контроллер будет содержать метод для каждой из доступных операций с ресурсами. Затем Вы можете зарегистрировать маршрут ресурса, который указывает на контроллер:

    use App\Http\Controllers\PhotoController;

    Route::resource('photos', PhotoController::class);

Это объявление единого маршрута создает несколько маршрутов для обработки множества действий с ресурсом. Сгенерированный контроллер уже будет иметь заглушки для каждого из этих действий. Помните, что вы всегда можете получить быстрый обзор маршрутов вашего приложения, выполнив Artisan-команду `route:list`.

Вы даже можете зарегистрировать сразу несколько контроллеров ресурсов, передав массив методу `resources`:

    Route::resources([
        'photos' => PhotoController::class,
        'posts' => PostController::class,
    ]);

<a name="actions-handled-by-resource-controller"></a>
#### Действия, выполняемые контроллером ресурсов

Методы    | URI                    | Экшен        | Название маршрута
----------|------------------------|--------------|---------------------
GET       | `/photos`              | index        | photos.index
GET       | `/photos/create`       | create       | photos.create
POST      | `/photos`              | store        | photos.store
GET       | `/photos/{photo}`      | show         | photos.show
GET       | `/photos/{photo}/edit` | edit         | photos.edit
PUT/PATCH | `/photos/{photo}`      | update       | photos.update
DELETE    | `/photos/{photo}`      | destroy      | photos.destroy

<a name="customizing-missing-model-behavior"></a>
#### Настройка поведения отсутствующей модели

Обычно ответ HTTP 404 генерируется, если не найдена неявно привязанная модель ресурсов. Однако вы можете настроить это поведение, вызвав метод `missing` при определении маршрута к вашему ресурсу. Метод `missing` принимает закрытие, которое будет вызываться, если неявно связанная модель не может быть найдена ни для одного из маршрутов ресурса:

    use App\Http\Controllers\PhotoController;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Redirect;

    Route::resource('photos', PhotoController::class)
            ->missing(function (Request $request) {
                return Redirect::route('photos.index');
            });

<a name="specifying-the-resource-model"></a>
#### Определение модели ресурсов

Если Вы используете [привязку модели маршрута](/docs/{{version}}/routing#route-model-binding) и хотите, чтобы методы контроллера ресурсов указывали тип экземпляра модели, Вы можете использовать опцию `--model` при генерации контроллера:

    php artisan make:controller PhotoController --resource --model=Photo

<a name="restful-partial-resource-routes"></a>
### Частичные маршруты ресурсов

При объявлении маршрута ресурса Вы можете указать подмножество действий, которые должен обрабатывать контроллер, вместо полного набора действий по умолчанию:

    use App\Http\Controllers\PhotoController;

    Route::resource('photos', PhotoController::class)->only([
        'index', 'show'
    ]);

    Route::resource('photos', PhotoController::class)->except([
        'create', 'store', 'update', 'destroy'
    ]);

<a name="api-resource-routes"></a>
#### Маршруты ресурсов API

При объявлении маршрутов ресурсов, которые будут использоваться API-интерфейсами, Вы обычно хотите исключить маршруты, которые представляют HTML-шаблоны, такие как `create` и `edit`. Для удобства Вы можете использовать метод `apiResource`, чтобы автоматически исключить эти два маршрута:

    use App\Http\Controllers\PhotoController;

    Route::apiResource('photos', PhotoController::class);

Вы можете зарегистрировать сразу несколько контроллеров ресурсов API, передав массив методу `apiResources`:

    use App\Http\Controllers\PhotoController;
    use App\Http\Controllers\PostController;

    Route::apiResources([
        'photos' => PhotoController::class,
        'posts' => PostController::class,
    ]);

Чтобы быстро сгенерировать контроллер ресурсов API, который не включает методы `create` или `edit`, используйте переключатель `--api` при выполнении команды `make:controller`:

    php artisan make:controller PhotoController --api

<a name="restful-nested-resources"></a>
### Вложенные ресурсы

Иногда Вам может потребоваться определить маршруты к вложенному ресурсу. Например, фоторесурс может иметь несколько комментариев, которые могут быть прикреплены к фотографии. Чтобы вложить контроллеры ресурсов, Вы можете использовать нотацию с точкой в объявлении маршрута:

    use App\Http\Controllers\PhotoCommentController;

    Route::resource('photos.comments', PhotoCommentController::class);

Этот маршрут зарегистрирует вложенный ресурс, к которому можно будет получить доступ с помощью URI, подобных следующим:

    /photos/{photo}/comments/{comment}

<a name="scoping-nested-resources"></a>
#### Определение вложенных ресурсов

Функция Laravel [неявная привязка модели](/docs/{{version}}/routing#implicit-model-binding-scoping) может автоматически ограничивать вложенные привязки, так что разрешенная дочерняя модель подтверждается принадлежностью к родительской модели. Используя метод `scoped` при определении Вашего вложенного ресурса, Вы можете включить автоматическое определение области, а также указать Laravel, по какому полю должен быть извлечен дочерний ресурс. Для получения дополнительных сведений о том, как это сделать, смотрите документацию по [определению маршрутов ресурсов](#restful-scoping-resource-routes).

<a name="shallow-nesting"></a>
#### Неглубокая вложенность

Часто нет необходимости иметь в URI и родительский, и дочерний идентификаторы, поскольку дочерний идентификатор уже является уникальным идентификатором. При использовании уникальных идентификаторов, таких как автоматически увеличивающиеся первичные ключи, для идентификации Ваших моделей в сегментах URI, Вы можете выбрать «неглубокую вложенность»:

    use App\Http\Controllers\CommentController;

    Route::resource('photos.comments', CommentController::class)->shallow();

This route definition will define the following routes:

Метод     | URI                               | Действие     | Название маршрута
----------|-----------------------------------|--------------|---------------------
GET       | `/photos/{photo}/comments`        | index        | photos.comments.index
GET       | `/photos/{photo}/comments/create` | create       | photos.comments.create
POST      | `/photos/{photo}/comments`        | store        | photos.comments.store
GET       | `/comments/{comment}`             | show         | comments.show
GET       | `/comments/{comment}/edit`        | edit         | comments.edit
PUT/PATCH | `/comments/{comment}`             | update       | comments.update
DELETE    | `/comments/{comment}`             | destroy      | comments.destroy

<a name="restful-naming-resource-routes"></a>
### Именование маршрутов ресурсов

По умолчанию все действия контроллера ресурсов имеют имя маршрута; однако Вы можете переопределить эти имена, передав массив имен с желаемыми именами маршрутов:

    use App\Http\Controllers\PhotoController;

    Route::resource('photos', PhotoController::class)->names([
        'create' => 'photos.build'
    ]);

<a name="restful-naming-resource-route-parameters"></a>
### Именование параметров маршрута ресурса

По умолчанию, `Route::resource` создает параметры маршрута для Ваших маршрутов ресурсов на основе "сингулярной" версии имени ресурса. Вы можете легко переопределить это для каждого ресурса, используя метод `parameters`. Массив, передаваемый в метод `parameters`, должен быть ассоциативным массивом имен ресурсов и имен параметров:

    use App\Http\Controllers\AdminUserController;

    Route::resource('users', AdminUserController::class)->parameters([
        'users' => 'admin_user'
    ]);

 В приведенном выше примере создается следующий URI для маршрута `show` ресурса:

    /users/{admin_user}

<a name="restful-scoping-resource-routes"></a>
### Обзор маршрутов ресурсов

Функция Laravel [scoped implicit model binding](/docs/{{version}}/routing#implicit-model-binding-scoping) может автоматически ограничивать вложенные привязки таким образом, что разрешенная дочерняя модель подтверждается принадлежностью к родительской модели. Используя метод `scoped` при определении вложенного ресурса, Вы можете включить автоматическое определение области, а также указать Laravel, в каком поле дочерний ресурс должен быть получен:

    use App\Http\Controllers\PhotoCommentController;

    Route::resource('photos.comments', PhotoCommentController::class)->scoped([
        'comment' => 'slug',
    ]);

Этот маршрут зарегистрирует вложенный ресурс с ограниченной областью действия, к которому можно получить доступ с помощью таких URI, как следующие:

    /photos/{photo}/comments/{comment:slug}

При использовании настраиваемой неявной привязки с ключом в качестве параметра вложенного маршрута Laravel автоматически задает область запроса для получения вложенной модели своим родителем, используя соглашения, чтобы угадать имя отношения на родительском элементе. В этом случае предполагается, что модель `Photo` имеет отношение с именем `comments` (множественное число от имени параметра маршрута), которое можно использовать для получения модели `Comment`.

<a name="restful-localizing-resource-uris"></a>
### Локализация URI ресурсов

По умолчанию, `Route::resource` создает URI ресурсов с использованием английских глаголов. Если Вам нужно локализовать команды действий `create` и `edit`, Вы можете использовать метод `Route::resourceVerbs`. Это можно сделать в начале метода `boot` внутри Вашего приложения `App\Providers\RouteServiceProvider`:

    /**
     * Определите привязки модели маршрута, фильтры шаблонов и т.д.
     *
     * @return void
     */
    public function boot()
    {
        Route::resourceVerbs([
            'create' => 'crear',
            'edit' => 'editar',
        ]);

        // ...
    }

После того, как глаголы были настроены, регистрация маршрута ресурса, такая как `Route::resource('fotos', PhotoController::class)`, создаст следующие URI:

    /fotos/crear

    /fotos/{foto}/editar

<a name="restful-supplementing-resource-controllers"></a>
### Дополнительные контроллеры ресурсов

Если Вам нужно добавить дополнительные маршруты к контроллеру ресурсов помимо набора маршрутов ресурсов по умолчанию, Вы должны определить эти маршруты до вызова метода `Route::resource`; в противном случае маршруты, определенные методом `resource`, могут непреднамеренно иметь приоритет над Вашими дополнительными маршрутами:

    use App\Http\Controller\PhotoController;

    Route::get('/photos/popular', [PhotoController::class, 'popular']);
    Route::resource('photos', PhotoController::class);

> {tip} Не забудьте сосредоточить внимание Ваших контроллеров. Если Вам постоянно нужны методы, выходящие за рамки типичного набора действий с ресурсами, рассмотрите возможность разделения Вашего контроллера на два меньших контроллера.

<a name="dependency-injection-and-controllers"></a>
## Внедрение зависимостей и контроллеры

<a name="constructor-injection"></a>
#### Внедрение конструктора

Laravel [сервисный контейнер](/docs/{{version}}/container) используется для разрешения всех контроллеров Laravel. В результате Вы можете указать любые зависимости, которые могут понадобиться Вашему контроллеру в его конструкторе. Объявленные зависимости будут автоматически разрешены и внедрены в экземпляр контроллера:

    <?php

    namespace App\Http\Controllers;

    use App\Repositories\UserRepository;

    class UserController extends Controller
    {
        /**
         * Экземпляр пользовательского репозитория.
         */
        protected $users;

        /**
         * Создайте новый экземпляр контроллера.
         *
         * @param  \App\Repositories\UserRepository  $users
         * @return void
         */
        public function __construct(UserRepository $users)
        {
            $this->users = $users;
        }
    }

<a name="method-injection"></a>
#### Внедрение метода

Помимо внедрения конструктора, Вы также можете указать зависимости типа от методов Вашего контроллера. Обычный вариант использования для внедрения метода - это внедрение экземпляра `Illuminate\Http\Request` в методы Вашего контроллера:

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class UserController extends Controller
    {
        /**
         * Сохранить нового пользователя.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function store(Request $request)
        {
            $name = $request->name;

            //
        }
    }

Если Ваш метод контроллера также ожидает ввода от параметра маршрута, укажите аргументы маршрута после других зависимостей. Например, если Ваш маршрут определен так:

    use App\Http\Controllers\UserController;

    Route::put('/user/{id}', [UserController::class, 'update']);

Вы по-прежнему можете ввести `Illuminate\Http\Request` и получить доступ к Вашему параметру `id`, определив свой метод контроллера следующим образом:

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class UserController extends Controller
    {
        /**
         * Обновить данного пользователя.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  string  $id
         * @return \Illuminate\Http\Response
         */
        public function update(Request $request, $id)
        {
            //
        }
    }
