# Файловое хранилище

- [Введение](#introduction)
- [Конфигурация](#configuration)
    - [Локальный драйвер](#the-local-driver)
    - [Публичный диск](#the-public-disk)
    - [Требования к драйверам](#driver-prerequisites)
    - [Файловые системы, совместимые с Amazon S3](#amazon-s3-compatible-filesystems)
    - [Кеширование](#caching)
- [Получение экземпляров дисков](#obtaining-disk-instances)
- [Получение файлов](#retrieving-files)
    - [Скачивание файлов](#downloading-files)
    - [URL-адреса файлов](#file-urls)
    - [Метаданные файла](#file-metadata)
- [Сохранение файлов](#storing-files)
    - [Загрузка файлов](#file-uploads)
    - [Видимость файла](#file-visibility)
- [Удаление файлов](#deleting-files)
- [Директории](#directories)
- [Пользовательские файловые системы](#custom-filesystems)

<a name="introduction"></a>
## Введение

Laravel предоставляет мощную абстракцию файловой системы благодаря замечательному PHP-пакету [Flysystem](https://github.com/thephpleague/flysystem) от Фрэнка де Йонге. Интеграция Laravel Flysystem предоставляет простые драйверы для работы с локальными файловыми системами, SFTP и Amazon S3. Более того, удивительно просто переключаться между этими вариантами хранения между локальной машиной разработки и производственным сервером, поскольку API остается одинаковым для каждой системы.

<a name="configuration"></a>
## Конфигурация

Файл конфигурации файловой системы Laravel находится в `config/filesystems.php`. В этом файле вы можете настроить все «диски» файловой системы. Каждый диск представляет собой определенный драйвер хранилища и место хранения. Примеры конфигураций для каждого поддерживаемого драйвера включены в файл конфигурации, поэтому вы можете изменить конфигурацию, чтобы отразить ваши предпочтения в хранилище и учетные данные.

Драйвер `local` взаимодействует с файлами, хранящимися локально на сервере, на котором запущено приложение Laravel, в то время как драйвер `s3` используется для записи в службу облачного хранилища Amazon S3.

> {tip} Вы можете настроить столько дисков, сколько захотите, и даже можете иметь несколько дисков, использующих один и тот же драйвер.

<a name="the-local-driver"></a>
### Локальный драйвер

При использовании драйвера `local` все операции с файлами выполняются относительно корневого каталога `root`, определенного в файле конфигурации файловой системы `filesystems`. По умолчанию это значение устанавливается в каталог `storage/app`. Следовательно, следующий метод будет записывать в `storage/app/example.txt`:

    use Illuminate\Support\Facades\Storage;

    Storage::disk('local')->put('example.txt', 'Contents');

<a name="the-public-disk"></a>
### Публичный диск

Диск `public`, включенный в файл конфигурации `filesystems`вашего приложения, предназначен для файлов, которые будут общедоступными. По умолчанию публичный диск `public` использует драйвер `local` и хранит свои файлы в `storage/app/public`.

Чтобы сделать эти файлы доступными из Интернета, вы должны создать символическую ссылку с `public/storage` на `storage/app/public`. Использование этого соглашения о папках позволит хранить ваши общедоступные файлы в одном каталоге, который можно легко использовать в разных развертываниях при использовании систем развертывания с нулевым временем простоя, таких как [Envoyer](https://envoyer.io).

Чтобы создать символическую ссылку, вы можете использовать Artisan-команду `storage:link`:

    php artisan storage:link

После того, как файл был сохранен и была создана символическая ссылка, вы можете создать URL-адрес файлов, используя помощник `asset`:

    echo asset('storage/file.txt');

Вы можете настроить дополнительные символические ссылки в файле конфигурации файловой системы `filesystems`. Каждая из настроенных ссылок будет создана, когда вы запустите команду `storage:link`:

    'links' => [
        public_path('storage') => storage_path('app/public'),
        public_path('images') => storage_path('app/images'),
    ],

<a name="driver-prerequisites"></a>
### Требования к драйверам

<a name="composer-packages"></a>
#### Пакеты композера

Перед использованием драйверов S3 или SFTP вам необходимо установить соответствующий пакет через диспетчер пакетов Composer:

- Amazon S3: `composer require league/flysystem-aws-s3-v3 "~1.0"`
- SFTP: `composer require league/flysystem-sftp "~1.0"`

Кроме того, вы можете установить кэшированный адаптер для повышения производительности:

- CachedAdapter: `composer require league/flysystem-cached-adapter "~1.0"`

<a name="s3-driver-configuration"></a>
#### Конфигурация драйвера S3

Информация о конфигурации драйвера S3 находится в вашем конфигурационном файле `config/filesystems.php`. Этот файл содержит пример массива конфигурации для драйвера S3. Вы можете изменить этот массив своей собственной конфигурацией S3 и учетными данными. Для удобства эти переменные среды соответствуют соглашению об именах, используемому в интерфейсе командной строки AWS.

<a name="ftp-driver-configuration"></a>
#### Конфигурация драйвера FTP

Интеграция Laravel Flysystem отлично работает с FTP; однако образец конфигурации не включен в стандартный файл конфигурации фреймворка `filesystems.php`. Если вам нужно настроить файловую систему FTP, вы можете использовать приведенный ниже пример конфигурации:

    'ftp' => [
        'driver' => 'ftp',
        'host' => 'ftp.example.com',
        'username' => 'your-username',
        'password' => 'your-password',

        // Optional FTP Settings...
        // 'port' => 21,
        // 'root' => '',
        // 'passive' => true,
        // 'ssl' => true,
        // 'timeout' => 30,
    ],

<a name="sftp-driver-configuration"></a>
#### Конфигурация драйвера SFTP

Интеграция Laravel Flysystem отлично работает с SFTP; однако образец конфигурации не включен в стандартный файл конфигурации фреймворка `filesystems.php`. Если вам нужно настроить файловую систему SFTP, вы можете использовать пример конфигурации ниже:

    'sftp' => [
        'driver' => 'sftp',
        'host' => 'example.com',
        'username' => 'your-username',
        'password' => 'your-password',

        // Settings for SSH key based authentication...
        'privateKey' => '/path/to/privateKey',
        'password' => 'encryption-password',

        // Optional SFTP Settings...
        // 'port' => 22,
        // 'root' => '',
        // 'timeout' => 30,
    ],

<a name="amazon-s3-compatible-filesystems"></a>
### Файловые системы, совместимые с Amazon S3

По умолчанию файл конфигурации вашего приложения `filesystems` содержит конфигурацию диска для диска `s3`. Помимо использования этого диска для взаимодействия с Amazon S3, вы можете использовать его для взаимодействия с любой совместимой с S3 службой хранения файлов, такой как [MinIO](https://github.com/minio/minio) или [DigitalOcean Spaces](https://www.digitalocean.com/products/spaces/).

Обычно после обновления учетных данных диска в соответствии с учетными данными службы, которую вы планируете использовать, вам нужно только обновить значение параметра конфигурации `url`. Значение этой опции обычно определяется через переменную окружения `AWS_ENDPOINT`:

    'endpoint' => env('AWS_ENDPOINT', 'https://minio:9000'),

<a name="caching"></a>
### Кеширование

Чтобы включить кэширование для данного диска, вы можете добавить директиву `cache` в параметры конфигурации диска. Опция `cache` должна быть массивом опций кеширования, содержащим имя `disk`, время `expire` в секундах и `prefix` кеша:

    's3' => [
        'driver' => 's3',

        // Other Disk Options...

        'cache' => [
            'store' => 'memcached',
            'expire' => 600,
            'prefix' => 'cache-prefix',
        ],
    ],

<a name="obtaining-disk-instances"></a>
## Получение экземпляров дисков

Фасад `Storage` может использоваться для взаимодействия с любым из ваших сконфигурированных дисков. Например, вы можете использовать метод `put` на фасаде для сохранения аватара на диске по умолчанию. Если вы вызываете методы фасада `Storage` без предварительного вызова метода `disk`, метод будет автоматически передан на диск по умолчанию:

    use Illuminate\Support\Facades\Storage;

    Storage::put('avatars/1', $content);

Если ваше приложение взаимодействует с несколькими дисками, вы можете использовать метод `disk` на фасаде `Storage` для работы с файлами на определенном диске:

    Storage::disk('s3')->put('avatars/1', $content);

<a name="retrieving-files"></a>
## Получение файлов

Метод `get` может использоваться для получения содержимого файла. Необработанное строковое содержимое файла будет возвращено методом. Помните, что все пути к файлам должны быть указаны относительно «корневого» расположения диска:

    $contents = Storage::get('file.jpg');

Метод `exists` может использоваться для определения, существует ли файл на диске:

    if (Storage::disk('s3')->exists('file.jpg')) {
        // ...
    }

Метод `missing` может использоваться для определения отсутствия файла на диске:

    if (Storage::disk('s3')->missing('file.jpg')) {
        // ...
    }

<a name="downloading-files"></a>
### Скачивание файлов

Метод `download` может использоваться для генерации ответа, который заставляет браузер пользователя загружать файл по заданному пути. Метод `download` принимает имя файла в качестве второго аргумента метода, который определяет имя файла, которое видит пользователь, загружающий файл. Наконец, вы можете передать массив заголовков HTTP в качестве третьего аргумента метода:

    return Storage::download('file.jpg');

    return Storage::download('file.jpg', $name, $headers);

<a name="file-urls"></a>
### URL-адреса файлов

Вы можете использовать метод `url`, чтобы получить URL для данного файла. Если вы используете драйвер `local`, он обычно просто добавляет `/storage` к заданному пути и возвращает относительный URL-адрес файла. Если вы используете драйвер `s3`, будет возвращен полный удаленный URL:

    use Illuminate\Support\Facades\Storage;

    $url = Storage::url('file.jpg');

При использовании драйвера `local`, все файлы, которые должны быть общедоступными, должны быть помещены в каталог `storage/app/public`. Кроме того, вы должны [создать символическую ссылку](#the-public-disk) на `public/storage`, которая указывает на каталог `storage/app/public`.

> {note} При использовании драйвера `local`, возвращаемое значение `url` не кодируется URL-адресом. По этой причине мы рекомендуем всегда хранить ваши файлы, используя имена, которые будут создавать действительные URL-адреса.

<a name="temporary-urls"></a>
#### Временные URL

Используя метод `temporaryUrl`, вы можете создавать временные URL-адреса для файлов, хранящихся с помощью драйвера `s3`. Этот метод принимает путь и экземпляр `DateTime`, указывающий, когда должен истечь URL:

    use Illuminate\Support\Facades\Storage;

    $url = Storage::temporaryUrl(
        'file.jpg', now()->addMinutes(5)
    );

Если вам нужно указать дополнительные [параметры запроса S3](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html#RESTObjectGET-requests), вы можете передать массив параметров запроса в качестве третьего аргумент методу `temporaryUrl`:

    $url = Storage::temporaryUrl(
        'file.jpg',
        now()->addMinutes(5),
        [
            'ResponseContentType' => 'application/octet-stream',
            'ResponseContentDisposition' => 'attachment; filename=file2.jpg',
        ]
    );

<a name="url-host-customization"></a>
#### Настройка хоста URL

Если вы хотите заранее определить хост для URL-адресов, сгенерированных с помощью фасада `Storage`, вы можете добавить параметр `url` в массив конфигурации диска:

    'public' => [
        'driver' => 'local',
        'root' => storage_path('app/public'),
        'url' => env('APP_URL').'/storage',
        'visibility' => 'public',
    ],

<a name="file-metadata"></a>
### Метаданные файла

Помимо чтения и записи файлов, Laravel также может предоставлять информацию о самих файлах. Например, метод `size` может использоваться для получения размера файла в байтах:

    use Illuminate\Support\Facades\Storage;

    $size = Storage::size('file.jpg');

Метод `lastModified` возвращает временную метку UNIX последнего изменения файла:

    $time = Storage::lastModified('file.jpg');

<a name="file-paths"></a>
#### Пути к файлам

Вы можете использовать метод `path`, чтобы получить путь к заданному файлу. Если вы используете драйвер `local`, он вернет абсолютный путь к файлу. Если вы используете драйвер `s3`, этот метод вернет относительный путь к файлу в корзине S3:

    use Illuminate\Support\Facades\Storage;

    $path = Storage::path('file.jpg');

<a name="storing-files"></a>
## Сохранение файлов

Метод `put` может использоваться для хранения содержимого файла на диске. Вы также можете передать PHP-ресурс `resource` методу `put`, который будет использовать базовую поддержку потока Flysystem. Помните, что все пути к файлам должны быть указаны относительно «корневого» расположения, настроенного для диска:

    use Illuminate\Support\Facades\Storage;

    Storage::put('file.jpg', $contents);

    Storage::put('file.jpg', $resource);

<a name="automatic-streaming"></a>
#### Автоматический стриминг

Потоковая передача файлов в хранилище позволяет значительно сократить использование памяти. Если вы хотите, чтобы Laravel автоматически управлял потоковой передачей данного файла в ваше хранилище, вы можете использовать метод `putFile` или `putFileAs`. Этот метод принимает экземпляр `Illuminate\Http\File` или `Illuminate\Http\UploadedFile` и автоматически передает файл в нужное место:

    use Illuminate\Http\File;
    use Illuminate\Support\Facades\Storage;

    // Automatically generate a unique ID for filename...
    $path = Storage::putFile('photos', new File('/path/to/photo'));

    // Manually specify a filename...
    $path = Storage::putFileAs('photos', new File('/path/to/photo'), 'photo.jpg');

Следует отметить несколько важных моментов, связанных с методом `putFile`. Обратите внимание, что мы указали только имя каталога, а не имя файла. По умолчанию метод `putFile` генерирует уникальный идентификатор, который будет служить именем файла. Расширение файла будет определено путем проверки MIME-типа файла. Путь к файлу будет возвращен методом `putFile`, так что вы можете сохранить путь, включая сгенерированное имя файла, в вашей базе данных.

Методы `putFile` и `putFileAs` также принимают аргумент для определения «видимости» сохраненного файла. Это особенно полезно, если вы храните файл на облачном диске, таком как Amazon S3, и хотите, чтобы файл был общедоступным через сгенерированные URL-адреса:

    Storage::putFile('photos', new File('/path/to/photo'), 'public');

<a name="prepending-appending-to-files"></a>
#### Подготовка и добавление к файлам

Методы `prepend` и `append` позволяют вам писать в начало или конец файла:

    Storage::prepend('file.log', 'Prepended Text');

    Storage::append('file.log', 'Appended Text');

<a name="copying-moving-files"></a>
#### Копирование и перемещение файлов

Метод `copy` может использоваться для копирования существующего файла в новое место на диске, а метод `move` может использоваться для переименования или перемещения существующего файла в новое место:

    Storage::copy('old/file.jpg', 'new/file.jpg');

    Storage::move('old/file.jpg', 'new/file.jpg');

<a name="file-uploads"></a>
### Загрузка файлов

В веб-приложениях одним из наиболее распространенных вариантов использования файлов является хранение загруженных пользователем файлов, таких как фотографии и документы. Laravel упрощает хранение загруженных файлов с помощью метода `store` для экземпляра загруженного файла. Вызовите метод `store`, указав путь, по которому вы хотите сохранить загруженный файл:

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\Request;

    class UserAvatarController extends Controller
    {
        /**
         * Update the avatar for the user.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function update(Request $request)
        {
            $path = $request->file('avatar')->store('avatars');

            return $path;
        }
    }

В этом примере следует отметить несколько важных моментов. Обратите внимание, что мы указали только имя каталога, а не имя файла. По умолчанию метод `store` генерирует уникальный идентификатор, который будет служить именем файла. Расширение файла будет определено путем проверки MIME-типа файла. Путь к файлу будет возвращен методом `store`, поэтому вы можете сохранить путь, включая сгенерированное имя файла, в своей базе данных.

Вы также можете вызвать метод `putFile` на фасаде `Storage`, чтобы выполнить ту же операцию хранения файлов, что и в примере выше:

    $path = Storage::putFile('avatars', $request->file('avatar'));

<a name="specifying-a-file-name"></a>
#### Указание имени файла

Если вы не хотите, чтобы имя файла автоматически присваивалось вашему сохраненному файлу, вы можете использовать метод `storeAs`, который получает путь, имя файла и (необязательный) диск в качестве аргументов:

    $path = $request->file('avatar')->storeAs(
        'avatars', $request->user()->id
    );

Вы также можете использовать метод `putFileAs` на фасаде `Storage`, который будет выполнять ту же операцию хранения файлов, что и в примере выше:

    $path = Storage::putFileAs(
        'avatars', $request->file('avatar'), $request->user()->id
    );

> {note} Непечатаемые и недопустимые символы Unicode будут автоматически удалены из путей к файлам. Поэтому вы можете очистить пути к файлам перед их передачей в методы хранения файлов Laravel. Пути к файлам нормализуются с использованием метода `League\Flysystem\Util::normalizePath`.

<a name="specifying-a-disk"></a>
#### Указание диска

По умолчанию метод `store` этого загруженного файла будет использовать ваш диск по умолчанию. Если вы хотите указать другой диск, передайте имя диска в качестве второго аргумента методу `store`:

    $path = $request->file('avatar')->store(
        'avatars/'.$request->user()->id, 's3'
    );

Если вы используете метод `storeAs`, вы можете передать имя диска в качестве третьего аргумента метода:

    $path = $request->file('avatar')->storeAs(
        'avatars',
        $request->user()->id,
        's3'
    );

<a name="other-uploaded-file-information"></a>
#### Другая информация о загруженном файле

Если вы хотите получить исходное имя загруженного файла, вы можете сделать это с помощью метода `getClientOriginalName`:

    $name = $request->file('avatar')->getClientOriginalName();

Метод `extension` может использоваться для получения расширения загруженного файла:

    $extension = $request->file('avatar')->extension();

<a name="file-visibility"></a>
### Видимость файла

В интеграции Laravel Flysystem «видимость» - это абстракция прав доступа к файлам на нескольких платформах. Файлы могут быть объявлены `public` или `private`. Когда файл объявляется `public`, вы указываете, что файл обычно должен быть доступен для других. Например, при использовании драйвера S3 вы можете получить URL-адреса для `public` файлов.

Вы можете установить видимость при записи файла с помощью метода `put`:

    use Illuminate\Support\Facades\Storage;

    Storage::put('file.jpg', $contents, 'public');

Если файл уже был сохранен, его видимость можно получить и установить с помощью методов `getVisibility` и `setVisibility`:

    $visibility = Storage::getVisibility('file.jpg');

    Storage::setVisibility('file.jpg', 'public');

При взаимодействии с загруженными файлами вы можете использовать методы `storePublicly` и `storePubliclyAs` для сохранения загруженного файла с видимостью `public`:

    $path = $request->file('avatar')->storePublicly('avatars', 's3');

    $path = $request->file('avatar')->storePubliclyAs(
        'avatars',
        $request->user()->id,
        's3'
    );

<a name="local-files-and-visibility"></a>
#### Локальные файлы и видимость

При использовании драйвера `local`, `public` [visibility](#file-visibility) преобразуется в разрешения `0755` для каталогов и разрешения `0644` для файлов. Вы можете изменить сопоставление разрешений в файле конфигурации вашего приложения `filesystems`:

    'local' => [
        'driver' => 'local',
        'root' => storage_path('app'),
        'permissions' => [
            'file' => [
                'public' => 0664,
                'private' => 0600,
            ],
            'dir' => [
                'public' => 0775,
                'private' => 0700,
            ],
        ],
    ],

<a name="deleting-files"></a>
## Удаление файлов

Метод `delete` принимает одно имя файла или массив файлов для удаления:

    use Illuminate\Support\Facades\Storage;

    Storage::delete('file.jpg');

    Storage::delete(['file.jpg', 'file2.jpg']);

При необходимости вы можете указать диск, с которого следует удалить файл:

    use Illuminate\Support\Facades\Storage;

    Storage::disk('s3')->delete('path/file.jpg');

<a name="directories"></a>
## Директории

<a name="get-all-files-within-a-directory"></a>
#### Получить все файлы в каталоге

Метод `files` возвращает массив всех файлов в данном каталоге. Если вы хотите получить список всех файлов в данном каталоге, включая все подкаталоги, вы можете использовать метод `allFiles`:

    use Illuminate\Support\Facades\Storage;

    $files = Storage::files($directory);

    $files = Storage::allFiles($directory);

<a name="get-all-directories-within-a-directory"></a>
#### Получить все каталоги в каталоге

Метод `directories` возвращает массив всех каталогов в данном каталоге. Кроме того, вы можете использовать метод `allDirectories`, чтобы получить список всех каталогов в данном каталоге и всех его подкаталогах:

    $directories = Storage::directories($directory);

    $directories = Storage::allDirectories($directory);

<a name="create-a-directory"></a>
#### Создать каталог

Метод `makeDirectory` создаст указанный каталог, включая все необходимые подкаталоги:

    Storage::makeDirectory($directory);

<a name="delete-a-directory"></a>
#### Удалить каталог

Наконец, метод `deleteDirectory` может использоваться для удаления каталога и всех его файлов:

    Storage::deleteDirectory($directory);

<a name="custom-filesystems"></a>
## Пользовательские файловые системы

Интеграция Laravel с Flysystem обеспечивает поддержку нескольких «драйверов» из коробки; однако Flysystem этим не ограничивается и имеет адаптеры для многих других систем хранения. Вы можете создать собственный драйвер, если хотите использовать один из этих дополнительных адаптеров в своем приложении Laravel.

Для определения собственной файловой системы вам понадобится адаптер Flysystem. Давайте добавим в наш проект адаптер Dropbox, поддерживаемый сообществом:

    composer require spatie/flysystem-dropbox

Затем вы можете зарегистрировать драйвер в методе `boot` одного из [сервис провайдеров](/docs/{{version}}/providers) вашего приложения. Для этого вы должны использовать метод `extend` фасада `Storage`:

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Storage;
    use Illuminate\Support\ServiceProvider;
    use League\Flysystem\Filesystem;
    use Spatie\Dropbox\Client as DropboxClient;
    use Spatie\FlysystemDropbox\DropboxAdapter;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
         *
         * @return void
         */
        public function register()
        {
            //
        }

        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            Storage::extend('dropbox', function ($app, $config) {
                $client = new DropboxClient(
                    $config['authorization_token']
                );

                return new Filesystem(new DropboxAdapter($client));
            });
        }
    }

Первый аргумент метода `extend` - это имя драйвера, а второй - замыкание, которое получает переменные `$app` и `$config`. Замыкание должно возвращать экземпляр `League\Flysystem\Filesystem`. Переменная `$config` содержит значения, определенные в `config/filesystems.php` для указанного диска.

После того, как вы создали и зарегистрировали поставщика услуг расширения, вы можете использовать драйвер `dropbox` в вашем файле конфигурации `config/filesystems.php`.
