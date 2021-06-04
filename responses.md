# HTTP-ответы

- [Создание ответов](#creating-responses)
    - [Прикрепление заголовков к ответам](#attaching-headers-to-responses)
    - [Прикрепление файлов cookie к ответам](#attaching-cookies-to-responses)
    - [Файлы cookie и шифрование](#cookies-and-encryption)
- [Редиректы](#redirects)
    - [Перенаправление на именованные маршруты](#redirecting-named-routes)
    - [Перенаправление к действиям контроллера](#redirecting-controller-actions)
    - [Перенаправление на внешние домены](#redirecting-external-domains)
    - [Перенаправление с флеш данными сессии](#redirecting-with-flashed-session-data)
- [Другие типы ответов](#other-response-types)
    - [Ответы представлений](#view-responses)
    - [Ответы JSON](#json-responses)
    - [Загрузки файлов](#file-downloads)
    - [Файловые ответы](#file-responses)
- [Макросы ответа](#response-macros)

<a name="creating-responses"></a>
## Создание ответов

<a name="strings-arrays"></a>
#### Строки и массивы

Все маршруты и контроллеры должны возвращать ответ, который будет отправлен обратно в браузер пользователя. Laravel предоставляет несколько разных способов вернуть ответы. Самый простой ответ - это возврат строки от маршрута или контроллера. Фреймворк автоматически преобразует строку в полный HTTP-ответ:

    Route::get('/', function () {
        return 'Hello World';
    });

Помимо возврата строк из Ваших маршрутов и контроллеров, Вы также можете возвращать массивы. Фреймворк автоматически преобразует массив в ответ JSON:

    Route::get('/', function () {
        return [1, 2, 3];
    });

> {tip} Знаете ли Вы, что Вы также можете возвращать [коллекции Eloquent](/docs/{{version}}/eloquent-collections) с Ваших маршрутов или контроллеров? Они будут автоматически преобразованы в JSON. Дать ему шанс!

<a name="response-objects"></a>
#### Объекты ответа

Как правило, Вы не будете просто возвращать простые строки или массивы из действий маршрута. Вместо этого Вы вернете полные экземпляры `Illuminate\Http\Response` или [представления](/docs/{{version}}/views).

Возврат полного экземпляра `Response` позволяет Вам настроить код состояния HTTP и заголовки ответа. Экземпляр `Response` наследуется от класса `Symfony\Component\HttpFoundation\Response`, который предоставляет множество методов для построения HTTP-ответов:

    Route::get('/home', function () {
        return response('Hello World', 200)
                      ->header('Content-Type', 'text/plain');
    });

<a name="eloquent-models-and-collections"></a>
#### Eloquent модели и коллекции

Вы также можете вернуть модели и коллекции [Eloquent ORM](/docs/{{version}}/eloquent) прямо из Ваших маршрутов и контроллеров. Когда Вы это сделаете, Laravel автоматически преобразует модели и коллекции в Ответы JSON, соблюдая [скрытые атрибуты](/docs/{{version}}/eloquent-serialization#hiding-attributes-from-json) модели:

    use App\Models\User;

    Route::get('/user/{user}', function (User $user) {
        return $user;
    });

<a name="attaching-headers-to-responses"></a>
### Прикрепление заголовков к ответам

Имейте в виду, что большинство методов ответа объединяются в цепочку, что позволяет плавно создавать экземпляры ответа. Например, Вы можете использовать метод `header` для добавления серии заголовков к ответу перед его отправкой обратно пользователю:

    return response($content)
                ->header('Content-Type', $type)
                ->header('X-Header-One', 'Header Value')
                ->header('X-Header-Two', 'Header Value');

Или Вы можете использовать метод `withHeaders`, чтобы указать массив заголовков, который будет добавлен к ответу:

    return response($content)
                ->withHeaders([
                    'Content-Type' => $type,
                    'X-Header-One' => 'Header Value',
                    'X-Header-Two' => 'Header Value',
                ]);

<a name="cache-control-middleware"></a>
#### Мидлвар управления кешем

Laravel включает в себя мидлвар `cache.headers`, которое можно использовать для быстрой установки заголовка `Cache-Control` для группы маршрутов. Если в списке директив указан `etag`, хэш MD5 содержимого ответа будет автоматически установлен как идентификатор ETag:

    Route::middleware('cache.headers:public;max_age=2628000;etag')->group(function () {
        Route::get('/privacy', function () {
            // ...
        });

        Route::get('/terms', function () {
            // ...
        });
    });

<a name="attaching-cookies-to-responses"></a>
### Прикрепление файлов cookie к ответам

Вы можете прикрепить cookie к исходящему экземпляру `Illuminate\Http\Response`, используя метод `cookie`. Вы должны передать этому методу имя, значение и количество минут, в течение которых файл cookie должен считаться действительным:

    return response('Hello World')->cookie(
        'name', 'value', $minutes
    );

Метод `cookie` также принимает еще несколько аргументов, которые используются реже. Как правило, эти аргументы имеют то же назначение и значение, что и аргументы, передаваемые встроенному в PHP методу [setcookie](https://secure.php.net/manual/en/function.setcookie.php) method:

    return response('Hello World')->cookie(
        'name', 'value', $minutes, $path, $domain, $secure, $httpOnly
    );

Если Вы хотите, чтобы cookie отправлялся вместе с исходящим ответом, но у Вас еще нет экземпляра этого ответа, Вы можете использовать фасад `Cookie`, чтобы «поставить в очередь» файлы cookie для прикрепления к ответу при его отправке. Метод `queue` принимает аргументы, необходимые для создания экземпляра cookie. Эти файлы cookie будут прикреплены к исходящему ответу перед его отправкой в браузер:

    use Illuminate\Support\Facades\Cookie;

    Cookie::queue('name', 'value', $minutes);

<a name="generating-cookie-instances"></a>
#### Создание экземпляров файлов cookie

Если Вы хотите сгенерировать экземпляр `Symfony\Component\HttpFoundation\Cookie`, который может быть присоединен к экземпляру ответа позже, Вы можете использовать глобальный помощник `cookie`. Этот файл cookie не будет отправлен обратно клиенту, если он не прикреплен к экземпляру ответа:

    $cookie = cookie('name', 'value', $minutes);

    return response('Hello World')->cookie($cookie);

<a name="expiring-cookies-early"></a>
#### Срок действия файлов cookie истекает раньше

Вы можете удалить куки-файл, истек в его срок действия с помощью метода исходящего ответа `withoutCookie`:

    return response('Hello World')->withoutCookie('name');

Если у вас еще нет экземпляра исходящего ответа, вы можете использовать метод `expire` фасада `Cookie` для истечения срока действия cookie:

    Cookie::expire('name');

<a name="cookies-and-encryption"></a>
### Файлы cookie и шифрование

По умолчанию все файлы cookie, сгенерированные Laravel, зашифрованы и подписаны, поэтому клиент не может их изменить или прочитать. Если Вы хотите отключить шифрование для подмножества файлов cookie, созданных Вашим приложением, Вы можете использовать свойство `$except` мидлвара `App\Http\Middleware\EncryptCookies`, которое находится в `app/Http/Middleware` каталог:

    /**
     * Имена файлов cookie, которые не следует шифровать.
     *
     * @var array
     */
    protected $except = [
        'cookie_name',
    ];

<a name="redirects"></a>
## Редиректы

Ответы на перенаправление являются экземплярами класса `Illuminate\Http\RedirectResponse` и содержат правильные заголовки, необходимые для перенаправления пользователя на другой URL. Есть несколько способов сгенерировать экземпляр `RedirectResponse`. Самый простой способ - использовать глобальный помощник `redirect`:

    Route::get('/dashboard', function () {
        return redirect('home/dashboard');
    });

Иногда Вы можете захотеть перенаправить пользователя в его предыдущее местоположение, например, когда отправленная форма недействительна. Вы можете сделать это с помощью глобальной вспомогательной функции `back`. Поскольку эта функция использует [сессии](/docs/{{version}}/session), убедитесь, что маршрут, вызывающий функцию `back`, использует группу мидлваров `web`:

    Route::post('/user/profile', function () {
        // Validate the request...

        return back()->withInput();
    });

<a name="redirecting-named-routes"></a>
### Перенаправление на именованные маршруты

Когда Вы вызываете помощник `redirect` без параметров, возвращается экземпляр `Illuminate\Routing\Redirector`, что позволяет Вам вызывать любой метод экземпляра `Redirector`. Например, чтобы сгенерировать `RedirectResponse` на именованный маршрут, Вы можете использовать метод `route`:

    return redirect()->route('login');

Если у Вашего маршрута есть параметры, Вы можете передать их как второй аргумент методу `route`:

    // Для маршрута со следующим URI: /profile/{id}

    return redirect()->route('profile', ['id' => 1]);

<a name="populating-parameters-via-eloquent-models"></a>
#### Заполнение параметров с помощью Eloquent моделей

Если Вы перенаправляете на маршрут с параметром «ID», который заполняется из модели Eloquent, Вы можете передать саму модель. ID будет извлечен автоматически:

    // Для маршрута со следующим URI: /profile/{id}

    return redirect()->route('profile', [$user]);

Если Вы хотите настроить значение, которое помещается в параметр маршрута, Вы можете указать столбец в определении параметра маршрута (`/profile/{id:slug}`) или Вы можете переопределить метод `getRouteKey` в Вашем Eloquent модель:

    /**
     * Получить значение ключа маршрута модели.
     *
     * @return mixed
     */
    public function getRouteKey()
    {
        return $this->slug;
    }

<a name="redirecting-controller-actions"></a>
### Перенаправление к действиям контроллера

Вы также можете генерировать перенаправления на [действия контроллера](/docs/{{version}}/controllers). Для этого передайте имя контроллера и действия методу `action`:

    use App\Http\Controllers\UserController;

    return redirect()->action([UserController::class, 'index']);

Если Ваш маршрут контроллера требует параметров, Вы можете передать их в качестве второго аргумента методу `action`:

    return redirect()->action(
        [UserController::class, 'profile'], ['id' => 1]
    );

<a name="redirecting-external-domains"></a>
### Перенаправление на внешние домены

Иногда может потребоваться перенаправление на домен за пределами Вашего приложения. Вы можете сделать это, вызвав метод `away`, который создает `RedirectResponse` без какой-либо дополнительной кодировки URL, проверки или проверки:

    return redirect()->away('https://www.google.com');

<a name="redirecting-with-flashed-session-data"></a>
### Перенаправление с флеш данными сессии

Перенаправление на новый URL-адрес и [проброс данных в сессию](/docs/{{version}}/session#flash-data) обычно выполняются одновременно. Обычно это делается после успешного выполнения действия, когда Вы отправляете сообщение об успешном завершении сеанса. Для удобства Вы можете создать экземпляр `RedirectResponse` и передать данные в сеанс в единой плавной цепочке методов:

    Route::post('/user/profile', function () {
        // ...

        return redirect('dashboard')->with('status', 'Profile updated!');
    });

После перенаправления пользователя Вы можете отобразить проброшенное сообщение из [сессии](/docs/{{version}}/session). Например, используя [синтаксис Blade](/docs/{{version}}/blade):

    @if (session('status'))
        <div class="alert alert-success">
            {{ session('status') }}
        </div>
    @endif

<a name="redirecting-with-input"></a>
#### Перенаправление с вводом

Вы можете использовать метод `withInput`, предоставляемый экземпляром `RedirectResponse`, для передачи входных данных текущего запроса в сеанс перед перенаправлением пользователя в новое место. Обычно это делается, если пользователь обнаружил ошибку проверки. После того, как ввод был передан в сеанс, Вы можете легко [получить его](/docs/{{version}}/requests#retrieving-old-input) во время следующего запроса на повторное заполнение формы:

    return back()->withInput();

<a name="other-response-types"></a>
## Другие типы ответов

Помощник `response` может использоваться для генерации других типов экземпляров ответа. Когда помощник `response` вызывается без аргументов, возвращается реализация `Illuminate\Contracts\Routing\ResponseFactory` [контракт](/docs/{{version}}/contracts). Этот контракт предоставляет несколько полезных методов для генерации ответов.

<a name="view-responses"></a>
### Ответы представлений

Если Вам нужен контроль над статусом и заголовками ответа, но также необходимо вернуть [представление](/docs/{{version}}/views) в качестве содержимого ответа, Вам следует использовать метод `view`:

    return response()
                ->view('hello', $data, 200)
                ->header('Content-Type', $type);

Конечно, если Вам не нужно передавать пользовательский код состояния HTTP или пользовательские заголовки, Вы можете использовать глобальную вспомогательную функцию `view`.

<a name="json-responses"></a>
### Ответы JSON

Метод `json` автоматически установит заголовок `Content-Type` в `application/json`, а также преобразует данный массив в JSON с помощью PHP-функции `json_encode`:

    return response()->json([
        'name' => 'Abigail',
        'state' => 'CA',
    ]);

Если Вы хотите создать ответ JSONP, Вы можете использовать метод `json` в сочетании с методом `withCallback`:

    return response()
                ->json(['name' => 'Abigail', 'state' => 'CA'])
                ->withCallback($request->input('callback'));

<a name="file-downloads"></a>
### Загрузки файлов

Метод `download` может использоваться для генерации ответа, который заставляет браузер пользователя загружать файл по заданному пути. Метод `download` принимает имя файла в качестве второго аргумента метода, который будет определять имя файла, которое видит пользователь, загружающий файл. Наконец, Вы можете передать массив заголовков HTTP в качестве третьего аргумента метода:

    return response()->download($pathToFile);

    return response()->download($pathToFile, $name, $headers);

> {note} Symfony HttpFoundation, который управляет загрузкой файлов, требует, чтобы загружаемый файл имел имя файла ASCII.

<a name="streamed-downloads"></a>
#### Потоковые загрузки

Иногда Вы можете захотеть превратить строковый ответ данной операции в загружаемый ответ без необходимости записывать содержимое операции на диск. В этом сценарии Вы можете использовать метод `streamDownload`. Этот метод принимает в качестве аргументов обратный вызов, имя файла и необязательный массив заголовков:

    use App\Services\GitHub;

    return response()->streamDownload(function () {
        echo GitHub::api('repo')
                    ->contents()
                    ->readme('laravel', 'laravel')['contents'];
    }, 'laravel-readme.md');

<a name="file-responses"></a>
### Файловые ответы

Метод `file` может использоваться для отображения файла, такого как изображение или PDF, непосредственно в браузере пользователя вместо того, чтобы инициировать загрузку. Этот метод принимает путь к файлу в качестве первого аргумента и массив заголовков в качестве второго аргумента:

    return response()->file($pathToFile);

    return response()->file($pathToFile, $headers);

<a name="response-macros"></a>
## Макросы ответа

Если вы хотите определить собственный ответ, который вы можете повторно использовать в различных ваших маршрутах и контроллерах, вы можете использовать метод `macro` на фасаде `Response`. Как правило, этот метод следует вызывать из метода `boot` одного из [сервис провайдеров](/docs/{{version}}/providers) вашего приложения, например, сервис провайдера `App\Providers\AppServiceProvider`:

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Response;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Загрузка любых сервисов приложения.
         *
         * @return void
         */
        public function boot()
        {
            Response::macro('caps', function ($value) {
                return Response::make(strtoupper($value));
            });
        }
    }

Функция `macro` принимает имя в качестве первого аргумента и замыкание в качестве второго аргумента. Замыкание макроса будет выполнено при вызове имени макроса из реализации `ResponseFactory` или вспомогательной функции `response`:

    return response()->caps('foo');
