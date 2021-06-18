# События

- [Введение](#introduction)
- [Регистрация событий и слушателей](#registering-events-and-listeners)
    - [Генерация событий и слушателей](#generating-events-and-listeners)
    - [Регистрация событий вручную](#manually-registering-events)
    - [Обнаружение событий](#event-discovery)
- [Определение событий](#defining-events)
- [Определение слушателей](#defining-listeners)
- [Слушатели событий в очереди](#queued-event-listeners)
    - [Взаимодействие с очередью вручную](#manually-interacting-with-the-queue)
    - [Слушатели событий в очереди и транзакции базы данных](#queued-event-listeners-and-database-transactions)
    - [Обработка невыполненных заданий](#handling-failed-jobs)
- [Отправка событий](#dispatching-events)
- [Подписчики событий](#event-subscribers)
    - [Написание подписчиков события](#writing-event-subscribers)
    - [Регистрация подписчиков на событие](#registering-event-subscribers)

<a name="introduction"></a>
## Введение

События Laravel обеспечивают простую реализацию шаблона наблюдателя, позволяя вам подписываться и отслеживать различные события, происходящие в вашем приложении. Классы событий обычно хранятся в каталоге `app/Events`, а их слушатели в `app/Listeners`. Не беспокойтесь, если вы не видите эти каталоги в своем приложении, поскольку они будут созданы для вас, когда вы будете генерировать события и слушатели с помощью консольных команд Artisan.

События служат отличным способом разделения различных аспектов вашего приложения, поскольку у одного события может быть несколько слушателей, которые не зависят друг от друга. Например, вы можете захотеть отправлять уведомление Slack своему пользователю каждый раз, когда заказ будет отправлен. Вместо того, чтобы связывать код обработки заказа с кодом уведомления Slack, вы можете вызвать событие `App\Events\OrderShipped`, которое слушатель может получить и использовать для отправки уведомления Slack.

<a name="registering-events-and-listeners"></a>
## Регистрация событий и слушателей

`App\Providers\EventServiceProvider`, включенный в ваше приложение Laravel, предоставляет удобное место для регистрации всех слушателей событий вашего приложения. Свойство `listen` содержит массив всех событий (ключей) и их слушателей (значений). Вы можете добавить в этот массив столько событий, сколько требуется вашему приложению. Например, добавим событие `OrderShipped`:

    use App\Events\OrderShipped;
    use App\Listeners\SendShipmentNotification;

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        OrderShipped::class => [
            SendShipmentNotification::class,
        ],
    ];

> {tip} Команда `event:list` может использоваться для отображения списка всех событий и слушателей, зарегистрированных вашим приложением.

<a name="generating-events-and-listeners"></a>
### Генерация событий и слушателей

Конечно, вручную создавать файлы для каждого события и слушателя сложно. Вместо этого добавьте слушателей и события в свой `EventServiceProvider` и используйте Artisan-команду `event:generate`. Эта команда сгенерирует любые события или прослушиватели, перечисленные в вашем `EventServiceProvider`, которые еще не существуют:

    php artisan event:generate

В качестве альтернативы вы можете использовать Artisan-команды `make:event` и `make:listener` для генерации отдельных событий и слушателей:

    php artisan make:event PodcastProcessed

    php artisan make:listener SendPodcastNotification --event=PodcastProcessed

<a name="manually-registering-events"></a>
### Регистрация событий вручную

Обычно события должны регистрироваться через массив `EventServiceProvider` `$listen`; однако вы также можете зарегистрировать слушатели событий на основе класса или замыкания вручную в методе `boot` вашего `EventServiceProvider`:

    use App\Events\PodcastProcessed;
    use App\Listeners\SendPodcastNotification;
    use Illuminate\Support\Facades\Event;

    /**
     * Register any other events for your application.
     *
     * @return void
     */
    public function boot()
    {
        Event::listen(
            PodcastProcessed::class,
            [SendPodcastNotification::class, 'handle']
        );

        Event::listen(function (PodcastProcessed $event) {
            //
        });
    }

<a name="queuable-anonymous-event-listeners"></a>
#### Слушатели анонимных событий в очереди

При регистрации слушателей событий на основе замыкания вручную вы можете заключить замыкание слушателя в функцию `Illuminate\Events\queueable`, чтобы дать Laravel команду выполнить слушателя с помощью [queue](/docs/{{version}}/queues):

    use App\Events\PodcastProcessed;
    use function Illuminate\Events\queueable;
    use Illuminate\Support\Facades\Event;

    /**
     * Register any other events for your application.
     *
     * @return void
     */
    public function boot()
    {
        Event::listen(queueable(function (PodcastProcessed $event) {
            //
        }));
    }

Как и задания в очереди, вы можете использовать методы `onConnection`, `onQueue` и `delay` для настройки выполнения слушателя в очереди:

    Event::listen(queueable(function (PodcastProcessed $event) {
        //
    })->onConnection('redis')->onQueue('podcasts')->delay(now()->addSeconds(10)));

Если вы хотите обрабатывать сбои анонимного слушателя в очереди, вы можете обеспечить замыкание метода `catch` при определении слушателя `queueable`. Это замыкание получит экземпляр события и экземпляр `Throwable`, вызвавший сбой слушателя:

    use App\Events\PodcastProcessed;
    use function Illuminate\Events\queueable;
    use Illuminate\Support\Facades\Event;
    use Throwable;

    Event::listen(queueable(function (PodcastProcessed $event) {
        //
    })->catch(function (PodcastProcessed $event, Throwable $e) {
        // The queued listener failed...
    }));

<a name="wildcard-event-listeners"></a>
#### Слушатели событий с подстановочными знаками

Вы даже можете зарегистрировать слушателей, используя `*` в качестве параметра подстановочного знака, что позволит вам перехватывать несколько событий на одном и том же слушателе. Слушатели подстановочных знаков получают имя события в качестве первого аргумента и весь массив данных события в качестве второго аргумента:

    Event::listen('event.*', function ($eventName, array $data) {
        //
    });

<a name="event-discovery"></a>
### Обнаружение событий

Вместо того, чтобы вручную регистрировать события и слушателей в массиве `$listen` в `EventServiceProvider`, вы можете включить автоматическое обнаружение событий. Когда обнаружение событий включено, Laravel автоматически найдет и зарегистрирует ваши события и слушателей, сканируя каталог `Listeners` вашего приложения. Кроме того, любые явно определенные события, перечисленные в `EventServiceProvider` по-прежнему будут регистрироваться.

Laravel находит слушателей событий, сканируя классы слушателей с помощью служб отражения PHP. Когда Laravel находит какой-либо метод класса слушателя, который начинается с `handle`, Laravel зарегистрирует эти методы как слушатели событий для события, тип которого указан в сигнатуре метода:

    use App\Events\PodcastProcessed;

    class SendPodcastNotification
    {
        /**
         * Handle the given event.
         *
         * @param  \App\Events\PodcastProcessed  $event
         * @return void
         */
        public function handle(PodcastProcessed $event)
        {
            //
        }
    }

Обнаружение событий по умолчанию отключено, но вы можете включить его, переопределив метод `shouldDiscoverEvents` вашего приложения `EventServiceProvider`:

    /**
     * Determine if events and listeners should be automatically discovered.
     *
     * @return bool
     */
    public function shouldDiscoverEvents()
    {
        return true;
    }

По умолчанию будут просканированы все слушатели в каталоге вашего приложения `app/Listeners`. Если вы хотите определить дополнительные каталоги для сканирования, вы можете переопределить метод `discoverEventsWithin` в своем `EventServiceProvider`:

    /**
     * Get the listener directories that should be used to discover events.
     *
     * @return array
     */
    protected function discoverEventsWithin()
    {
        return [
            $this->app->path('Listeners'),
        ];
    }

<a name="event-discovery-in-production"></a>
#### Обнаружение событий в продакшене

В производственной среде для фреймворка неэффективно сканировать всех ваших слушателей по каждому запросу. Следовательно, в процессе развертывания вы должны запустить Artisan-команду `event:cache`, чтобы кэшировать манифест всех событий и слушателей вашего приложения. Этот манифест будет использоваться платформой для ускорения процесса регистрации события. Команда `event:clear` может использоваться для уничтожения кеша.

<a name="defining-events"></a>
## Определение событий

Класс события - это, по сути, контейнер данных, который содержит информацию, относящуюся к событию. Например, предположим, что событие `App\Events\OrderShipped` получает объект [Eloquent ORM](/docs/{{version}}/eloquent):

    <?php

    namespace App\Events;

    use App\Models\Order;
    use Illuminate\Broadcasting\InteractsWithSockets;
    use Illuminate\Foundation\Events\Dispatchable;
    use Illuminate\Queue\SerializesModels;

    class OrderShipped
    {
        use Dispatchable, InteractsWithSockets, SerializesModels;

        /**
         * The order instance.
         *
         * @var \App\Models\Order
         */
        public $order;

        /**
         * Create a new event instance.
         *
         * @param  \App\Models\Order  $order
         * @return void
         */
        public function __construct(Order $order)
        {
            $this->order = $order;
        }
    }

Как видите, в этом классе событий нет логики. Это контейнер для экземпляра `App\Models\Order`, который был приобретен. Трейт `SerializesModels`, используемый событием, будет изящно сериализовать любые модели Eloquent, если объект события сериализуется с использованием функции PHP `serialize`, например, при использовании [слушателей в очереди](#queued-event-listeners).

<a name="defining-listeners"></a>
## Определение слушателей

Затем давайте посмотрим на слушателя для нашего примера события. Слушатели событий получают экземпляры событий в своем методе `handle`. Команды Artisan `event:generate` и `make:listener` автоматически импортируют соответствующий класс события и напечатают событие в методе `handle`. В методе `handle` вы можете выполнять любые действия, необходимые для ответа на событие:

    <?php

    namespace App\Listeners;

    use App\Events\OrderShipped;

    class SendShipmentNotification
    {
        /**
         * Create the event listener.
         *
         * @return void
         */
        public function __construct()
        {
            //
        }

        /**
         * Handle the event.
         *
         * @param  \App\Events\OrderShipped  $event
         * @return void
         */
        public function handle(OrderShipped $event)
        {
            // Access the order using $event->order...
        }
    }

> {tip} Ваши слушатели событий также могут указывать любые зависимости, которые им нужны, от их конструкторов. Все слушатели событий разрешаются через [сервис контейнер](/docs/{{version}}/container) Laravel, поэтому зависимости будут внедрены автоматически.

<a name="stopping-the-propagation-of-an-event"></a>
#### Остановка распространения события

Иногда вы можете захотеть остановить распространение события другим слушателям. Вы можете сделать это, вернув `false` из метода `handle` вашего слушателя.

<a name="queued-event-listeners"></a>
## Слушатели событий в очереди

Очередь слушателей может быть полезна, если ваш слушатель будет выполнять медленную задачу, такую как отправка электронной почты или выполнение HTTP-запроса. Перед использованием слушателей в очереди не забудьте [настроить свою очередь](/docs/{{version}}/queues) и запустить обработчика очереди на вашем сервере или в локальной среде разработки.

Чтобы указать, что слушатель должен быть поставлен в очередь, добавьте интерфейс `ShouldQueue` в класс слушателя. Слушатели, сгенерированные Artisan-командами `event:generate` и `make:listener`, уже импортировали этот интерфейс в текущее пространство имен, поэтому вы можете использовать его немедленно:

    <?php

    namespace App\Listeners;

    use App\Events\OrderShipped;
    use Illuminate\Contracts\Queue\ShouldQueue;

    class SendShipmentNotification implements ShouldQueue
    {
        //
    }

Это оно! Теперь, когда отправляется событие, обрабатываемое этим слушателем, слушатель автоматически ставится в очередь диспетчером событий с использованием [системы очередей](/docs/{{version}}/queues). Если при выполнении слушателя очередью не возникает никаких исключений, задание в очереди будет автоматически удалено после завершения обработки.

<a name="customizing-the-queue-connection-queue-name"></a>
#### Настройка подключения к очереди и имени очереди

Если вы хотите настроить соединение очереди, имя очереди или время задержки очереди слушателя событий, вы можете определить свойства `$connection`, `$queue` или `$delay` в своем классе слушателя:

    <?php

    namespace App\Listeners;

    use App\Events\OrderShipped;
    use Illuminate\Contracts\Queue\ShouldQueue;

    class SendShipmentNotification implements ShouldQueue
    {
        /**
         * The name of the connection the job should be sent to.
         *
         * @var string|null
         */
        public $connection = 'sqs';

        /**
         * The name of the queue the job should be sent to.
         *
         * @var string|null
         */
        public $queue = 'listeners';

        /**
         * The time (seconds) before the job should be processed.
         *
         * @var int
         */
        public $delay = 60;
    }

Если вы хотите определить очередь слушателя во время выполнения, вы можете определить метод `viaQueue` для слушателя:

    /**
     * Get the name of the listener's queue.
     *
     * @return string
     */
    public function viaQueue()
    {
        return 'listeners';
    }

<a name="conditionally-queueing-listeners"></a>
#### Условная постановка слушателей в очередь

Иногда вам может потребоваться определить, следует ли ставить слушателя в очередь на основе некоторых данных, которые доступны только во время выполнения. Для этого к слушателю может быть добавлен метод `shouldQueue`, чтобы определить, следует ли поставить слушателя в очередь. Если метод `shouldQueue` возвращает `false`, слушатель не будет выполнен:

    <?php

    namespace App\Listeners;

    use App\Events\OrderCreated;
    use Illuminate\Contracts\Queue\ShouldQueue;

    class RewardGiftCard implements ShouldQueue
    {
        /**
         * Reward a gift card to the customer.
         *
         * @param  \App\Events\OrderCreated  $event
         * @return void
         */
        public function handle(OrderCreated $event)
        {
            //
        }

        /**
         * Determine whether the listener should be queued.
         *
         * @param  \App\Events\OrderCreated  $event
         * @return bool
         */
        public function shouldQueue(OrderCreated $event)
        {
            return $event->order->subtotal >= 5000;
        }
    }

<a name="manually-interacting-with-the-queue"></a>
### Взаимодействие с очередью вручную

Если вам нужно вручную получить доступ к методам `delete` и `release` базового задания очереди слушателя, вы можете сделать это с помощью трейта `Illuminate\Queue\InteractsWithQueue`. Этот трейт по умолчанию импортируется в сгенерированные слушатели и обеспечивает доступ к этим методам:

    <?php

    namespace App\Listeners;

    use App\Events\OrderShipped;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Queue\InteractsWithQueue;

    class SendShipmentNotification implements ShouldQueue
    {
        use InteractsWithQueue;

        /**
         * Handle the event.
         *
         * @param  \App\Events\OrderShipped  $event
         * @return void
         */
        public function handle(OrderShipped $event)
        {
            if (true) {
                $this->release(30);
            }
        }
    }

<a name="queued-event-listeners-and-database-transactions"></a>
### Слушатели событий в очереди и транзакции базы данных

Когда слушатели в очереди отправляются в транзакциях базы данных, они могут быть обработаны очередью до того, как транзакция базы данных будет зафиксирована. Когда это происходит, любые обновления, внесенные вами в модели или записи базы данных во время транзакции базы данных, могут еще не быть отражены в базе данных. Кроме того, любые модели или записи базы данных, созданные в рамках транзакции, могут не существовать в базе данных. Если ваш слушатель зависит от этих моделей, могут возникнуть непредвиденные ошибки при обработке задания, которое отправляет поставленный в очередь слушатель.

Если для параметра конфигурации вашего соединения с очередью `after_commit` установлено значение `false`, вы все равно можете указать, что конкретный слушатель в очереди должен быть отправлен после того, как все открытые транзакции базы данных были зафиксированы, путем определения свойства `$afterCommit` в классе слушателя:

    <?php

    namespace App\Listeners;

    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Queue\InteractsWithQueue;

    class SendShipmentNotification implements ShouldQueue
    {
        use InteractsWithQueue;

        public $afterCommit = true;
    }

> {tip} Чтобы узнать больше о том, как обойти эти проблемы, ознакомьтесь с документацией, касающейся [заданий в очереди и транзакций базы данных](/docs/{{version}}/queues#jobs-and-database-transactions).

<a name="handling-failed-jobs"></a>
### Обработка невыполненных заданий

Иногда ваши слушатели событий в очереди могут дать сбой. Если слушатель в очереди превышает максимальное количество попыток, определенное вашим обработчиком очереди, для вашего слушателя будет вызван метод `failed`. Метод `failed` получает экземпляр события и `Throwable`, вызвавший сбой:

    <?php

    namespace App\Listeners;

    use App\Events\OrderShipped;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Queue\InteractsWithQueue;

    class SendShipmentNotification implements ShouldQueue
    {
        use InteractsWithQueue;

        /**
         * Handle the event.
         *
         * @param  \App\Events\OrderShipped  $event
         * @return void
         */
        public function handle(OrderShipped $event)
        {
            //
        }

        /**
         * Handle a job failure.
         *
         * @param  \App\Events\OrderShipped  $event
         * @param  \Throwable  $exception
         * @return void
         */
        public function failed(OrderShipped $event, $exception)
        {
            //
        }
    }

<a name="specifying-queued-listener-maximum-attempts"></a>
#### Указание максимального количества попыток слушателя в очереди

Если один из ваших слушателей в очереди обнаруживает ошибку, вы, вероятно, не хотите, чтобы он продолжал повторять попытки бесконечно. Таким образом, Laravel предоставляет различные способы указать, сколько раз и как долго может выполняться попытка прослушивания.

Вы можете определить свойство `$tries` в своем классе слушателя, чтобы указать, сколько раз можно попытаться выполнить прослушивание, прежде чем он будет считаться неудачным:

    <?php

    namespace App\Listeners;

    use App\Events\OrderShipped;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Queue\InteractsWithQueue;

    class SendShipmentNotification implements ShouldQueue
    {
        use InteractsWithQueue;

        /**
         * The number of times the queued listener may be attempted.
         *
         * @var int
         */
        public $tries = 5;
    }

В качестве альтернативы определению того, сколько раз можно попытаться выполнить слушатель, прежде чем он потерпит неудачу, вы можете определить время, в которое больше не следует пытаться выполнить слушатель. Это позволяет попытаться выполнить прослушивание любое количество раз в течение заданного периода времени. Чтобы определить время, в которое больше не следует пытаться выполнить слушатель, добавьте метод `retryUntil` в свой класс слушателя. Этот метод должен возвращать экземпляр `DateTime`:

    /**
     * Determine the time at which the listener should timeout.
     *
     * @return \DateTime
     */
    public function retryUntil()
    {
        return now()->addMinutes(5);
    }

<a name="dispatching-events"></a>
## Отправка событий

Чтобы отправить событие, вы можете вызвать статический метод `dispatch` для события. Этот метод доступен в событии с помощью трейта `Illuminate\Foundation\Events\Dispatchable`. Любые аргументы, переданные методу `dispatch`, будут переданы конструктору события:

    <?php

    namespace App\Http\Controllers;

    use App\Events\OrderShipped;
    use App\Http\Controllers\Controller;
    use App\Models\Order;
    use Illuminate\Http\Request;

    class OrderShipmentController extends Controller
    {
        /**
         * Ship the given order.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function store(Request $request)
        {
            $order = Order::findOrFail($request->order_id);

            // Order shipment logic...

            OrderShipped::dispatch($order);
        }
    }

> {tip} При тестировании может быть полезно утверждать, что определенные события были отправлены без фактического запуска их слушателей. [Встроенные помощники по тестированию](/docs/{{version}}/mocking#event-fake) в Laravel упрощают работу.

<a name="event-subscribers"></a>
## Подписчики событий

<a name="writing-event-subscribers"></a>
### Написание подписчиков события

Подписчики событий - это классы, которые могут подписываться на несколько событий из самого класса подписчика, что позволяет вам определять несколько обработчиков событий в одном классе. Подписчики должны определить метод `subscribe`, которому будет передан экземпляр диспетчера событий. Вы можете вызвать метод `listen` для данного диспетчера, чтобы зарегистрировать слушателей событий:

    <?php

    namespace App\Listeners;

    class UserEventSubscriber
    {
        /**
         * Handle user login events.
         */
        public function handleUserLogin($event) {}

        /**
         * Handle user logout events.
         */
        public function handleUserLogout($event) {}

        /**
         * Register the listeners for the subscriber.
         *
         * @param  \Illuminate\Events\Dispatcher  $events
         * @return void
         */
        public function subscribe($events)
        {
            $events->listen(
                'Illuminate\Auth\Events\Login',
                [UserEventSubscriber::class, 'handleUserLogin']
            );

            $events->listen(
                'Illuminate\Auth\Events\Logout',
                [UserEventSubscriber::class, 'handleUserLogout']
            );
        }
    }

<a name="registering-event-subscribers"></a>
### Регистрация подписчиков на событие

После написания подписчика вы готовы зарегистрировать его в диспетчере событий. Вы можете регистрировать подписчиков, используя свойство `$subscribe` в `EventServiceProvider`. Например, добавим в список `UserEventSubscriber`:

    <?php

    namespace App\Providers;

    use App\Listeners\UserEventSubscriber;
    use Illuminate\Foundation\Support\Providers\EventServiceProvider as ServiceProvider;

    class EventServiceProvider extends ServiceProvider
    {
        /**
         * The event listener mappings for the application.
         *
         * @var array
         */
        protected $listen = [
            //
        ];

        /**
         * The subscriber classes to register.
         *
         * @var array
         */
        protected $subscribe = [
            UserEventSubscriber::class,
        ];
    }
