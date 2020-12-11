# Контракты

- [Введение](#introduction)
    - [Контракты против Фасадов](#contracts-vs-facades)
- [Когда использовать контракты](#when-to-use-contracts)
- [Как использовать контракты](#how-to-use-contracts)
- [Справочник контрактов](#contract-reference)

<a name="introduction"></a>
## Введение

«Контракты» Laravel - это набор интерфейсов, которые определяют основные сервисы, предоставляемые фреймворком. Например, контракт `Illuminate\Contracts\Queue\Queue` определяет методы, необходимые для постановки заданий в очередь, а контракт `Illuminate\Contracts\Mail\Mailer` определяет методы, необходимые для отправки электронной почты.

Каждый контракт имеет соответствующую реализацию, предусмотренную структурой. Например, Laravel предоставляет реализацию очереди с множеством драйверов и реализацию почтовой программы, которая работает на [SwiftMailer](https://swiftmailer.symfony.com/).

Все контракты Laravel находятся в [их собственном репозитории GitHub](https://github.com/illuminate/contracts). Это обеспечивает быструю справочную информацию для всех доступных контрактов, а также единый несвязанный пакет, который можно использовать при создании пакетов, взаимодействующих со службами Laravel.

<a name="contracts-vs-facades"></a>
### Контракты против Фасадов

Laravel [фасады](/docs/{{version}}/facades) и вспомогательные функции предоставляют простой способ использования сервисов Laravel без необходимости вводить подсказки и разрешать контракты из контейнера сервиса. В большинстве случаев каждый фасад имеет эквивалентный контракт.

В отличие от фасадов, которые не требуют, чтобы Вы требовали их в конструкторе Вашего класса, контракты позволяют Вам определять явные зависимости для Ваших классов. Некоторые разработчики предпочитают явно определять свои зависимости таким образом и поэтому предпочитают использовать контракты, в то время как другим разработчикам нравится удобство фасадов. **В общем, большинство приложений могут без проблем использовать фасады во время разработки.**

<a name="when-to-use-contracts"></a>
## Когда использовать контракты

Решение использовать контракты или фасады будет зависеть от личного вкуса и вкусов Вашей команды разработчиков. И контракты, и фасады могут использоваться для создания надежных, хорошо протестированных приложений Laravel. Контракты и фасады не исключают друг друга. Некоторые части Ваших приложений могут использовать фасады, а другие зависят от контрактов. Пока Вы сосредотачиваете обязанности своего класса, Вы заметите очень мало практических различий между использованием контрактов и фасадов.

В общем, большинство приложений могут без проблем использовать фасады во время разработки. Если Вы создаете пакет, который интегрируется с несколькими фреймворками PHP, Вы можете использовать пакет `illuminate/contracts` для определения своей интеграции со службами Laravel без необходимости требовать конкретных реализаций Laravel в файле `composer.json` Вашего пакета.

<a name="how-to-use-contracts"></a>
## Как использовать контракты

Итак, как получить выполнение контракта? На самом деле это довольно просто.

Многие типы классов в Laravel разрешаются через [сервис контейнер](/docs/{{version}}/container), включая контроллеры, прослушиватели событий, мидлвары, задания в очереди и даже замыкания маршрутов. Итак, чтобы получить реализацию контракта, Вы можете просто «напечатать» интерфейс в конструкторе разрешаемого класса.

Например, взгляните на этот прослушиватель событий:

    <?php

    namespace App\Listeners;

    use App\Events\OrderWasPlaced;
    use App\Models\User;
    use Illuminate\Contracts\Redis\Factory;

    class CacheOrderInformation
    {
        /**
         * Реализация фабрики Redis.
         *
         * @var \Illuminate\Contracts\Redis\Factory
         */
        protected $redis;

        /**
         * Создайте новый экземпляр обработчика событий.
         *
         * @param  \Illuminate\Contracts\Redis\Factory  $redis
         * @return void
         */
        public function __construct(Factory $redis)
        {
            $this->redis = $redis;
        }

        /**
         * Обработать событие.
         *
         * @param  \App\Events\OrderWasPlaced  $event
         * @return void
         */
        public function handle(OrderWasPlaced $event)
        {
            //
        }
    }

Когда прослушиватель событий разрешен, сервис-контейнер будет читать подсказки типов в конструкторе класса и вводить соответствующее значение. Чтобы узнать больше о регистрации вещей в сервис-контейнере, ознакомьтесь с [его документацией](/docs/{{version}}/container).

<a name="contract-reference"></a>
## Справочник контрактов

Эта таблица предоставляет быстрый справочник по всем контрактам Laravel и их эквивалентным фасадам:

Контракт  |  Справочник Фасадов
------------- | -------------
[Illuminate\Contracts\Auth\Access\Authorizable](https://github.com/illuminate/contracts/blob/{{version}}/Auth/Access/Authorizable.php) | &nbsp;
[Illuminate\Contracts\Auth\Access\Gate](https://github.com/illuminate/contracts/blob/{{version}}/Auth/Access/Gate.php) | `Gate`
[Illuminate\Contracts\Auth\Authenticatable](https://github.com/illuminate/contracts/blob/{{version}}/Auth/Authenticatable.php) | &nbsp;
[Illuminate\Contracts\Auth\CanResetPassword](https://github.com/illuminate/contracts/blob/{{version}}/Auth/CanResetPassword.php) | &nbsp;
[Illuminate\Contracts\Auth\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Auth/Factory.php) | `Auth`
[Illuminate\Contracts\Auth\Guard](https://github.com/illuminate/contracts/blob/{{version}}/Auth/Guard.php) | `Auth::guard()`
[Illuminate\Contracts\Auth\PasswordBroker](https://github.com/illuminate/contracts/blob/{{version}}/Auth/PasswordBroker.php) | `Password::broker()`
[Illuminate\Contracts\Auth\PasswordBrokerFactory](https://github.com/illuminate/contracts/blob/{{version}}/Auth/PasswordBrokerFactory.php) | `Password`
[Illuminate\Contracts\Auth\StatefulGuard](https://github.com/illuminate/contracts/blob/{{version}}/Auth/StatefulGuard.php) | &nbsp;
[Illuminate\Contracts\Auth\SupportsBasicAuth](https://github.com/illuminate/contracts/blob/{{version}}/Auth/SupportsBasicAuth.php) | &nbsp;
[Illuminate\Contracts\Auth\UserProvider](https://github.com/illuminate/contracts/blob/{{version}}/Auth/UserProvider.php) | &nbsp;
[Illuminate\Contracts\Bus\Dispatcher](https://github.com/illuminate/contracts/blob/{{version}}/Bus/Dispatcher.php) | `Bus`
[Illuminate\Contracts\Bus\QueueingDispatcher](https://github.com/illuminate/contracts/blob/{{version}}/Bus/QueueingDispatcher.php) | `Bus::dispatchToQueue()`
[Illuminate\Contracts\Broadcasting\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Broadcasting/Factory.php) | `Broadcast`
[Illuminate\Contracts\Broadcasting\Broadcaster](https://github.com/illuminate/contracts/blob/{{version}}/Broadcasting/Broadcaster.php)  | `Broadcast::connection()`
[Illuminate\Contracts\Broadcasting\ShouldBroadcast](https://github.com/illuminate/contracts/blob/{{version}}/Broadcasting/ShouldBroadcast.php) | &nbsp;
[Illuminate\Contracts\Broadcasting\ShouldBroadcastNow](https://github.com/illuminate/contracts/blob/{{version}}/Broadcasting/ShouldBroadcastNow.php) | &nbsp;
[Illuminate\Contracts\Cache\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Cache/Factory.php) | `Cache`
[Illuminate\Contracts\Cache\Lock](https://github.com/illuminate/contracts/blob/{{version}}/Cache/Lock.php) | &nbsp;
[Illuminate\Contracts\Cache\LockProvider](https://github.com/illuminate/contracts/blob/{{version}}/Cache/LockProvider.php) | &nbsp;
[Illuminate\Contracts\Cache\Repository](https://github.com/illuminate/contracts/blob/{{version}}/Cache/Repository.php) | `Cache::driver()`
[Illuminate\Contracts\Cache\Store](https://github.com/illuminate/contracts/blob/{{version}}/Cache/Store.php) | &nbsp;
[Illuminate\Contracts\Config\Repository](https://github.com/illuminate/contracts/blob/{{version}}/Config/Repository.php) | `Config`
[Illuminate\Contracts\Console\Application](https://github.com/illuminate/contracts/blob/{{version}}/Console/Application.php) | &nbsp;
[Illuminate\Contracts\Console\Kernel](https://github.com/illuminate/contracts/blob/{{version}}/Console/Kernel.php) | `Artisan`
[Illuminate\Contracts\Container\Container](https://github.com/illuminate/contracts/blob/{{version}}/Container/Container.php) | `App`
[Illuminate\Contracts\Cookie\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Cookie/Factory.php) | `Cookie`
[Illuminate\Contracts\Cookie\QueueingFactory](https://github.com/illuminate/contracts/blob/{{version}}/Cookie/QueueingFactory.php) | `Cookie::queue()`
[Illuminate\Contracts\Database\ModelIdentifier](https://github.com/illuminate/contracts/blob/{{version}}/Database/ModelIdentifier.php) | &nbsp;
[Illuminate\Contracts\Debug\ExceptionHandler](https://github.com/illuminate/contracts/blob/{{version}}/Debug/ExceptionHandler.php) | &nbsp;
[Illuminate\Contracts\Encryption\Encrypter](https://github.com/illuminate/contracts/blob/{{version}}/Encryption/Encrypter.php) | `Crypt`
[Illuminate\Contracts\Events\Dispatcher](https://github.com/illuminate/contracts/blob/{{version}}/Events/Dispatcher.php) | `Event`
[Illuminate\Contracts\Filesystem\Cloud](https://github.com/illuminate/contracts/blob/{{version}}/Filesystem/Cloud.php) | `Storage::cloud()`
[Illuminate\Contracts\Filesystem\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Filesystem/Factory.php) | `Storage`
[Illuminate\Contracts\Filesystem\Filesystem](https://github.com/illuminate/contracts/blob/{{version}}/Filesystem/Filesystem.php) | `Storage::disk()`
[Illuminate\Contracts\Foundation\Application](https://github.com/illuminate/contracts/blob/{{version}}/Foundation/Application.php) | `App`
[Illuminate\Contracts\Hashing\Hasher](https://github.com/illuminate/contracts/blob/{{version}}/Hashing/Hasher.php) | `Hash`
[Illuminate\Contracts\Http\Kernel](https://github.com/illuminate/contracts/blob/{{version}}/Http/Kernel.php) | &nbsp;
[Illuminate\Contracts\Mail\MailQueue](https://github.com/illuminate/contracts/blob/{{version}}/Mail/MailQueue.php) | `Mail::queue()`
[Illuminate\Contracts\Mail\Mailable](https://github.com/illuminate/contracts/blob/{{version}}/Mail/Mailable.php) | &nbsp;
[Illuminate\Contracts\Mail\Mailer](https://github.com/illuminate/contracts/blob/{{version}}/Mail/Mailer.php) | `Mail`
[Illuminate\Contracts\Notifications\Dispatcher](https://github.com/illuminate/contracts/blob/{{version}}/Notifications/Dispatcher.php) | `Notification`
[Illuminate\Contracts\Notifications\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Notifications/Factory.php) | `Notification`
[Illuminate\Contracts\Pagination\LengthAwarePaginator](https://github.com/illuminate/contracts/blob/{{version}}/Pagination/LengthAwarePaginator.php) | &nbsp;
[Illuminate\Contracts\Pagination\Paginator](https://github.com/illuminate/contracts/blob/{{version}}/Pagination/Paginator.php) | &nbsp;
[Illuminate\Contracts\Pipeline\Hub](https://github.com/illuminate/contracts/blob/{{version}}/Pipeline/Hub.php) | &nbsp;
[Illuminate\Contracts\Pipeline\Pipeline](https://github.com/illuminate/contracts/blob/{{version}}/Pipeline/Pipeline.php) | &nbsp;
[Illuminate\Contracts\Queue\EntityResolver](https://github.com/illuminate/contracts/blob/{{version}}/Queue/EntityResolver.php) | &nbsp;
[Illuminate\Contracts\Queue\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Queue/Factory.php) | `Queue`
[Illuminate\Contracts\Queue\Job](https://github.com/illuminate/contracts/blob/{{version}}/Queue/Job.php) | &nbsp;
[Illuminate\Contracts\Queue\Monitor](https://github.com/illuminate/contracts/blob/{{version}}/Queue/Monitor.php) | `Queue`
[Illuminate\Contracts\Queue\Queue](https://github.com/illuminate/contracts/blob/{{version}}/Queue/Queue.php) | `Queue::connection()`
[Illuminate\Contracts\Queue\QueueableCollection](https://github.com/illuminate/contracts/blob/{{version}}/Queue/QueueableCollection.php) | &nbsp;
[Illuminate\Contracts\Queue\QueueableEntity](https://github.com/illuminate/contracts/blob/{{version}}/Queue/QueueableEntity.php) | &nbsp;
[Illuminate\Contracts\Queue\ShouldQueue](https://github.com/illuminate/contracts/blob/{{version}}/Queue/ShouldQueue.php) | &nbsp;
[Illuminate\Contracts\Redis\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Redis/Factory.php) | `Redis`
[Illuminate\Contracts\Routing\BindingRegistrar](https://github.com/illuminate/contracts/blob/{{version}}/Routing/BindingRegistrar.php) | `Route`
[Illuminate\Contracts\Routing\Registrar](https://github.com/illuminate/contracts/blob/{{version}}/Routing/Registrar.php) | `Route`
[Illuminate\Contracts\Routing\ResponseFactory](https://github.com/illuminate/contracts/blob/{{version}}/Routing/ResponseFactory.php) | `Response`
[Illuminate\Contracts\Routing\UrlGenerator](https://github.com/illuminate/contracts/blob/{{version}}/Routing/UrlGenerator.php) | `URL`
[Illuminate\Contracts\Routing\UrlRoutable](https://github.com/illuminate/contracts/blob/{{version}}/Routing/UrlRoutable.php) | &nbsp;
[Illuminate\Contracts\Session\Session](https://github.com/illuminate/contracts/blob/{{version}}/Session/Session.php) | `Session::driver()`
[Illuminate\Contracts\Support\Arrayable](https://github.com/illuminate/contracts/blob/{{version}}/Support/Arrayable.php) | &nbsp;
[Illuminate\Contracts\Support\Htmlable](https://github.com/illuminate/contracts/blob/{{version}}/Support/Htmlable.php) | &nbsp;
[Illuminate\Contracts\Support\Jsonable](https://github.com/illuminate/contracts/blob/{{version}}/Support/Jsonable.php) | &nbsp;
[Illuminate\Contracts\Support\MessageBag](https://github.com/illuminate/contracts/blob/{{version}}/Support/MessageBag.php) | &nbsp;
[Illuminate\Contracts\Support\MessageProvider](https://github.com/illuminate/contracts/blob/{{version}}/Support/MessageProvider.php) | &nbsp;
[Illuminate\Contracts\Support\Renderable](https://github.com/illuminate/contracts/blob/{{version}}/Support/Renderable.php) | &nbsp;
[Illuminate\Contracts\Support\Responsable](https://github.com/illuminate/contracts/blob/{{version}}/Support/Responsable.php) | &nbsp;
[Illuminate\Contracts\Translation\Loader](https://github.com/illuminate/contracts/blob/{{version}}/Translation/Loader.php) | &nbsp;
[Illuminate\Contracts\Translation\Translator](https://github.com/illuminate/contracts/blob/{{version}}/Translation/Translator.php) | `Lang`
[Illuminate\Contracts\Validation\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Validation/Factory.php) | `Validator`
[Illuminate\Contracts\Validation\ImplicitRule](https://github.com/illuminate/contracts/blob/{{version}}/Validation/ImplicitRule.php) | &nbsp;
[Illuminate\Contracts\Validation\Rule](https://github.com/illuminate/contracts/blob/{{version}}/Validation/Rule.php) | &nbsp;
[Illuminate\Contracts\Validation\ValidatesWhenResolved](https://github.com/illuminate/contracts/blob/{{version}}/Validation/ValidatesWhenResolved.php) | &nbsp;
[Illuminate\Contracts\Validation\Validator](https://github.com/illuminate/contracts/blob/{{version}}/Validation/Validator.php) | `Validator::make()`
[Illuminate\Contracts\View\Engine](https://github.com/illuminate/contracts/blob/{{version}}/View/Engine.php) | &nbsp;
[Illuminate\Contracts\View\Factory](https://github.com/illuminate/contracts/blob/{{version}}/View/Factory.php) | `View`
[Illuminate\Contracts\View\View](https://github.com/illuminate/contracts/blob/{{version}}/View/View.php) | `View::make()`
