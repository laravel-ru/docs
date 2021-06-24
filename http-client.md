# HTTP-клиент

- [Введение](#introduction)
- [Выполнение запросов](#making-requests)
    - [Данные запроса](#request-data)
    - [Заголовки](#headers)
    - [Аутентификация](#authentication)
    - [Тайм-аут](#timeout)
    - [Повторные попытки](#retries)
    - [Обработка ошибок](#error-handling)
    - [Параметры Guzzle](#guzzle-options)
- [Параллельные запросы](#concurrent-requests)
- [Тестирование](#testing)
    - [Имитация ответов](#faking-responses)
    - [Проверка запросов](#inspecting-requests)

<a name="introduction"></a>
## Введение

Laravel предоставляет выразительный минимальный API для [HTTP-клиента Guzzle](http://docs.guzzlephp.org/en/stable/), позволяющий быстро выполнять исходящие HTTP-запросы для связи с другими веб-приложениями. Обертка Laravel вокруг Guzzle сосредоточена на наиболее распространенных вариантах использования и дает прекрасные возможности для разработчиков.

Перед тем как начать, вы должны убедиться, что вы установили пакет Guzzle как зависимость вашего приложения. По умолчанию Laravel автоматически включает эту зависимость. Однако, если вы ранее удалили пакет, вы можете снова установить его через Composer:

    composer require guzzlehttp/guzzle

<a name="making-requests"></a>
## Выполнение запросов

Для выполнения запросов вы можете использовать методы `get`, `post`, `put`, `patch` и `delete`, предоставляемые фасадом `Http`. Сначала рассмотрим, как сделать базовый запрос `GET` к другому URL:

    use Illuminate\Support\Facades\Http;

    $response = Http::get('http://example.com');

Метод `get` возвращает экземпляр `Illuminate\Http\Client\Response`, который предоставляет различные методы, которые можно использовать для проверки ответа:

    $response->body() : string;
    $response->json() : array|mixed;
    $response->collect() : Illuminate\Support\Collection;
    $response->status() : int;
    $response->ok() : bool;
    $response->successful() : bool;
    $response->failed() : bool;
    $response->serverError() : bool;
    $response->clientError() : bool;
    $response->header($header) : string;
    $response->headers() : array;

Объект `Illuminate\Http\Client\Response` также реализует интерфейс PHP `ArrayAccess`, позволяя вам получить доступ к данным ответа JSON непосредственно в ответе:

    return Http::get('http://example.com/users/1')['name'];

<a name="dumping-requests"></a>
#### Дамп запросов

Если вы хотите получить дамп экземпляр исходящего запроса перед его отправкой и прекратить выполнение скрипта, вы можете добавить метод `dd` в начало определения вашего запроса:

    return Http::dd()->get('http://example.com');

<a name="request-data"></a>
### Данные запроса

Конечно, при запросах `POST`, `PUT` и `PATCH` обычно отправляются дополнительные данные с вашим запросом, поэтому эти методы принимают массив данных в качестве второго аргумента. По умолчанию данные будут отправляться с использованием типа содержимого `application/json`:

    use Illuminate\Support\Facades\Http;

    $response = Http::post('http://example.com/users', [
        'name' => 'Steve',
        'role' => 'Network Administrator',
    ]);

<a name="get-request-query-parameters"></a>
#### Параметры строки запроса GET-запроса

При выполнении запросов `GET` вы можете либо напрямую добавить строку запроса к URL-адресу, либо передать массив пар ключ / значение в качестве второго аргумента метода `get`:

    $response = Http::get('http://example.com/users', [
        'name' => 'Taylor',
        'page' => 1,
    ]);

<a name="sending-form-url-encoded-requests"></a>
#### Отправка запросов с закодированными URL-адресами

Если вы хотите отправлять данные с использованием типа содержимого `application/x-www-form-urlencoded`, вы должны вызвать метод `asForm` перед тем, как сделать свой запрос:

    $response = Http::asForm()->post('http://example.com/users', [
        'name' => 'Sara',
        'role' => 'Privacy Consultant',
    ]);

<a name="sending-a-raw-request-body"></a>
#### Отправка необработанного тела запроса

Вы можете использовать метод `withBody`, если хотите предоставить необработанное тело запроса при его отправке. Тип содержимого может быть предоставлен через второй аргумент метода:

    $response = Http::withBody(
        base64_encode($photo), 'image/jpeg'
    )->post('http://example.com/photo');

<a name="multi-part-requests"></a>
#### Многокомпонентные запросы

Если вы хотите отправлять файлы как запросы, состоящие из нескольких частей, вы должны вызвать метод `attach`, прежде чем делать свой запрос. Этот метод принимает имя файла и его содержимое. При необходимости вы можете указать третий аргумент, который будет считаться именем файла:

    $response = Http::attach(
        'attachment', file_get_contents('photo.jpg'), 'photo.jpg'
    )->post('http://example.com/attachments');

Вместо передачи необработанного содержимого файла вы можете передать ресурс потока:

    $photo = fopen('photo.jpg', 'r');

    $response = Http::attach(
        'attachment', $photo, 'photo.jpg'
    )->post('http://example.com/attachments');

<a name="headers"></a>
### Заголовки

Заголовки могут быть добавлены к запросам с помощью метода `withHeaders`. Этот метод `withHeaders` принимает массив пар ключ / значение:

    $response = Http::withHeaders([
        'X-First' => 'foo',
        'X-Second' => 'bar'
    ])->post('http://example.com/users', [
        'name' => 'Taylor',
    ]);

Вы можете использовать метод `accept`, чтобы указать тип контента, который ваше приложение ожидает в ответ на ваш запрос:

    $response = Http::accept('application/json')->get('http://example.com/users');

Для удобства вы можете использовать метод `acceptJson`, чтобы быстро указать, что ваше приложение ожидает тип содержимого `application/json` в ответ на ваш запрос:

    $response = Http::acceptJson()->get('http://example.com/users');

<a name="authentication"></a>
### Аутентификация

Вы можете указать учетные данные базовой и дайджест-аутентификации, используя методы `withBasicAuth` и `withDigestAuth` соответственно:

    // Обычная аутентификация...
    $response = Http::withBasicAuth('taylor@laravel.com', 'secret')->post(...);

    // Дайджест-аутентификация...
    $response = Http::withDigestAuth('taylor@laravel.com', 'secret')->post(...);

<a name="bearer-tokens"></a>
#### Bearer токены

Если вы хотите быстро добавить токен-носитель в заголовок запроса `Authorization`, вы можете использовать метод `withToken`:

    $response = Http::withToken('token')->post(...);

<a name="timeout"></a>
### Тайм-аут

Метод `timeout` может использоваться для указания максимального количества секунд ожидания ответа:

    $response = Http::timeout(3)->get(...);

Если заданный тайм-аут превышен, будет выброшен экземпляр `Illuminate\Http\Client\ConnectionException`.

<a name="retries"></a>
### Повторные попытки

Если вы хотите, чтобы HTTP-клиент автоматически повторял запрос при возникновении ошибки клиента или сервера, вы можете использовать метод `retry`. Метод `retry` принимает два аргумента: максимальное количество попыток запроса и количество миллисекунд, которые Laravel должен ждать между попытками:

    $response = Http::retry(3, 100)->post(...);

Если все запросы терпят неудачу, будет выброшен экземпляр `Illuminate\Http\Client\RequestException`.

<a name="error-handling"></a>
### Обработка ошибок

В отличие от поведения Guzzle по умолчанию, клиентская оболочка HTTP Laravel не генерирует исключения при ошибках клиента или сервера (ответы серверов на уровне `400` и `500`). Вы можете определить, была ли возвращена одна из этих ошибок, используя методы `successful`, `clientError` или `serverError`:

    // Определите, является ли код состояния >= 200 и < 300...
    $response->successful();

    // Определите, является ли код состояния >= 400...
    $response->failed();

    // Определите, имеет ли ответ код состояния 400 уровня...
    $response->clientError();

    // Определите, имеет ли ответ код состояния 500 уровня...
    $response->serverError();

<a name="throwing-exceptions"></a>
#### Выбрасывание исключений

Если у вас есть экземпляр ответа и вы хотите выбросить экземпляр `Illuminate\Http\Client\RequestException`, если код состояния ответа указывает на ошибку клиента или сервера, вы можете использовать метод `throw`:

    $response = Http::post(...);

    // Выбрасывать исключение, если произошла ошибка клиента или сервера...
    $response->throw();

    return $response['user']['id'];

Экземпляр `Illuminate\Http\Client\RequestException` имеет общедоступное свойство `$response`, которое позволит вам проверить возвращаемый ответ.

Метод `throw` возвращает экземпляр ответа, если ошибок не произошло, что позволяет связать другие операции с методом `throw`:

    return Http::post(...)->throw()->json();

Если вы хотите выполнить некоторую дополнительную логику до того, как будет сгенерировано исключение, вы можете передать замыкание методу `throw`. Исключение будет сгенерировано автоматически после вызова замыкания, поэтому вам не нужно повторно генерировать исключение изнутри замыкания:

    return Http::post(...)->throw(function ($response, $e) {
        //
    })->json();

<a name="guzzle-options"></a>
### Параметры Guzzle

Вы можете указать дополнительные [параметры запроса Guzzle](http://docs.guzzlephp.org/en/stable/request-options.html), используя метод `withOptions`. Метод `withOptions` принимает массив пар ключ / значение:

    $response = Http::withOptions([
        'debug' => true,
    ])->get('http://example.com/users');

<a name="concurrent-requests"></a>
## Параллельные запросы

Иногда вам может понадобиться выполнить несколько HTTP-запросов одновременно. Другими словами, вы хотите, чтобы несколько запросов отправлялись одновременно, а не отправляли запросы последовательно. Это может привести к существенному повышению производительности при взаимодействии с медленными HTTP API.

К счастью, вы можете сделать это с помощью метода `pool`. Метод `pool` принимает замыкание, которое получает экземпляр `Illuminate\Http\Client\Pool`, что позволяет вам легко добавлять запросы в пул запросов для отправки:

    use Illuminate\Http\Client\Pool;
    use Illuminate\Support\Facades\Http;

    $responses = Http::pool(fn (Pool $pool) => [
        $pool->get('http://localhost/first'),
        $pool->get('http://localhost/second'),
        $pool->get('http://localhost/third'),
    ]);

    return $responses[0]->ok() &&
           $responses[1]->ok() &&
           $responses[2]->ok();

Как видите, к каждому экземпляру ответа можно получить доступ в зависимости от того, в каком порядке он был добавлен в пул. При желании вы можете назвать запросы, используя метод `as`, который позволяет вам получить доступ к соответствующим ответам по имени:

    use Illuminate\Http\Client\Pool;
    use Illuminate\Support\Facades\Http;

    $responses = Http::pool(fn (Pool $pool) => [
        $pool->as('first')->get('http://localhost/first'),
        $pool->as('second')->get('http://localhost/second'),
        $pool->as('third')->get('http://localhost/third'),
    ]);

    return $responses['first']->ok();

<a name="testing"></a>
## Тестирование

Многие сервисы Laravel предоставляют функциональные возможности, которые помогут вам легко и выразительно писать тесты, и HTTP-оболочка Laravel не является исключением. Метод `fake` фасада `Http` позволяет указать HTTP-клиенту возвращать заглушки или фиктивные ответы при выполнении запросов.

<a name="faking-responses"></a>
### Имитация ответов

Например, чтобы указать HTTP-клиенту возвращать пустые ответы с кодом состояния `200` для каждого запроса, вы можете вызвать метод `fake` без аргументов:

    use Illuminate\Support\Facades\Http;

    Http::fake();

    $response = Http::post(...);

> {note} При подделке запросов мидлвар HTTP-клиента не выполняется. Вы должны определить ожидания для ложных ответов, как если бы это мидлвар работал правильно.

<a name="faking-specific-urls"></a>
#### Подделка определенных URL-адресов

В качестве альтернативы вы можете передать массив методу `fake`. Ключи массива должны представлять шаблоны URL-адресов, которые вы хотите подделать, и связанные с ними ответы. Символ `*` может использоваться как подстановочный знак. Любые запросы к URL-адресам, которые не были подделаны, будут фактически выполнены. Вы можете использовать метод `response` фасада `Http` для создания тупиковых / фальшивых ответов для этих конечных точек:

    Http::fake([
        // Заглушить ответ JSON для конечных точек GitHub...
        'github.com/*' => Http::response(['foo' => 'bar'], 200, $headers),

        // Заглушить строковый ответ для конечных точек Google...
        'google.com/*' => Http::response('Hello World', 200, $headers),
    ]);

If you would like to specify a fallback URL pattern that will stub all unmatched URLs, you may use a single `*` character:

    Http::fake([
        // Заглушить ответ JSON для конечных точек GitHub...
        'github.com/*' => Http::response(['foo' => 'bar'], 200, ['Headers']),

        // Заглушить строковый ответ для всех остальных конечных точек...
        '*' => Http::response('Hello World', 200, ['Headers']),
    ]);

<a name="faking-response-sequences"></a>
#### Поддельные последовательности ответов

Иногда вам может потребоваться указать, что один URL-адрес должен возвращать серию поддельных ответов в определенном порядке. Вы можете сделать это, используя метод `Http::sequence` для построения ответов:

    Http::fake([
        // Заглушить серию ответов для конечных точек GitHub...
        'github.com/*' => Http::sequence()
                                ->push('Hello World', 200)
                                ->push(['foo' => 'bar'], 200)
                                ->pushStatus(404),
    ]);

Когда все ответы в последовательности ответов будут использованы, любые дальнейшие запросы приведут к тому, что последовательность ответов вызовет исключение. Если вы хотите указать ответ по умолчанию, который должен возвращаться, когда последовательность пуста, вы можете использовать метод `whenEmpty`:

    Http::fake([
        // Заглушить серию ответов для конечных точек GitHub...
        'github.com/*' => Http::sequence()
                                ->push('Hello World', 200)
                                ->push(['foo' => 'bar'], 200)
                                ->whenEmpty(Http::response()),
    ]);

Если вы хотите подделать последовательность ответов, но не должны указывать конкретный шаблон URL, который следует подделать, вы можете использовать метод `Http::fakeSequence`:

    Http::fakeSequence()
            ->push('Hello World', 200)
            ->whenEmpty(Http::response());

<a name="fake-callback"></a>
#### Поддельный обратный вызов

Если вам требуется более сложная логика для определения того, какие ответы возвращать для определенных конечных точек, вы можете передать замыкание методу `fake`. Это замыкание получит экземпляр `Illuminate\Http\Client\Request` и должно вернуть экземпляр ответа. В рамках вашего замыкание вы можете выполнить любую логику, необходимую для определения того, какой тип ответа нужно вернуть:

    Http::fake(function ($request) {
        return Http::response('Hello World', 200);
    });

<a name="inspecting-requests"></a>
### Проверка запросов

При подделке ответов вы можете иногда захотеть проверить запросы, которые получает клиент, чтобы убедиться, что ваше приложение отправляет правильные данные или заголовки. Вы можете сделать это, вызвав метод `Http::assertSent` после вызова `Http::fake`.

Метод `assertSent` принимает замыкание, которое получит экземпляр `Illuminate\Http\Client\Request` и должно вернуть логическое значение, указывающее, соответствует ли запрос вашим ожиданиям. Чтобы тест прошел, должен быть отправлен хотя бы один запрос, соответствующий заданным ожиданиям:

    use Illuminate\Http\Client\Request;
    use Illuminate\Support\Facades\Http;

    Http::fake();

    Http::withHeaders([
        'X-First' => 'foo',
    ])->post('http://example.com/users', [
        'name' => 'Taylor',
        'role' => 'Developer',
    ]);

    Http::assertSent(function (Request $request) {
        return $request->hasHeader('X-First', 'foo') &&
               $request->url() == 'http://example.com/users' &&
               $request['name'] == 'Taylor' &&
               $request['role'] == 'Developer';
    });

При необходимости вы можете утверждать, что конкретный запрос не был отправлен, используя метод `assertNotSent`:

    use Illuminate\Http\Client\Request;
    use Illuminate\Support\Facades\Http;

    Http::fake();

    Http::post('http://example.com/users', [
        'name' => 'Taylor',
        'role' => 'Developer',
    ]);

    Http::assertNotSent(function (Request $request) {
        return $request->url() === 'http://example.com/posts';
    });

Или вы можете использовать метод `assertNothingSent`, чтобы подтвердить, что во время теста не было отправлено никаких запросов:

    Http::fake();

    Http::assertNothingSent();
