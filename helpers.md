# Помощники

- [Введение](#introduction)
- [Доступные методы](#available-methods)

<a name="introduction"></a>
## Введение

Laravel включает в себя множество глобальных «вспомогательных» функций PHP. Многие из этих функций используются самим фреймворком; однако вы можете использовать их в своих собственных приложениях, если сочтете их удобными.

<a name="available-methods"></a>
## Доступные методы

<style>
    .collection-method-list > p {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 2em; -moz-column-gap: 2em; -webkit-column-gap: 2em;
    }

    .collection-method-list a {
        display: block;
    }
</style>

<a name="arrays-and-objects-method-list"></a>
### Массивы и объекты

<div class="collection-method-list" markdown="1">

[Arr::accessible](#method-array-accessible)
[Arr::add](#method-array-add)
[Arr::collapse](#method-array-collapse)
[Arr::crossJoin](#method-array-crossjoin)
[Arr::divide](#method-array-divide)
[Arr::dot](#method-array-dot)
[Arr::except](#method-array-except)
[Arr::exists](#method-array-exists)
[Arr::first](#method-array-first)
[Arr::flatten](#method-array-flatten)
[Arr::forget](#method-array-forget)
[Arr::get](#method-array-get)
[Arr::has](#method-array-has)
[Arr::hasAny](#method-array-hasany)
[Arr::isAssoc](#method-array-isassoc)
[Arr::last](#method-array-last)
[Arr::only](#method-array-only)
[Arr::pluck](#method-array-pluck)
[Arr::prepend](#method-array-prepend)
[Arr::pull](#method-array-pull)
[Arr::query](#method-array-query)
[Arr::random](#method-array-random)
[Arr::set](#method-array-set)
[Arr::shuffle](#method-array-shuffle)
[Arr::sort](#method-array-sort)
[Arr::sortRecursive](#method-array-sort-recursive)
[Arr::where](#method-array-where)
[Arr::wrap](#method-array-wrap)
[data_fill](#method-data-fill)
[data_get](#method-data-get)
[data_set](#method-data-set)
[head](#method-head)
[last](#method-last)
</div>

<a name="paths-method-list"></a>
### Пути

<div class="collection-method-list" markdown="1">

[app_path](#method-app-path)
[base_path](#method-base-path)
[config_path](#method-config-path)
[database_path](#method-database-path)
[mix](#method-mix)
[public_path](#method-public-path)
[resource_path](#method-resource-path)
[storage_path](#method-storage-path)

</div>

<a name="strings-method-list"></a>
### Строки

<div class="collection-method-list" markdown="1">

[\__](#method-__)
[class_basename](#method-class-basename)
[e](#method-e)
[preg_replace_array](#method-preg-replace-array)
[Str::after](#method-str-after)
[Str::afterLast](#method-str-after-last)
[Str::ascii](#method-str-ascii)
[Str::before](#method-str-before)
[Str::beforeLast](#method-str-before-last)
[Str::between](#method-str-between)
[Str::camel](#method-camel-case)
[Str::contains](#method-str-contains)
[Str::containsAll](#method-str-contains-all)
[Str::endsWith](#method-ends-with)
[Str::finish](#method-str-finish)
[Str::is](#method-str-is)
[Str::isAscii](#method-str-is-ascii)
[Str::isUuid](#method-str-is-uuid)
[Str::kebab](#method-kebab-case)
[Str::length](#method-str-length)
[Str::limit](#method-str-limit)
[Str::lower](#method-str-lower)
[Str::markdown](#method-str-markdown)
[Str::orderedUuid](#method-str-ordered-uuid)
[Str::padBoth](#method-str-padboth)
[Str::padLeft](#method-str-padleft)
[Str::padRight](#method-str-padright)
[Str::plural](#method-str-plural)
[Str::pluralStudly](#method-str-plural-studly)
[Str::random](#method-str-random)
[Str::remove](#method-str-remove)
[Str::replace](#method-str-replace)
[Str::replaceArray](#method-str-replace-array)
[Str::replaceFirst](#method-str-replace-first)
[Str::replaceLast](#method-str-replace-last)
[Str::singular](#method-str-singular)
[Str::slug](#method-str-slug)
[Str::snake](#method-snake-case)
[Str::start](#method-str-start)
[Str::startsWith](#method-starts-with)
[Str::studly](#method-studly-case)
[Str::substr](#method-str-substr)
[Str::substrCount](#method-str-substrcount)
[Str::title](#method-title-case)
[Str::ucfirst](#method-str-ucfirst)
[Str::upper](#method-str-upper)
[Str::uuid](#method-str-uuid)
[Str::wordCount](#method-str-word-count)
[Str::words](#method-str-words)
[trans](#method-trans)
[trans_choice](#method-trans-choice)

</div>

<a name="fluent-strings-method-list"></a>
### Свободные строки

<div class="collection-method-list" markdown="1">

[after](#method-fluent-str-after)
[afterLast](#method-fluent-str-after-last)
[append](#method-fluent-str-append)
[ascii](#method-fluent-str-ascii)
[basename](#method-fluent-str-basename)
[before](#method-fluent-str-before)
[beforeLast](#method-fluent-str-before-last)
[camel](#method-fluent-str-camel)
[contains](#method-fluent-str-contains)
[containsAll](#method-fluent-str-contains-all)
[dirname](#method-fluent-str-dirname)
[endsWith](#method-fluent-str-ends-with)
[exactly](#method-fluent-str-exactly)
[explode](#method-fluent-str-explode)
[finish](#method-fluent-str-finish)
[is](#method-fluent-str-is)
[isAscii](#method-fluent-str-is-ascii)
[isEmpty](#method-fluent-str-is-empty)
[isNotEmpty](#method-fluent-str-is-not-empty)
[kebab](#method-fluent-str-kebab)
[length](#method-fluent-str-length)
[limit](#method-fluent-str-limit)
[lower](#method-fluent-str-lower)
[ltrim](#method-fluent-str-ltrim)
[markdown](#method-fluent-str-markdown)
[match](#method-fluent-str-match)
[matchAll](#method-fluent-str-match-all)
[padBoth](#method-fluent-str-padboth)
[padLeft](#method-fluent-str-padleft)
[padRight](#method-fluent-str-padright)
[pipe](#method-fluent-str-pipe)
[plural](#method-fluent-str-plural)
[prepend](#method-fluent-str-prepend)
[remove](#method-fluent-str-remove)
[replace](#method-fluent-str-replace)
[replaceArray](#method-fluent-str-replace-array)
[replaceFirst](#method-fluent-str-replace-first)
[replaceLast](#method-fluent-str-replace-last)
[replaceMatches](#method-fluent-str-replace-matches)
[rtrim](#method-fluent-str-rtrim)
[singular](#method-fluent-str-singular)
[slug](#method-fluent-str-slug)
[snake](#method-fluent-str-snake)
[split](#method-fluent-str-split)
[start](#method-fluent-str-start)
[startsWith](#method-fluent-str-starts-with)
[studly](#method-fluent-str-studly)
[substr](#method-fluent-str-substr)
[tap](#method-fluent-str-tap)
[test](#method-fluent-str-test)
[title](#method-fluent-str-title)
[trim](#method-fluent-str-trim)
[ucfirst](#method-fluent-str-ucfirst)
[upper](#method-fluent-str-upper)
[when](#method-fluent-str-when)
[whenEmpty](#method-fluent-str-when-empty)
[wordCount](#method-fluent-str-word-count)
[words](#method-fluent-str-words)

</div>

<a name="urls-method-list"></a>
### URL-адреса

<div class="collection-method-list" markdown="1">

[action](#method-action)
[asset](#method-asset)
[route](#method-route)
[secure_asset](#method-secure-asset)
[secure_url](#method-secure-url)
[url](#method-url)

</div>

<a name="miscellaneous-method-list"></a>
### Дополнительные

<div class="collection-method-list" markdown="1">

[abort](#method-abort)
[abort_if](#method-abort-if)
[abort_unless](#method-abort-unless)
[app](#method-app)
[auth](#method-auth)
[back](#method-back)
[bcrypt](#method-bcrypt)
[blank](#method-blank)
[broadcast](#method-broadcast)
[cache](#method-cache)
[class_uses_recursive](#method-class-uses-recursive)
[collect](#method-collect)
[config](#method-config)
[cookie](#method-cookie)
[csrf_field](#method-csrf-field)
[csrf_token](#method-csrf-token)
[dd](#method-dd)
[dispatch](#method-dispatch)
[dump](#method-dump)
[env](#method-env)
[event](#method-event)
[filled](#method-filled)
[info](#method-info)
[logger](#method-logger)
[method_field](#method-method-field)
[now](#method-now)
[old](#method-old)
[optional](#method-optional)
[policy](#method-policy)
[redirect](#method-redirect)
[report](#method-report)
[request](#method-request)
[rescue](#method-rescue)
[resolve](#method-resolve)
[response](#method-response)
[retry](#method-retry)
[session](#method-session)
[tap](#method-tap)
[throw_if](#method-throw-if)
[throw_unless](#method-throw-unless)
[today](#method-today)
[trait_uses_recursive](#method-trait-uses-recursive)
[transform](#method-transform)
[validator](#method-validator)
[value](#method-value)
[view](#method-view)
[with](#method-with)

</div>

<a name="method-listing"></a>
## Список методов

<style>
    #collection-method code {
        font-size: 14px;
    }

    #collection-method:not(.first-collection-method) {
        margin-top: 50px;
    }
</style>

<a name="arrays"></a>
## Массивы и объекты

<a name="method-array-accessible"></a>
#### `Arr::accessible()` {#collection-method .first-collection-method}

Метод `Arr::accessible` определяет, доступно ли данное значение массиву:

    use Illuminate\Support\Arr;
    use Illuminate\Support\Collection;

    $isAccessible = Arr::accessible(['a' => 1, 'b' => 2]);

    // true

    $isAccessible = Arr::accessible(new Collection);

    // true

    $isAccessible = Arr::accessible('abc');

    // false

    $isAccessible = Arr::accessible(new stdClass);

    // false

<a name="method-array-add"></a>
#### `Arr::add()` {#collection-method}

Метод `Arr::add` добавляет заданную пару ключ/значение в массив, если данный ключ еще не существует в массиве или установлен в `null`:

    use Illuminate\Support\Arr;

    $array = Arr::add(['name' => 'Desk'], 'price', 100);

    // ['name' => 'Desk', 'price' => 100]

    $array = Arr::add(['name' => 'Desk', 'price' => null], 'price', 100);

    // ['name' => 'Desk', 'price' => 100]


<a name="method-array-collapse"></a>
#### `Arr::collapse()` {#collection-method}

Метод `Arr::collapse` сворачивает массив массивов в один массив:

    use Illuminate\Support\Arr;

    $array = Arr::collapse([[1, 2, 3], [4, 5, 6], [7, 8, 9]]);

    // [1, 2, 3, 4, 5, 6, 7, 8, 9]

<a name="method-array-crossjoin"></a>
#### `Arr::crossJoin()` {#collection-method}

Метод `Arr::crossJoin` объединяет указанные массивы, возвращая Декартово произведение со всеми возможными перестановками:

    use Illuminate\Support\Arr;

    $matrix = Arr::crossJoin([1, 2], ['a', 'b']);

    /*
        [
            [1, 'a'],
            [1, 'b'],
            [2, 'a'],
            [2, 'b'],
        ]
    */

    $matrix = Arr::crossJoin([1, 2], ['a', 'b'], ['I', 'II']);

    /*
        [
            [1, 'a', 'I'],
            [1, 'a', 'II'],
            [1, 'b', 'I'],
            [1, 'b', 'II'],
            [2, 'a', 'I'],
            [2, 'a', 'II'],
            [2, 'b', 'I'],
            [2, 'b', 'II'],
        ]
    */

<a name="method-array-divide"></a>
#### `Arr::divide()` {#collection-method}

Метод `Arr::divide` возвращает два массива: один содержит ключи, а другой - значения данного массива:

    use Illuminate\Support\Arr;

    [$keys, $values] = Arr::divide(['name' => 'Desk']);

    // $keys: ['name']

    // $values: ['Desk']

<a name="method-array-dot"></a>
#### `Arr::dot()` {#collection-method}

Метод `Arr::dot` объединяет многомерный массив в одноуровневый массив, который использует «точечную» нотацию для обозначения глубины:

    use Illuminate\Support\Arr;

    $array = ['products' => ['desk' => ['price' => 100]]];

    $flattened = Arr::dot($array);

    // ['products.desk.price' => 100]

<a name="method-array-except"></a>
#### `Arr::except()` {#collection-method}

Метод `Arr::except` удаляет заданные пары ключ/значение из массива:

    use Illuminate\Support\Arr;

    $array = ['name' => 'Desk', 'price' => 100];

    $filtered = Arr::except($array, ['price']);

    // ['name' => 'Desk']

<a name="method-array-exists"></a>
#### `Arr::exists()` {#collection-method}

Метод `Arr::exists` проверяет, существует ли данный ключ в предоставленном массиве:

    use Illuminate\Support\Arr;

    $array = ['name' => 'John Doe', 'age' => 17];

    $exists = Arr::exists($array, 'name');

    // true

    $exists = Arr::exists($array, 'salary');

    // false

<a name="method-array-first"></a>
#### `Arr::first()` {#collection-method}

Метод `Arr::first` возвращает первый элемент массива, прошедшего заданный тест истинности:

    use Illuminate\Support\Arr;

    $array = [100, 200, 300];

    $first = Arr::first($array, function ($value, $key) {
        return $value >= 150;
    });

    // 200

Значение по умолчанию также может быть передано в метод в качестве третьего параметра. Это значение будет возвращено, если ни одно значение не прошло проверку истинности:

    use Illuminate\Support\Arr;

    $first = Arr::first($array, $callback, $default);

<a name="method-array-flatten"></a>
#### `Arr::flatten()` {#collection-method}

Метод `Arr::flatten` объединяет многомерный массив в одноуровневый массив:

    use Illuminate\Support\Arr;

    $array = ['name' => 'Joe', 'languages' => ['PHP', 'Ruby']];

    $flattened = Arr::flatten($array);

    // ['Joe', 'PHP', 'Ruby']

<a name="method-array-forget"></a>
#### `Arr::forget()` {#collection-method}

Метод `Arr::forget` удаляет заданную пару ключ/значение из глубоко вложенного массива, используя «точечную» нотацию:

    use Illuminate\Support\Arr;

    $array = ['products' => ['desk' => ['price' => 100]]];

    Arr::forget($array, 'products.desk');

    // ['products' => []]

<a name="method-array-get"></a>
#### `Arr::get()` {#collection-method}

Метод `Arr::get` извлекает значение из глубоко вложенного массива, используя «точечную» нотацию:

    use Illuminate\Support\Arr;

    $array = ['products' => ['desk' => ['price' => 100]]];

    $price = Arr::get($array, 'products.desk.price');

    // 100

Метод `Arr::get` также принимает значение по умолчанию, которое будет возвращено, если указанный ключ отсутствует в массиве:

    use Illuminate\Support\Arr;

    $discount = Arr::get($array, 'products.desk.discount', 0);

    // 0

<a name="method-array-has"></a>
#### `Arr::has()` {#collection-method}

Метод `Arr::has` проверяет, существует ли данный элемент или элементы в массиве, используя «точечную» нотацию:

    use Illuminate\Support\Arr;

    $array = ['product' => ['name' => 'Desk', 'price' => 100]];

    $contains = Arr::has($array, 'product.name');

    // true

    $contains = Arr::has($array, ['product.price', 'product.discount']);

    // false

<a name="method-array-hasany"></a>
#### `Arr::hasAny()` {#collection-method}

Метод `Arr::hasAny` проверяет, существует ли какой-либо элемент в данном наборе в массиве, используя «точечную» нотацию:

    use Illuminate\Support\Arr;

    $array = ['product' => ['name' => 'Desk', 'price' => 100]];

    $contains = Arr::hasAny($array, 'product.name');

    // true

    $contains = Arr::hasAny($array, ['product.name', 'product.discount']);

    // true

    $contains = Arr::hasAny($array, ['category', 'product.discount']);

    // false

<a name="method-array-isassoc"></a>
#### `Arr::isAssoc()` {#collection-method}

`Arr::isAssoc` возвращает `true`, если данный массив является ассоциативным. Массив считается "ассоциативным", если в нем нет последовательных цифровых ключей, начинающихся с нуля:

    use Illuminate\Support\Arr;

    $isAssoc = Arr::isAssoc(['product' => ['name' => 'Desk', 'price' => 100]]);

    // true

    $isAssoc = Arr::isAssoc([1, 2, 3]);

    // false

<a name="method-array-last"></a>
#### `Arr::last()` {#collection-method}

Метод `Arr::last` возвращает последний элемент массива, прошедшего заданный тест истинности:

    use Illuminate\Support\Arr;

    $array = [100, 200, 300, 110];

    $last = Arr::last($array, function ($value, $key) {
        return $value >= 150;
    });

    // 300

В качестве третьего аргумента метода может быть передано значение по умолчанию. Это значение будет возвращено, если ни одно значение не прошло проверку истинности:

    use Illuminate\Support\Arr;

    $last = Arr::last($array, $callback, $default);

<a name="method-array-only"></a>
#### `Arr::only()` {#collection-method}

Метод `Arr::only` возвращает только указанные пары ключ / значение из данного массива:

    use Illuminate\Support\Arr;

    $array = ['name' => 'Desk', 'price' => 100, 'orders' => 10];

    $slice = Arr::only($array, ['name', 'price']);

    // ['name' => 'Desk', 'price' => 100]

<a name="method-array-pluck"></a>
#### `Arr::pluck()` {#collection-method}

Метод `Arr::pluck` извлекает все значения для данного ключа из массива:

    use Illuminate\Support\Arr;

    $array = [
        ['developer' => ['id' => 1, 'name' => 'Taylor']],
        ['developer' => ['id' => 2, 'name' => 'Abigail']],
    ];

    $names = Arr::pluck($array, 'developer.name');

    // ['Taylor', 'Abigail']

Вы также можете указать, как вы хотите, чтобы результирующий список был оформлен в виде ключа:

    use Illuminate\Support\Arr;

    $names = Arr::pluck($array, 'developer.name', 'developer.id');

    // [1 => 'Taylor', 2 => 'Abigail']

<a name="method-array-prepend"></a>
#### `Arr::prepend()` {#collection-method}

Метод `Arr::prepend` помещает элемент в начало массива:

    use Illuminate\Support\Arr;

    $array = ['one', 'two', 'three', 'four'];

    $array = Arr::prepend($array, 'zero');

    // ['zero', 'one', 'two', 'three', 'four']

При необходимости вы можете указать ключ, который следует использовать для значения:

    use Illuminate\Support\Arr;

    $array = ['price' => 100];

    $array = Arr::prepend($array, 'Desk', 'name');

    // ['name' => 'Desk', 'price' => 100]

<a name="method-array-pull"></a>
#### `Arr::pull()` {#collection-method}

Метод `Arr::pull` возвращает и удаляет пару ключ / значение из массива:

    use Illuminate\Support\Arr;

    $array = ['name' => 'Desk', 'price' => 100];

    $name = Arr::pull($array, 'name');

    // $name: Desk

    // $array: ['price' => 100]

В качестве третьего аргумента метода может быть передано значение по умолчанию. Это значение будет возвращено, если ключ не существует:

    use Illuminate\Support\Arr;

    $value = Arr::pull($array, $key, $default);

<a name="method-array-query"></a>
#### `Arr::query()` {#collection-method}

Метод `Arr::query` преобразует массив в строку запроса:

    use Illuminate\Support\Arr;

    $array = [
        'name' => 'Taylor',
        'order' => [
            'column' => 'created_at',
            'direction' => 'desc'
        ]
    ];

    Arr::query($array);

    // name=Taylor&order[column]=created_at&order[direction]=desc

<a name="method-array-random"></a>
#### `Arr::random()` {#collection-method}

Метод `Arr::random` возвращает случайное значение из массива:

    use Illuminate\Support\Arr;

    $array = [1, 2, 3, 4, 5];

    $random = Arr::random($array);

    // 4 - (retrieved randomly)

Вы также можете указать количество возвращаемых элементов в качестве необязательного второго аргумента. Обратите внимание, что предоставление этого аргумента вернет массив, даже если требуется только один элемент:

    use Illuminate\Support\Arr;

    $items = Arr::random($array, 2);

    // [2, 5] - (retrieved randomly)

<a name="method-array-set"></a>
#### `Arr::set()` {#collection-method}

Метод `Arr::set` устанавливает значение внутри глубоко вложенного массива, используя «точечную» нотацию:

    use Illuminate\Support\Arr;

    $array = ['products' => ['desk' => ['price' => 100]]];

    Arr::set($array, 'products.desk.price', 200);

    // ['products' => ['desk' => ['price' => 200]]]

<a name="method-array-shuffle"></a>
#### `Arr::shuffle()` {#collection-method}

Метод `Arr::shuffle` случайным образом перемешивает элементы в массиве:

    use Illuminate\Support\Arr;

    $array = Arr::shuffle([1, 2, 3, 4, 5]);

    // [3, 2, 5, 1, 4] - (generated randomly)

<a name="method-array-sort"></a>
#### `Arr::sort()` {#collection-method}

Метод `Arr::sort` сортирует массив по его значениям:

    use Illuminate\Support\Arr;

    $array = ['Desk', 'Table', 'Chair'];

    $sorted = Arr::sort($array);

    // ['Chair', 'Desk', 'Table']

Вы также можете отсортировать массив по результатам данного замыкания:

    use Illuminate\Support\Arr;

    $array = [
        ['name' => 'Desk'],
        ['name' => 'Table'],
        ['name' => 'Chair'],
    ];

    $sorted = array_values(Arr::sort($array, function ($value) {
        return $value['name'];
    }));

    /*
        [
            ['name' => 'Chair'],
            ['name' => 'Desk'],
            ['name' => 'Table'],
        ]
    */

<a name="method-array-sort-recursive"></a>
#### `Arr::sortRecursive()` {#collection-method}

Метод `Arr::sortRecursive` рекурсивно сортирует массив, используя функцию `sort` для подмассивов с числовым индексом и функцию `ksort` для ассоциативных подмассивов:

    use Illuminate\Support\Arr;

    $array = [
        ['Roman', 'Taylor', 'Li'],
        ['PHP', 'Ruby', 'JavaScript'],
        ['one' => 1, 'two' => 2, 'three' => 3],
    ];

    $sorted = Arr::sortRecursive($array);

    /*
        [
            ['JavaScript', 'PHP', 'Ruby'],
            ['one' => 1, 'three' => 3, 'two' => 2],
            ['Li', 'Roman', 'Taylor'],
        ]
    */

<a name="method-array-where"></a>
#### `Arr::where()` {#collection-method}

Метод `Arr::where` фильтрует массив, используя заданное замыкание:

    use Illuminate\Support\Arr;

    $array = [100, '200', 300, '400', 500];

    $filtered = Arr::where($array, function ($value, $key) {
        return is_string($value);
    });

    // [1 => '200', 3 => '400']

<a name="method-array-wrap"></a>
#### `Arr::wrap()` {#collection-method}

Метод `Arr::wrap` помещает заданное значение в массив. Если данное значение уже является массивом, оно будет возвращено без изменений:

    use Illuminate\Support\Arr;

    $string = 'Laravel';

    $array = Arr::wrap($string);

    // ['Laravel']

Если заданное значение равно `null`, будет возвращен пустой массив:

    use Illuminate\Support\Arr;

    $array = Arr::wrap(null);

    // []

<a name="method-data-fill"></a>
#### `data_fill()` {#collection-method}

Функция `data_fill` устанавливает отсутствующее значение во вложенном массиве или объекте, используя «точечную» нотацию:

    $data = ['products' => ['desk' => ['price' => 100]]];

    data_fill($data, 'products.desk.price', 200);

    // ['products' => ['desk' => ['price' => 100]]]

    data_fill($data, 'products.desk.discount', 10);

    // ['products' => ['desk' => ['price' => 100, 'discount' => 10]]]

Эта функция также принимает звездочки в качестве подстановочных знаков и соответствующим образом заполняет цель:

    $data = [
        'products' => [
            ['name' => 'Desk 1', 'price' => 100],
            ['name' => 'Desk 2'],
        ],
    ];

    data_fill($data, 'products.*.price', 200);

    /*
        [
            'products' => [
                ['name' => 'Desk 1', 'price' => 100],
                ['name' => 'Desk 2', 'price' => 200],
            ],
        ]
    */

<a name="method-data-get"></a>
#### `data_get()` {#collection-method}

Функция `data_get` извлекает значение из вложенного массива или объекта, используя «точечную» нотацию:

    $data = ['products' => ['desk' => ['price' => 100]]];

    $price = data_get($data, 'products.desk.price');

    // 100

Функция `data_get` также принимает значение по умолчанию, которое будет возвращено, если указанный ключ не найден:

    $discount = data_get($data, 'products.desk.discount', 0);

    // 0

Функция также принимает подстановочные знаки с использованием звездочек, которые могут указывать на любой ключ массива или объекта:

    $data = [
        'product-one' => ['name' => 'Desk 1', 'price' => 100],
        'product-two' => ['name' => 'Desk 2', 'price' => 150],
    ];

    data_get($data, '*.name');

    // ['Desk 1', 'Desk 2'];

<a name="method-data-set"></a>
#### `data_set()` {#collection-method}

Функция `data_set` устанавливает значение внутри вложенного массива или объекта, используя «точечную» нотацию:

    $data = ['products' => ['desk' => ['price' => 100]]];

    data_set($data, 'products.desk.price', 200);

    // ['products' => ['desk' => ['price' => 200]]]

Эта функция также принимает подстановочные знаки с использованием звездочек и соответственно устанавливает значения для цели:

    $data = [
        'products' => [
            ['name' => 'Desk 1', 'price' => 100],
            ['name' => 'Desk 2', 'price' => 150],
        ],
    ];

    data_set($data, 'products.*.price', 200);

    /*
        [
            'products' => [
                ['name' => 'Desk 1', 'price' => 200],
                ['name' => 'Desk 2', 'price' => 200],
            ],
        ]
    */

По умолчанию все существующие значения перезаписываются. Если вы хотите установить значение только в том случае, если оно не существует, вы можете передать `false` в качестве четвертого аргумента функции:

    $data = ['products' => ['desk' => ['price' => 100]]];

    data_set($data, 'products.desk.price', 200, $overwrite = false);

    // ['products' => ['desk' => ['price' => 100]]]

<a name="method-head"></a>
#### `head()` {#collection-method}

Функция `head` возвращает первый элемент в данном массиве:

    $array = [100, 200, 300];

    $first = head($array);

    // 100

<a name="method-last"></a>
#### `last()` {#collection-method}

Функция `last` возвращает последний элемент в данном массиве:

    $array = [100, 200, 300];

    $last = last($array);

    // 300

<a name="paths"></a>
## Пути

<a name="method-app-path"></a>
#### `app_path()` {#collection-method}

Функция `app_path` возвращает полный путь к каталогу `app` вашего приложения directory. Вы также можете использовать функцию `app_path` для генерации полного пути к файлу относительно каталога приложения:

    $path = app_path();

    $path = app_path('Http/Controllers/Controller.php');

<a name="method-base-path"></a>
#### `base_path()` {#collection-method}

Функция `base_path` возвращает полный путь к каталогу `root` вашего приложения. Вы также можете использовать функцию `base_path` для генерации полного пути к заданному файлу относительно корневого каталога проекта:

    $path = base_path();

    $path = base_path('vendor/bin');

<a name="method-config-path"></a>
#### `config_path()` {#collection-method}

Функция `config_path` возвращает полный путь к каталогу `config` вашего приложения. Вы также можете использовать функцию `config_path` для генерации полного пути к заданному файлу в каталоге конфигурации приложения:

    $path = config_path();

    $path = config_path('app.php');

<a name="method-database-path"></a>
#### `database_path()` {#collection-method}

Функция `database_path` возвращает полный путь к каталогу `database` вашего приложения. Вы также можете использовать функцию `database_path` для генерации полного пути к заданному файлу в каталоге базы данных:

    $path = database_path();

    $path = database_path('factories/UserFactory.php');

<a name="method-mix"></a>
#### `mix()` {#collection-method}

The `mix` function returns the path to a [versioned Mix file](/docs/{{version}}/mix):

    $path = mix('css/app.css');

<a name="method-public-path"></a>
#### `public_path()` {#collection-method}

Функция `public_path` возвращает полный путь к каталогу `public` вашего приложения. Вы также можете использовать функцию `public_path` для генерации полного пути к заданному файлу в общедоступном каталоге:

    $path = public_path();

    $path = public_path('css/app.css');

<a name="method-resource-path"></a>
#### `resource_path()` {#collection-method}

Функция `resource_path` возвращает полный путь к каталогу `resources` вашего приложения. Вы также можете использовать функцию `resource_path` для генерации полного пути к заданному файлу в каталоге ресурсов:

    $path = resource_path();

    $path = resource_path('sass/app.scss');

<a name="method-storage-path"></a>
#### `storage_path()` {#collection-method}

Функция `storage_path` возвращает полный путь к каталогу `storage` вашего приложения. Вы также можете использовать функцию `storage_path` для генерации полного пути к заданному файлу в каталоге хранилища:

    $path = storage_path();

    $path = storage_path('app/file.txt');

<a name="strings"></a>
## Строки

<a name="method-__"></a>
#### `__()` {#collection-method}

Функция `__` переводит заданную строку перевода или ключ перевода, используя ваши [файлы локализации](/docs/{{version}}/localization):

    echo __('Welcome to our application');

    echo __('messages.welcome');

Если указанная строка перевода или ключ не существует, функция `__` вернет заданное значение. Итак, используя приведенный выше пример, функция `__` вернет `messages.welcome`, если этот ключ перевода не существует.

<a name="method-class-basename"></a>
#### `class_basename()` {#collection-method}

Функция `class_basename` возвращает имя класса данного класса с удаленным пространством имен класса:

    $class = class_basename('Foo\Bar\Baz');

    // Baz

<a name="method-e"></a>
#### `e()` {#collection-method}

Функция `e` запускает функцию PHP `htmlspecialchars` с параметром `double_encode`, установленным по умолчанию в значение `true`:

    echo e('<html>foo</html>');

    // &lt;html&gt;foo&lt;/html&gt;

<a name="method-preg-replace-array"></a>
#### `preg_replace_array()` {#collection-method}

Функция `preg_replace_array` последовательно заменяет заданный шаблон в строке, используя массив:

    $string = 'The event will take place between :start and :end';

    $replaced = preg_replace_array('/:[a-z_]+/', ['8:30', '9:00'], $string);

    // The event will take place between 8:30 and 9:00

<a name="method-str-after"></a>
#### `Str::after()` {#collection-method}

Метод `Str::after` возвращает все, что находится после заданного значения в строке. Вся строка будет возвращена, если значение не существует в строке:

    use Illuminate\Support\Str;

    $slice = Str::after('This is my name', 'This is');

    // ' my name'

<a name="method-str-after-last"></a>
#### `Str::afterLast()` {#collection-method}

Метод `Str::afterLast` возвращает все, что находится после последнего появления данного значения в строке. Вся строка будет возвращена, если значение не существует в строке:

    use Illuminate\Support\Str;

    $slice = Str::afterLast('App\Http\Controllers\Controller', '\\');

    // 'Controller'

<a name="method-str-ascii"></a>
#### `Str::ascii()` {#collection-method}

Метод `Str::ascii` попытается транслитерировать строку в значение ASCII:

    use Illuminate\Support\Str;

    $slice = Str::ascii('û');

    // 'u'

<a name="method-str-before"></a>
#### `Str::before()` {#collection-method}

Метод `Str::before` возвращает все, что находится перед заданным значением в строке:

    use Illuminate\Support\Str;

    $slice = Str::before('This is my name', 'my name');

    // 'This is '

<a name="method-str-before-last"></a>
#### `Str::beforeLast()` {#collection-method}

Метод `Str::beforeLast` возвращает все, что было до последнего появления данного значения в строке:

    use Illuminate\Support\Str;

    $slice = Str::beforeLast('This is my name', 'is');

    // 'This '

<a name="method-str-between"></a>
#### `Str::between()` {#collection-method}

Метод `Str::between` возвращает часть строки между двумя значениями:

    use Illuminate\Support\Str;

    $slice = Str::between('This is my name', 'This', 'name');

    // ' is my '

<a name="method-camel-case"></a>
#### `Str::camel()` {#collection-method}

Метод `Str::camel` преобразует данную строку в `camelCase`:

    use Illuminate\Support\Str;

    $converted = Str::camel('foo_bar');

    // fooBar

<a name="method-str-contains"></a>
#### `Str::contains()` {#collection-method}

Метод `Str::contains` определяет, содержит ли данная строка заданное значение. Этот метод чувствителен к регистру:

    use Illuminate\Support\Str;

    $contains = Str::contains('This is my name', 'my');

    // true

Вы также можете передать массив значений, чтобы определить, содержит ли данная строка какое-либо из значений в массиве:

    use Illuminate\Support\Str;

    $contains = Str::contains('This is my name', ['my', 'foo']);

    // true

<a name="method-str-contains-all"></a>
#### `Str::containsAll()` {#collection-method}

Метод `Str::containsAll` определяет, содержит ли данная строка все значения в данном массиве:

    use Illuminate\Support\Str;

    $containsAll = Str::containsAll('This is my name', ['my', 'name']);

    // true

<a name="method-ends-with"></a>
#### `Str::endsWith()` {#collection-method}

Метод `Str::endsWith` определяет, заканчивается ли данная строка заданным значением:

    use Illuminate\Support\Str;

    $result = Str::endsWith('This is my name', 'name');

    // true


Вы также можете передать массив значений, чтобы определить, заканчивается ли данная строка каким-либо из значений в массиве:

    use Illuminate\Support\Str;

    $result = Str::endsWith('This is my name', ['name', 'foo']);

    // true

    $result = Str::endsWith('This is my name', ['this', 'foo']);

    // false

<a name="method-str-finish"></a>
#### `Str::finish()` {#collection-method}

Метод `Str::finish` добавляет один экземпляр данного значения в строку, если она еще не заканчивается этим значением:

    use Illuminate\Support\Str;

    $adjusted = Str::finish('this/string', '/');

    // this/string/

    $adjusted = Str::finish('this/string/', '/');

    // this/string/

<a name="method-str-is"></a>
#### `Str::is()` {#collection-method}

Метод `Str::is` определяет, соответствует ли данная строка заданному шаблону. Звездочки могут использоваться как значения подстановочных знаков:

    use Illuminate\Support\Str;

    $matches = Str::is('foo*', 'foobar');

    // true

    $matches = Str::is('baz*', 'foobar');

    // false

<a name="method-str-is-ascii"></a>
#### `Str::isAscii()` {#collection-method}

Метод `Str::isAscii` определяет, является ли данная строка 7-битной ASCII:

    use Illuminate\Support\Str;

    $isAscii = Str::isAscii('Taylor');

    // true

    $isAscii = Str::isAscii('ü');

    // false

<a name="method-str-is-uuid"></a>
#### `Str::isUuid()` {#collection-method}

Метод `Str::isUuid` определяет, является ли данная строка допустимым UUID:

    use Illuminate\Support\Str;

    $isUuid = Str::isUuid('a0a2a2d2-0b87-4a18-83f2-2529882be2de');

    // true

    $isUuid = Str::isUuid('laravel');

    // false

<a name="method-kebab-case"></a>
#### `Str::kebab()` {#collection-method}

Метод `Str::kebab` преобразует заданную строку в `kebab-case`:

    use Illuminate\Support\Str;

    $converted = Str::kebab('fooBar');

    // foo-bar

<a name="method-str-length"></a>
#### `Str::length()` {#collection-method}

Метод `Str::length` возвращает длину заданной строки:

    use Illuminate\Support\Str;

    $length = Str::length('Laravel');

    // 7

<a name="method-str-limit"></a>
#### `Str::limit()` {#collection-method}

Метод `Str::limit` обрезает данную строку до указанной длины:

    use Illuminate\Support\Str;

    $truncated = Str::limit('The quick brown fox jumps over the lazy dog', 20);

    // The quick brown fox...

Вы можете передать третий аргумент методу, чтобы изменить строку, которая будет добавлена в конец усеченной строки:

    use Illuminate\Support\Str;

    $truncated = Str::limit('The quick brown fox jumps over the lazy dog', 20, ' (...)');

    // The quick brown fox (...)

<a name="method-str-lower"></a>
#### `Str::lower()` {#collection-method}

Метод `Str::lower` преобразует данную строку в нижний регистр:

    use Illuminate\Support\Str;

    $converted = Str::lower('LARAVEL');

    // laravel

<a name="method-str-markdown"></a>
#### `Str::markdown()` {#collection-method}

Метод `Str::markdown` преобразует  GitHub flavored Markdown в HTML:

    use Illuminate\Support\Str;

    $html = Str::markdown('# Laravel');

    // <h1>Laravel</h1>

    $html = Str::markdown('# Taylor <b>Otwell</b>', [
        'html_input' => 'strip',
    ]);

    // <h1>Taylor Otwell</h1>

<a name="method-str-ordered-uuid"></a>
#### `Str::orderedUuid()` {#collection-method}

Метод `Str::orderedUuid` генерирует UUID "сначала временная метка", который может быть эффективно сохранен в индексированном столбце базы данных. Каждый UUID, созданный с помощью этого метода, будет отсортирован после UUID, ранее созданных с помощью этого метода:

    use Illuminate\Support\Str;

    return (string) Str::orderedUuid();

<a name="method-str-padboth"></a>
#### `Str::padBoth()` {#collection-method}

Метод `Str::padBoth` оборачивает функцию PHP `str_pad`, заполняя обе стороны строки другой строкой, пока последняя строка не достигнет желаемой длины:

    use Illuminate\Support\Str;

    $padded = Str::padBoth('James', 10, '_');

    // '__James___'

    $padded = Str::padBoth('James', 10);

    // '  James   '

<a name="method-str-padleft"></a>
#### `Str::padLeft()` {#collection-method}

Метод `Str::padLeft` оборачивает функцию PHP `str_pad`, заполняя левую часть строки другой строкой, пока последняя строка не достигнет желаемой длины:

    use Illuminate\Support\Str;

    $padded = Str::padLeft('James', 10, '-=');

    // '-=-=-James'

    $padded = Str::padLeft('James', 10);

    // '     James'

<a name="method-str-padright"></a>
#### `Str::padRight()` {#collection-method}

Метод `Str::padRight` оборачивает функцию PHP `str_pad`, заполняя правую часть строки другой строкой, пока последняя строка не достигнет желаемой длины:

    use Illuminate\Support\Str;

    $padded = Str::padRight('James', 10, '-');

    // 'James-----'

    $padded = Str::padRight('James', 10);

    // 'James     '

<a name="method-str-plural"></a>
#### `Str::plural()` {#collection-method}

Метод `Str::plural` преобразует строку единственного слова во множественное число. В настоящее время эта функция поддерживает только английский язык:

    use Illuminate\Support\Str;

    $plural = Str::plural('car');

    // cars

    $plural = Str::plural('child');

    // children

Вы можете указать целое число в качестве второго аргумента функции для получения единственного или множественного числа строки:

    use Illuminate\Support\Str;

    $plural = Str::plural('child', 2);

    // children

    $singular = Str::plural('child', 1);

    // child

<a name="method-str-plural-studly"></a>
#### `Str::pluralStudly()` {#collection-method}

Метод `Str::pluralStudly` преобразует строку единственного числа, отформатированную в регистре заглавных букв, в форму множественного числа. В настоящее время эта функция поддерживает только английский язык:

    use Illuminate\Support\Str;

    $plural = Str::pluralStudly('VerifiedHuman');

    // VerifiedHumans

    $plural = Str::pluralStudly('UserFeedback');

    // UserFeedback

Вы можете указать целое число в качестве второго аргумента функции для получения единственного или множественного числа строки:

    use Illuminate\Support\Str;

    $plural = Str::pluralStudly('VerifiedHuman', 2);

    // VerifiedHumans

    $singular = Str::pluralStudly('VerifiedHuman', 1);

    // VerifiedHuman

<a name="method-str-random"></a>
#### `Str::random()` {#collection-method}

Метод `Str::random` генерирует случайную строку указанной длины. Эта функция использует функцию PHP `random_bytes`:

    use Illuminate\Support\Str;

    $random = Str::random(40);

<a name="method-str-remove"></a>
#### `Str::remove()` {#collection-method}

Метод `Str::remove` удаляет заданное значение или массив значений из строки:

    use Illuminate\Support\Str;

    $string = 'Peter Piper picked a peck of pickled peppers.';

    $removed = Str::remove('e', $string);

    // Ptr Pipr pickd a pck of pickld ppprs.

Вы также можете передать `false` в качестве третьего аргумента методу `remove`, чтобы игнорировать регистр при удалении строк.

<a name="method-str-replace"></a>
#### `Str::replace()` {#collection-method}

Метод `Str::replace` заменяет заданную строку внутри строки:

    use Illuminate\Support\Str;

    $string = 'Laravel 8.x';

    $replaced = Str::replace('8.x', '9.x', $string);

    // Laravel 9.x

<a name="method-str-replace-array"></a>
#### `Str::replaceArray()` {#collection-method}

Метод `Str::replaceArray` заменяет заданное значение в строке последовательно, используя массив:

    use Illuminate\Support\Str;

    $string = 'The event will take place between ? and ?';

    $replaced = Str::replaceArray('?', ['8:30', '9:00'], $string);

    // The event will take place between 8:30 and 9:00

<a name="method-str-replace-first"></a>
#### `Str::replaceFirst()` {#collection-method}

Метод `Str::replaceFirst` заменяет первое вхождение данного значения в строке:

    use Illuminate\Support\Str;

    $replaced = Str::replaceFirst('the', 'a', 'the quick brown fox jumps over the lazy dog');

    // a quick brown fox jumps over the lazy dog

<a name="method-str-replace-last"></a>
#### `Str::replaceLast()` {#collection-method}

Метод `Str::replaceLast` заменяет последнее вхождение данного значения в строке:

    use Illuminate\Support\Str;

    $replaced = Str::replaceLast('the', 'a', 'the quick brown fox jumps over the lazy dog');

    // the quick brown fox jumps over a lazy dog

<a name="method-str-singular"></a>
#### `Str::singular()` {#collection-method}

Метод `Str::singular` преобразует строку в ее единственную форму. В настоящее время эта функция поддерживает только английский язык:

    use Illuminate\Support\Str;

    $singular = Str::singular('cars');

    // car

    $singular = Str::singular('children');

    // child

<a name="method-str-slug"></a>
#### `Str::slug()` {#collection-method}

Метод `Str::slug` генерирует дружественный URL-адрес "slug" из заданной строки:

    use Illuminate\Support\Str;

    $slug = Str::slug('Laravel 5 Framework', '-');

    // laravel-5-framework

<a name="method-snake-case"></a>
#### `Str::snake()` {#collection-method}

Метод `Str::snake` преобразует данную строку в `snake_case`:

    use Illuminate\Support\Str;

    $converted = Str::snake('fooBar');

    // foo_bar

<a name="method-str-start"></a>
#### `Str::start()` {#collection-method}

Метод `Str::start` добавляет в строку единственный экземпляр данного значения, если он еще не начинается с этого значения:

    use Illuminate\Support\Str;

    $adjusted = Str::start('this/string', '/');

    // /this/string

    $adjusted = Str::start('/this/string', '/');

    // /this/string

<a name="method-starts-with"></a>
#### `Str::startsWith()` {#collection-method}

Метод `Str::startsWith` определяет, начинается ли данная строка с заданного значения:

    use Illuminate\Support\Str;

    $result = Str::startsWith('This is my name', 'This');

    // true

Если передан массив возможных значений, метод `startsWith` вернет `true`, если строка начинается с любого из заданных значений:

    $result = Str::startsWith('This is my name', ['This', 'That', 'There']);

    // true

<a name="method-studly-case"></a>
#### `Str::studly()` {#collection-method}

Метод `Str::studly` преобразует заданную строку в `StudlyCase`:

    use Illuminate\Support\Str;

    $converted = Str::studly('foo_bar');

    // FooBar

<a name="method-str-substr"></a>
#### `Str::substr()` {#collection-method}

Метод `Str::substr` возвращает часть строки, указанную параметрами start и length:

    use Illuminate\Support\Str;

    $converted = Str::substr('The Laravel Framework', 4, 7);

    // Laravel

<a name="method-str-substrcount"></a>
#### `Str::substrCount()` {#collection-method}

Метод `Str::substrCount` возвращает количество вхождений данного значения в данную строку:

    use Illuminate\Support\Str;

    $count = Str::substrCount('If you like ice cream, you will like snow cones.', 'like');

    // 2

<a name="method-title-case"></a>
#### `Str::title()` {#collection-method}

Метод `Str::title` преобразует заданную строку в `Title Case`:

    use Illuminate\Support\Str;

    $converted = Str::title('a nice title uses the correct case');

    // A Nice Title Uses The Correct Case

<a name="method-str-ucfirst"></a>
#### `Str::ucfirst()` {#collection-method}

Метод `Str::ucfirst` возвращает заданную строку с первым символом, написанным с заглавной буквы:

    use Illuminate\Support\Str;

    $string = Str::ucfirst('foo bar');

    // Foo bar

<a name="method-str-upper"></a>
#### `Str::upper()` {#collection-method}

Метод `Str::upper` преобразует данную строку в верхний регистр:

    use Illuminate\Support\Str;

    $string = Str::upper('laravel');

    // LARAVEL

<a name="method-str-uuid"></a>
#### `Str::uuid()` {#collection-method}

Метод `Str::uuid` генерирует UUID (версия 4):

    use Illuminate\Support\Str;

    return (string) Str::uuid();

<a name="method-str-word-count"></a>
### `wordCount`

Функция `wordCount` возвращает количество слов, содержащихся в строке:

```php
use Illuminate\Support\Str;

Str::wordCount('Hello, world!'); // 2
```

<a name="method-str-words"></a>
#### `Str::words()` {#collection-method}

Метод `Str::words` ограничивает количество слов в строке. Дополнительная строка может быть передана этому методу через его третий аргумент, чтобы указать, какая строка должна быть добавлена в конец усеченной строки:

    use Illuminate\Support\Str;

    return Str::words('Perfectly balanced, as all things should be.', 3, ' >>>');

    // Perfectly balanced, as >>>

<a name="method-trans"></a>
#### `trans()` {#collection-method}

Функция `trans` переводит заданный ключ перевода, используя ваши [файлы локализации](/docs/{{version}}/localization):

    echo trans('messages.welcome');

Если указанный ключ перевода не существует, функция `trans` вернет данный ключ. Итак, используя приведенный выше пример, функция `trans` вернет `messages.welcome`, если ключ перевода не существует.

<a name="method-trans-choice"></a>
#### `trans_choice()` {#collection-method}

Функция `trans_choice` переводит заданный ключ перевода с отклонением:

    echo trans_choice('messages.notifications', $unreadCount);

Если указанный ключ перевода не существует, функция `trans_choice` вернет данный ключ. Итак, используя приведенный выше пример, функция `trans_choice` вернет `messages.notifications`, если ключ перевода не существует.

<a name="fluent-strings"></a>
## Свободные строки

Свободные строки обеспечивают более плавный объектно-ориентированный интерфейс для работы со строковыми значениями, позволяя объединять несколько строковых операций вместе с использованием более удобочитаемого синтаксиса по сравнению с традиционными строковыми операциями.

<a name="method-fluent-str-after"></a>
#### `after` {#collection-method}

Метод `after` возвращает все, что находится после заданного значения в строке. Вся строка будет возвращена, если значение не существует в строке:

    use Illuminate\Support\Str;

    $slice = Str::of('This is my name')->after('This is');

    // ' my name'

<a name="method-fluent-str-after-last"></a>
#### `afterLast` {#collection-method}

Метод `afterLast` возвращает все, что находится после последнего появления данного значения в строке. Вся строка будет возвращена, если значение не существует в строке:

    use Illuminate\Support\Str;

    $slice = Str::of('App\Http\Controllers\Controller')->afterLast('\\');

    // 'Controller'

<a name="method-fluent-str-append"></a>
#### `append` {#collection-method}

Метод `append` добавляет заданные значения в строку:

    use Illuminate\Support\Str;

    $string = Str::of('Taylor')->append(' Otwell');

    // 'Taylor Otwell'

<a name="method-fluent-str-ascii"></a>
#### `ascii` {#collection-method}

Метод `ascii` попытается транслитерировать строку в значение ASCII:

    use Illuminate\Support\Str;

    $string = Str::of('ü')->ascii();

    // 'u'

<a name="method-fluent-str-basename"></a>
#### `basename` {#collection-method}

Метод `basename` вернет завершающий компонент имени данной строки:

    use Illuminate\Support\Str;

    $string = Str::of('/foo/bar/baz')->basename();

    // 'baz'

При необходимости вы можете предоставить «расширение», которое будет удалено из конечного компонента:

    use Illuminate\Support\Str;

    $string = Str::of('/foo/bar/baz.jpg')->basename('.jpg');

    // 'baz'

<a name="method-fluent-str-before"></a>
#### `before` {#collection-method}

Метод `before` возвращает все, что находится перед заданным значением в строке:

    use Illuminate\Support\Str;

    $slice = Str::of('This is my name')->before('my name');

    // 'This is '

<a name="method-fluent-str-before-last"></a>
#### `beforeLast` {#collection-method}

Метод `beforeLast` возвращает все, что было до последнего появления данного значения в строке:

    use Illuminate\Support\Str;

    $slice = Str::of('This is my name')->beforeLast('is');

    // 'This '

<a name="method-fluent-str-camel"></a>
#### `camel` {#collection-method}

Метод `camel` преобразует данную строку в `camelCase`:

    use Illuminate\Support\Str;

    $converted = Str::of('foo_bar')->camel();

    // fooBar

<a name="method-fluent-str-contains"></a>
#### `contains` {#collection-method}

Метод `contains` определяет, содержит ли данная строка данное значение. Этот метод чувствителен к регистру:

    use Illuminate\Support\Str;

    $contains = Str::of('This is my name')->contains('my');

    // true

Вы также можете передать массив значений, чтобы определить, содержит ли данная строка какое-либо из значений в массиве:

    use Illuminate\Support\Str;

    $contains = Str::of('This is my name')->contains(['my', 'foo']);

    // true

<a name="method-fluent-str-contains-all"></a>
#### `containsAll` {#collection-method}

Метод `containsAll` определяет, содержит ли данная строка все значения в данном массиве:

    use Illuminate\Support\Str;

    $containsAll = Str::of('This is my name')->containsAll(['my', 'name']);

    // true

<a name="method-fluent-str-dirname"></a>
#### `dirname` {#collection-method}

Метод `dirname` возвращает часть родительского каталога данной строки:

    use Illuminate\Support\Str;

    $string = Str::of('/foo/bar/baz')->dirname();

    // '/foo/bar'

При необходимости вы можете указать, сколько уровней каталогов вы хотите вырезать из строки:

    use Illuminate\Support\Str;

    $string = Str::of('/foo/bar/baz')->dirname(2);

    // '/foo'

<a name="method-fluent-str-ends-with"></a>
#### `endsWith` {#collection-method}

Метод `endsWith` определяет, заканчивается ли данная строка заданным значением:

    use Illuminate\Support\Str;

    $result = Str::of('This is my name')->endsWith('name');

    // true

Вы также можете передать массив значений, чтобы определить, заканчивается ли данная строка каким-либо из значений в массиве:

    use Illuminate\Support\Str;

    $result = Str::of('This is my name')->endsWith(['name', 'foo']);

    // true

    $result = Str::of('This is my name')->endsWith(['this', 'foo']);

    // false

<a name="method-fluent-str-exactly"></a>
#### `exactly` {#collection-method}

Метод `exactly` определяет, является ли данная строка точным совпадением с другой строкой:

    use Illuminate\Support\Str;

    $result = Str::of('Laravel')->exactly('Laravel');

    // true

<a name="method-fluent-str-explode"></a>
#### `explode` {#collection-method}

Метод `explode` разбивает строку по заданному разделителю и возвращает коллекцию, содержащую каждый раздел разбитой строки:

    use Illuminate\Support\Str;

    $collection = Str::of('foo bar baz')->explode(' ');

    // collect(['foo', 'bar', 'baz'])

<a name="method-fluent-str-finish"></a>
#### `finish` {#collection-method}

Метод `finish` добавляет один экземпляр данного значения к строке, если она еще не заканчивается этим значением:

    use Illuminate\Support\Str;

    $adjusted = Str::of('this/string')->finish('/');

    // this/string/

    $adjusted = Str::of('this/string/')->finish('/');

    // this/string/

<a name="method-fluent-str-is"></a>
#### `is` {#collection-method}

Метод `is` определяет, соответствует ли данная строка заданному шаблону. Звездочки могут использоваться как подстановочные знаки:

    use Illuminate\Support\Str;

    $matches = Str::of('foobar')->is('foo*');

    // true

    $matches = Str::of('foobar')->is('baz*');

    // false

<a name="method-fluent-str-is-ascii"></a>
#### `isAscii` {#collection-method}

Метод `isAscii` определяет, является ли данная строка строкой ASCII:

    use Illuminate\Support\Str;

    $result = Str::of('Taylor')->isAscii();

    // true

    $result = Str::of('ü')->isAscii();

    // false

<a name="method-fluent-str-is-empty"></a>
#### `isEmpty` {#collection-method}

Метод `isEmpty` определяет, является ли данная строка пустой:

    use Illuminate\Support\Str;

    $result = Str::of('  ')->trim()->isEmpty();

    // true

    $result = Str::of('Laravel')->trim()->isEmpty();

    // false

<a name="method-fluent-str-is-not-empty"></a>
#### `isNotEmpty` {#collection-method}

Метод `isNotEmpty` определяет, не пуста ли данная строка:


    use Illuminate\Support\Str;

    $result = Str::of('  ')->trim()->isNotEmpty();

    // false

    $result = Str::of('Laravel')->trim()->isNotEmpty();

    // true

<a name="method-fluent-str-kebab"></a>
#### `kebab` {#collection-method}

Метод `kebab` преобразует данную строку в `kebab-case`:

    use Illuminate\Support\Str;

    $converted = Str::of('fooBar')->kebab();

    // foo-bar

<a name="method-fluent-str-length"></a>
#### `length` {#collection-method}

Метод `length` возвращает длину данной строки:

    use Illuminate\Support\Str;

    $length = Str::of('Laravel')->length();

    // 7

<a name="method-fluent-str-limit"></a>
#### `limit` {#collection-method}

Метод `limit` обрезает данную строку до указанной длины:

    use Illuminate\Support\Str;

    $truncated = Str::of('The quick brown fox jumps over the lazy dog')->limit(20);

    // The quick brown fox...

Вы также можете передать второй аргумент, чтобы изменить строку, которая будет добавлена в конец усеченной строки:

    use Illuminate\Support\Str;

    $truncated = Str::of('The quick brown fox jumps over the lazy dog')->limit(20, ' (...)');

    // The quick brown fox (...)

<a name="method-fluent-str-lower"></a>
#### `lower` {#collection-method}

Метод `lower` преобразует данную строку в нижний регистр:

    use Illuminate\Support\Str;

    $result = Str::of('LARAVEL')->lower();

    // 'laravel'

<a name="method-fluent-str-ltrim"></a>
#### `ltrim` {#collection-method}

Метод `ltrim` обрезает левую часть строки:

    use Illuminate\Support\Str;

    $string = Str::of('  Laravel  ')->ltrim();

    // 'Laravel  '

    $string = Str::of('/Laravel/')->ltrim('/');

    // 'Laravel/'

<a name="method-fluent-str-markdown"></a>
#### `markdown` {#collection-method}

Метод `markdown` конвертирует GitHub flavored Markdown в HTML:

    use Illuminate\Support\Str;

    $html = Str::of('# Laravel')->markdown();

    // <h1>Laravel</h1>

    $html = Str::of('# Taylor <b>Otwell</b>')->markdown([
        'html_input' => 'strip',
    ]);

    // <h1>Taylor Otwell</h1>

<a name="method-fluent-str-match"></a>
#### `match` {#collection-method}

Метод `match` вернет часть строки, которая соответствует заданному шаблону регулярного выражения:

    use Illuminate\Support\Str;

    $result = Str::of('foo bar')->match('/bar/');

    // 'bar'

    $result = Str::of('foo bar')->match('/foo (.*)/');

    // 'bar'

<a name="method-fluent-str-match-all"></a>
#### `matchAll` {#collection-method}

Метод `matchAll` вернет коллекцию, содержащую части строки, соответствующие заданному шаблону регулярного выражения:

    use Illuminate\Support\Str;

    $result = Str::of('bar foo bar')->matchAll('/bar/');

    // collect(['bar', 'bar'])

Если вы укажете соответствующую группу в выражении, Laravel вернет коллекцию совпадений этой группы:

    use Illuminate\Support\Str;

    $result = Str::of('bar fun bar fly')->matchAll('/f(\w*)/');

    // collect(['un', 'ly']);

Если совпадений не найдено, будет возвращена пустая коллекция.

<a name="method-fluent-str-padboth"></a>
#### `padBoth` {#collection-method}

Метод `padBoth` оборачивает функцию PHP `str_pad`, заполняя обе стороны строки другой строкой, пока конечная строка не достигнет желаемой длины:

    use Illuminate\Support\Str;

    $padded = Str::of('James')->padBoth(10, '_');

    // '__James___'

    $padded = Str::of('James')->padBoth(10);

    // '  James   '

<a name="method-fluent-str-padleft"></a>
#### `padLeft` {#collection-method}

Метод `padLeft` оборачивает функцию PHP `str_pad`, заполняя левую часть строки другой строкой, пока последняя строка не достигнет желаемой длины:

    use Illuminate\Support\Str;

    $padded = Str::of('James')->padLeft(10, '-=');

    // '-=-=-James'

    $padded = Str::of('James')->padLeft(10);

    // '     James'

<a name="method-fluent-str-padright"></a>
#### `padRight` {#collection-method}

Метод `padRight` оборачивает функцию PHP `str_pad`, заполняя правую часть строки другой строкой, пока конечная строка не достигнет желаемой длины:

    use Illuminate\Support\Str;

    $padded = Str::of('James')->padRight(10, '-');

    // 'James-----'

    $padded = Str::of('James')->padRight(10);

    // 'James     '

<a name="method-fluent-str-pipe"></a>
#### `pipe` {#collection-method}

Метод `pipe` позволяет вам преобразовать строку, передав ее текущее значение заданному вызываемому объекту:

    use Illuminate\Support\Str;

    $hash = Str::of('Laravel')->pipe('md5')->prepend('Checksum: ');

    // 'Checksum: a5c95b86291ea299fcbe64458ed12702'

    $closure = Str::of('foo')->pipe(function ($str) {
        return 'bar';
    });

    // 'bar'

<a name="method-fluent-str-plural"></a>
#### `plural` {#collection-method}

Метод `plural` преобразует строку единственного числа в форму множественного числа. В настоящее время эта функция поддерживает только английский язык:

    use Illuminate\Support\Str;

    $plural = Str::of('car')->plural();

    // cars

    $plural = Str::of('child')->plural();

    // children

Вы можете указать целое число в качестве второго аргумента функции для получения единственного или множественного числа строки:

    use Illuminate\Support\Str;

    $plural = Str::of('child')->plural(2);

    // children

    $plural = Str::of('child')->plural(1);

    // child

<a name="method-fluent-str-prepend"></a>
#### `prepend` {#collection-method}

Метод `prepend` добавляет указанные значения в строку:

    use Illuminate\Support\Str;

    $string = Str::of('Framework')->prepend('Laravel ');

    // Laravel Framework

<a name="method-fluent-str-remove"></a>
#### `remove` {#collection-method}

Метод `remove` удаляет заданное значение или массив значений из строки:

    use Illuminate\Support\Str;

    $string = Str::of('Arkansas is quite beautiful!')->remove('quite');

    // Arkansas is beautiful!

Вы также можете передать `false` в качестве второго параметра, чтобы игнорировать регистр при удалении.

<a name="method-fluent-str-replace"></a>
#### `replace` {#collection-method}

Метод `replace` заменяет заданную строку внутри строки:

    use Illuminate\Support\Str;

    $replaced = Str::of('Laravel 6.x')->replace('6.x', '7.x');

    // Laravel 7.x

<a name="method-fluent-str-replace-array"></a>
#### `replaceArray` {#collection-method}

Метод `replaceArray` последовательно заменяет заданное значение в строке, используя массив:

    use Illuminate\Support\Str;

    $string = 'The event will take place between ? and ?';

    $replaced = Str::of($string)->replaceArray('?', ['8:30', '9:00']);

    // The event will take place between 8:30 and 9:00

<a name="method-fluent-str-replace-first"></a>
#### `replaceFirst` {#collection-method}

Метод `replaceFirst` заменяет первое вхождение данного значения в строке:

    use Illuminate\Support\Str;

    $replaced = Str::of('the quick brown fox jumps over the lazy dog')->replaceFirst('the', 'a');

    // a quick brown fox jumps over the lazy dog

<a name="method-fluent-str-replace-last"></a>
#### `replaceLast` {#collection-method}

Метод `replaceLast` заменяет последнее вхождение данного значения в строке:

    use Illuminate\Support\Str;

    $replaced = Str::of('the quick brown fox jumps over the lazy dog')->replaceLast('the', 'a');

    // the quick brown fox jumps over a lazy dog

<a name="method-fluent-str-replace-matches"></a>
#### `replaceMatches` {#collection-method}

Метод `replaceMatches` заменяет все части строки, соответствующие шаблону, заданной строкой замены:

    use Illuminate\Support\Str;

    $replaced = Str::of('(+1) 501-555-1000')->replaceMatches('/[^A-Za-z0-9]++/', '')

    // '15015551000'

Метод `replaceMatches` также принимает замыкание, которое будет вызываться с каждой частью строки, соответствующей данному шаблону, что позволяет вам выполнить логику замены внутри замыкания и вернуть замененное значение:

    use Illuminate\Support\Str;

    $replaced = Str::of('123')->replaceMatches('/\d/', function ($match) {
        return '['.$match[0].']';
    });

    // '[1][2][3]'

<a name="method-fluent-str-rtrim"></a>
#### `rtrim` {#collection-method}

Метод `rtrim` обрезает правую часть данной строки:

    use Illuminate\Support\Str;

    $string = Str::of('  Laravel  ')->rtrim();

    // '  Laravel'

    $string = Str::of('/Laravel/')->rtrim('/');

    // '/Laravel'

<a name="method-fluent-str-singular"></a>
#### `singular` {#collection-method}

Метод `singular` преобразует строку в ее особую форму. В настоящее время эта функция поддерживает только английский язык:

    use Illuminate\Support\Str;

    $singular = Str::of('cars')->singular();

    // car

    $singular = Str::of('children')->singular();

    // child

<a name="method-fluent-str-slug"></a>
#### `slug` {#collection-method}

Метод `slug` генерирует дружественный URL-адрес "slug" из заданной строки:

    use Illuminate\Support\Str;

    $slug = Str::of('Laravel Framework')->slug('-');

    // laravel-framework

<a name="method-fluent-str-snake"></a>
#### `snake` {#collection-method}

Метод `snake` преобразует данную строку в `snake_case`:

    use Illuminate\Support\Str;

    $converted = Str::of('fooBar')->snake();

    // foo_bar

<a name="method-fluent-str-split"></a>
#### `split` {#collection-method}

Метод `split` разбивает строку на коллекцию с помощью регулярного выражения:

    use Illuminate\Support\Str;

    $segments = Str::of('one, two, three')->split('/[\s,]+/');

    // collect(["one", "two", "three"])

<a name="method-fluent-str-start"></a>
#### `start` {#collection-method}

Метод `start` добавляет один экземпляр данного значения к строке, если он еще не начинается с этого значения:

    use Illuminate\Support\Str;

    $adjusted = Str::of('this/string')->start('/');

    // /this/string

    $adjusted = Str::of('/this/string')->start('/');

    // /this/string

<a name="method-fluent-str-starts-with"></a>
#### `startsWith` {#collection-method}

Метод `startsWith` определяет, начинается ли данная строка с заданного значения:

    use Illuminate\Support\Str;

    $result = Str::of('This is my name')->startsWith('This');

    // true

<a name="method-fluent-str-studly"></a>
#### `studly` {#collection-method}

Метод `studly` преобразует данную строку в `StudlyCase`:

    use Illuminate\Support\Str;

    $converted = Str::of('foo_bar')->studly();

    // FooBar

<a name="method-fluent-str-substr"></a>
#### `substr` {#collection-method}

Метод `substr` возвращает часть строки, указанную заданными параметрами start и length:

    use Illuminate\Support\Str;

    $string = Str::of('Laravel Framework')->substr(8);

    // Framework

    $string = Str::of('Laravel Framework')->substr(8, 5);

    // Frame

<a name="method-fluent-str-tap"></a>
#### `tap` {#collection-method}

Метод `tap` передает строку заданному замыканию, позволяя вам исследовать строку и взаимодействовать с ней, не затрагивая при этом саму строку. Исходная строка возвращается методом `tap` независимо от того, что возвращает замыкание:

    use Illuminate\Support\Str;

    $string = Str::of('Laravel')
        ->append(' Framework')
        ->tap(function ($string) {
            dump('String after append: ' . $string);
        })
        ->upper();

    // LARAVEL FRAMEWORK

<a name="method-fluent-str-test"></a>
#### `test` {#collection-method}

Метод `test` определяет, соответствует ли строка заданному шаблону регулярного выражения:

    use Illuminate\Support\Str;

    $result = Str::of('Laravel Framework')->test('/Laravel/');

    // true

<a name="method-fluent-str-title"></a>
#### `title` {#collection-method}

Метод `title` преобразует данную строку в `Title Case`:

    use Illuminate\Support\Str;

    $converted = Str::of('a nice title uses the correct case')->title();

    // A Nice Title Uses The Correct Case

<a name="method-fluent-str-trim"></a>
#### `trim` {#collection-method}

Метод `trim` обрезает данную строку:

    use Illuminate\Support\Str;

    $string = Str::of('  Laravel  ')->trim();

    // 'Laravel'

    $string = Str::of('/Laravel/')->trim('/');

    // 'Laravel'

<a name="method-fluent-str-ucfirst"></a>
#### `ucfirst` {#collection-method}

Метод `ucfirst` возвращает заданную строку с заглавными буквами первого символа:

    use Illuminate\Support\Str;

    $string = Str::of('foo bar')->ucfirst();

    // Foo bar

<a name="method-fluent-str-upper"></a>
#### `upper` {#collection-method}

Метод `upper` преобразует данную строку в верхний регистр:

    use Illuminate\Support\Str;

    $adjusted = Str::of('laravel')->upper();

    // LARAVEL

<a name="method-fluent-str-when"></a>
#### `when` {#collection-method}

Метод `when` вызывает данное замыкание, если данное условие `true`. Замыкание получит экземпляр свободной строки:

    use Illuminate\Support\Str;

    $string = Str::of('Taylor')
                    ->when(true, function ($string) {
                        return $string->append(' Otwell');
                    });

    // 'Taylor Otwell'

При необходимости вы можете передать другое замыкание в качестве третьего параметра методу `when`. Это замыкание будет выполнено, если параметр условия будет иметь значение `false`.

<a name="method-fluent-str-when-empty"></a>
#### `whenEmpty` {#collection-method}

Метод `whenEmpty` вызывает данное замыкание, если строка пуста. Если замыкание возвращает значение, это значение также будет возвращено методом `whenEmpty`. Если замыкание не возвращает значение, будет возвращен свободный экземпляр строки:

    use Illuminate\Support\Str;

    $string = Str::of('  ')->whenEmpty(function ($string) {
        return $string->trim()->prepend('Laravel');
    });

    // 'Laravel'

<a name="method-fluent-str-word-count"></a>
### `wordCount`

Функция `wordCount` возвращает количество слов, содержащихся в строке:

```php
use Illuminate\Support\Str;

Str::of('Hello, world!')->wordCount(); // 2
```

<a name="method-fluent-str-words"></a>
#### `words` {#collection-method}

Метод `words` ограничивает количество слов в строке. При необходимости вы можете указать дополнительную строку, которая будет добавлена к усеченной строке:

    use Illuminate\Support\Str;

    $string = Str::of('Perfectly balanced, as all things should be.')->words(3, ' >>>');

    // Perfectly balanced, as >>>

<a name="urls"></a>
## URL-адреса

<a name="method-action"></a>
#### `action()` {#collection-method}

Функция `action` генерирует URL-адрес для данного действия контроллера:

    use App\Http\Controllers\HomeController;

    $url = action([HomeController::class, 'index']);

Если метод принимает параметры маршрута, вы можете передать их в качестве второго аргумента метода:

    $url = action([UserController::class, 'profile'], ['id' => 1]);

<a name="method-asset"></a>
#### `asset()` {#collection-method}

Функция `asset` генерирует URL для актива, используя текущую схему запроса (HTTP или HTTPS):

    $url = asset('img/photo.jpg');

Вы можете настроить хост URL ресурса, установив переменную `ASSET_URL` в вашем файле `.env`. Это может быть полезно, если вы размещаете свои активы на внешнем сервисе, таком как Amazon S3 или другой CDN:

    // ASSET_URL=http://example.com/assets

    $url = asset('img/photo.jpg'); // http://example.com/assets/img/photo.jpg

<a name="method-route"></a>
#### `route()` {#collection-method}

Функция `route` генерирует URL для заданного [именованного маршрута](/docs/{{version}}/routing#named-routes):

    $url = route('route.name');

Если маршрут принимает параметры, вы можете передать их в качестве второго аргумента функции:

    $url = route('route.name', ['id' => 1]);

По умолчанию функция `route` генерирует абсолютный URL. Если вы хотите сгенерировать относительный URL-адрес, вы можете передать `false` в качестве третьего аргумента функции:

    $url = route('route.name', ['id' => 1], false);

<a name="method-secure-asset"></a>
#### `secure_asset()` {#collection-method}

Функция `secure_asset` генерирует URL для актива с использованием HTTPS:

    $url = secure_asset('img/photo.jpg');

<a name="method-secure-url"></a>
#### `secure_url()` {#collection-method}

Функция `secure_url` генерирует полный URL HTTPS для заданного пути. Дополнительные сегменты URL могут быть переданы во втором аргументе функции:

    $url = secure_url('user/profile');

    $url = secure_url('user/profile', [1]);

<a name="method-url"></a>
#### `url()` {#collection-method}

Функция `url` генерирует полный URL для указанного пути:

    $url = url('user/profile');

    $url = url('user/profile', [1]);

Если путь не указан, возвращается экземпляр `Illuminate\Routing\UrlGenerator`:

    $current = url()->current();

    $full = url()->full();

    $previous = url()->previous();

<a name="miscellaneous"></a>
## Дополнительные

<a name="method-abort"></a>
#### `abort()` {#collection-method}

Функция `abort` генерирует [исключение HTTP](/docs/{{version}}/errors#http-exceptions), которое будет обработано [обработчиком исключения](/docs/{{version}}/errors#the-exception-handler):

    abort(403);

Вы также можете предоставить сообщение об исключении и настраиваемые заголовки HTTP-ответа, которые должны быть отправлены в браузер:

    abort(403, 'Unauthorized.', $headers);

<a name="method-abort-if"></a>
#### `abort_if()` {#collection-method}

Функция `abort_if` генерирует исключение HTTP, если данное логическое выражение оценивается как `true`:

    abort_if(! Auth::user()->isAdmin(), 403);

Подобно методу `abort`, вы также можете предоставить текст ответа исключения в качестве третьего аргумента и массив пользовательских заголовков ответа в качестве четвертого аргумента функции.

<a name="method-abort-unless"></a>
#### `abort_unless()` {#collection-method}

Функция `abort_unless` генерирует исключение HTTP, если данное логическое выражение оценивается как `false`:

    abort_unless(Auth::user()->isAdmin(), 403);

Подобно методу `abort`, вы также можете предоставить текст ответа исключения в качестве третьего аргумента и массив пользовательских заголовков ответа в качестве четвертого аргумента функции.

<a name="method-app"></a>
#### `app()` {#collection-method}

Функция `app` возвращает экземпляр [сервис контейнера](/docs/{{version}}/container):

    $container = app();

Вы можете передать имя класса или интерфейса, чтобы разрешить его из контейнера:

    $api = app('HelpSpot\API');

<a name="method-auth"></a>
#### `auth()` {#collection-method}

Функция `auth` возвращает экземпляр [аутентификатора](/docs/{{version}}/authentication). Вы можете использовать его как альтернативу фасаду `Auth`:

    $user = auth()->user();

При необходимости вы можете указать, к какому экземпляру защиты вы хотите получить доступ:

    $user = auth('admin')->user();

<a name="method-back"></a>
#### `back()` {#collection-method}

Функция `back` генерирует [HTTP-ответ перенаправления](/docs/{{version}}/responses#redirects) в предыдущее местоположение пользователя:

    return back($status = 302, $headers = [], $fallback = '/');

    return back();

<a name="method-bcrypt"></a>
#### `bcrypt()` {#collection-method}

Функция `bcrypt` [хеширует](/docs/{{version}}/hashing) заданное значение с помощью Bcrypt. Вы можете использовать эту функцию как альтернативу фасаду `Hash`:

    $password = bcrypt('my-secret-password');

<a name="method-blank"></a>
#### `blank()` {#collection-method}

Функция `blank` определяет, является ли данное значение «пустым»:

    blank('');
    blank('   ');
    blank(null);
    blank(collect());

    // true

    blank(0);
    blank(true);
    blank(false);

    // false

Для обратного к `blank`, смотрите метод [`filled`](#method-filled).

<a name="method-broadcast"></a>
#### `broadcast()` {#collection-method}

Функция `broadcast` [броадкастинга](/docs/{{version}}/broadcasting) передает данное [событие](/docs/{{version}}/events) своим слушателям:

    broadcast(new UserRegistered($user));

    broadcast(new UserRegistered($user))->toOthers();

<a name="method-cache"></a>
#### `cache()` {#collection-method}

Функция `cache` может использоваться для получения значений из [cache](/docs/{{version}}/cache). Если данный ключ не существует в кеше, будет возвращено необязательное значение по умолчанию:

    $value = cache('key');

    $value = cache('key', 'default');

Вы можете добавлять элементы в кеш, передавая в функцию массив пар ключ / значение. Вы также должны передать количество секунд или продолжительность, в течение которых кешированное значение должно считаться действительным:

    cache(['key' => 'value'], 300);

    cache(['key' => 'value'], now()->addSeconds(10));

<a name="method-class-uses-recursive"></a>
#### `class_uses_recursive()` {#collection-method}

Функция `class_uses_recursive` возвращает все признаки, используемые классом, включая признаки, используемые всеми его родительскими классами:

    $traits = class_uses_recursive(App\Models\User::class);

<a name="method-collect"></a>
#### `collect()` {#collection-method}

Функция `collect` создает экземпляр [коллекции](/docs/{{version}}/collections) из заданного значения:

    $collection = collect(['taylor', 'abigail']);

<a name="method-config"></a>
#### `config()` {#collection-method}

Функция `config` получает значение переменной [конфигурации](/docs/{{version}}/configuration). Доступ к значениям конфигурации можно получить с помощью синтаксиса «точка», который включает имя файла и параметр, к которому вы хотите получить доступ. Может быть указано значение по умолчанию, которое возвращается, если параметр конфигурации не существует:

    $value = config('app.timezone');

    $value = config('app.timezone', $default);

Вы можете установить переменные конфигурации во время выполнения, передав массив пар ключ / значение. Однако обратите внимание, что эта функция влияет только на значение конфигурации для текущего запроса и не обновляет ваши фактические значения конфигурации:

    config(['app.debug' => true]);

<a name="method-cookie"></a>
#### `cookie()` {#collection-method}

Функция `cookie` создает новый экземпляр [cookie](/docs/{{version}}/requests#cookies):

    $cookie = cookie('name', 'value', $minutes);

<a name="method-csrf-field"></a>
#### `csrf_field()` {#collection-method}

Функция `csrf_field` генерирует HTML-поле ввода `hidden`, содержащее значение токена CSRF. Например, используя [синтаксис Blade](/docs/{{version}}/blade):

    {{ csrf_field() }}

<a name="method-csrf-token"></a>
#### `csrf_token()` {#collection-method}

Функция `csrf_token` извлекает значение текущего токена CSRF:

    $token = csrf_token();

<a name="method-dd"></a>
#### `dd()` {#collection-method}

Функция `dd` выгружает заданные переменные и завершает выполнение скрипта:

    dd($value);

    dd($value1, $value2, $value3, ...);

Если вы не хотите останавливать выполнение вашего скрипта, используйте вместо этого функцию [`dump`](#method-dump).

<a name="method-dispatch"></a>
#### `dispatch()` {#collection-method}

Функция `dispatch` помещает данное [задание](/docs/{{version}}/queues#creating-jobs) в [очередь заданий](/docs/{{version}}/queues) Laravel:

    dispatch(new App\Jobs\SendEmails);

<a name="method-dump"></a>
#### `dump()` {#collection-method}

Функция `dump` выгружает заданные переменные:

    dump($value);

    dump($value1, $value2, $value3, ...);

Если вы хотите остановить выполнение сценария после сброса переменных, используйте вместо этого функцию [`dd`](#method-dd).

<a name="method-env"></a>
#### `env()` {#collection-method}

Функция `env` извлекает значение [переменной среды](/docs/{{version}}/configuration#environment-configuration) или возвращает значение по умолчанию:

    $env = env('APP_ENV');

    $env = env('APP_ENV', 'production');

> {note} Если вы выполняете команду `config:cache` в процессе развертывания, вы должны быть уверены, что вызываете функцию `env` только из ваших файлов конфигурации. После кэширования конфигурации файл `.env` не будет загружен, и все вызовы функции `env` вернут `null`.

<a name="method-event"></a>
#### `event()` {#collection-method}

Функция `event` отправляет данное [событие](/docs/{{version}}/events) своим слушателям:

    event(new UserRegistered($user));

<a name="method-filled"></a>
#### `filled()` {#collection-method}

Функция `filled` определяет, не является ли данное значение «пустым»:

    filled(0);
    filled(true);
    filled(false);

    // true

    filled('');
    filled('   ');
    filled(null);
    filled(collect());

    // false

Чтобы узнать об обратном методе `filled`, смотрите метод [`blank`](#method-blank).

<a name="method-info"></a>
#### `info()` {#collection-method}

Функция `info` запишет информацию в [журнал](/docs/{{version}}/logging) вашего приложения:

    info('Some helpful information!');

В функцию также может быть передан массив контекстных данных:

    info('User login attempt failed.', ['id' => $user->id]);

<a name="method-logger"></a>
#### `logger()` {#collection-method}

Функцию `logger` можно использовать для записи сообщения уровня `debug` в [журнале](/docs/{{version}}/logging):

    logger('Debug message');

В функцию также может быть передан массив контекстных данных:

    logger('User has logged in.', ['id' => $user->id]);

Экземпляр [logger](/docs/{{version}}/errors#logging) будет возвращен, если в функцию не передано значение:

    logger()->error('You are not allowed here.');

<a name="method-method-field"></a>
#### `method_field()` {#collection-method}

Функция `method_field` генерирует HTML-поле ввода `hidden`, содержащее поддельное значение HTTP-глагола формы. Например, используя [синтаксис Blade](/docs/{{version}}/blade):

    <form method="POST">
        {{ method_field('DELETE') }}
    </form>

<a name="method-now"></a>
#### `now()` {#collection-method}

Функция `now` создает новый экземпляр `Illuminate\Support\Carbon` для текущего времени:

    $now = now();

<a name="method-old"></a>
#### `old()` {#collection-method}

Функция `old` [извлекает](/docs/{{version}}/requests#retrieving-input) значение [старого ввода](/docs/{{version}}/requests#old-input), которое передается в сеанс :

    $value = old('value');

    $value = old('value', 'default');

<a name="method-optional"></a>
#### `optional()` {#collection-method}

Функция `optional` принимает любой аргумент и позволяет вам получать доступ к свойствам или вызывать методы этого объекта. Если данный объект имеет значение `null`, свойства и методы будут возвращать `null` вместо того, чтобы вызывать ошибку:

    return optional($user->address)->street;

    {!! old('name', optional($user)->name) !!}

Функция `optional` также принимает замыкание в качестве второго аргумента. Замыкание будет вызвано, если значение, указанное в качестве первого аргумента, не равно нулю:

    return optional(User::find($id), function ($user) {
        return $user->name;
    });

<a name="method-policy"></a>
#### `policy()` {#collection-method}

Метод `policy` извлекает экземпляр [политики](/docs/{{version}}/authorization#creating-policies) для данного класса:

    $policy = policy(App\Models\User::class);

<a name="method-redirect"></a>
#### `redirect()` {#collection-method}

Функция `redirect` возвращает [HTTP-ответ перенаправления](/docs/{{version}}/responses#redirects) или возвращает экземпляр перенаправителя, если вызывается без аргументов:

    return redirect($to = null, $status = 302, $headers = [], $https = null);

    return redirect('/home');

    return redirect()->route('route.name');

<a name="method-report"></a>
#### `report()` {#collection-method}

Функция `report` сообщит об исключении, используя ваш [обработчик исключений](/docs/{{version}}/errors#the-exception-handler):

    report($e);

Функция `report` также принимает строку в качестве аргумента. Когда в функцию передается строка, функция создает исключение с данной строкой в качестве сообщения:

    report('Something went wrong.');

<a name="method-request"></a>
#### `request()` {#collection-method}

Функция `request` возвращает текущий экземпляр [запроса](/docs/{{version}}/requests) или получает значение поля ввода из текущего запроса:

    $request = request();

    $value = request('key', $default);

<a name="method-rescue"></a>
#### `rescue()` {#collection-method}

Функция `rescue` выполняет заданное замыкание и перехватывает любые исключения, возникающие во время его выполнения. Все перехваченные исключения будут отправлены вашему [обработчику исключений](/docs/{{version}}/errors#the-exception-handler); однако запрос продолжит обработку:

    return rescue(function () {
        return $this->method();
    });

Вы также можете передать второй аргумент функции `rescue`. Этот аргумент будет значением "по умолчанию", которое должно быть возвращено, если во время выполнения замыкания возникает исключение:

    return rescue(function () {
        return $this->method();
    }, false);

    return rescue(function () {
        return $this->method();
    }, function () {
        return $this->failure();
    });

<a name="method-resolve"></a>
#### `resolve()` {#collection-method}

Функция `resolve` преобразует данный класс или имя интерфейса в экземпляр, используя [сервис контенейр](/docs/{{version}}/container):

    $api = resolve('HelpSpot\API');

<a name="method-response"></a>
#### `response()` {#collection-method}

Функция `response` создает экземпляр [ответа](/docs/{{version}}/responses) или получает экземпляр фабрики ответов:

    return response('Hello World', 200, $headers);

    return response()->json(['foo' => 'bar'], 200, $headers);

<a name="method-retry"></a>
#### `retry()` {#collection-method}

Функция `retry` пытается выполнить данный обратный вызов, пока не будет достигнут заданный максимальный порог попытки. Если обратный вызов не вызывает исключения, возвращается его возвращаемое значение. Если обратный вызов вызывает исключение, он будет автоматически повторен. Если максимальное количество попыток превышено, будет сгенерировано исключение:

    return retry(5, function () {
        // Attempt 5 times while resting 100ms in between attempts...
    }, 100);

<a name="method-session"></a>
#### `session()` {#collection-method}

Функцию `session` можно использовать для получения или установки значений [сессий](/docs/{{version}}/session):

    $value = session('key');

Вы можете установить значения, передав в функцию массив пар ключ / значение:

    session(['chairs' => 7, 'instruments' => 3]);

Хранилище сеансов будет возвращено, если функции не передано значение:

    $value = session()->get('key');

    session()->put('key', $value);

<a name="method-tap"></a>
#### `tap()` {#collection-method}

Функция `tap` принимает два аргумента: произвольное `$value` и замыкание. `$value` будет передано в замыкание, а затем будет возвращено функцией `tap`. Возвращаемое значение замыкания не имеет значения:

    $user = tap(User::first(), function ($user) {
        $user->name = 'taylor';

        $user->save();
    });

Если в функцию `tap` не передается замыкание, вы можете вызвать любой метод для данного `$value`. Возвращаемое значение метода, который вы вызываете, всегда будет `$value`, независимо от того, что метод фактически возвращает в своем определении. Например, метод `update` Eloquent обычно возвращает целое число. Однако мы можем заставить метод возвращать саму модель, связав вызов метода `update` через функцию `tap`:

    $user = tap($user)->update([
        'name' => $name,
        'email' => $email,
    ]);

Чтобы добавить к классу метод `tap`, вы можете добавить в класс трейта `Illuminate\Support\Traits\Tappable`. Метод `tap` этой черты принимает замыкание как единственный аргумент. Сам экземпляр объекта будет передан Замыканию, а затем будет возвращен методом `tap`:

    return $user->tap(function ($user) {
        //
    });

<a name="method-throw-if"></a>
#### `throw_if()` {#collection-method}

Функция `throw_if` генерирует данное исключение, если данное логическое выражение оценивается как `true`:

    throw_if(! Auth::user()->isAdmin(), AuthorizationException::class);

    throw_if(
        ! Auth::user()->isAdmin(),
        AuthorizationException::class,
        'You are not allowed to access this page.'
    );

<a name="method-throw-unless"></a>
#### `throw_unless()` {#collection-method}

Функция `throw_unless` генерирует данное исключение, если данное логическое выражение оценивается как `false`:

    throw_unless(Auth::user()->isAdmin(), AuthorizationException::class);

    throw_unless(
        Auth::user()->isAdmin(),
        AuthorizationException::class,
        'You are not allowed to access this page.'
    );

<a name="method-today"></a>
#### `today()` {#collection-method}

Функция `today` создает новый экземпляр `Illuminate\Support\Carbon` для текущей даты:

    $today = today();

<a name="method-trait-uses-recursive"></a>
#### `trait_uses_recursive()` {#collection-method}

Функция `trait_uses_recursive` возвращает все трейты, используемые трейтом:

    $traits = trait_uses_recursive(\Illuminate\Notifications\Notifiable::class);

<a name="method-transform"></a>
#### `transform()` {#collection-method}

Функция `transform` выполняет замыкание для данного значения, если значение не [пустое](#method-blank), а затем возвращает возвращаемое значение замыкания:

    $callback = function ($value) {
        return $value * 2;
    };

    $result = transform(5, $callback);

    // 10

В качестве третьего аргумента функции может быть передано значение по умолчанию или замыкание. Это значение будет возвращено, если данное значение пусто:

    $result = transform(null, $callback, 'The value is blank');

    // The value is blank

<a name="method-validator"></a>
#### `validator()` {#collection-method}

Функция `validator` создает новый экземпляр [валидатора](/docs/{{version}}/validation) с заданными аргументами. Вы можете использовать его как альтернативу фасаду `Validator`:

    $validator = validator($data, $rules, $messages);

<a name="method-value"></a>
#### `value()` {#collection-method}

Функция `value` возвращает заданное значение. Однако, если вы передадите замыкание функции, замыкание будет выполнено, и его возвращенное значение будет возвращено:

    $result = value(true);

    // true

    $result = value(function () {
        return false;
    });

    // false

<a name="method-view"></a>
#### `view()` {#collection-method}

Функция `view` получает экземпляр [представления](/docs/{{version}}/views):

    return view('auth.login');

<a name="method-with"></a>
#### `with()` {#collection-method}

Функция `with` возвращает заданное значение. Если замыкание передается в качестве второго аргумента функции, замыкание будет выполнено, и будет возвращено его возвращаемое значение:

    $callback = function ($value) {
        return (is_numeric($value)) ? $value * 2 : 0;
    };

    $result = with(5, $callback);

    // 10

    $result = with(null, $callback);

    // 0

    $result = with(5, null);

    // 5
