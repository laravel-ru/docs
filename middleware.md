# Middleware

- [Введение](#introduction)
- [Определение мидлвара](#defining-middleware)
- [Регистрация мидлвара](#registering-middleware)
    - [Глобальный мидлвар](#global-middleware)
    - [Назначение мидлвара маршрутам](#assigning-middleware-to-routes)
    - [Группы мидлваров](#middleware-groups)
    - [Сортировка мидлваров](#sorting-middleware)
- [Параметры мидлвара](#middleware-parameters)
- [Завершаемый мидлвар](#terminable-middleware)

<a name="introduction"></a>
## Введение

Мидлвар обеспечивает удобный механизм для проверки и фильтрации HTTP-запросов, поступающих в Ваше приложение. Например, Laravel включает мидлвар, который проверяет, аутентифицирован ли пользователь Вашего приложения. Если пользователь не аутентифицирован, мидлвар перенаправит пользователя на экран входа в систему Вашего приложения. Однако, если пользователь аутентифицирован, мидлвар позволит запросу продвинуться дальше в приложение.

Может быть написано дополнительное мидлвар для выполнения множества задач помимо аутентификации. Например, мидлвар для ведения журнала может регистрировать все входящие запросы к Вашему приложению. В структуру Laravel включено несколько мидлваров, включая мидлвар для аутентификации и защиты CSRF. Все это мидлвар находится в каталоге `app/Http/Middleware`.

<a name="defining-middleware"></a>
## Определение мидлвара

Чтобы создать новый мидлвар, используйте Artisan-команду `make:middleware`:

    php artisan make:middleware EnsureTokenIsValid

Эта команда поместит новый класс `EnsureTokenIsValid` в Ваш каталог `app/Http/Middleware`. В этом мидлваре мы будем разрешать доступ к маршруту только в том случае, если предоставленный вход `token` соответствует указанному значению. В противном случае мы перенаправим пользователей обратно на URI `home`:

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class EnsureTokenIsValid
    {
        /**
         * Обработка входящего запроса.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @return mixed
         */
        public function handle($request, Closure $next)
        {
            if ($request->input('token') !== 'my-secret-token') {
                return redirect('home');
            }

            return $next($request);
        }
    }

Как видите, если данный токен не совпадает с нашим секретным токеном `token`, мидлвар вернет клиенту HTTP-перенаправление; в противном случае запрос будет передан в приложение. Чтобы передать запрос глубже в приложение (позволяя мидлвару «пройти»), Вы должны вызвать обратный вызов `$next` с помощью `$request`.

Лучше всего представить себе мидлвар как серию "слоев" HTTP-запросов, которые должны пройти, прежде чем они попадут в Ваше приложение. Каждый уровень может изучить запрос и даже полностью его отклонить.

> {tip} Все мидлвары разрешаются через [сервисный контейнер](/docs/{{version}}/container), поэтому Вы можете указать любые зависимости, которые Вам нужны, в конструкторе мидлвара.

<a name="before-after-middleware"></a>
<a name="middleware-and-responses"></a>
#### Мидлвары и Ответы

Конечно, мидлвар может выполнять задачи до или после передачи запроса в приложение. Например, следующий мидлвар будет выполнять некоторую задачу **до** того, как запрос будет обработан приложением:

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class BeforeMiddleware
    {
        public function handle($request, Closure $next)
        {
            // Выполнить действие

            return $next($request);
        }
    }

However, this middleware would perform its task **after** the request is handled by the application:

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class AfterMiddleware
    {
        public function handle($request, Closure $next)
        {
            $response = $next($request);

            // Выполнить действие

            return $response;
        }
    }

<a name="registering-middleware"></a>
## Регистрация мидлвара

<a name="global-middleware"></a>
### Глобальный мидлвар

Если Вы хотите, чтобы мидлвар запускался во время каждого HTTP-запроса к Вашему приложению, укажите класс мидлвара в свойстве `$middleware` Вашего класса `app/Http/Kernel.php`.

<a name="assigning-middleware-to-routes"></a>
### Назначение мидлвара маршрутам

Если Вы хотите назначить мидлвар для определенных маршрутов, Вы должны сначала назначить мидлвару ключ в файле `app/Http/Kernel.php` Вашего приложения. По умолчанию свойство `$routeMiddleware` этого класса содержит записи для мидлвара, включенного в Laravel. Вы можете добавить свое собственное промежуточное ПО в этот список и назначить ему ключ по Вашему выбору:

    // Внутри класса App\Http\Kernel...

    protected $routeMiddleware = [
        'auth' => \App\Http\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
        'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
        'can' => \Illuminate\Auth\Middleware\Authorize::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
        'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
        'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
    ];

После того как мидлвар определен в ядре HTTP, Вы можете использовать метод `middleware` для назначения мидлвара для маршрута:

    Route::get('/profile', function () {
        //
    })->middleware('auth');

Вы можете назначить несколько мидлваров для маршрута, передав массив имен мидлваров методу `middleware`:

    Route::get('/', function () {
        //
    })->middleware(['first', 'second']);

При назначении мидлвара Вы также можете передать полное имя класса:

    use App\Http\Middleware\EnsureTokenIsValid;

    Route::get('/profile', function () {
        //
    })->middleware(EnsureTokenIsValid::class);

При назначении мидлвара группе маршрутов иногда может потребоваться запретить применение мидлвара к отдельному маршруту в группе. Вы можете сделать это с помощью метода `withoutMiddleware`:

    use App\Http\Middleware\EnsureTokenIsValid;

    Route::middleware([EnsureTokenIsValid::class])->group(function () {
        Route::get('/', function () {
            //
        });

        Route::get('/profile', function () {
            //
        })->withoutMiddleware([EnsureTokenIsValid::class]);
    });

Метод `withoutMiddleware` может удалить только мидлвар маршрутизации и не применяется к [глобальному мидлвару](#global-middleware).

<a name="middleware-groups"></a>
### Группы мидлваров

Иногда Вам может потребоваться сгруппировать несколько мидлваров под одним ключом, чтобы упростить их назначение маршрутам. Вы можете сделать это, используя свойство `$middlewareGroups` Вашего HTTP-ядра.

По умолчанию Laravel поставляется с группами мидлваров `web` и `api`, которые содержат общий мидлвар, который Вы, возможно, захотите применить к своей сети и маршрутам API. Помните, что эта группа мидлваров автоматически применяется сервис провайдером Вашего приложения `App\Providers\RouteServiceProvider` для маршрутов в Ваших соответствующих файлах маршрутов `web` и `api`:

    /**
     * Группы мидлваров маршрута приложения.
     *
     * @var array
     */
    protected $middlewareGroups = [
        'web' => [
            \App\Http\Middleware\EncryptCookies::class,
            \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
            \Illuminate\Session\Middleware\StartSession::class,
            // \Illuminate\Session\Middleware\AuthenticateSession::class,
            \Illuminate\View\Middleware\ShareErrorsFromSession::class,
            \App\Http\Middleware\VerifyCsrfToken::class,
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],

        'api' => [
            'throttle:api',
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],
    ];

Группы мидлваров могут быть назначены маршрутам и экшенам контроллера с использованием того же синтаксиса, что и индивидуальный мидлвар. Опять же, группы мидлваров делают более удобным назначать несколько мидлваров для маршрута одновременно:

    Route::get('/', function () {
        //
    })->middleware('web');

    Route::middleware(['web'])->group(function () {
        //
    });

> {tip} Изначально группы мидлваров `web` и `api` автоматически применяются к соответствующим файлам Вашего приложения `routes/web.php` и `routes/api.php` с помощью `App\Providers\RouteServiceProvider`.

<a name="sorting-middleware"></a>
### Сортировка мидлваров

В редких случаях Вам может потребоваться, чтобы Ваш мидлвар выполнялся в определенном порядке, но Вы не можете контролировать их порядок, когда они назначаются для маршрута. В этом случае Вы можете указать приоритет Вашего мидлвара, используя свойство `$middlewarePriority` Вашего файла `app/Http/Kernel.php`. Это свойство может отсутствовать в Вашем HTTP-ядре по умолчанию. Если он не существует, Вы можете скопировать его определение по умолчанию ниже:

    /**
     * Список мидлваров с сортировкой по приоритету.
     *
     * Это заставляет не глобальный мидлвар всегда находиться в заданном порядке.
     *
     * @var array
     */
    protected $middlewarePriority = [
        \Illuminate\Cookie\Middleware\EncryptCookies::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \Illuminate\Contracts\Auth\Middleware\AuthenticatesRequests::class,
        \Illuminate\Routing\Middleware\ThrottleRequests::class,
        \Illuminate\Session\Middleware\AuthenticateSession::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
        \Illuminate\Auth\Middleware\Authorize::class,
    ];

<a name="middleware-parameters"></a>
## Параметры мидлвара

Мидлвар также может получать дополнительные параметры. Например, если Вашему приложению необходимо проверить, что аутентифицированный пользователь имеет заданную «роль» перед выполнением заданного действия, Вы можете создать мидлвар `EnsureUserHasRole`, который получает имя роли в качестве дополнительного аргумента.

Дополнительные параметры мидлвара будут переданы мидлвару после аргумента `$next`:

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class EnsureUserHasRole
    {
        /**
         * Обработать входящий запрос.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @param  string  $role
         * @return mixed
         */
        public function handle($request, Closure $next, $role)
        {
            if (! $request->user()->hasRole($role)) {
                // Redirect...
            }

            return $next($request);
        }

    }

Параметры мидлвара можно указать при определении маршрута, разделив имя мидлвара и параметры символом `:`. Несколько параметров следует разделять запятыми:

    Route::put('/post/{id}', function ($id) {
        //
    })->middleware('role:editor');

<a name="terminable-middleware"></a>
## Завершаемый мидлвар

Иногда мидлвару может потребоваться выполнить некоторую работу после того, как HTTP-ответ был отправлен в браузер. Если Вы определяете метод `terminate` в мидлваре, а Ваш веб-сервер использует FastCGI, метод `terminate` будет автоматически вызываться после отправки ответа в браузер:

    <?php

    namespace Illuminate\Session\Middleware;

    use Closure;

    class TerminatingMiddleware
    {
        /**
         * Обработка входящего запроса.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @return mixed
         */
        public function handle($request, Closure $next)
        {
            return $next($request);
        }

        /**
         * Обработка задач после отправки ответа в браузер.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Illuminate\Http\Response  $response
         * @return void
         */
        public function terminate($request, $response)
        {
            // ...
        }
    }

Метод `terminate` должен получать и запрос, и ответ. После того, как Вы определили завершаемый мидлвар, Вы должны добавить его в список маршрутов или глобальный мидлвар в файле `app/Http/Kernel.php`.

При вызове метода `terminate` в Вашем мидлваре Laravel разрешит новый экземпляр мидлвара из [сервис контейнера](/docs/{{version}}/container). Если Вы хотите использовать один и тот же экземпляр мидлвара при вызове методов `handle` и `terminate`, зарегистрируйте мидлвар в контейнере, используя метод контейнера `singleton`. Обычно это должно быть сделано в методе `register` Вашего `AppServiceProvider`:

    use App\Http\Middleware\TerminatingMiddleware;

    /**
     * Зарегистрируйте любые сервисы приложения.
     *
     * @return void
     */
    public function register()
    {
        $this->app->singleton(TerminatingMiddleware::class);
    }
