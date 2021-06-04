# Генерация URL-адресов

- [Введение](#introduction)
- [Основы](#the-basics)
    - [Создание URL-адресов](#generating-urls)
    - [Доступ к текущему URL-адресу](#accessing-the-current-url)
- [URLs For Named Routes](#urls-for-named-routes)
    - [Подписанные URL](#signed-urls)
- [URL-адреса для действий контроллера](#urls-for-controller-actions)
- [Значения по умолчанию](#default-values)

<a name="introduction"></a>
## Введение

Laravel предоставляет несколько помощников, которые помогут вам в создании URL-адресов для вашего приложения. Эти помощники в первую очередь полезны при построении ссылок в ваших шаблонах и ответах API или при создании ответов перенаправления в другую часть вашего приложения.

<a name="the-basics"></a>
## Основы

<a name="generating-urls"></a>
### Создание URL-адресов

Помощник `url` может использоваться для генерации произвольных URL-адресов для вашего приложения. Сгенерированный URL-адрес будет автоматически использовать схему (HTTP или HTTPS) и хост из текущего запроса, обрабатываемого приложением:

    $post = App\Models\Post::find(1);

    echo url("/posts/{$post->id}");

    // http://example.com/posts/1

<a name="accessing-the-current-url"></a>
### Доступ к текущему URL-адресу

Если не указан путь к хелперу `url`, возвращается экземпляр `Illuminate\Routing\UrlGenerator`, позволяющий вам получить доступ к информации о текущем URL:

    // Получить текущий URL без строки запроса...
    echo url()->current();

    // Получить текущий URL, включая строку запроса...
    echo url()->full();

    // Получить полный URL для предыдущего запроса...
    echo url()->previous();

К каждому из этих методов также можно получить доступ через `URL` [фасад](/docs/{{version}}/facades):

    use Illuminate\Support\Facades\URL;

    echo URL::current();

<a name="urls-for-named-routes"></a>
## URL-адреса для именованных маршрутов

Помощник `route` может использоваться для генерации URL-адресов для [именованных маршрутов](/docs/{{version}}/routing#named-routes). Именованные маршруты позволяют создавать URL-адреса без привязки к фактическому URL-адресу, определенному в маршруте. Следовательно, если URL-адрес маршрута изменится, никаких изменений в ваши вызовы функции `route` вносить не нужно. Например, представьте, что ваше приложение содержит маршрут, определенный следующим образом:

    Route::get('/post/{post}', function () {
        //
    })->name('post.show');

Чтобы сгенерировать URL-адрес этого маршрута, вы можете использовать помощник `route` следующим образом:

    echo route('post.show', ['post' => 1]);

    // http://example.com/post/1

Конечно, помощник `route` также может использоваться для генерации URL-адресов для маршрутов с несколькими параметрами:

    Route::get('/post/{post}/comment/{comment}', function () {
        //
    })->name('comment.show');

    echo route('comment.show', ['post' => 1, 'comment' => 3]);

    // http://example.com/post/1/comment/3

Любые дополнительные элементы массива, не соответствующие параметрам определения маршрута, будут добавлены в строку запроса URL:

    echo route('post.show', ['post' => 1, 'search' => 'rocket']);

    // http://example.com/post/1?search=rocket

<a name="eloquent-models"></a>
#### Модели Eloquent

Вы часто будете генерировать URL-адреса, используя первичный ключ [Eloquent модели](/docs/{{version}}/eloquent). По этой причине вы можете передавать модели Eloquent в качестве значений параметров. Помощник `route` автоматически извлечет первичный ключ модели:

    echo route('post.show', ['post' => $post]);

<a name="signed-urls"></a>
### Подписанные URL

Laravel позволяет вам легко создавать «подписанные» URL-адреса для именованных маршрутов. Эти URL-адреса имеют хэш «подписи», добавленный к строке запроса, который позволяет Laravel проверять, что URL-адрес не был изменен с момента его создания. Подписанные URL-адреса особенно полезны для маршрутов, которые общедоступны, но требуют уровня защиты от манипуляций с URL-адресами.

Например, вы можете использовать подписанные URL-адреса для реализации общедоступной ссылки «отказаться от подписки», которая отправляется вашим клиентам по электронной почте. Чтобы создать подписанный URL для именованного маршрута, используйте метод `signedRoute` фасада `URL`:

    use Illuminate\Support\Facades\URL;

    return URL::signedRoute('unsubscribe', ['user' => 1]);

Если вы хотите сгенерировать временный подписанный URL-адрес маршрута, срок действия которого истекает через определенное количество времени, вы можете использовать метод `temporarySignedRoute`. Когда Laravel проверяет временный подписанный URL-адрес маршрута, он гарантирует, что метка времени истечения срока, закодированная в подписанный URL-адрес, не истекла:

    use Illuminate\Support\Facades\URL;

    return URL::temporarySignedRoute(
        'unsubscribe', now()->addMinutes(30), ['user' => 1]
    );

<a name="validating-signed-route-requests"></a>
#### Проверка подписанных запросов маршрута

Чтобы убедиться, что входящий запрос имеет действительную подпись, вы должны вызвать метод `hasValidSignature` для входящего запроса `Request`:

    use Illuminate\Http\Request;

    Route::get('/unsubscribe/{user}', function (Request $request) {
        if (! $request->hasValidSignature()) {
            abort(401);
        }

        // ...
    })->name('unsubscribe');

В качестве альтернативы вы можете назначить маршруту `Illuminate\Routing\Middleware\ValidateSignature` [мидлвар](/docs/{{version}}/middleware). Если его еще нет, вы должны назначить этому мидлвару ключ в массиве `routeMiddleware` вашего ядра HTTP:

    /**
     * The application's route middleware.
     *
     * These middleware may be assigned to groups or used individually.
     *
     * @var array
     */
    protected $routeMiddleware = [
        'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
    ];

После того, как вы зарегистрировали мидлвар в своем ядре, вы можете присоединить его к маршруту. Если входящий запрос не имеет действительной подписи, мидлвар автоматически вернет HTTP-ответ `403`:

    Route::post('/unsubscribe/{user}', function (Request $request) {
        // ...
    })->name('unsubscribe')->middleware('signed');

<a name="responding-to-invalid-signed-routes"></a>
#### Ответ на недействительные подписанные маршруты

Когда кто-то посещает подписанный URL-адрес, срок действия которого истек, он получит общую страницу с ошибкой для кода состояния HTTP `403`. Однако вы можете настроить это поведение, определив настраиваемое «отображаемое» замыкание для исключения `InvalidSignatureException` в вашем обработчике исключений. Это замыкание должно вернуть HTTP-ответ:

    use Illuminate\Routing\Exceptions\InvalidSignatureException;

    /**
     * Register the exception handling callbacks for the application.
     *
     * @return void
     */
    public function register()
    {
        $this->renderable(function (InvalidSignatureException $e) {
            return response()->view('error.link-expired', [], 403);
        });
    }

<a name="urls-for-controller-actions"></a>
## URL-адреса для действий контроллера

Функция `action` генерирует URL-адрес для данного действия контроллера:

    use App\Http\Controllers\HomeController;

    $url = action([HomeController::class, 'index']);

Если метод контроллера принимает параметры маршрута, вы можете передать ассоциативный массив параметров маршрута в качестве второго аргумента функции:

    $url = action([UserController::class, 'profile'], ['id' => 1]);

<a name="default-values"></a>
## Значения по умолчанию

Для некоторых приложений вы можете указать значения по умолчанию для определенных параметров URL-адреса. Например, представьте, что многие из ваших маршрутов определяют параметр `{locale}`:

    Route::get('/{locale}/posts', function () {
        //
    })->name('post.index');

Обременительно всегда передавать `locale` каждый раз, когда вы вызываете помощник `route`. Итак, вы можете использовать метод `URL::defaults` для определения значения по умолчанию для этого параметра, которое всегда будет применяться во время текущего запроса. Вы можете вызвать этот метод из [мидлвара роута](/docs/{{version}}/middleware#assigning-middleware-to-routes), чтобы получить доступ к текущему запросу:

    <?php

    namespace App\Http\Middleware;

    use Closure;
    use Illuminate\Support\Facades\URL;

    class SetDefaultLocaleForUrls
    {
        /**
         * Handle the incoming request.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @return \Illuminate\Http\Response
         */
        public function handle($request, Closure $next)
        {
            URL::defaults(['locale' => $request->user()->locale]);

            return $next($request);
        }
    }

После установки значения по умолчанию для параметра `locale` вам больше не требуется передавать его значение при генерации URL-адресов с помощью помощника `route`.

<a name="url-defaults-middleware-priority"></a>
#### Параметры URL по умолчанию и приоритет мидлвара

Установка значений URL по умолчанию может помешать обработке Laravel неявных привязок модели. Таким образом, вы должны [определить приоритет вашего мидлвара](/docs/{{version}}/middleware#sorting-middleware), который устанавливает значения URL по умолчанию, которые должны выполняться перед собственным мидлваром Laravel `SubstituteBindings`. Вы можете добиться этого, убедившись, что ваш мидлвар находится перед мидлваром `SubstituteBindings` в свойстве `$middlewarePriority` ядра HTTP вашего приложения.

Свойство `$middlewarePriority` определено в базовом классе `Illuminate\Foundation\Http\Kernel`. Вы можете скопировать его определение из этого класса и перезаписать его в ядре HTTP вашего приложения, чтобы изменить его:

    /**
     * The priority-sorted list of middleware.
     *
     * This forces non-global middleware to always be in the given order.
     *
     * @var array
     */
    protected $middlewarePriority = [
        // ...
         \App\Http\Middleware\SetDefaultLocaleForUrls::class,
         \Illuminate\Routing\Middleware\SubstituteBindings::class,
         // ...
    ];
