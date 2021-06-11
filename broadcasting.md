# Широковещание

- [Введение](#introduction)
- [Установка на стороне сервера](#server-side-installation)
    - [Конфигурация](#configuration)
    - [Pusher Channels](#pusher-channels)
    - [Ably](#ably)
    - [Альтернативы с открытым исходным кодом](#open-source-alternatives)
- [Установка на стороне клиента](#client-side-installation)
    - [Pusher Channels](#client-pusher-channels)
    - [Ably](#client-ably)
- [Обзор концепции](#concept-overview)
    - [Использование примера приложения](#using-example-application)
- [Определение широковещательных событий](#defining-broadcast-events)
    - [Название трансляции](#broadcast-name)
    - [Данные трансляции](#broadcast-data)
    - [Очередь трансляции](#broadcast-queue)
    - [Условия трансляции](#broadcast-conditions)
    - [Трансляция и транзакции базы данных](#broadcasting-and-database-transactions)
- [Каналы авторизации](#authorizing-channels)
    - [Определение маршрутов авторизации](#defining-authorization-routes)
    - [Определение обратных вызовов авторизации](#defining-authorization-callbacks)
    - [Определение классов каналов](#defining-channel-classes)
- [Трансляция событий](#broadcasting-events)
    - [Только другим](#only-to-others)
- [Прием трансляций](#receiving-broadcasts)
    - [Прослушивание событий](#listening-for-events)
    - [Оставление канала](#leaving-a-channel)
    - [Пространства имён](#namespaces)
- [Каналы присутствия](#presence-channels)
    - [Авторизация каналов присутствия](#authorizing-presence-channels)
    - [Присоединение к каналам присутствия](#joining-presence-channels)
    - [Вещание на каналы присутствия](#broadcasting-to-presence-channels)
- [Клиентские события](#client-events)
- [Уведомления](#notifications)

<a name="introduction"></a>
## Введение

Во многих современных веб-приложениях WebSockets используются для реализации пользовательских интерфейсов, обновляемых в реальном времени. Когда некоторые данные обновляются на сервере, сообщение обычно отправляется через соединение WebSocket для обработки клиентом. WebSockets предоставляют более эффективную альтернативу постоянному опросу сервера вашего приложения на предмет изменений данных, которые должны быть отражены в вашем пользовательском интерфейсе.

Например, представьте, что ваше приложение может экспортировать данные пользователя в файл CSV и отправлять его им по электронной почте. Однако создание этого CSV-файла занимает несколько минут, поэтому вы выбираете создание и отправку CSV-файла в рамках [задания в очереди](/docs/{{version}}/queues). Когда CSV был создан и отправлен пользователю, мы можем использовать широковещательную рассылку событий для отправки события `App\Events\UserDataExported`, которое получает JavaScript нашего приложения. Как только событие получено, мы можем отобразить сообщение пользователю о том, что его CSV был отправлен ему по электронной почте, без необходимости обновлять страницу.

Чтобы помочь вам в создании этих типов функций, Laravel упрощает «трансляцию» серверных Laravel [событий](/docs/{{version}}/events) через соединение WebSocket. Трансляция ваших событий Laravel позволяет вам использовать одни и те же имена событий и данные между серверным приложением Laravel и клиентским приложением JavaScript.

Основные концепции трансляции просты: клиенты подключаются к именованным каналам во внешнем интерфейсе, в то время как ваше приложение Laravel транслирует события на эти каналы во внутреннем интерфейсе. Эти события могут содержать любые дополнительные данные, которые вы хотите сделать доступными для внешнего интерфейса.

<a name="supported-drivers"></a>
#### Поддерживаемые драйверы

По умолчанию Laravel включает два драйвера вещания на стороне сервера, из которых вы можете выбирать: [Pusher Channels](https://pusher.com/channels) и [Ably](https://ably.io). However, community driven packages such as [laravel-websockets](https://beyondco.de/docs/laravel-websockets/getting-started/introduction) provide additional broadcasting drivers that do not require commercial broadcasting providers.

> {tip} Прежде чем погрузиться в трансляцию событий, убедитесь, что вы прочитали документацию Laravel по [событиям и слушателям](/docs/{{version}}/events).

<a name="server-side-installation"></a>
## Установка на стороне сервера

Чтобы начать использовать трансляцию событий Laravel, нам нужно выполнить некоторую настройку в приложении Laravel, а также установить несколько пакетов.

Трансляция событий осуществляется серверным драйвером вещания, который транслирует ваши события Laravel, чтобы Laravel Echo (библиотека JavaScript) мог получать их в клиенте браузера. Не волнуйтесь - мы рассмотрим каждую часть процесса установки шаг за шагом.

<a name="configuration"></a>
### Конфигурация

Вся конфигурация трансляции событий вашего приложения хранится в файле конфигурации `config/broadcasting.php`. Laravel из коробки поддерживает несколько драйверов вещания: [Pusher Channels](https://pusher.com/channels), [Redis](/docs/{{version}}/redis), и драйвер `log` для локальной разработки и отладки. Кроме того, включен драйвер `null`, который позволяет полностью отключить трансляцию во время тестирования. Пример конфигурации включен для каждого из этих драйверов в файл конфигурации `config/broadcasting.php`.

<a name="broadcast-service-provider"></a>
#### Сервис провайдер широковещания

Перед трансляцией каких-либо событий вам сначала необходимо зарегистрировать `App\Providers\BroadcastServiceProvider`. IВ новых приложениях Laravel вам нужно только раскомментировать этого провайдера в массиве `providers` вашего конфигурационного файла `config/app.php`. Этот `BroadcastServiceProvider` содержит код, необходимый для регистрации маршрутов авторизации широковещательной передачи и обратных вызовов.

<a name="queue-configuration"></a>
#### Конфигурация очереди

Вам также потребуется настроить и запустить [работник очереди](/docs/{{version}}/queues). Все трансляции событий выполняются с помощью заданий в очереди, так что время отклика вашего приложения не сильно зависит от транслируемых событий.

<a name="pusher-channels"></a>
### Pusher Channels

Если вы планируете транслировать свои события с помощью [Pusher Channels](https://pusher.com/channels), вам следует установить Pusher Channels PHP SDK с помощью диспетчера пакетов Composer::

    composer require pusher/pusher-php-server

Затем вы должны настроить свои учетные данные для каналов Pusher в конфигурационном файле `config/broadcasting.php`. Пример конфигурации Pusher Channels уже включен в этот файл, что позволяет быстро указать свой ключ, секрет и идентификатор приложения. Как правило, эти значения должны быть установлены через `PUSHER_APP_KEY`, `PUSHER_APP_SECRET` и `PUSHER_APP_ID` [переменные среды](/docs/{{version}}/configuration#environment-configuration):

    PUSHER_APP_ID=your-pusher-app-id
    PUSHER_APP_KEY=your-pusher-key
    PUSHER_APP_SECRET=your-pusher-secret
    PUSHER_APP_CLUSTER=mt1

Конфигурация `pusher` в файле `config/broadcasting.php` также позволяет вам указать дополнительные опции `options`, которые поддерживаются каналами, например, кластер.

Затем вам нужно будет изменить драйвер вещания на `pusher` в файле `.env`:

    BROADCAST_DRIVER=pusher

Наконец, вы готовы установить и настроить [Laravel Echo](#client-side-installation), который будет получать широковещательные события на стороне клиента.

<a name="pusher-compatible-laravel-websockets"></a>
#### Веб-сокеты Laravel, совместимые с Pusher

Пакет [laravel-websockets](https://github.com/beyondcode/laravel-websockets) - это чистый PHP, совместимый с Pusher пакет WebSocket для Laravel. Этот пакет позволяет вам использовать всю мощь вещания Laravel без коммерческого поставщика WebSocket. Для получения дополнительной информации об установке и использовании этого пакета обратитесь к его [официальной документации](https://beyondco.de/docs/laravel-websockets).

<a name="ably"></a>
### Ably

If you plan to broadcast your events using [Ably](https://ably.io), you should install the Ably PHP SDK using the Composer package manager:

    composer require ably/ably-php

Next, you should configure your Ably credentials in the `config/broadcasting.php` configuration file. An example Ably configuration is already included in this file, allowing you to quickly specify your key. Typically, this value should be set via the `ABLY_KEY` [environment variable](/docs/{{version}}/configuration#environment-configuration):

    ABLY_KEY=your-ably-key

Затем вам нужно будет изменить драйвер вещания на `ably` в файле `.env`:

    BROADCAST_DRIVER=ably

Наконец, вы готовы установить и настроить [Laravel Echo](#client-side-installation), который будет получать широковещательные события на стороне клиента.

<a name="open-source-alternatives"></a>
### Альтернативы с открытым исходным кодом

Пакет [laravel-websockets](https://github.com/beyondcode/laravel-websockets) - это чистый PHP, совместимый с Pusher пакет WebSocket для Laravel. Этот пакет позволяет вам использовать всю мощь вещания Laravel без коммерческого поставщика WebSocket. Для получения дополнительной информации об установке и использовании этого пакета обратитесь к его [официальной документации](https://beyondco.de/docs/laravel-websockets).

<a name="client-side-installation"></a>
## Установка на стороне клиента

<a name="client-pusher-channels"></a>
### Pusher Channels

Laravel Echo - это библиотека JavaScript, которая упрощает подписку на каналы и прослушивание событий, транслируемых вашим драйвером вещания на стороне сервера. Вы можете установить Echo через диспетчер пакетов NPM. В этом примере мы также установим пакет `pusher-js` , поскольку мы будем использовать броадкастер Pusher Channels:

```bash
npm install --save-dev laravel-echo pusher-js
```

После установки Echo вы готовы создать новый экземпляр Echo в JavaScript вашего приложения. Отличное место для этого - внизу файла `resources/js/bootstrap.js`, который включен в фреймворк Laravel. По умолчанию в этот файл уже включен пример конфигурации Echo - вам просто нужно раскомментировать его:

```js
import Echo from 'laravel-echo';

window.Pusher = require('pusher-js');

window.Echo = new Echo({
    broadcaster: 'pusher',
    key: process.env.MIX_PUSHER_APP_KEY,
    cluster: process.env.MIX_PUSHER_APP_CLUSTER,
    forceTLS: true
});
```

После того, как вы раскомментировали и настроили конфигурацию Echo в соответствии с вашими потребностями, вы можете скомпилировать активы вашего приложения:

    npm run dev

> {tip} Чтобы узнать больше о компиляции ресурсов JavaScript вашего приложения, обратитесь к документации на [Laravel Mix](/docs/{{version}}/mix).

<a name="using-an-existing-client-instance"></a>
#### Использование существующего экземпляра клиента

Если у вас уже есть предварительно настроенный экземпляр клиента Pusher Channels, который вы хотели бы использовать в Echo, вы можете передать его Echo с помощью параметра конфигурации `client`:

```js
import Echo from 'laravel-echo';

const client = require('pusher-js');

window.Echo = new Echo({
    broadcaster: 'pusher',
    key: 'your-pusher-channels-key',
    client: client
});
```

<a name="client-ably"></a>
### Ably

Laravel Echo - это библиотека JavaScript, которая упрощает подписку на каналы и прослушивание событий, транслируемых вашим драйвером вещания на стороне сервера. Вы можете установить Echo через диспетчер пакетов NPM. В этом примере мы также установим пакет `pusher-js`.

Вы можете задаться вопросом, зачем нам устанавливать библиотеку JavaScript `pusher-js`, даже если мы используем Ably для трансляции наших событий. К счастью, Ably включает режим совместимости Pusher, который позволяет нам использовать протокол Pusher при прослушивании событий в нашем клиентском приложении:

```bash
npm install --save-dev laravel-echo pusher-js
```

**Прежде чем продолжить, вы должны включить поддержку протокола Pusher в настройках вашего приложения Ably. Вы можете включить эту функцию в разделе «Настройки адаптера протокола» на панели настроек вашего приложения Ably.**

После установки Echo вы готовы создать новый экземпляр Echo в JavaScript вашего приложения. Отличное место для этого - внизу файла `resources/js/bootstrap.js`, который включен в фреймворк Laravel. По умолчанию в этот файл уже включен пример конфигурации Echo; однако конфигурация по умолчанию в файле `bootstrap.js` предназначена для Pusher. Вы можете скопировать конфигурацию ниже, чтобы перенести вашу конфигурацию на Ably:

```js
import Echo from 'laravel-echo';

window.Pusher = require('pusher-js');

window.Echo = new Echo({
    broadcaster: 'pusher',
    key: process.env.MIX_ABLY_PUBLIC_KEY,
    wsHost: 'realtime-pusher.ably.io',
    wsPort: 443,
    disableStats: true,
    encrypted: true,
});
```

Обратите внимание, что наша конфигурация Ably Echo ссылается на переменную окружения `MIX_ABLY_PUBLIC_KEY`. Значение этой переменной должно быть вашим открытым ключом Ably. Ваш открытый ключ - это часть вашего ключа Ably, которая стоит перед символом `:`.

После того, как вы раскомментировали и настроили конфигурацию Echo в соответствии с вашими потребностями, вы можете скомпилировать активы вашего приложения:

    npm run dev

> {tip} Чтобы узнать больше о компиляции ресурсов JavaScript вашего приложения, обратитесь к документации на [Laravel Mix](/docs/{{version}}/mix).

<a name="concept-overview"></a>
## Обзор концепции

Трансляция событий Laravel позволяет транслировать события Laravel на стороне сервера в приложение JavaScript на стороне клиента, используя подход к WebSockets на основе драйверов. В настоящее время Laravel поставляется с драйверами [Pusher Channels](https://pusher.com/channels) и [Ably](https://ably.io). События могут быть легко обработаны на стороне клиента с помощью пакета JavaScript [Laravel Echo](#client-side-installation).

События транслируются по «каналам», которые могут быть общедоступными или частными. Любой посетитель вашего приложения может подписаться на общедоступный канал без какой-либо аутентификации или авторизации; однако, чтобы подписаться на частный канал, пользователь должен быть аутентифицирован и авторизован для прослушивания на этом канале.

> {tip} Если вы хотите использовать PHP-альтернативу Pusher с открытым исходным кодом, ознакомьтесь с пакетом [laravel-websockets](https://github.com/beyondcode/laravel-websockets).

<a name="using-example-application"></a>
### Использование примера приложения

Прежде чем углубляться в каждый компонент трансляции событий, давайте сделаем общий обзор на примере магазина электронной коммерции.

В нашем приложении предположим, что у нас есть страница, которая позволяет пользователям просматривать статус доставки своих заказов. Предположим также, что событие `OrderShipmentStatusUpdated` запускается, когда приложение обрабатывает обновление статуса доставки:

    use App\Events\OrderShipmentStatusUpdated;

    OrderShipmentStatusUpdated::dispatch($order);

<a name="the-shouldbroadcast-interface"></a>
#### Интерфейс `ShouldBroadcast`

Когда пользователь просматривает один из своих заказов, мы не хотим, чтобы ему приходилось обновлять страницу для просмотра обновлений статуса. Вместо этого мы хотим транслировать обновления в приложение по мере их создания. Итак, нам нужно пометить событие `OrderShipmentStatusUpdated` интерфейсом `ShouldBroadcast`. Это проинструктирует Laravel транслировать событие при его запуске:

    <?php

    namespace App\Events;

    use App\Order;
    use Illuminate\Broadcasting\Channel;
    use Illuminate\Broadcasting\InteractsWithSockets;
    use Illuminate\Broadcasting\PresenceChannel;
    use Illuminate\Broadcasting\PrivateChannel;
    use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
    use Illuminate\Queue\SerializesModels;

    class OrderShipmentStatusUpdated implements ShouldBroadcast
    {
        /**
         * The order instance.
         *
         * @var \App\Order
         */
        public $order;
    }

Интерфейс `ShouldBroadcast` требует, чтобы наше событие определяло метод `broadcastOn`. Этот метод отвечает за возврат каналов, по которым должно транслироваться событие. Пустая заглушка этого метода уже определена в сгенерированных классах событий, поэтому нам нужно только заполнить ее детали. Мы только хотим, чтобы создатель заказа мог просматривать обновления статуса, поэтому мы будем транслировать событие на частном канале, привязанном к заказу:

    /**
     * Get the channels the event should broadcast on.
     *
     * @return \Illuminate\Broadcasting\PrivateChannel
     */
    public function broadcastOn()
    {
        return new PrivateChannel('orders.'.$this->order->id);
    }

<a name="example-application-authorizing-channels"></a>
#### Авторизация каналов

Помните, что пользователи должны иметь разрешение на прослушивание частных каналов. Мы можем определить наши правила авторизации каналов в файле `routes/channels.php`. В этом примере нам нужно убедиться, что любой пользователь, пытающийся прослушивать частный канал `order.1`, на самом деле является создателем заказа:

    use App\Models\Order;

    Broadcast::channel('orders.{orderId}', function ($user, $orderId) {
        return $user->id === Order::findOrNew($orderId)->user_id;
    });

Метод `channel` принимает два аргумента: имя канала и обратный вызов, который возвращает `true` или `false`, указывающие, авторизован ли пользователь для прослушивания канала.

Все обратные вызовы авторизации получают текущего аутентифицированного пользователя в качестве своего первого аргумента и любые дополнительные параметры подстановки в качестве своих последующих аргументов. В этом примере мы используем заполнитель `{orderId}`, чтобы указать, что часть «ID» имени канала является подстановочным знаком.

<a name="listening-for-event-broadcasts"></a>
#### Прослушивание трансляций событий

Далее все, что остается, - это прослушивать событие в нашем приложении JavaScript. Мы можем сделать это с помощью Laravel Echo. Во-первых, мы будем использовать метод `private` для подписки на частный канал. Затем мы можем использовать метод `listen` для прослушивания события `OrderShipmentStatusUpdated`. По умолчанию все общедоступные свойства события будут включены в событие трансляции:

```js
Echo.private(`orders.${orderId}`)
    .listen('OrderShipmentStatusUpdated', (e) => {
        console.log(e.order);
    });
```

<a name="defining-broadcast-events"></a>
## Определение широковещательных событий

Чтобы сообщить Laravel, что данное событие должно транслироваться, вы должны реализовать интерфейс `Illuminate\Contracts\Broadcasting\ShouldBroadcast` в классе событий. Этот интерфейс уже импортирован во все классы событий, сгенерированные фреймворком, поэтому вы можете легко добавить его к любому из ваших событий.

Интерфейс `ShouldBroadcast` требует, чтобы вы реализовали единственный метод: `broadcastOn`. Метод `broadcastOn` должен возвращать канал или массив каналов, по которым должно транслироваться событие. Каналы должны быть экземплярами `Channel`, `PrivateChannel` или `PresenceChannel`. Экземпляры `Channel` представляют собой общедоступные каналы, на которые может подписаться любой пользователь, в то время как `PrivateChannels` и `PresenceChannels` представляют собой частные каналы, требующие [авторизации канала](#authorizing-channels):

    <?php

    namespace App\Events;

    use App\Models\User;
    use Illuminate\Broadcasting\Channel;
    use Illuminate\Broadcasting\InteractsWithSockets;
    use Illuminate\Broadcasting\PresenceChannel;
    use Illuminate\Broadcasting\PrivateChannel;
    use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
    use Illuminate\Queue\SerializesModels;

    class ServerCreated implements ShouldBroadcast
    {
        use SerializesModels;

        /**
         * The user that created the server.
         *
         * @var \App\Models\User
         */
        public $user;

        /**
         * Create a new event instance.
         *
         * @param  \App\Models\User  $user
         * @return void
         */
        public function __construct(User $user)
        {
            $this->user = $user;
        }

        /**
         * Get the channels the event should broadcast on.
         *
         * @return Channel|array
         */
        public function broadcastOn()
        {
            return new PrivateChannel('user.'.$this->user->id);
        }
    }

После реализации интерфейса `ShouldBroadcast` вам нужно только [запустить событие](/docs/{{version}}/events), как обычно. После запуска события [задание в очереди](/docs/{{version}}/queues) автоматически транслирует событие, используя указанный вами драйвер вещания.

<a name="broadcast-name"></a>
### Название трансляции

По умолчанию Laravel будет транслировать событие, используя имя класса события. Однако вы можете настроить имя трансляции, определив для события метод `broadcastAs`:

    /**
     * The event's broadcast name.
     *
     * @return string
     */
    public function broadcastAs()
    {
        return 'server.created';
    }

Если вы настраиваете имя трансляции с помощью метода `broadcastAs`, вы должны убедиться, что зарегистрировали ваш слушатель с ведущим символом `.`. Это проинструктирует Echo не добавлять пространство имен приложения к событию:

    .listen('.server.created', function (e) {
        ....
    });

<a name="broadcast-data"></a>
### Данные трансляции

Когда событие транслируется, все его общедоступные свойства `public` автоматически сериализуются и транслируются как полезная нагрузка события, что позволяет вам получить доступ к любым его общедоступным данным из вашего приложения JavaScript. Так, например, если ваше событие имеет единственное общедоступное свойство `$user`, которое содержит модель Eloquent, полезная нагрузка широковещательной передачи события будет:

    {
        "user": {
            "id": 1,
            "name": "Patrick Stewart"
            ...
        }
    }

Однако, если вы хотите иметь более детальный контроль над полезной нагрузкой широковещательной передачи, вы можете добавить к своему событию метод `broadcastWith`. Этот метод должен возвращать массив данных, которые вы хотите транслировать в качестве полезной нагрузки события:

    /**
     * Get the data to broadcast.
     *
     * @return array
     */
    public function broadcastWith()
    {
        return ['id' => $this->user->id];
    }

<a name="broadcast-queue"></a>
### Очередь трансляции

По умолчанию каждое широковещательное событие помещается в очередь по умолчанию для соединения очереди по умолчанию, указанного в вашем файле конфигурации `queue.php`. Вы можете настроить соединение очереди и имя, используемое вещательной компанией, определив свойства `connection` и `queue` в своем классе событий:

    /**
     * The name of the queue connection to use when broadcasting the event.
     *
     * @var string
     */
    public $connection = 'redis';

    /**
     * The name of the queue on which to place the broadcasting job.
     *
     * @var string
     */
    public $queue = 'default';

Если вы хотите транслировать свое событие с использованием очереди `sync` вместо драйвера очереди по умолчанию, вы можете реализовать интерфейс `ShouldBroadcastNow` вместо `ShouldBroadcast`:

    <?php

    use Illuminate\Contracts\Broadcasting\ShouldBroadcastNow;

    class OrderShipmentStatusUpdated implements ShouldBroadcastNow
    {
        //
    }

<a name="broadcast-conditions"></a>
### Условия трансляции

Иногда вы хотите транслировать свое мероприятие, только если выполняется определенное условие. Вы можете определить эти условия, добавив метод `broadcastWhen` к вашему классу событий:

    /**
     * Determine if this event should broadcast.
     *
     * @return bool
     */
    public function broadcastWhen()
    {
        return $this->order->value > 100;
    }

<a name="broadcasting-and-database-transactions"></a>
#### Трансляция и транзакции базы данных

Когда широковещательные события отправляются в транзакциях базы данных, они могут быть обработаны очередью до того, как транзакция базы данных будет зафиксирована. Когда это происходит, любые обновления, внесенные вами в модели или записи базы данных во время транзакции базы данных, могут еще не быть отражены в базе данных. Кроме того, любые модели или записи базы данных, созданные в рамках транзакции, могут не существовать в базе данных. Если ваше событие зависит от этих моделей, при обработке задания, транслирующего событие, могут возникнуть непредвиденные ошибки.

Если для параметра конфигурации вашего соединения с очередью `after_commit` установлено значение `false`, вы все равно можете указать, что конкретное широковещательное событие должно быть отправлено после того, как все открытые транзакции базы данных были зафиксированы, путем определения свойства `$afterCommit` в классе событий:

    <?php

    namespace App\Events;

    use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
    use Illuminate\Queue\SerializesModels;

    class ServerCreated implements ShouldBroadcast
    {
        use SerializesModels;

        public $afterCommit = true;
    }

> {tip} Чтобы узнать больше о том, как обойти эти проблемы, ознакомьтесь с документацией, касающейся [заданий в очереди и транзакций базы данных](/docs/{{version}}/queues#jobs-and-database-transactions).

<a name="authorizing-channels"></a>
## Каналы авторизации

Частные каналы требуют, чтобы вы авторизовали, чтобы текущий аутентифицированный пользователь действительно мог прослушивать канал. Это достигается путем отправки HTTP-запроса вашему приложению Laravel с именем канала и разрешения вашему приложению определять, может ли пользователь прослушивать этот канал. При использовании [Laravel Echo](#client-side-installation) HTTP-запрос на авторизацию подписок на частные каналы будет выполнен автоматически; однако вам необходимо определить правильные маршруты для ответа на эти запросы.

<a name="defining-authorization-routes"></a>
### Определение маршрутов авторизации

К счастью, Laravel упрощает определение маршрутов для ответа на запросы авторизации канала. В приложении `App\Providers\BroadcastServiceProvider`, включенном в ваше приложение Laravel, вы увидите вызов метода `Broadcast::routes`. Этот метод зарегистрирует маршрут `/broadcasting/auth` для обработки запросов авторизации:

    Broadcast::routes();

Метод `Broadcast::routes` автоматически поместит свои маршруты в группу мидлвара `web`; однако вы можете передать в метод массив атрибутов маршрута, если хотите настроить назначенные атрибуты:

    Broadcast::routes($attributes);

<a name="customizing-the-authorization-endpoint"></a>
#### Настройка конечной точки авторизации

По умолчанию Echo будет использовать конечную точку `/broadcasting/auth` для авторизации доступа к каналу. Однако вы можете указать свою собственную конечную точку авторизации, передав параметр конфигурации `authEndpoint` вашему экземпляру Echo:

    window.Echo = new Echo({
        broadcaster: 'pusher',
        // ...
        authEndpoint: '/custom/endpoint/auth'
    });

<a name="defining-authorization-callbacks"></a>
### Определение обратных вызовов авторизации

Затем нам нужно определить логику, которая будет определять, может ли текущий аутентифицированный пользователь прослушивать данный канал. Это делается в файле `routes/channels.php`, который включен в ваше приложение. В этом файле вы можете использовать метод `Broadcast::channel` для регистрации обратных вызовов авторизации канала:

    Broadcast::channel('orders.{orderId}', function ($user, $orderId) {
        return $user->id === Order::findOrNew($orderId)->user_id;
    });

Метод `channel` принимает два аргумента: имя канала и обратный вызов, который возвращает `true` или `false`, указывающие, авторизован ли пользователь для прослушивания канала.

Все обратные вызовы авторизации получают текущего аутентифицированного пользователя в качестве своего первого аргумента и любые дополнительные параметры подстановки в качестве своих последующих аргументов. В этом примере мы используем заполнитель `{orderId}`, чтобы указать, что часть «ID» имени канала является подстановочным знаком.

<a name="authorization-callback-model-binding"></a>
#### Привязка модели обратного вызова авторизации

Как и HTTP-маршруты, для маршрутов каналов также могут использоваться преимущества неявного и явного [привязки модели маршрута](/docs/{{version}}/routing#route-model-binding). Например, вместо получения строкового или числового идентификатора заказа вы можете запросить фактический экземпляр модели `Order`:

    use App\Models\Order;

    Broadcast::channel('orders.{order}', function ($user, Order $order) {
        return $user->id === $order->user_id;
    });

> {note} В отличие от привязки модели маршрута HTTP, привязка модели канала не поддерживает автоматическое [неявное определение области привязки модели](/docs/{{version}}/routing#implicit-model-binding-scoping). Однако это редко является проблемой, поскольку область действия большинства каналов может быть определена на основе уникального первичного ключа одной модели.

<a name="authorization-callback-authentication"></a>
#### Авторизация Обратный вызов Аутентификации

Частные каналы и широковещательные каналы присутствия аутентифицируют текущего пользователя через стандартную защиту аутентификации вашего приложения. Если пользователь не аутентифицирован, авторизация канала автоматически отклоняется, и обратный вызов авторизации никогда не выполняется. Однако вы можете назначить несколько настраиваемых защитников, которые должны при необходимости аутентифицировать входящий запрос:

    Broadcast::channel('channel', function () {
        // ...
    }, ['guards' => ['web', 'admin']]);

<a name="defining-channel-classes"></a>
### Определение классов каналов

Если ваше приложение использует много разных каналов, ваш файл `routes/channels.php` может стать громоздким. Таким образом, вместо использования замыканий для авторизации каналов вы можете использовать классы каналов. Чтобы сгенерировать класс канала, используйте Artisan-команду `make:channel`. Эта команда поместит новый класс канала в каталог `App/Broadcasting`.

    php artisan make:channel OrderChannel

Затем зарегистрируйте свой канал в файле `routes/channels.php`:

    use App\Broadcasting\OrderChannel;

    Broadcast::channel('orders.{order}', OrderChannel::class);

Наконец, вы можете поместить логику авторизации для вашего канала в метод' `join` класса канала. Этот метод `join` будет содержать ту же логику, которую вы обычно использовали бы при закрытии авторизации вашего канала. Вы также можете воспользоваться преимуществами привязки модели канала:

    <?php

    namespace App\Broadcasting;

    use App\Models\Order;
    use App\Models\User;

    class OrderChannel
    {
        /**
         * Create a new channel instance.
         *
         * @return void
         */
        public function __construct()
        {
            //
        }

        /**
         * Authenticate the user's access to the channel.
         *
         * @param  \App\Models\User  $user
         * @param  \App\Models\Order  $order
         * @return array|bool
         */
        public function join(User $user, Order $order)
        {
            return $user->id === $order->user_id;
        }
    }

> {tip} Как и многие другие классы в Laravel, классы каналов будут автоматически разрешены [сервисным контейнером](/docs/{{version}}/container). Итак, вы можете указать любые зависимости, требуемые для вашего канала, в его конструкторе.

<a name="broadcasting-events"></a>
## Трансляция событий

После того, как вы определили событие и отметили его интерфейсом `ShouldBroadcast`, вам нужно только запустить событие, используя метод отправки события. Диспетчер событий заметит, что событие помечено интерфейсом `ShouldBroadcast`, и поставит событие в очередь для трансляции:

    use App\Events\OrderShipmentStatusUpdated;

    OrderShipmentStatusUpdated::dispatch($order);

<a name="only-to-others"></a>
### Только другим

При создании приложения, использующего широковещательную рассылку событий, иногда может потребоваться широковещательная передача события всем подписчикам данного канала, кроме текущего пользователя. Вы можете сделать это с помощью помощника `broadcast` и метода `toOthers`:

    use App\Events\OrderShipmentStatusUpdated;

    broadcast(new OrderShipmentStatusUpdated($update))->toOthers();

Чтобы лучше понять, когда вы можете захотеть использовать метод `toOthers`, давайте представим приложение со списком задач, в котором пользователь может создать новую задачу, введя имя задачи. Чтобы создать задачу, ваше приложение может сделать запрос к URL-адресу `/task`, который транслирует создание задачи и возвращает JSON-представление новой задачи. Когда ваше приложение JavaScript получает ответ от конечной точки, оно может напрямую вставить новую задачу в свой список задач следующим образом:

    axios.post('/task', task)
        .then((response) => {
            this.tasks.push(response.data);
        });

Однако помните, что мы также транслируем создание задачи. Если ваше приложение JavaScript также прослушивает это событие, чтобы добавить задачи в список задач, у вас будут дублирующиеся задачи в вашем списке: одна из конечной точки и одна из трансляции. Вы можете решить эту проблему, используя метод `toOthers`, чтобы указать вещательной компании не транслировать событие текущему пользователю.

> {note} Ваше событие должно использовать трейт `Illuminate\Broadcasting\InteractsWithSockets` для вызова метода `toOthers`.

<a name="only-to-others-configuration"></a>
#### Конфигурация

Когда вы инициализируете экземпляр Laravel Echo, соединению назначается идентификатор сокета. Если вы используете глобальный экземпляр [Axios](https://github.com/mzabriskie/axios) для выполнения HTTP-запросов из вашего приложения JavaScript, идентификатор сокета будет автоматически прикрепляться к каждому исходящему запросу как `X-Socket-ID`. Затем, когда вы вызываете метод `toOthers`, Laravel извлекает идентификатор сокета из заголовка и инструктирует вещателя не транслировать никакие соединения с этим идентификатором сокета.

Если вы не используете глобальный экземпляр Axios, вам необходимо вручную настроить приложение JavaScript для отправки заголовка `X-Socket-ID` со всеми исходящими запросами. Вы можете получить идентификатор сокета с помощью метода `Echo.socketId`:

    var socketId = Echo.socketId();

<a name="receiving-broadcasts"></a>
## Прием трансляций

<a name="listening-for-events"></a>
### Прослушивание событий

После того, как вы [установили и создали экземпляр Laravel Echo](#client-side-installation), вы готовы начать прослушивание событий, которые транслируются из вашего приложения Laravel. Сначала используйте метод `channel` для получения экземпляра канала, затем вызовите метод `listen` для прослушивания указанного события:

```js
Echo.channel(`orders.${this.order.id}`)
    .listen('OrderShipmentStatusUpdated', (e) => {
        console.log(e.order.name);
    });
```

Если вы хотите прослушивать события на частном канале, используйте вместо этого метод `private`. Вы можете продолжить цепочку вызовов метода `listen` для прослушивания нескольких событий на одном канале:

```js
Echo.private(`orders.${this.order.id}`)
    .listen(...)
    .listen(...)
    .listen(...);
```

<a name="leaving-a-channel"></a>
### Оставление канала

Чтобы покинуть канал, вы можете вызвать метод `leaveChannel` в вашем экземпляре Echo:

```js
Echo.leaveChannel(`orders.${this.order.id}`);
```

Если вы хотите покинуть канал, а также связанные с ним частные каналы и каналы присутствия, вы можете вызвать метод `leave`:

```js
Echo.leave(`orders.${this.order.id}`);
```
<a name="namespaces"></a>
### Пространства имён

Вы могли заметить в приведенных выше примерах, что мы не указали полное пространство имен `App\Events` для классов событий. Это связано с тем, что Echo автоматически предполагает, что события находятся в пространстве имен `App\Events`. Однако вы можете настроить корневое пространство имен при создании экземпляра Echo, передав параметр конфигурации `namespace`:

```js
window.Echo = new Echo({
    broadcaster: 'pusher',
    // ...
    namespace: 'App.Other.Namespace'
});
```

В качестве альтернативы вы можете добавить к классам событий префикс `.` при подписке на них с помощью Echo. Это позволит вам всегда указывать полное имя класса:

```js
Echo.channel('orders')
    .listen('.Namespace\\Event\\Class', (e) => {
        //
    });
```

<a name="presence-channels"></a>
## Каналы присутствия

Каналы присутствия основаны на безопасности частных каналов, в то же время раскрывая дополнительную функцию осведомленности о том, кто подписан на канал. Это упрощает создание мощных функций приложений для совместной работы, таких как уведомление пользователей, когда другой пользователь просматривает ту же страницу, или перечисление жителей комнаты чата.

<a name="authorizing-presence-channels"></a>
### Авторизация каналов присутствия

Все каналы присутствия также являются частными; следовательно, пользователи должны быть [авторизованы для доступа к ним](#authorizing-channels). Однако при определении обратных вызовов авторизации для каналов присутствия вы не вернете `true`, если пользователь авторизован для присоединения к каналу. Вместо этого вы должны вернуть массив данных о пользователе.

Данные, возвращаемые обратным вызовом авторизации, будут доступны для прослушивателей событий канала присутствия в вашем приложении JavaScript. Если пользователь не авторизован для присоединения к каналу присутствия, вы должны вернуть `false` или `null`:

    Broadcast::channel('chat.{roomId}', function ($user, $roomId) {
        if ($user->canJoinRoom($roomId)) {
            return ['id' => $user->id, 'name' => $user->name];
        }
    });

<a name="joining-presence-channels"></a>
### Присоединение к каналам присутствия

Чтобы присоединиться к каналу присутствия, вы можете использовать метод Echo `join`. Метод `join` вернет реализацию `PresenceChannel`, которая, наряду с предоставлением метода `listen`, позволяет вам подписаться на события `here`, `joining` и `leaving`.

    Echo.join(`chat.${roomId}`)
        .here((users) => {
            //
        })
        .joining((user) => {
            console.log(user.name);
        })
        .leaving((user) => {
            console.log(user.name);
        })
        .error((error) => {
            console.error(error);
        });

Обратный вызов `here` будет выполнен сразу же после успешного присоединения канала и получит массив, содержащий информацию о пользователях для всех других пользователей, которые в настоящее время подписаны на канал. Метод `joining` будет выполняться, когда новый пользователь присоединяется к каналу, а метод `leaving` будет выполняться, когда пользователь покинет канал. Метод `error` будет выполнен, когда конечная точка аутентификации вернет код состояния HTTP, отличный от 200, или если возникнет проблема с синтаксическим анализом возвращенного JSON.

<a name="broadcasting-to-presence-channels"></a>
### Вещание на каналы присутствия

Каналы присутствия могут получать события так же, как общедоступные или частные каналы. Используя пример чата, мы можем захотеть транслировать события `NewMessage` на канал присутствия комнаты. Для этого мы вернем экземпляр `PresenceChannel` из метода `broadcastOn`:

    /**
     * Get the channels the event should broadcast on.
     *
     * @return Channel|array
     */
    public function broadcastOn()
    {
        return new PresenceChannel('room.'.$this->message->room_id);
    }

Как и в случае с другими событиями, вы можете использовать помощник `broadcast` и метод `toOthers`, чтобы исключить текущего пользователя из приема трансляции:

    broadcast(new NewMessage($message));

    broadcast(new NewMessage($message))->toOthers();

Как типично для других типов событий, вы можете прослушивать события, отправленные в каналы присутствия, используя метод Echo `listen`:

    Echo.join(`chat.${roomId}`)
        .here(...)
        .joining(...)
        .leaving(...)
        .listen('NewMessage', (e) => {
            //
        });

<a name="client-events"></a>
## Клиентские события

> {tip} При использовании [Pusher Channels](https://pusher.com/channels), необходимо включить опцию «Клиентские события» в разделе «Настройки приложения» на [панели управления приложения](https://dashboard.pusher.com/) для отправки клиентских событий.

Иногда вы можете захотеть транслировать событие другим подключенным клиентам, вообще не затрагивая ваше приложение Laravel. Это может быть особенно полезно для таких вещей, как «ввод» уведомлений, когда вы хотите предупредить пользователей вашего приложения о том, что другой пользователь печатает сообщение на заданном экране.

Чтобы транслировать клиентские события, вы можете использовать метод Echo `whisper`:

    Echo.private(`chat.${roomId}`)
        .whisper('typing', {
            name: this.user.name
        });

Чтобы прослушивать клиентские события, вы можете использовать метод `listenForWhisper`:

    Echo.private(`chat.${roomId}`)
        .listenForWhisper('typing', (e) => {
            console.log(e.name);
        });

<a name="notifications"></a>
## Уведомления

Если связать трансляцию событий с [уведомлениями](/docs/{{version}}/notifications), ваше приложение JavaScript может получать новые уведомления по мере их возникновения без необходимости обновлять страницу. Перед началом работы обязательно прочтите документацию по использованию [канала широковещательных уведомлений](/docs/{{version}}/notifications#broadcast-notifications).

После того, как вы настроили уведомление для использования широковещательного канала, вы можете прослушивать широковещательные события, используя метод Echo `notification`. Помните, что имя канала должно соответствовать имени класса объекта, получающего уведомления:

    Echo.private(`App.Models.User.${userId}`)
        .notification((notification) => {
            console.log(notification.type);
        });

В этом примере все уведомления, отправленные экземплярам `App\Models\User` через канал `broadcast`, будут получены обратным вызовом. Обратный вызов авторизации канала для канала `App.Models.User.{id}` включен в стандартный `BroadcastServiceProvider`, который поставляется с фреймворком Laravel.
