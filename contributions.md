# Руководство по внесению вклада

- [Отчеты об ошибках](#bug-reports)
- [Вопросы поддержки](#support-questions)
- [Обсуждение разработки ядра](#core-development-discussion)
- [Какая ветка?](#which-branch)
- [Скомпилированные активы](#compiled-assets)
- [Уязвимости безопасности](#security-vulnerabilities)
- [Стиль кодирования](#coding-style)
    - [PHPDoc](#phpdoc)
    - [StyleCI](#styleci)
- [Нормы поведения](#code-of-conduct)

<a name="bug-reports"></a>
## Отчеты об ошибках

Чтобы стимулировать активное сотрудничество, Laravel настоятельно поощряет пулл реквесты, а не только отчеты об ошибках. «Отчеты об ошибках» также могут быть отправлены в форме пулл реквеста, содержащего неудачный тест. Пулл реквесты будут рассматриваться только в том случае, если они помечены как «готовые к рассмотрению» (не в состоянии «черновик») и все тесты для новых функций пройдены. Устаревшие неактивные пулл реквесты, оставшиеся в состоянии «черновик», будут закрыты через несколько дней.

Однако, если Вы отправляете отчет об ошибке, Ваша проблема должна содержать заголовок и четкое описание проблемы. Вы также должны включить как можно больше релевантной информации и образец кода, демонстрирующий проблему. Цель отчета об ошибке - упростить для Вас и окружающих воспроизведение ошибки и разработку исправления.

Помните, что отчеты об ошибках создаются в надежде, что другие, у кого возникла такая же проблема, смогут сотрудничать с Вами в ее решении. Не ожидайте, что в отчете об ошибке будет автоматически отображаться какая-либо активность или что другие попытаются ее исправить. Создание отчета об ошибке помогает Вам и другим начать путь к устранению проблемы. Если Вы хотите внести свой вклад, Вы можете помочь, исправив [любые ошибки, перечисленные в наших средствах отслеживания проблем](https://github.com/issues?q=is%3Aopen+is%3Aissue+label%3Abug+user%3Alaravel). Вы должны быть аутентифицированы на GitHub, чтобы просматривать все проблемы Laravel.

Исходный код Laravel управляется на GitHub, и для каждого проекта Laravel есть репозитории:

<div class="content-list" markdown="1">
- [Laravel Application](https://github.com/laravel/laravel)
- [Laravel Art](https://github.com/laravel/art)
- [Laravel Documentation](https://github.com/laravel/docs)
- [Laravel Dusk](https://github.com/laravel/dusk)
- [Laravel Cashier Stripe](https://github.com/laravel/cashier)
- [Laravel Cashier Paddle](https://github.com/laravel/cashier-paddle)
- [Laravel Echo](https://github.com/laravel/echo)
- [Laravel Envoy](https://github.com/laravel/envoy)
- [Laravel Framework](https://github.com/laravel/framework)
- [Laravel Homestead](https://github.com/laravel/homestead)
- [Laravel Homestead Build Scripts](https://github.com/laravel/settler)
- [Laravel Horizon](https://github.com/laravel/horizon)
- [Laravel Jetstream](https://github.com/laravel/jetstream)
- [Laravel Passport](https://github.com/laravel/passport)
- [Laravel Sail](https://github.com/laravel/sail)
- [Laravel Sanctum](https://github.com/laravel/sanctum)
- [Laravel Scout](https://github.com/laravel/scout)
- [Laravel Socialite](https://github.com/laravel/socialite)
- [Laravel Telescope](https://github.com/laravel/telescope)
- [Laravel Website](https://github.com/laravel/laravel.com-next)
</div>

<a name="support-questions"></a>
## Вопросы поддержки

Трекеры проблем Laravel GitHub не предназначены для предоставления помощи или поддержки Laravel. Вместо этого используйте один из следующих каналов:

<div class="content-list" markdown="1">
- [GitHub Discussions](https://github.com/laravel/framework/discussions)
- [Laracasts Forums](https://laracasts.com/discuss)
- [Laravel.io Forums](https://laravel.io/forum)
- [StackOverflow](https://stackoverflow.com/questions/tagged/laravel)
- [Discord](https://discordapp.com/invite/KxwQuKb)
- [Larachat](https://larachat.co)
- [IRC](https://webchat.freenode.net/?nick=artisan&channels=%23laravel&prompt=1)
</div>

<a name="core-development-discussion"></a>
## Обсуждение разработки ядра

Вы можете предлагать новые функции или улучшения существующего поведения Laravel на [доске задач](https://github.com/laravel/ideas/issues). Если Вы предлагаете новую функцию, пожалуйста, будьте готовы реализовать хотя бы часть кода, который потребуется для завершения функции.

Неформальное обсуждение ошибок, новых функций и реализации существующих функций происходит в канале `#internals` на [Laravel Discord server](https://discordapp.com/invite/mPZNm7A). Тейлор Отвелл, сопровождающий Laravel, обычно присутствует на канале в будние дни с 8:00 до 17:00 (UTC-06: 00 или Америка/Чикаго) и время от времени присутствует на канале.

<a name="which-branch"></a>
## Какая ветка?

**All** исправления ошибок следует отправлять в последнюю стабильную ветку или в [текущую ветку LTS](/docs/{{version}}/releases#support-policy). Исправления ошибок **никогда** не следует отправлять в ветку `master`, если они не исправляют функции, существующие только в следующем выпуске.

**Minor** функции, **полностью обратно совместимые** с текущим выпуском, могут быть отправлены в последнюю стабильную ветку.

**Major** новые функции всегда следует отправлять в ветку `master`, которая содержит предстоящий выпуск.

Если Вы не уверены, относится ли ваша функция к основной или второстепенной, спросите Тейлора Отвелла на канале `#internals` на [Laravel Discord server](https://discordapp.com/invite/mPZNm7A).

<a name="compiled-assets"></a>
## Скомпилированные активы

Если Вы отправляете изменение, которое повлияет на скомпилированный файл, например, на большинство файлов в `resources/css` или `resources/js` репозитория `laravel/laravel`, не фиксируйте скомпилированные файлы. Из-за их большого размера они не могут быть реально рассмотрены сопровождающим. Это может быть использовано как способ внедрения вредоносного кода в Laravel. Чтобы предотвратить это, все скомпилированные файлы будут сгенерированы и зафиксированы сопровождающими Laravel.

<a name="security-vulnerabilities"></a>
## Уязвимости безопасности

Если Вы обнаружите уязвимость в системе безопасности в Laravel, отправьте электронное письмо Тейлору Отвеллу по адресу <a href="mailto:taylor@laravel.com">taylor@laravel.com</a>. Все уязвимости безопасности будут незамедлительно устранены.

<a name="coding-style"></a>
## Стиль кодирования

Laravel следует стандарту кодирования [PSR-2](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md) и [PSR-4](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md) стандарт автозагрузки.

<a name="phpdoc"></a>
### PHPDoc

Ниже приведен пример действующего блока документации Laravel. Обратите внимание, что за атрибутом `@param` идут два пробела, тип аргумента, еще два пробела и, наконец, имя переменной:

    /**
     * Register a binding with the container.
     *
     * @param  string|array  $abstract
     * @param  \Closure|string|null  $concrete
     * @param  bool  $shared
     * @return void
     *
     * @throws \Exception
     */
    public function bind($abstract, $concrete = null, $shared = false)
    {
        //
    }

<a name="styleci"></a>
### StyleCI

Не волнуйтесь, если стиль Вашего кода не идеален! [StyleCI](https://styleci.io/) автоматически объединит любые исправления стиля в репозиторий Laravel после объединения запросов на вытягивание. Это позволяет нам сосредоточиться на содержании статьи, а не на стиле кода.

<a name="code-of-conduct"></a>
## Нормы поведения

Кодекс поведения Laravel является производным от кодекса поведения Ruby. О любых нарушениях кодекса поведения можно сообщить Тейлору Отвеллу (taylor@laravel.com):

<div class="content-list" markdown="1">
- Участники будут терпимо относиться к противоположным взглядам.
- Участники должны гарантировать, что их язык и действия не содержат личных нападок и пренебрежительных личных замечаний.
- Толкуя слова и действия других, участники всегда должны исходить из добрых намерений.
- Не допускается поведение, которое можно обоснованно считать преследованием.
</div>
