# Валидация

- [Введение](#introduction)
- [Быстрый старт валидации](#validation-quickstart)
    - [Определение маршрутов](#quick-defining-the-routes)
    - [Создание контроллера](#quick-creating-the-controller)
    - [Написание логики валидации](#quick-writing-the-validation-logic)
    - [Отображение ошибок валидации](#quick-displaying-the-validation-errors)
    - [Перезаполнение форм](#repopulating-forms)
    - [Примечание о необязательных полях](#a-note-on-optional-fields)
- [Валидация запроса формы](#form-request-validation)
    - [Создание запросов формы](#creating-form-requests)
    - [Авторизация запросов формы](#authorizing-form-requests)
    - [Настройка сообщений об ошибках](#customizing-the-error-messages)
    - [Подготовка ввода для валидации](#preparing-input-for-validation)
- [Создание валидаторов вручную](#manually-creating-validators)
    - [Автоматическое перенаправление](#automatic-redirection)
    - [Именованные пакеты ошибки](#named-error-bags)
    - [Настройка сообщений об ошибках](#manual-customizing-the-error-messages)
    - [После проверки](#after-validation-hook)
- [Работа с сообщениями об ошибке](#working-with-error-messages)
    - [Указание пользовательских сообщений в языковых файлах](#specifying-custom-messages-in-language-files)
    - [Указание атрибутов в языковых файлах](#specifying-attribute-in-language-files)
    - [Указание значений в языковых файлах](#specifying-values-in-language-files)
- [Доступные правила валидации](#available-validation-rules)
- [Условное добавление правил](#conditionally-adding-rules)
- [Валидация массивов](#validating-arrays)
- [Валидация паролей](#validating-passwords)
- [Пользовательские правила проверки](#custom-validation-rules)
    - [Использование объектов правила](#using-rule-objects)
    - [Использование замыканий](#using-closures)
    - [Неявные правила](#implicit-rules)

<a name="introduction"></a>
## Введение

Laravel предоставляет несколько различных подходов для проверки входящих данных вашего приложения. Чаще всего используется метод `validate`, доступный для всех входящих HTTP-запросов. Однако мы обсудим и другие подходы к валидации.

Laravel включает в себя широкий спектр удобных правил проверки, которые вы можете применять к данным, даже предоставляя возможность проверять, являются ли значения уникальными в данной таблице базы данных. Мы подробно рассмотрим каждое из этих правил проверки, чтобы вы были знакомы со всеми функциями проверки Laravel.

<a name="validation-quickstart"></a>
## Быстрый старт валидации

Чтобы узнать о мощных функциях проверки Laravel, давайте рассмотрим полный пример проверки формы и отображения сообщений об ошибках обратно пользователю. Прочитав этот общий обзор, вы сможете получить хорошее общее представление о том, как проверять данные входящего запроса с помощью Laravel:

<a name="quick-defining-the-routes"></a>
### Определение маршрутов

Во-первых, предположим, что в нашем файле `routes/web.php` определены следующие маршруты:

    use App\Http\Controllers\PostController;

    Route::get('/post/create', [PostController::class, 'create']);
    Route::post('/post', [PostController::class, 'store']);

Маршрут `GET` отобразит форму для пользователя для создания нового сообщения в блоге, а маршрут `POST` сохранит новое сообщение в базе данных.

<a name="quick-creating-the-controller"></a>
### Создание контроллера

Далее давайте посмотрим на простой контроллер, который обрабатывает входящие запросы на эти маршруты. Пока оставим метод `store` пустым:

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\Request;

    class PostController extends Controller
    {
        /**
         * Show the form to create a new blog post.
         *
         * @return \Illuminate\View\View
         */
        public function create()
        {
            return view('post.create');
        }

        /**
         * Store a new blog post.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function store(Request $request)
        {
            // Validate and store the blog post...
        }
    }

<a name="quick-writing-the-validation-logic"></a>
### Написание логики валидации

Теперь мы готовы заполнить наш метод `store` логикой для проверки нового сообщения в блоге. Для этого мы будем использовать метод `validate`, предоставляемый объектом `Illuminate\Http\Request`. Если правила проверки пройдут, ваш код продолжит нормально выполняться; однако, если проверка завершится неудачно, будет сгенерировано исключение, и правильный ответ об ошибке будет автоматически отправлен обратно пользователю.

Если проверка не выполняется во время традиционного HTTP-запроса, будет сгенерирован ответ перенаправления на предыдущий URL-адрес. Если входящий запрос является запросом XHR, будет возвращен ответ JSON, содержащий сообщения об ошибках проверки.

Чтобы лучше понять метод `validate`, давайте вернемся к методу `store`:

    /**
     * Store a new blog post.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function store(Request $request)
    {
        $validated = $request->validate([
            'title' => 'required|unique:posts|max:255',
            'body' => 'required',
        ]);

        // The blog post is valid...
    }

Как видите, правила проверки передаются в метод `validate`. Не волнуйтесь - все доступные правила проверки [задокументированы](#available-validation-rules). Опять же, если проверка не удалась, автоматически будет сгенерирован правильный ответ. Если проверка пройдет успешно, наш контроллер продолжит работу в обычном режиме.

В качестве альтернативы, правила проверки могут быть указаны как массивы правил вместо одной строки с разделителями `|`:

    $validatedData = $request->validate([
        'title' => ['required', 'unique:posts', 'max:255'],
        'body' => ['required'],
    ]);

Кроме того, вы можете использовать метод `validateWithBag` для проверки запроса и сохранения любых сообщений об ошибках в [именованном пакете ошибок](#named-error-bags):

    $validatedData = $request->validateWithBag('post', [
        'title' => ['required', 'unique:posts', 'max:255'],
        'body' => ['required'],
    ]);

<a name="stopping-on-first-validation-failure"></a>
#### Остановка при первой ошибке проверки

Иногда может потребоваться прекратить выполнение правил проверки для атрибута после первого сбоя проверки. Для этого присвойте атрибуту правило `bail`:

    $request->validate([
        'title' => 'bail|required|unique:posts|max:255',
        'body' => 'required',
    ]);

В этом примере, если правило `unique` для атрибута `title` не выполняется, правило `max` не проверяется. Правила будут проверяться в порядке их назначения.

<a name="a-note-on-nested-attributes"></a>
#### Замечание о вложенных атрибутах

Если входящий HTTP-запрос содержит данные «вложенных» полей, вы можете указать эти поля в своих правилах проверки, используя синтаксис «точка»:

    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'author.name' => 'required',
        'author.description' => 'required',
    ]);

С другой стороны, если ваше имя поля содержит буквальную точку, вы можете явно запретить ее интерпретацию как синтаксис «точка», экранировав точку с помощью обратной косой черты:

    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'v1\.0' => 'required',
    ]);

<a name="quick-displaying-the-validation-errors"></a>
### Отображение ошибок валидации

Итак, что, если поля входящего запроса не проходят заданные правила проверки? Как упоминалось ранее, Laravel автоматически перенаправит пользователя обратно в его предыдущее местоположение. Кроме того, все ошибки проверки и [запрос ввода](/docs/{{version}}/requests#retrieving-old-input) будут автоматически [перенесены в сеанс](/docs/{{version}}/session#flash-data).

Переменная `$errors` используется для всех представлений вашего приложения мидлвар `Illuminate\View\Middleware\ShareErrorsFromSession`, который предоставляется группой мидлваров `web`. Когда применяется этот мидлвар, в ваших представлениях всегда будет доступна переменная `$errors`, что позволяет вам удобно предполагать, что переменная `$errors` всегда определена и может безопасно использоваться. Переменная `$errors` будет экземпляром `Illuminate\Support\MessageBag`. Для получения дополнительной информации о работе с этим объектом [ознакомьтесь с его документацией](#working-with-error-messages).

Итак, в нашем примере пользователь будет перенаправлен на метод нашего контроллера `create`, когда проверка завершится неудачно, что позволит нам отображать сообщения об ошибках в представлении:

```html
<!-- /resources/views/post/create.blade.php -->

<h1>Create Post</h1>

@if ($errors->any())
    <div class="alert alert-danger">
        <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif

<!-- Create Post Form -->
```

<a name="quick-customizing-the-error-messages"></a>
#### Настройка сообщений об ошибках

Каждое из встроенных правил проверки Laravel содержит сообщение об ошибке, которое находится в файле `resources/lang/en/validation.php` вашего приложения. В этом файле вы найдете запись перевода для каждого правила проверки. Вы можете изменять или модифицировать эти сообщения в зависимости от потребностей вашего приложения.

Кроме того, вы можете скопировать этот файл в каталог другого языка перевода, чтобы переводить сообщения на язык вашего приложения. Чтобы узнать больше о локализации Laravel, ознакомьтесь с полной [документацией по локализации](/docs/{{version}}/localization).

<a name="quick-xhr-requests-and-validation"></a>
#### XHR запросы и валидация

В этом примере мы использовали традиционную форму для отправки данных в приложение. Однако многие приложения получают запросы XHR от внешнего интерфейса на базе JavaScript. При использовании метода `validate` во время запроса XHR Laravel не генерирует ответ перенаправления. Вместо этого Laravel генерирует ответ JSON, содержащий все ошибки проверки. Этот ответ JSON будет отправлен с кодом состояния 422 HTTP.

<a name="the-at-error-directive"></a>
#### Директива `@error`

Вы можете использовать директиву `@error` [Blade](/docs/{{version}}/blade), чтобы быстро определить, существуют ли сообщения об ошибках проверки для данного атрибута. В директиве `@error` вы можете повторить переменную `$message`, чтобы отобразить сообщение об ошибке:

```html
<!-- /resources/views/post/create.blade.php -->

<label for="title">Post Title</label>

<input id="title" type="text" name="title" class="@error('title') is-invalid @enderror">

@error('title')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```

<a name="repopulating-forms"></a>
### Перезаполнение форм

Когда Laravel генерирует ответ перенаправления из-за ошибки проверки, фреймворк автоматически [все флеш входные данные запроса в сессию](/docs/{{version}}/session#flash-data). Это сделано для того, чтобы вы могли удобно получить доступ к вводу во время следующего запроса и повторно заполнить форму, которую пользователь пытался отправить.

Чтобы получить флэш-ввод из предыдущего запроса, вызовите метод `old` для экземпляра `Illuminate\Http\Request`. Метод `old` извлечет ранее записанные входные данные из [сессии](/docs/{{version}}/session):

    $title = $request->old('title');

Laravel также предоставляет глобального помощника `old`. Если вы показываете старый ввод в [шаблоне Blade](/docs/{{version}}/blade), удобнее использовать помощник `old` для повторного заполнения формы. Если для данного поля нет старого ввода, будет возвращен `null`:

    <input type="text" name="title" value="{{ old('title') }}">

<a name="a-note-on-optional-fields"></a>
### Примечание о необязательных полях

По умолчанию Laravel включает мидлвары `TrimStrings` и `ConvertEmptyStringsToNull` в глобальный стек мидлваров вашего приложения. Эти мидлвары перечислены в стеке классом `App\Http\Kernel`. Из-за этого вам часто нужно будет помечать ваши «необязательные» поля запроса как `nullable` если вы не хотите, чтобы валидатор считал значения `null` недействительными. Например:

    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
        'publish_at' => 'nullable|date',
    ]);

В этом примере мы указываем, что поле `publish_at` может иметь значение `null` или допустимое представление даты. Если модификатор `nullable` не добавлен в определение правила, валидатор сочтет `null` недопустимой датой.

<a name="form-request-validation"></a>
## Валидация запроса формы

<a name="creating-form-requests"></a>
### Создание запросов формы

Для более сложных сценариев проверки вы можете создать «запрос формы». Запросы формы - это настраиваемые классы запросов, которые инкапсулируют свою собственную логику проверки и авторизации. Чтобы создать класс запроса формы, вы можете использовать команду Artisan CLI `make:request`:

    php artisan make:request StorePostRequest

Сгенерированный класс запроса формы будет помещен в каталог `app/Http/Requests`. Если этот каталог не существует, он будет создан, когда вы запустите команду `make:request`. Каждый запрос формы, сгенерированный Laravel, имеет два метода: `authorize` и `rules`.

Как вы могли догадаться, метод `authorize` отвечает за определение того, может ли текущий аутентифицированный пользователь выполнить действие, представленное запросом, в то время как метод `rules` возвращает правила проверки, которые должны применяться к данным запроса:

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        return [
            'title' => 'required|unique:posts|max:255',
            'body' => 'required',
        ];
    }

> {tip} Вы можете указать любые зависимости, которые вам нужны, в сигнатуре метода `rules`. Они будут автоматически разрешены через Laravel [сервисный контейнер](/docs/{{version}}/container).

Итак, как оцениваются правила проверки? Все, что вам нужно сделать, это ввести подсказку запроса к методу вашего контроллера. Входящий запрос формы проверяется перед вызовом метода контроллера, что означает, что вам не нужно загромождать контроллер какой-либо логикой проверки:

    /**
     * Store a new blog post.
     *
     * @param  \App\Http\Requests\StorePostRequest  $request
     * @return Illuminate\Http\Response
     */
    public function store(StorePostRequest $request)
    {
        // The incoming request is valid...

        // Retrieve the validated input data...
        $validated = $request->validated();
    }

Если проверка не удалась, будет сгенерирован ответ перенаправления, чтобы отправить пользователя обратно в его предыдущее местоположение. Ошибки также будут перенесены в сеанс, чтобы они были доступны для отображения. Если запрос был запросом XHR, пользователю будет возвращен HTTP-ответ с кодом состояния 422, включая JSON-представление ошибок проверки.

<a name="adding-after-hooks-to-form-requests"></a>
#### Добавление хуков после к запросам формы

Если вы хотите добавить обработчик проверки «после» к запросу формы, вы можете использовать метод `withValidator`. Этот метод получает полностью сконструированный валидатор, позволяющий вызвать любой из его методов до того, как правила валидации будут фактически оценены:

    /**
     * Configure the validator instance.
     *
     * @param  \Illuminate\Validation\Validator  $validator
     * @return void
     */
    public function withValidator($validator)
    {
        $validator->after(function ($validator) {
            if ($this->somethingElseIsInvalid()) {
                $validator->errors()->add('field', 'Something is wrong with this field!');
            }
        });
    }

<a name="request-stopping-on-first-validation-rule-failure"></a>
#### Остановка при первой ошибке проверки атрибута

Добавляя свойство `stopOnFirstFailure` к вашему классу запроса, вы можете сообщить валидатору, что он должен прекратить проверку всех атрибутов после того, как произошла единственная ошибка валидации:

    /**
     * Indicates if the validator should stop on the first rule failure.
     *
     * @var bool
     */
    protected $stopOnFirstFailure = true;

<a name="authorizing-form-requests"></a>
### Авторизация запросов формы

Класс запроса формы также содержит метод `authorize`. В этом методе вы можете определить, действительно ли аутентифицированный пользователь имеет право обновлять данный ресурс. Например, вы можете определить, действительно ли пользователь владеет комментарием в блоге, который он пытается обновить. Скорее всего, вы будете взаимодействовать со своими [шлюзами и политиками авторизации](/docs/{{version}}/authorization) в этом методе:

    use App\Models\Comment;

    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        $comment = Comment::find($this->route('comment'));

        return $comment && $this->user()->can('update', $comment);
    }

Поскольку все запросы формы расширяют базовый класс запросов Laravel, мы можем использовать метод `user` для доступа к текущему аутентифицированному пользователю. Также обратите внимание на вызов метода `route` в приведенном выше примере. Этот метод предоставляет вам доступ к параметрам URI, определенным для вызываемого маршрута, таким как параметр `{comment}` в примере ниже:

    Route::post('/comment/{comment}');

Поэтому, если ваше приложение использует [привязку модели маршрута](/docs/{{version}}/routing#route-model-binding), ваш код можно сделать еще более кратким, если вы получите доступ к разрешенной модели как к свойству запрос:

    return $this->user()->can('update', $this->comment);

Если метод `authorize` возвращает `false`, ответ HTTP с кодом состояния 403 будет автоматически возвращен, и метод вашего контроллера не будет выполняться.

Если вы планируете обрабатывать логику авторизации для запроса в другой части вашего приложения, вы можете просто вернуть `true` из метода `authorize`:

    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        return true;
    }

> {tip} You may type-hint any dependencies you need within the `authorize` method's signature. They will automatically be resolved via the Laravel [service container](/docs/{{version}}/container).

<a name="customizing-the-error-messages"></a>
### Настройка сообщений об ошибках

Вы можете настроить сообщения об ошибках, используемые в запросе формы, переопределив метод `messages`. Этот метод должен возвращать массив пар атрибут / правило и соответствующие им сообщения об ошибках:

    /**
     * Get the error messages for the defined validation rules.
     *
     * @return array
     */
    public function messages()
    {
        return [
            'title.required' => 'A title is required',
            'body.required' => 'A message is required',
        ];
    }

<a name="customizing-the-validation-attributes"></a>
#### Настройка атрибутов проверки

Многие сообщения об ошибках встроенных правил проверки Laravel содержат плейсхолдер `:attribute`. Если вы хотите, чтобы заполнитель `:attribute` вашего сообщения проверки был заменен именем настраиваемого атрибута, вы можете указать настраиваемые имена, переопределив метод `attributes`. Этот метод должен возвращать массив пар атрибут / имя:

    /**
     * Get custom attributes for validator errors.
     *
     * @return array
     */
    public function attributes()
    {
        return [
            'email' => 'email address',
        ];
    }

<a name="preparing-input-for-validation"></a>
### Подготовка ввода для валидации

Если вам нужно подготовить или очистить какие-либо данные из запроса перед применением правил проверки, вы можете использовать метод `prepareForValidation`:

    use Illuminate\Support\Str;

    /**
     * Prepare the data for validation.
     *
     * @return void
     */
    protected function prepareForValidation()
    {
        $this->merge([
            'slug' => Str::slug($this->slug),
        ]);
    }

<a name="manually-creating-validators"></a>
## Создание валидаторов вручную

Если вы не хотите использовать метод `validate` в запросе, вы можете создать экземпляр валидатора вручную, используя `Validator` [фасад](/docs/{{version}}/facades). Метод `make` на фасаде генерирует новый экземпляр валидатора:

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Validator;

    class PostController extends Controller
    {
        /**
         * Store a new blog post.
         *
         * @param  Request  $request
         * @return Response
         */
        public function store(Request $request)
        {
            $validator = Validator::make($request->all(), [
                'title' => 'required|unique:posts|max:255',
                'body' => 'required',
            ]);

            if ($validator->fails()) {
                return redirect('post/create')
                            ->withErrors($validator)
                            ->withInput();
            }

            // Store the blog post...
        }
    }

Первый аргумент, переданный методу `make`- это проверяемые данные. Второй аргумент - это массив правил проверки, которые должны применяться к данным.

После определения того, не прошла ли проверка запроса, вы можете использовать метод `withErrors` для передачи сообщений об ошибках в сеанс. При использовании этого метода переменная `$errors` будет автоматически передана вашим представлениям после перенаправления, что позволит вам легко отображать их обратно пользователю. Метод `withErrors` принимает валидатор, `MessageBag`, или PHP `array`.

#### Остановка при первой ошибке проверки

Метод `stopOnFirstFailure` проинформирует валидатор о том, что он должен прекратить проверку всех атрибутов после того, как произошла единственная ошибка валидации:

    if ($validator->stopOnFirstFailure()->fails()) {
        // ...
    }

<a name="automatic-redirection"></a>
### Автоматическое перенаправление

Если вы хотите создать экземпляр валидатора вручную, но по-прежнему пользуетесь преимуществами автоматического перенаправления, предлагаемого методом `validate` HTTP-запроса, вы можете вызвать метод `validate` для существующего экземпляра валидатора. Если проверка не удалась, пользователь будет автоматически перенаправлен или, в случае запроса XHR, будет возвращен ответ JSON:

    Validator::make($request->all(), [
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
    ])->validate();

Вы можете использовать метод `validateWithBag` для сохранения сообщений об ошибках в [именованный пакет ошибок](#named-error-bags), если проверка не удалась:

    Validator::make($request->all(), [
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
    ])->validateWithBag('post');

<a name="named-error-bags"></a>
### Именованные пакеты ошибки

Если у вас есть несколько форм на одной странице, вы можете захотеть назвать `MessageBag`, содержащий ошибки проверки, что позволит вам получать сообщения об ошибках для конкретной формы. Для этого передайте имя в качестве второго аргумента в `withErrors`:

    return redirect('register')->withErrors($validator, 'login');

Затем вы можете получить доступ к именованному экземпляру `MessageBag` из переменной `$errors`:

    {{ $errors->login->first('email') }}

<a name="manual-customizing-the-error-messages"></a>
### Настройка сообщений об ошибках

При необходимости вы можете предоставить настраиваемые сообщения об ошибках, которые должен использовать экземпляр валидатора вместо сообщений об ошибках по умолчанию, предоставляемых Laravel. Есть несколько способов указать собственные сообщения. Во-первых, вы можете передать пользовательские сообщения в качестве третьего аргумента методу `Validator::make`:

    $validator = Validator::make($input, $rules, $messages = [
        'required' => 'The :attribute field is required.',
    ]);

В этом примере плейсхолдер `:attribute` будет заменен фактическим именем проверяемого поля. Вы также можете использовать другие заполнители в сообщениях проверки. Например:

    $messages = [
        'same' => 'The :attribute and :other must match.',
        'size' => 'The :attribute must be exactly :size.',
        'between' => 'The :attribute value :input is not between :min - :max.',
        'in' => 'The :attribute must be one of the following types: :values',
    ];

<a name="specifying-a-custom-message-for-a-given-attribute"></a>
#### Указание настраиваемого сообщения для заданного атрибута

Иногда вы можете указать собственное сообщение об ошибке только для определенного атрибута. Вы можете сделать это, используя "точечную" нотацию. Сначала укажите имя атрибута, а затем правило:

    $messages = [
        'email.required' => 'We need to know your email address!',
    ];

<a name="specifying-custom-attribute-values"></a>
#### Указание значений настраиваемых атрибутов

Многие из встроенных сообщений об ошибках Laravel включают плейсхолдер `:attribute`, который заменяется именем проверяемого поля или атрибута. Чтобы настроить значения, используемые для замены этих заполнителей для определенных полей, вы можете передать массив настраиваемых атрибутов в качестве четвертого аргумента методу `Validator::make`:

    $validator = Validator::make($input, $rules, $messages, [
        'email' => 'email address',
    ]);

<a name="after-validation-hook"></a>
### После проверки

Вы также можете прикрепить обратные вызовы, которые будут запускаться после завершения проверки. Это позволяет легко выполнять дальнейшую проверку и даже добавлять сообщения об ошибках в коллекцию сообщений. Для начала вызовите метод `after` на экземпляре валидатора:

    $validator = Validator::make(...);

    $validator->after(function ($validator) {
        if ($this->somethingElseIsInvalid()) {
            $validator->errors()->add(
                'field', 'Something is wrong with this field!'
            );
        }
    });

    if ($validator->fails()) {
        //
    }

<a name="working-with-error-messages"></a>
## Работа с сообщениями об ошибке

После вызова метода `errors` для экземпляра `Validator` вы получите экземпляр `Illuminate\Support\MessageBag`, который имеет множество удобных методов для работы с сообщениями об ошибках. Переменная `$errors`, которая автоматически становится доступной для всех представлений, также является экземпляром класса `MessageBag`.

<a name="retrieving-the-first-error-message-for-a-field"></a>
#### Получение первого сообщения об ошибке для поля

Чтобы получить первое сообщение об ошибке для данного поля, используйте метод `first`:

    $errors = $validator->errors();

    echo $errors->first('email');

<a name="retrieving-all-error-messages-for-a-field"></a>
#### Получение всех сообщений об ошибках для поля

Если вам нужно получить массив всех сообщений для данного поля, используйте метод `get`:

    foreach ($errors->get('email') as $message) {
        //
    }

Если вы проверяете поле формы массива, вы можете получить все сообщения для каждого из элементов массива, используя символ `*`:

    foreach ($errors->get('attachments.*') as $message) {
        //
    }

<a name="retrieving-all-error-messages-for-all-fields"></a>
#### Получение всех сообщений об ошибках для всех полей

Чтобы получить массив всех сообщений для всех полей, используйте метод `all`:

    foreach ($errors->all() as $message) {
        //
    }

<a name="determining-if-messages-exist-for-a-field"></a>
#### Определение наличия сообщений для поля

Метод `has` может использоваться, чтобы определить, существуют ли какие-либо сообщения об ошибках для данного поля:

    if ($errors->has('email')) {
        //
    }

<a name="specifying-custom-messages-in-language-files"></a>
### Указание пользовательских сообщений в языковых файлах

Каждое из встроенных правил проверки Laravel содержит сообщение об ошибке, которое находится в файле `resources/lang/en/validation.php` вашего приложения. В этом файле вы найдете запись перевода для каждого правила проверки. Вы можете изменять или модифицировать эти сообщения в зависимости от потребностей вашего приложения.

Кроме того, вы можете скопировать этот файл в каталог другого языка перевода, чтобы переводить сообщения на язык вашего приложения. Чтобы узнать больше о локализации Laravel, ознакомьтесь с полной [документацией по локализации](/docs/{{version}}/localization).

<a name="custom-messages-for-specific-attributes"></a>
#### Пользовательские сообщения для определенных атрибутов

Вы можете настроить сообщения об ошибках, используемые для указанных комбинаций атрибутов и правил, в файлах языка проверки вашего приложения. Для этого добавьте настройки сообщения в массив `custom` языкового файла `resources/lang/xx/validation.php` вашего приложения:

    'custom' => [
        'email' => [
            'required' => 'We need to know your email address!',
            'max' => 'Your email address is too long!'
        ],
    ],

<a name="specifying-attribute-in-language-files"></a>
### Указание атрибутов в языковых файлах

Многие из встроенных сообщений об ошибках Laravel включают заполнитель `:attribute`, который заменяется именем проверяемого поля или атрибута. Если вы хотите, чтобы часть вашего сообщения проверки `:attribute` была заменена настраиваемым значением, вы можете указать имя настраиваемого атрибута в массиве `attributes` языкового файла `resources/lang/xx/validation.php`:

    'attributes' => [
        'email' => 'email address',
    ],

<a name="specifying-values-in-language-files"></a>
### Указание значений в языковых файлах

Некоторые сообщения об ошибках встроенных правил проверки Laravel содержат заполнитель `:value`, который заменяется текущим значением атрибута запроса. Однако иногда вам может понадобиться заменить часть вашего сообщения проверки `:value` на пользовательское представление значения. Например, рассмотрим следующее правило, которое указывает, что номер кредитной карты требуется, если `payment_type` имеет значение `cc`:

    Validator::make($request->all(), [
        'credit_card_number' => 'required_if:payment_type,cc'
    ]);

В случае сбоя этого правила проверки будет выдано следующее сообщение об ошибке:

    The credit card number field is required when payment type is cc.

Вместо того, чтобы отображать `cc` в качестве значения типа платежа, вы можете указать более удобное для пользователя представление значения в вашем языковом файле `resources/lang/xx/validation.php`, определив массив значений `values`:

    'values' => [
        'payment_type' => [
            'cc' => 'credit card'
        ],
    ],

После определения этого значения правило проверки выдаст следующее сообщение об ошибке:

    The credit card number field is required when payment type is credit card.

<a name="available-validation-rules"></a>
## Доступные правила валидации

Ниже приведен список всех доступных правил проверки и их функции:

<style>
    .collection-method-list > p {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 2em; -moz-column-gap: 2em; -webkit-column-gap: 2em;
    }

    .collection-method-list a {
        display: block;
    }
</style>

<div class="collection-method-list" markdown="1">

[Accepted](#rule-accepted)
[Active URL](#rule-active-url)
[After (Date)](#rule-after)
[After Or Equal (Date)](#rule-after-or-equal)
[Alpha](#rule-alpha)
[Alpha Dash](#rule-alpha-dash)
[Alpha Numeric](#rule-alpha-num)
[Array](#rule-array)
[Bail](#rule-bail)
[Before (Date)](#rule-before)
[Before Or Equal (Date)](#rule-before-or-equal)
[Between](#rule-between)
[Boolean](#rule-boolean)
[Confirmed](#rule-confirmed)
[Date](#rule-date)
[Date Equals](#rule-date-equals)
[Date Format](#rule-date-format)
[Different](#rule-different)
[Digits](#rule-digits)
[Digits Between](#rule-digits-between)
[Dimensions (Image Files)](#rule-dimensions)
[Distinct](#rule-distinct)
[Email](#rule-email)
[Ends With](#rule-ends-with)
[Exclude If](#rule-exclude-if)
[Exclude Unless](#rule-exclude-unless)
[Exists (Database)](#rule-exists)
[File](#rule-file)
[Filled](#rule-filled)
[Greater Than](#rule-gt)
[Greater Than Or Equal](#rule-gte)
[Image (File)](#rule-image)
[In](#rule-in)
[In Array](#rule-in-array)
[Integer](#rule-integer)
[IP Address](#rule-ip)
[JSON](#rule-json)
[Less Than](#rule-lt)
[Less Than Or Equal](#rule-lte)
[Max](#rule-max)
[MIME Types](#rule-mimetypes)
[MIME Type By File Extension](#rule-mimes)
[Min](#rule-min)
[Multiple Of](#multiple-of)
[Not In](#rule-not-in)
[Not Regex](#rule-not-regex)
[Nullable](#rule-nullable)
[Numeric](#rule-numeric)
[Password](#rule-password)
[Present](#rule-present)
[Prohibited](#rule-prohibited)
[Prohibited If](#rule-prohibited-if)
[Prohibited Unless](#rule-prohibited-unless)
[Regular Expression](#rule-regex)
[Required](#rule-required)
[Required If](#rule-required-if)
[Required Unless](#rule-required-unless)
[Required With](#rule-required-with)
[Required With All](#rule-required-with-all)
[Required Without](#rule-required-without)
[Required Without All](#rule-required-without-all)
[Same](#rule-same)
[Size](#rule-size)
[Sometimes](#conditionally-adding-rules)
[Starts With](#rule-starts-with)
[String](#rule-string)
[Timezone](#rule-timezone)
[Unique (Database)](#rule-unique)
[URL](#rule-url)
[UUID](#rule-uuid)

</div>

<a name="rule-accepted"></a>
#### accepted

Проверяемое поле должно иметь значение `"yes"`, `"on"`, `1` или `true`. Это полезно для проверки принятия "Условий использования" или аналогичных полей.

<a name="rule-active-url"></a>
#### active_url

Проверяемое поле должно иметь допустимую запись A или AAAA в соответствии с функцией PHP `dns_get_record`. Имя хоста предоставленного URL извлекается с помощью функции PHP `parse_url` перед передачей в `dns_get_record`.

<a name="rule-after"></a>
#### after:_date_

Проверяемое поле должно иметь значение после заданной даты. Даты будут переданы в функцию PHP `strtotime` для преобразования в действительный экземпляр `DateTime`:

    'start_date' => 'required|date|after:tomorrow'

Вместо передачи строки даты для оценки `strtotime`, вы можете указать другое поле для сравнения с датой:

    'finish_date' => 'required|date|after:start_date'

<a name="rule-after-or-equal"></a>
#### after\_or\_equal:_date_

Проверяемое поле должно иметь значение после указанной даты или равное ей. Для получения дополнительной информации смотрите правило [after](#rule-after).

<a name="rule-alpha"></a>
#### alpha

Проверяемое поле должно состоять полностью из буквенных символов.

<a name="rule-alpha-dash"></a>
#### alpha_dash

Проверяемое поле может содержать буквенно-цифровые символы, а также дефисы и подчеркивания.

<a name="rule-alpha-num"></a>
#### alpha_num

Проверяемое поле должно состоять полностью из буквенно-цифровых символов.

<a name="rule-array"></a>
#### array

Проверяемое поле должно быть массивом PHP `array`.

Когда для правила массива `array` предоставляются дополнительные значения, каждый ключ во входном массиве должен присутствовать в списке значений, предоставленных правилу. В следующем примере ключ `admin` во входном массиве недействительна, так как она не содержится в списке значений, предоставленных правилу `array`:

    use Illuminate\Support\Facades\Validator;

    $input = [
        'user' => [
            'name' => 'Taylor Otwell',
            'username' => 'taylorotwell',
            'admin' => true,
        ],
    ];

    Validator::make($input, [
        'user' => 'array:username,locale',
    ]);

<a name="rule-bail"></a>
#### bail

Остановите выполнение правил проверки для поля после первого сбоя проверки.

В то время как правило `bail` прекращает проверку определенного поля только при сбое проверки, метод `stopOnFirstFailure` сообщит валидатору, что он должен прекратить проверку всех атрибутов после того, как произошла единственная ошибка проверки:

    if ($validator->stopOnFirstFailure()->fails()) {
        // ...
    }

<a name="rule-before"></a>
#### before:_date_

Проверяемое поле должно быть значением, предшествующим указанной дате. Даты будут переданы в функцию PHP `strtotime` для преобразования в действительный экземпляр `DateTime`. Кроме того, как правило [`after`](#rule-after), имя другого проверяемого поля может быть указано как значение `date`.

<a name="rule-before-or-equal"></a>
#### before\_or\_equal:_date_

Проверяемое поле должно иметь значение, предшествующее указанной дате или равное ей. Даты будут переданы в функцию PHP `strtotime` для преобразования в действительный экземпляр `DateTime`. Кроме того, как правило [`after`](#rule-after), имя другого проверяемого поля может быть указано как значение `date`.

<a name="rule-between"></a>
#### between:_min_,_max_

Проверяемое поле должно иметь размер между заданными _min_ и _max_. Строки, числа, массивы и файлы оцениваются так же, как правило [`size`](#rule-size).

<a name="rule-boolean"></a>
#### boolean

Проверяемое поле должно иметь возможность преобразования в логическое значение. Допустимые значения: `true`, `false`, `1`, `0`, `"1"` и `"0"`.

<a name="rule-confirmed"></a>
#### confirmed

Проверяемое поле должно иметь совпадающее поле `{field}_confirmation`. Например, если проверяемое поле - это `password`, соответствующее поле `password_confirmation` должно присутствовать во входных данных.

<a name="rule-date"></a>
#### date

Проверяемое поле должно быть действительной, не относительной датой в соответствии с функцией PHP `strtotime`.

<a name="rule-date-equals"></a>
#### date_equals:_date_

Проверяемое поле должно совпадать с указанной датой. Даты будут переданы в функцию PHP `strtotime` для преобразования в действительный экземпляр `DateTime`.

<a name="rule-date-format"></a>
#### date_format:_format_

Проверяемое поле должно соответствовать заданному _format_. При проверке поля следует использовать **либо** `date` или `date_format`, а не то и другое вместе. Это правило проверки поддерживает все форматы, поддерживаемые классом PHP [DateTime](https://www.php.net/manual/en/class.datetime.php).

<a name="rule-different"></a>
#### different:_field_

Проверяемое поле должно иметь значение, отличное от _field_.

<a name="rule-digits"></a>
#### digits:_value_

Проверяемое поле должно быть _numeric_ и иметь точную длину _value_.

<a name="rule-digits-between"></a>
#### digits_between:_min_,_max_

Проверяемое поле должно быть _numeric_ и иметь длину между заданными _min_ и _max_.

<a name="rule-dimensions"></a>
#### dimensions

Проверяемый файл должен быть изображением, отвечающим ограничениям размеров, указанным в параметрах правила:

    'avatar' => 'dimensions:min_width=100,min_height=200'

Доступные ограничения: _min\_width_, _max\_width_, _min\_height_, _max\_height_, _width_, _height_, _ratio_.

Ограничение _ratio_ должно быть представлено как ширина, разделенная на высоту. Это может быть указано дробью вроде `3/2` или числом с плавающей запятой, например, `1.5`:

    'avatar' => 'dimensions:ratio=3/2'

Поскольку для этого правила требуется несколько аргументов, вы можете использовать метод `Rule::dimensions` для плавного построения правила:

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'avatar' => [
            'required',
            Rule::dimensions()->maxWidth(1000)->maxHeight(500)->ratio(3 / 2),
        ],
    ]);

<a name="rule-distinct"></a>
#### distinct

При проверке массивов проверяемое поле не должно иметь повторяющихся значений:

    'foo.*.id' => 'distinct'

По умолчанию использует свободные сравнения переменных. Чтобы использовать строгие сравнения, вы можете добавить параметр `strict` в определение правила проверки:

    'foo.*.id' => 'distinct:strict'

Вы можете добавить `ignore_case` к аргументам правила проверки, чтобы правило игнорировало различия в использовании заглавных букв:

    'foo.*.id' => 'distinct:ignore_case'

<a name="rule-email"></a>
#### email

Проверяемое поле должно быть отформатировано как адрес электронной почты. Это правило проверки использует пакет [`egulias/email-validator`](https://github.com/egulias/EmailValidator) для проверки адреса электронной почты. По умолчанию применяется валидатор `RFCValidation`, но вы также можете применить другие стили валидации:

    'email' => 'email:rfc,dns'

В приведенном выше примере будут применяться проверки `RFCValidation` и `DNSCheckValidation`. Вот полный список стилей проверки, которые вы можете применить:

<div class="content-list" markdown="1">
- `rfc`: `RFCValidation`
- `strict`: `NoRFCWarningsValidation`
- `dns`: `DNSCheckValidation`
- `spoof`: `SpoofCheckValidation`
- `filter`: `FilterEmailValidation`
</div>

Валидатор `filter`, который использует функцию PHP `filter_var`, поставляется с Laravel и был поведением проверки электронной почты по умолчанию до Laravel версии 5.8.

> {note} Валидаторы `dns` и `spoof` требуют расширения PHP `intl`.

<a name="rule-ends-with"></a>
#### ends_with:_foo_,_bar_,...

Проверяемое поле должно заканчиваться одним из указанных значений.

<a name="rule-exclude-if"></a>
#### exclude_if:_anotherfield_,_value_

Проверяемое поле будет исключено из данных запроса, возвращаемых методами `validate` и `validated`, если поле _anotherfield_ равно _value_.

<a name="rule-exclude-unless"></a>
#### exclude_unless:_anotherfield_,_value_

Проверяемое поле будет исключено из данных запроса, возвращаемых методами `validate` и `validated` methods unless _anotherfield_ не равно _value_. Если _value_ равно `null` (`exclude_unless:name,null`), проверяемое поле будет исключено, если поле сравнения не имеет значение `null` или поле сравнения отсутствует в данных запроса.

<a name="rule-exists"></a>
#### exists:_table_,_column_

Проверяемое поле должно существовать в данной таблице базы данных.

<a name="basic-usage-of-exists-rule"></a>
#### Основное использование правила Exists

    'state' => 'exists:states'

Если опция `column` не указана, будет использоваться имя поля. Таким образом, в этом случае правило будет проверять, что таблица базы данных `states` содержит запись со значением столбца `state`, совпадающим со значением атрибута запроса `state`.

<a name="specifying-a-custom-column-name"></a>
#### Указание кастомного имени столбца

Вы можете явно указать имя столбца базы данных, которое должно использоваться правилом проверки, поместив его после имени таблицы базы данных:

    'state' => 'exists:states,abbreviation'

Иногда вам может потребоваться указать конкретное соединение с базой данных, которое будет использоваться для `exists` запроса. Вы можете сделать это, добавив имя подключения к имени таблицы:

    'email' => 'exists:connection.staff,email'

Вместо того, чтобы указывать имя таблицы напрямую, вы можете указать модель Eloquent, которая должна использоваться для определения имени таблицы:

    'user_id' => 'exists:App\Models\User,id'

Если вы хотите настроить запрос, выполняемый правилом проверки, вы можете использовать класс `Rule` для плавного определения правила. В этом примере мы также укажем правила проверки в виде массива вместо использования символа `|` для их разделения:

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'email' => [
            'required',
            Rule::exists('staff')->where(function ($query) {
                return $query->where('account_id', 1);
            }),
        ],
    ]);

<a name="rule-file"></a>
#### file

Проверяемое поле должно быть успешно загруженным файлом.

<a name="rule-filled"></a>
#### filled

Проверяемое поле не должно быть пустым, если оно присутствует.

<a name="rule-gt"></a>
#### gt:_field_

Проверяемое поле должно быть больше заданного _field_. Два поля должны быть одного типа. Строки, числа, массивы и файлы оцениваются с использованием тех же соглашений, что и правило [`size`](#rule-size).

<a name="rule-gte"></a>
#### gte:_field_

Проверяемое поле должно быть больше или равно заданному _field_. Два поля должны быть одного типа. Строки, числа, массивы и файлы оцениваются с использованием тех же соглашений, что и правило [`size`](#rule-size).

<a name="rule-image"></a>
#### image

Проверяемый файл должен быть изображением (jpg, jpeg, png, bmp, gif, svg или webp).

<a name="rule-in"></a>
#### in:_foo_,_bar_,...

Проверяемое поле должно быть включено в указанный список значений. Поскольку это правило часто требует, чтобы вы `implode` массив, метод `Rule::in` может быть использован для плавного построения правила:

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'zones' => [
            'required',
            Rule::in(['first-zone', 'second-zone']),
        ],
    ]);

Когда правило `in` комбинируется с правилом `array`, каждое значение во входном массиве должно присутствовать в списке значений, предоставленных правилу `in`. В следующем примере код аэропорта `LAS` во входном массиве недействителен, так как он не содержится в списке аэропортов, предоставленном правилу `in`:

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    $input = [
        'airports' => ['NYC', 'LAS'],
    ];

    Validator::make($input, [
        'airports' => [
            'required',
            'array',
            Rule::in(['NYC', 'LIT']),
        ],
    ]);

<a name="rule-in-array"></a>
#### in_array:_anotherfield_.*

Проверяемое поле должно существовать в значениях _anotherfield_.

<a name="rule-integer"></a>
#### integer

Проверяемое поле должно быть целым числом.

> {note} Это правило проверки не проверяет, что ввод относится к типу переменной «целое число», а только что ввод относится к типу, принятому правилом PHP `FILTER_VALIDATE_INT`. Если вам нужно проверить ввод как число, используйте это правило в сочетании с [правилом проверки `numeric`](#rule-numeric).

<a name="rule-ip"></a>
#### ip

Проверяемое поле должно быть IP-адресом.

<a name="ipv4"></a>
#### ipv4

Проверяемое поле должно быть адресом IPv4.

<a name="ipv6"></a>
#### ipv6

Проверяемое поле должно быть адресом IPv6.

<a name="rule-json"></a>
#### json

Проверяемое поле должно быть допустимой строкой JSON.

<a name="rule-lt"></a>
#### lt:_field_

Проверяемое поле должно быть меньше заданного _field_. Два поля должны быть одного типа. Строки, числа, массивы и файлы оцениваются с использованием тех же соглашений, что и правило [`size`](#rule-size).

<a name="rule-lte"></a>
#### lte:_field_

Проверяемое поле должно быть меньше или равно заданному _field_. Два поля должны быть одного типа. Строки, числа, массивы и файлы оцениваются с использованием тех же соглашений, что и правило [`size`](#rule-size).

<a name="rule-max"></a>
#### max:_value_

Проверяемое поле должно быть меньше или равно максимальному _value_. Строки, числа, массивы и файлы оцениваются так же, как правило [`size`](#rule-size).

<a name="rule-mimetypes"></a>
#### mimetypes:_text/plain_,...

Проверяемый файл должен соответствовать одному из указанных типов MIME:

    'video' => 'mimetypes:video/avi,video/mpeg,video/quicktime'

Чтобы определить тип MIME загруженного файла, содержимое файла будет прочитано, и платформа попытается угадать тип MIME, который может отличаться от типа MIME, предоставленного клиентом.

<a name="rule-mimes"></a>
#### mimes:_foo_,_bar_,...

Проверяемый файл должен иметь тип MIME, соответствующий одному из перечисленных расширений.

<a name="basic-usage-of-mime-rule"></a>
#### Основное использование правила MIME

    'photo' => 'mimes:jpg,bmp,png'

Несмотря на то, что вам нужно только указать расширения, это правило фактически проверяет MIME-тип файла, читая содержимое файла и угадывая его MIME-тип. Полный список типов MIME и их соответствующих расширений можно найти по следующему адресу:

[https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types](https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types)

<a name="rule-min"></a>
#### min:_value_

Проверяемое поле должно иметь минимальное значение _value_. Строки, числа, массивы и файлы оцениваются так же, как правило [`size`](#rule-size).

<a name="multiple-of"></a>
#### multiple_of:_value_

Проверяемое поле должно быть кратным _value_.

<a name="rule-not-in"></a>
#### not_in:_foo_,_bar_,...

Проверяемое поле не должно быть включено в данный список значений. Метод `Rule::notIn` может использоваться для плавного построения правила:

    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'toppings' => [
            'required',
            Rule::notIn(['sprinkles', 'cherries']),
        ],
    ]);

<a name="rule-not-regex"></a>
#### not_regex:_pattern_

Проверяемое поле не должно соответствовать заданному регулярному выражению.

Внутренне это правило использует функцию PHP `preg_match`. Указанный шаблон должен подчиняться тому же форматированию, которое требуется для `preg_match`, и, следовательно, также включать допустимые разделители. Например: `'email' => 'not_regex:/^.+$/i'`.

> {note} При использовании шаблонов `regex` / `not_regex` может потребоваться указать ваши правила проверки с использованием массива вместо использования разделителей `|`, особенно если регулярное выражение содержит символ `|`.

<a name="rule-nullable"></a>
#### nullable

Проверяемое поле может быть `null`.

<a name="rule-numeric"></a>
#### numeric

Проверяемое поле должно быть [numeric](https://www.php.net/manual/en/function.is-numeric.php).

<a name="rule-password"></a>
#### password

Проверяемое поле должно соответствовать паролю аутентифицированного пользователя. Вы можете указать [защиту аутентификации](/docs/{{version}}/authentication), используя первый параметр правила:

    'password' => 'password:api'

<a name="rule-present"></a>
#### present

Проверяемое поле должно присутствовать во входных данных, но может быть пустым.

<a name="rule-prohibited"></a>
#### prohibited

Проверяемое поле должно быть пустым или отсутствовать.

<a name="rule-prohibited-if"></a>
#### prohibited_if:_anotherfield_,_value_,...

Проверяемое поле должно быть пустым или отсутствовать, если поле _anotherfield_ равно любому _value_.

<a name="rule-prohibited-unless"></a>
#### prohibited_unless:_anotherfield_,_value_,...

Проверяемое поле должно быть пустым или отсутствовать, если поле _anotherfield_ не равно какому-либо _value_.

<a name="rule-regex"></a>
#### regex:_pattern_

Проверяемое поле должно соответствовать заданному регулярному выражению.

Внутренне это правило использует функцию PHP `preg_match`. Указанный шаблон должен подчиняться тому же форматированию, которое требуется для `preg_match`, и, следовательно, также включать допустимые разделители. Например: `'email' => 'regex:/^.+@.+$/i'`.

> {note} При использовании шаблонов `regex` / `not_regex` может потребоваться указать правила в массиве вместо использования разделителей `|`, особенно если регулярное выражение содержит символ `|`.

<a name="rule-required"></a>
#### required

Проверяемое поле должно присутствовать во входных данных и не быть пустым. Поле считается «пустым», если выполняется одно из следующих условий:

<div class="content-list" markdown="1">

- Значение равно `null`.
- Значение - пустая строка.
- Значение представляет собой пустой массив или пустой объект `Countable`.
- Значение - загруженный файл без пути.

</div>

<a name="rule-required-if"></a>
#### required_if:_anotherfield_,_value_,...

Проверяемое поле должно присутствовать и не быть пустым, если поле _anotherfield_ равно любому _value_.

Если вы хотите создать более сложное условие для правила `required_if`, вы можете использовать метод `Rule::requiredIf`. Этот метод принимает логическое значение или замыкание. При передаче закрытия оно должно возвращать `true` или `false`, чтобы указать, требуется ли проверяемое поле:

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    Validator::make($request->all(), [
        'role_id' => Rule::requiredIf($request->user()->is_admin),
    ]);

    Validator::make($request->all(), [
        'role_id' => Rule::requiredIf(function () use ($request) {
            return $request->user()->is_admin;
        }),
    ]);

<a name="rule-required-unless"></a>
#### required_unless:_anotherfield_,_value_,...

Проверяемое поле должно присутствовать и не быть пустым, если поле _anotherfield_ не равно какому-либо _value_. Это также означает, что в данных запроса должно присутствовать _anotherfield_, если _value_ не имеет значения `null`. Если _value_ равно `null` (`required_unless:name,null`), проверяемое поле будет обязательным, если поле сравнения не равно `null` или поле сравнения отсутствует в данных запроса.

<a name="rule-required-with"></a>
#### required_with:_foo_,_bar_,...

Проверяемое поле должно присутствовать и не быть пустым, _only if_ присутствует любое из других указанных полей, а не пустое.

<a name="rule-required-with-all"></a>
#### required_with_all:_foo_,_bar_,...

Проверяемое поле должно присутствовать и не быть пустым, _only if_ все другие указанные поля присутствуют, а не пусто.

<a name="rule-required-without"></a>
#### required_without:_foo_,_bar_,...

Проверяемое поле должно присутствовать и не быть пустым, _only when_ только если любое из других указанных полей пусто или отсутствует.

<a name="rule-required-without-all"></a>
#### required_without_all:_foo_,_bar_,...

Поле для проверки должно присутствовать и не быть пустым, _only when_ все другие указанные поля пусты или отсутствуют.

<a name="rule-same"></a>
#### same:_field_

Данное _field_ должно соответствовать проверяемому полю.

<a name="rule-size"></a>
#### size:_value_

Проверяемое поле должно иметь размер, соответствующий заданному _value_. Для строковых данных _value_ соответствует количеству символов. Для числовых данных _value_ соответствует заданному целочисленному значению (атрибут также должен иметь правило `numeric` или `integer`). Для массива _size_ соответствует `count` массива. Для файлов _size_ соответствует размеру файла в килобайтах. Давайте посмотрим на несколько примеров:

    // Убедитесь, что длина строки составляет ровно 12 символов...
    'title' => 'size:12';

    // Убедитесь, что указанное целое число равно 10...
    'seats' => 'integer|size:10';

    // Убедитесь, что в массиве ровно 5 элементов...
    'tags' => 'array|size:5';

    // Убедитесь, что загруженный файл составляет ровно 512 килобайт...
    'image' => 'file|size:512';

<a name="rule-starts-with"></a>
#### starts_with:_foo_,_bar_,...

Проверяемое поле должно начинаться с одного из указанных значений.

<a name="rule-string"></a>
#### string

Проверяемое поле должно быть строкой. Если вы хотите, чтобы поле также было `null`, вы должны назначить этому полю правило `nullable`.

<a name="rule-timezone"></a>
#### timezone

Проверяемое поле должно быть действительным идентификатором часового пояса в соответствии с функцией PHP `timezone_identifiers_list`.

<a name="rule-unique"></a>
#### unique:_table_,_column_,_except_,_idColumn_

Проверяемое поле не должно существовать в данной таблице базы данных.

**Указание настраиваемого имени таблицы / столбца:**

Вместо того, чтобы указывать имя таблицы напрямую, вы можете указать модель Eloquent, которая должна использоваться для определения имени таблицы:

    'email' => 'unique:App\Models\User,email_address'

Параметр `column` может использоваться для указания соответствующего столбца базы данных поля. Если опция `column` не указана, будет использоваться имя проверяемого поля.

    'email' => 'unique:users,email_address'

**Указание настраиваемого подключения к базе данных**

Иногда вам может потребоваться настроить пользовательское соединение для запросов к базе данных, сделанных валидатором. Для этого вы можете добавить имя подключения к имени таблицы:

    'email' => 'unique:connection.users,email_address'

**Принуждение уникального правила игнорировать данный идентификатор:**

Иногда вы можете захотеть проигнорировать данный идентификатор во время уникальной проверки. Например, рассмотрим экран «обновить профиль», который включает имя пользователя, адрес электронной почты и местоположение. Вероятно, вы захотите убедиться, что адрес электронной почты уникален. Однако, если пользователь изменяет только поле имени, а не поле электронной почты, вы не хотите, чтобы возникла ошибка проверки, потому что пользователь уже является владельцем рассматриваемого адреса электронной почты.

Чтобы указать валидатору игнорировать идентификатор пользователя, мы воспользуемся классом `Rule` для плавного определения правила. В этом примере мы также укажем правила проверки в виде массива вместо использования символа `|` для ограничения правил:

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'email' => [
            'required',
            Rule::unique('users')->ignore($user->id),
        ],
    ]);

> {note} Вы никогда не должны передавать какие-либо запросы, контролируемые пользователем, в метод `ignore`. Вместо этого вы должны передавать только уникальный идентификатор, сгенерированный системой, такой как автоматически увеличивающийся идентификатор или UUID из экземпляра модели Eloquent. В противном случае ваше приложение будет уязвимо для атаки с использованием SQL-инъекции.

Вместо того, чтобы передавать значение ключа модели методу `ignore`, вы также можете передать весь экземпляр модели. Laravel автоматически извлечет ключ из модели:

    Rule::unique('users')->ignore($user)

Если ваша таблица использует имя столбца первичного ключа, отличное от `id`, вы можете указать имя столбца при вызове метода `ignore`:

    Rule::unique('users')->ignore($user->id, 'user_id')

По умолчанию правило `unique` проверяет уникальность столбца, совпадающего с именем проверяемого атрибута. Однако вы можете передать другое имя столбца в качестве второго аргумента метода `unique`:

    Rule::unique('users', 'email_address')->ignore($user->id),

**Добавление дополнительных пунктов Where:**

Вы можете указать дополнительные условия запроса, настроив запрос с помощью метода `where`. Например, давайте добавим условие запроса, которое ограничивает поиск только тех записей, которые имеют значение столбца `account_id` равно `1`:

    'email' => Rule::unique('users')->where(function ($query) {
        return $query->where('account_id', 1);
    })

<a name="rule-url"></a>
#### url

Проверяемое поле должно быть действительным URL.

<a name="rule-uuid"></a>
#### uuid

Проверяемое поле должно быть действительным универсальным уникальным идентификатором (UUID) RFC 4122 (версии 1, 3, 4 или 5).

<a name="conditionally-adding-rules"></a>
## Условное добавление правил

<a name="skipping-validation-when-fields-have-certain-values"></a>
#### Пропуск проверки, когда поля имеют определенные значения

Иногда вы можете захотеть не проверять данное поле, если другое поле имеет заданное значение. Вы можете сделать это, используя правило проверки `exclude_if`. В этом примере поля `appointment_date` и `doctor_name` не будут проверяться, если поле `has_appointment` имеет значение `false`:

    use Illuminate\Support\Facades\Validator;

    $validator = Validator::make($data, [
        'has_appointment' => 'required|boolean',
        'appointment_date' => 'exclude_if:has_appointment,false|required|date',
        'doctor_name' => 'exclude_if:has_appointment,false|required|string',
    ]);

В качестве альтернативы вы можете использовать правило `exclude_unless`, чтобы не проверять данное поле, если другое поле не имеет данного значения:

    $validator = Validator::make($data, [
        'has_appointment' => 'required|boolean',
        'appointment_date' => 'exclude_unless:has_appointment,true|required|date',
        'doctor_name' => 'exclude_unless:has_appointment,true|required|string',
    ]);

<a name="validating-when-present"></a>
#### Проверка при наличии

В некоторых ситуациях вы можете захотеть выполнить проверки для поля **только**, если это поле присутствует в проверяемых данных. Чтобы быстро добиться этого, добавьте правило `sometimes` в свой список правил:

    $v = Validator::make($request->all(), [
        'email' => 'sometimes|required|email',
    ]);

В приведенном выше примере поле `email` будет проверено, только если оно присутствует в массиве `$data`.

> {tip} Если вы пытаетесь проверить поле, которое всегда должно присутствовать, но может быть пустым, ознакомьтесь с [этим примечанием о дополнительных полях](#a-note-on-optional-fields).

<a name="complex-conditional-validation"></a>
#### Комплексная условная проверка

Иногда вы можете захотеть добавить правила проверки, основанные на более сложной условной логике. Например, вы можете потребовать заданное поле только в том случае, если другое поле имеет значение больше 100. Или вам может потребоваться, чтобы два поля имели заданное значение только при наличии другого поля. Добавление этих правил проверки не должно вызывать затруднений. Сначала создайте экземпляр `Validator` с вашими _статическими правилами_, которые никогда не меняются:

    use Illuminate\Support\Facades\Validator;

    $validator = Validator::make($request->all(), [
        'email' => 'required|email',
        'games' => 'required|numeric',
    ]);

Предположим, наше веб-приложение предназначено для коллекционеров игр. Если коллекционер игр регистрируется в нашем приложении и у него есть более 100 игр, мы хотим, чтобы он объяснил, почему у него так много игр. Например, возможно, у них есть магазин по перепродаже игр, или, может быть, им просто нравится коллекционировать игры. Чтобы условно добавить это требование, мы можем использовать метод `sometimes` в экземпляре `Validator`.

    $v->sometimes('reason', 'required|max:500', function ($input) {
        return $input->games >= 100;
    });

Первым аргументом, передаваемым методу `sometimes`, является имя поля, которое мы условно проверяем. Второй аргумент - это список правил, которые мы хотим добавить. Если закрытие, переданное в качестве третьего аргумента, возвращает `true`, правила будут добавлены. Этот метод упрощает создание сложных условных проверок. Вы даже можете добавить условные проверки сразу для нескольких полей:

    $v->sometimes(['reason', 'cost'], 'required', function ($input) {
        return $input->games >= 100;
    });

> {tip} Параметр `$input`, переданный вашему закрытию, будет экземпляром `Illuminate\Support\Fluent` и может использоваться для доступа к вашему вводу и файлам при проверке.

<a name="validating-arrays"></a>
## Валидация массивов

Проверка полей ввода формы на основе массива не должна вызывать затруднений. Вы можете использовать «точечную нотацию» для проверки атрибутов в массиве. Например, если входящий HTTP-запрос содержит поле `photos[profile]`, вы можете проверить его следующим образом:

    use Illuminate\Support\Facades\Validator;

    $validator = Validator::make($request->all(), [
        'photos.profile' => 'required|image',
    ]);

Вы также можете проверить каждый элемент массива. Например, чтобы убедиться, что каждое электронное письмо в заданном поле ввода массива уникально, вы можете сделать следующее:

    $validator = Validator::make($request->all(), [
        'person.*.email' => 'email|unique:users',
        'person.*.first_name' => 'required_with:person.*.last_name',
    ]);

Точно так же вы можете использовать символ `*` при указании [настраиваемых сообщений проверки в ваших языковых файлах](#custom-messages-for-specific-attributes), что упрощает использование одного сообщения проверки для полей на основе массива:

    'custom' => [
        'person.*.email' => [
            'unique' => 'Each person must have a unique email address',
        ]
    ],

<a name="validating-passwords"></a>
## Валидация паролей

Чтобы гарантировать, что пароли имеют адекватный уровень сложности, вы можете использовать объект правила Laravel `Password`:

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rules\Password;

    $validator = Validator::make($request->all(), [
        'password' => ['required', 'confirmed', Password::min(8)],
    ]);

Объект правила `Password` позволяет вам легко настроить требования к сложности пароля для вашего приложения, например указать, что для паролей требуется хотя бы одна буква, цифра, символ или символы со смешанным регистром:

    // Требуется не менее 8 символов...
    Password::min(8)

    // Требуется хотя бы одна буква...
    Password::min(8)->letters()

    // Требуется хотя бы одна заглавная и одна строчная буква...
    Password::min(8)->mixedCase()

    // Требовать хотя бы одна цифра...
    Password::min(8)->numbers()

    // Требуется хотя бы один символ...
    Password::min(8)->symbols()

Кроме того, вы можете убедиться, что пароль не был скомпрометирован в результате утечки данных публичного пароля, используя метод `uncompromised`:

    Password::min(8)->uncompromised()

Внутренне объект правила `Password` использует модель [k-Anonymity](https://en.wikipedia.org/wiki/K-anonymity), чтобы определить, не произошла ли утечка пароля через [haveibeenpwned.com](https://haveibeenpwned.com) без ущерба для конфиденциальности или безопасности пользователя.

По умолчанию, если пароль появляется хотя бы один раз в утечке данных, он будет считаться скомпрометированным. Вы можете настроить этот порог, используя первый аргумент метода `uncompromised`:

    // Убедитесь, что пароль появляется менее 3 раз в одной и той же утечке данных...
    Password::min(8)->uncompromised(3);

Конечно, вы можете связать все методы в приведенных выше примерах:

    Password::min(8)
        ->letters()
        ->mixedCase()
        ->numbers()
        ->symbols()
        ->uncompromised()

<a name="defining-default-password-rules"></a>
#### Определение правил паролей по умолчанию

Возможно, вам будет удобно указать правила проверки паролей по умолчанию в одном месте вашего приложения. Вы можете легко сделать это, используя метод `Password::defaults`, который принимает замыкание. Замыкание, данное методу `defaults`, должно возвращать конфигурацию правила пароля по умолчанию. Обычно правило `defaults` должно вызываться в методе `boot` одного из сервис провайдеров вашего приложения:

```php
use Illuminate\Validation\Rules\Password;

/**
 * Bootstrap any application services.
 *
 * @return void
 */
public function boot()
{
    Password::defaults(function () {
        $rule = Password::min(8);

        return $this->app->isProduction()
                    ? $rule->mixedCase()->uncompromised()
                    : $rule;
    });
}
```

Затем, когда вы хотите применить правила по умолчанию к конкретному паролю, проходящему проверку, вы можете вызвать метод `defaults` без аргументов:

    'password' => ['required', Password::defaults()],

<a name="custom-validation-rules"></a>
## Пользовательские правила проверки

<a name="using-rule-objects"></a>
### Использование объектов правила

Laravel предоставляет множество полезных правил проверки; однако вы можете указать свои собственные. Один из методов регистрации настраиваемых правил проверки - использование объектов правил. Чтобы сгенерировать новый объект правила, вы можете использовать Artisan-команду `make:rule`. Давайте воспользуемся этой командой, чтобы сгенерировать правило, которое проверяет, что строка является прописной. Laravel поместит новое правило в каталог `app/Rules`. Если этот каталог не существует, Laravel создаст его, когда вы выполните команду Artisan для создания своего правила:

    php artisan make:rule Uppercase

Как только правило создано, мы готовы определить его поведение. Объект правила содержит два метода: `passes` и `message`. Метод `passes` получает значение и имя атрибута и должен возвращать `true` или `false` в зависимости от того, является ли значение атрибута допустимым или нет. Метод `message` должен возвращать сообщение об ошибке проверки, которое следует использовать, если проверка не удалась:

    <?php

    namespace App\Rules;

    use Illuminate\Contracts\Validation\Rule;

    class Uppercase implements Rule
    {
        /**
         * Determine if the validation rule passes.
         *
         * @param  string  $attribute
         * @param  mixed  $value
         * @return bool
         */
        public function passes($attribute, $value)
        {
            return strtoupper($value) === $value;
        }

        /**
         * Get the validation error message.
         *
         * @return string
         */
        public function message()
        {
            return 'The :attribute must be uppercase.';
        }
    }

Вы можете вызвать помощника `trans` из вашего метода `message`, если хотите вернуть сообщение об ошибке из ваших файлов перевода:

    /**
     * Get the validation error message.
     *
     * @return string
     */
    public function message()
    {
        return trans('validation.uppercase');
    }

После определения правила вы можете присоединить его к валидатору, передав экземпляр объекта правила с другими вашими правилами валидации:

    use App\Rules\Uppercase;

    $request->validate([
        'name' => ['required', 'string', new Uppercase],
    ]);

<a name="using-closures"></a>
### Использование замыканий

Если вам нужна функциональность настраиваемого правила только один раз во всем приложении, вы можете использовать замыкание вместо объекта правила. Замыкание получает имя атрибута, значение атрибута и обратный вызов `$fail`, который должен вызываться в случае неудачной проверки:

    use Illuminate\Support\Facades\Validator;

    $validator = Validator::make($request->all(), [
        'title' => [
            'required',
            'max:255',
            function ($attribute, $value, $fail) {
                if ($value === 'foo') {
                    $fail('The '.$attribute.' is invalid.');
                }
            },
        ],
    ]);

<a name="implicit-rules"></a>
### Неявные правила

По умолчанию, когда проверяемый атрибут отсутствует или содержит пустую строку, обычные правила проверки, включая настраиваемые правила, не выполняются. Например, правило [`unique`](#rule-unique) не будет выполняться для пустой строки:

    use Illuminate\Support\Facades\Validator;

    $rules = ['name' => 'unique:users,name'];

    $input = ['name' => ''];

    Validator::make($input, $rules)->passes(); // true

Чтобы настраиваемое правило работало, даже если атрибут пуст, правило должно подразумевать, что атрибут является обязательным. Чтобы создать «неявное» правило, реализуйте интерфейс `Illuminate\Contracts\Validation\ImplicitRule`. Этот интерфейс служит «маркерным интерфейсом» для валидатора; следовательно, он не содержит каких-либо дополнительных методов, которые необходимо реализовать помимо методов, требуемых типичным интерфейсом `Rule`.

> {note} «Неявное» правило только _подразумевает_, что атрибут является обязательным. Независимо от того, аннулирует ли он отсутствующий или пустой атрибут, решать вам.
