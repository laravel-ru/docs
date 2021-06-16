# Коллекции

- [Введение](#introduction)
    - [Создание коллекций](#creating-collections)
    - [Расширение коллекций](#extending-collections)
- [Доступные методы](#available-methods)
- [Сообщения высшего порядка](#higher-order-messages)
- [Отложенные коллекции](#lazy-collections)
    - [Введение](#lazy-collection-introduction)
    - [Создание отложенных коллекций](#creating-lazy-collections)
    - [Перечисляемый контракт](#the-enumerable-contract)
    - [Методы отложенных коллекций](#lazy-collection-methods)

<a name="introduction"></a>
## Введение

Класс `Illuminate\Support\Collection` предоставляет плавную и удобную оболочку для работы с массивами данных. Например, посмотрите следующий код. Мы воспользуемся помощником `collect` для создания нового экземпляра коллекции из массива, запустим функцию `strtoupper` для каждого элемента, а затем удалим все пустые элементы:

    $collection = collect(['taylor', 'abigail', null])->map(function ($name) {
        return strtoupper($name);
    })->reject(function ($name) {
        return empty($name);
    });

Как видите, класс `Collection` позволяет вам объединять свои методы в цепочку для выполнения плавного сопоставления и сокращения базового массива. В общем, коллекции неизменяемы, что означает, что каждый метод `Collection` возвращает совершенно новый экземпляр `Collection`.

<a name="creating-collections"></a>
### Создание коллекций

Как упоминалось выше, помощник `collect` возвращает новый экземпляр `Illuminate\Support\Collection` для данного массива. Итак, создать коллекцию очень просто:

    $collection = collect([1, 2, 3]);

> {tip} Результаты запросов [Eloquent](/docs/{{version}}/eloquent) всегда возвращаются как экземпляры `Collection`.

<a name="extending-collections"></a>
### Расширение коллекций

Коллекции являются «макросъемочными», что позволяет вам добавлять дополнительные методы к классу `Collection` во время выполнения. Метод  `macro` класса `Illuminate\Support\Collection` принимает замыкание, которое будет выполнено при вызове вашего макроса. Замыкание макроса может получить доступ к другим методам коллекции через `$this`, как если бы это был реальный метод класса коллекции. Например, следующий код добавляет метод `toUpper` к классу `Collection`:

    use Illuminate\Support\Collection;
    use Illuminate\Support\Str;

    Collection::macro('toUpper', function () {
        return $this->map(function ($value) {
            return Str::upper($value);
        });
    });

    $collection = collect(['first', 'second']);

    $upper = $collection->toUpper();

    // ['FIRST', 'SECOND']

Как правило, вы должны объявлять макросы сбора в методе `boot` [сервис провайдера](/docs/{{version}}/providers).

<a name="macro-arguments"></a>
#### Макро-аргументы

При необходимости вы можете определить макросы, которые принимают дополнительные аргументы:

    use Illuminate\Support\Collection;
    use Illuminate\Support\Facades\Lang;
    use Illuminate\Support\Str;

    Collection::macro('toLocale', function ($locale) {
        return $this->map(function ($value) use ($locale) {
            return Lang::get($value, [], $locale);
        });
    });

    $collection = collect(['first', 'second']);

    $translated = $collection->toLocale('es');

<a name="available-methods"></a>
## Доступные методы

Для большей части оставшейся документации по коллекциям мы обсудим каждый метод, доступный в классе `Collection`. Помните, что все эти методы могут быть объединены в цепочку для беспрепятственного управления базовым массивом. Более того, почти каждый метод возвращает новый экземпляр `Collection`, позволяя вам при необходимости сохранить исходную копию коллекции:

<style>
    #collection-method-list > p {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 2em; -moz-column-gap: 2em; -webkit-column-gap: 2em;
    }

    #collection-method-list a {
        display: block;
    }
</style>

<div id="collection-method-list" markdown="1">

[all](#method-all)
[average](#method-average)
[avg](#method-avg)
[chunk](#method-chunk)
[chunkWhile](#method-chunkwhile)
[collapse](#method-collapse)
[collect](#method-collect)
[combine](#method-combine)
[concat](#method-concat)
[contains](#method-contains)
[containsStrict](#method-containsstrict)
[count](#method-count)
[countBy](#method-countBy)
[crossJoin](#method-crossjoin)
[dd](#method-dd)
[diff](#method-diff)
[diffAssoc](#method-diffassoc)
[diffKeys](#method-diffkeys)
[dump](#method-dump)
[duplicates](#method-duplicates)
[duplicatesStrict](#method-duplicatesstrict)
[each](#method-each)
[eachSpread](#method-eachspread)
[every](#method-every)
[except](#method-except)
[filter](#method-filter)
[first](#method-first)
[firstWhere](#method-first-where)
[flatMap](#method-flatmap)
[flatten](#method-flatten)
[flip](#method-flip)
[forget](#method-forget)
[forPage](#method-forpage)
[get](#method-get)
[groupBy](#method-groupby)
[has](#method-has)
[implode](#method-implode)
[intersect](#method-intersect)
[intersectByKeys](#method-intersectbykeys)
[isEmpty](#method-isempty)
[isNotEmpty](#method-isnotempty)
[join](#method-join)
[keyBy](#method-keyby)
[keys](#method-keys)
[last](#method-last)
[macro](#method-macro)
[make](#method-make)
[map](#method-map)
[mapInto](#method-mapinto)
[mapSpread](#method-mapspread)
[mapToGroups](#method-maptogroups)
[mapWithKeys](#method-mapwithkeys)
[max](#method-max)
[median](#method-median)
[merge](#method-merge)
[mergeRecursive](#method-mergerecursive)
[min](#method-min)
[mode](#method-mode)
[nth](#method-nth)
[only](#method-only)
[pad](#method-pad)
[partition](#method-partition)
[pipe](#method-pipe)
[pipeInto](#method-pipeinto)
[pluck](#method-pluck)
[pop](#method-pop)
[prepend](#method-prepend)
[pull](#method-pull)
[push](#method-push)
[put](#method-put)
[random](#method-random)
[reduce](#method-reduce)
[reject](#method-reject)
[replace](#method-replace)
[replaceRecursive](#method-replacerecursive)
[reverse](#method-reverse)
[search](#method-search)
[shift](#method-shift)
[shuffle](#method-shuffle)
[skip](#method-skip)
[skipUntil](#method-skipuntil)
[skipWhile](#method-skipwhile)
[slice](#method-slice)
[sole](#method-sole)
[some](#method-some)
[sort](#method-sort)
[sortBy](#method-sortby)
[sortByDesc](#method-sortbydesc)
[sortDesc](#method-sortdesc)
[sortKeys](#method-sortkeys)
[sortKeysDesc](#method-sortkeysdesc)
[splice](#method-splice)
[split](#method-split)
[splitIn](#method-splitin)
[sum](#method-sum)
[take](#method-take)
[takeUntil](#method-takeuntil)
[takeWhile](#method-takewhile)
[tap](#method-tap)
[times](#method-times)
[toArray](#method-toarray)
[toJson](#method-tojson)
[transform](#method-transform)
[union](#method-union)
[unique](#method-unique)
[uniqueStrict](#method-uniquestrict)
[unless](#method-unless)
[unlessEmpty](#method-unlessempty)
[unlessNotEmpty](#method-unlessnotempty)
[unwrap](#method-unwrap)
[values](#method-values)
[when](#method-when)
[whenEmpty](#method-whenempty)
[whenNotEmpty](#method-whennotempty)
[where](#method-where)
[whereStrict](#method-wherestrict)
[whereBetween](#method-wherebetween)
[whereIn](#method-wherein)
[whereInStrict](#method-whereinstrict)
[whereInstanceOf](#method-whereinstanceof)
[whereNotBetween](#method-wherenotbetween)
[whereNotIn](#method-wherenotin)
[whereNotInStrict](#method-wherenotinstrict)
[whereNotNull](#method-wherenotnull)
[whereNull](#method-wherenull)
[wrap](#method-wrap)
[zip](#method-zip)

</div>

<a name="method-listing"></a>
## Method Listing

<style>
    #collection-method code {
        font-size: 14px;
    }

    #collection-method:not(.first-collection-method) {
        margin-top: 50px;
    }
</style>

<a name="method-all"></a>
#### `all()` {#collection-method .first-collection-method}

Метод `all` возвращает базовый массив, представленный коллекцией:

    collect([1, 2, 3])->all();

    // [1, 2, 3]

<a name="method-average"></a>
#### `average()` {#collection-method}

Псевдоним для метода [`avg`](#method-avg).

<a name="method-avg"></a>
#### `avg()` {#collection-method}

Метод `avg` возвращает [среднее значение](https://en.wikipedia.org/wiki/Average) заданного ключа:

    $average = collect([
        ['foo' => 10],
        ['foo' => 10],
        ['foo' => 20],
        ['foo' => 40]
    ])->avg('foo');

    // 20

    $average = collect([1, 1, 2, 4])->avg();

    // 2

<a name="method-chunk"></a>
#### `chunk()` {#collection-method}

Метод `chunk` разбивает коллекцию на несколько меньших коллекций заданного размера:

    $collection = collect([1, 2, 3, 4, 5, 6, 7]);

    $chunks = $collection->chunk(4);

    $chunks->all();

    // [[1, 2, 3, 4], [5, 6, 7]]

Этот метод особенно полезен в [представлениях](/docs/{{version}}/views) при работе с сеткой, такой как [Bootstrap](https://getbootstrap.com/docs/4.1/layout/grid/). Например, представьте, что у вас есть набор моделей [Eloquent](/docs/{{version}}/eloquent), которые вы хотите отобразить в сетке:

    @foreach ($products->chunk(3) as $chunk)
        <div class="row">
            @foreach ($chunk as $product)
                <div class="col-xs-4">{{ $product->name }}</div>
            @endforeach
        </div>
    @endforeach

<a name="method-chunkwhile"></a>
#### `chunkWhile()` {#collection-method}

Метод `chunkWhile` разбивает коллекцию на несколько меньших по размеру коллекций на основе оценки данного обратного вызова. Переменная `$chunk`, переданная в замыкание, может использоваться для проверки предыдущего элемента:

    $collection = collect(str_split('AABBCCCD'));

    $chunks = $collection->chunkWhile(function ($value, $key, $chunk) {
        return $value === $chunk->last();
    });

    $chunks->all();

    // [['A', 'A'], ['B', 'B'], ['C', 'C', 'C'], ['D']]

<a name="method-collapse"></a>
#### `collapse()` {#collection-method}

Метод `collapse` сворачивает коллекцию массивов в единую плоскую коллекцию:

    $collection = collect([
        [1, 2, 3],
        [4, 5, 6],
        [7, 8, 9],
    ]);

    $collapsed = $collection->collapse();

    $collapsed->all();

    // [1, 2, 3, 4, 5, 6, 7, 8, 9]

<a name="method-collect"></a>
#### `collect()` {#collection-method}

Метод `collect` возвращает новый экземпляр `Collection` с элементами, находящимися в настоящее время в коллекции:

    $collectionA = collect([1, 2, 3]);

    $collectionB = $collectionA->collect();

    $collectionB->all();

    // [1, 2, 3]

Метод `collect` в первую очередь полезен для преобразования [отложенных коллекций](#lazy-collections) в стандартные экземпляры `Collection`:

    $lazyCollection = LazyCollection::make(function () {
        yield 1;
        yield 2;
        yield 3;
    });

    $collection = $lazyCollection->collect();

    get_class($collection);

    // 'Illuminate\Support\Collection'

    $collection->all();

    // [1, 2, 3]

> {tip} Метод `collect` особенно полезен, когда у вас есть экземпляр `Enumerable` и вам нужен экземпляр не отложенной коллекции. Поскольку `collect()` является частью контракта `Enumerable`, вы можете безопасно использовать его для получения экземпляра `Collection`.

<a name="method-combine"></a>
#### `combine()` {#collection-method}

Метод `combine` объединяет значения коллекции в качестве ключей со значениями другого массива или коллекции:

    $collection = collect(['name', 'age']);

    $combined = $collection->combine(['George', 29]);

    $combined->all();

    // ['name' => 'George', 'age' => 29]

<a name="method-concat"></a>
#### `concat()` {#collection-method}

Метод `concat` добавляет значения данного массива `array` или коллекции в конец другой коллекции:

    $collection = collect(['John Doe']);

    $concatenated = $collection->concat(['Jane Doe'])->concat(['name' => 'Johnny Doe']);

    $concatenated->all();

    // ['John Doe', 'Jane Doe', 'Johnny Doe']

<a name="method-contains"></a>
#### `contains()` {#collection-method}

Метод `contains` определяет, содержит ли коллекция данный элемент. Вы можете передать замыкание методу `contains`, чтобы определить, существует ли в коллекции элемент, соответствующий заданному критерию истинности:

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->contains(function ($value, $key) {
        return $value > 5;
    });

    // false

В качестве альтернативы вы можете передать строку методу `contains`, чтобы определить, содержит ли коллекция заданное значение элемента:

    $collection = collect(['name' => 'Desk', 'price' => 100]);

    $collection->contains('Desk');

    // true

    $collection->contains('New York');

    // false

Вы также можете передать пару ключ / значение методу `contains`, который определит, существует ли данная пара в коллекции:

    $collection = collect([
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Chair', 'price' => 100],
    ]);

    $collection->contains('product', 'Bookcase');

    // false

Метод `contains` использует "свободные" сравнения при проверке значений элементов, то есть строка с целочисленным значением будет считаться равной целому числу того же значения. Используйте метод [`containsStrict`](#method-containsstrict) для фильтрации с использованием «строгих» сравнений.

<a name="method-containsstrict"></a>
#### `containsStrict()` {#collection-method}

Этот метод имеет ту же сигнатуру, что и метод [`contains`](#method-contains); однако все значения сравниваются с использованием «строгих» сравнений.

> {tip} Поведение этого метода изменяется при использовании [Eloquent Collections](/docs/{{version}}/eloquent-collections#method-contains).

<a name="method-count"></a>
#### `count()` {#collection-method}

Метод `count` возвращает общее количество элементов в коллекции:

    $collection = collect([1, 2, 3, 4]);

    $collection->count();

    // 4

<a name="method-countBy"></a>
#### `countBy()` {#collection-method}

Метод `countBy` подсчитывает вхождения значений в коллекцию. По умолчанию метод подсчитывает вхождения каждого элемента, что позволяет вам подсчитывать определенные «типы» элементов в коллекции:

    $collection = collect([1, 2, 2, 2, 3]);

    $counted = $collection->countBy();

    $counted->all();

    // [1 => 1, 2 => 3, 3 => 1]

Вы передаете замыкание методу `countBy` для подсчета всех элементов по заданному значению:

    $collection = collect(['alice@gmail.com', 'bob@yahoo.com', 'carlos@gmail.com']);

    $counted = $collection->countBy(function ($email) {
        return substr(strrchr($email, "@"), 1);
    });

    $counted->all();

    // ['gmail.com' => 2, 'yahoo.com' => 1]

<a name="method-crossjoin"></a>
#### `crossJoin()` {#collection-method}

Метод `crossJoin` объединяет значения коллекции среди заданных массивов или коллекций, возвращая декартово произведение со всеми возможными перестановками:

    $collection = collect([1, 2]);

    $matrix = $collection->crossJoin(['a', 'b']);

    $matrix->all();

    /*
        [
            [1, 'a'],
            [1, 'b'],
            [2, 'a'],
            [2, 'b'],
        ]
    */

    $collection = collect([1, 2]);

    $matrix = $collection->crossJoin(['a', 'b'], ['I', 'II']);

    $matrix->all();

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

<a name="method-dd"></a>
#### `dd()` {#collection-method}

Метод `dd` выгружает элементы коллекции и завершает выполнение скрипта:

    $collection = collect(['John Doe', 'Jane Doe']);

    $collection->dd();

    /*
        Collection {
            #items: array:2 [
                0 => "John Doe"
                1 => "Jane Doe"
            ]
        }
    */

Если вы не хотите останавливать выполнение сценария, используйте вместо него метод [`dump`](#method-dump).

<a name="method-diff"></a>
#### `diff()` {#collection-method}

Метод `diff` сравнивает коллекцию с другой коллекцией или простым массивом PHP `array` на основе его значений. Этот метод вернет значения в исходной коллекции, которых нет в данной коллекции:

    $collection = collect([1, 2, 3, 4, 5]);

    $diff = $collection->diff([2, 4, 6, 8]);

    $diff->all();

    // [1, 3, 5]

> {tip} Поведение этого метода изменяется при использовании [Eloquent Collections](/docs/{{version}}/eloquent-collections#method-diff).

<a name="method-diffassoc"></a>
#### `diffAssoc()` {#collection-method}

Метод `diffAssoc` сравнивает коллекцию с другой коллекцией или простым массивом PHP `array` на основе его ключей и значений. Этот метод вернет пары ключ / значение в исходной коллекции, которых нет в данной коллекции:

    $collection = collect([
        'color' => 'orange',
        'type' => 'fruit',
        'remain' => 6,
    ]);

    $diff = $collection->diffAssoc([
        'color' => 'yellow',
        'type' => 'fruit',
        'remain' => 3,
        'used' => 6,
    ]);

    $diff->all();

    // ['color' => 'orange', 'remain' => 6]

<a name="method-diffkeys"></a>
#### `diffKeys()` {#collection-method}

Метод `diffKeys` сравнивает коллекцию с другой коллекцией или простым массивом PHP `array` на основе его ключей. Этот метод вернет пары ключ / значение в исходной коллекции, которых нет в данной коллекции:

    $collection = collect([
        'one' => 10,
        'two' => 20,
        'three' => 30,
        'four' => 40,
        'five' => 50,
    ]);

    $diff = $collection->diffKeys([
        'two' => 2,
        'four' => 4,
        'six' => 6,
        'eight' => 8,
    ]);

    $diff->all();

    // ['one' => 10, 'three' => 30, 'five' => 50]

<a name="method-dump"></a>
#### `dump()` {#collection-method}

Метод `dump` выгружает элементы коллекции:

    $collection = collect(['John Doe', 'Jane Doe']);

    $collection->dump();

    /*
        Collection {
            #items: array:2 [
                0 => "John Doe"
                1 => "Jane Doe"
            ]
        }
    */

Если вы хотите остановить выполнение сценария после сброса коллекции, используйте вместо него метод [`dd`](#method-dd).

<a name="method-duplicates"></a>
#### `duplicates()` {#collection-method}

Метод `duplicates` извлекает и возвращает повторяющиеся значения из коллекции:

    $collection = collect(['a', 'b', 'a', 'c', 'b']);

    $collection->duplicates();

    // [2 => 'a', 4 => 'b']

Если коллекция содержит массивы или объекты, вы можете передать ключ атрибутов, которые вы хотите проверить на наличие повторяющихся значений:

    $employees = collect([
        ['email' => 'abigail@example.com', 'position' => 'Developer'],
        ['email' => 'james@example.com', 'position' => 'Designer'],
        ['email' => 'victoria@example.com', 'position' => 'Developer'],
    ])

    $employees->duplicates('position');

    // [2 => 'Developer']

<a name="method-duplicatesstrict"></a>
#### `duplicatesStrict()` {#collection-method}

Этот метод имеет ту же сигнатуру, что и метод [`duplicates`](#method-duplicates); однако все значения сравниваются с использованием «строгих» сравнений.

<a name="method-each"></a>
#### `each()` {#collection-method}

Метод `each` выполняет итерацию по элементам в коллекции и передает каждый элемент в замыкание:

    $collection->each(function ($item, $key) {
        //
    });

Если вы хотите прекратить итерацию по элементам, вы можете вернуть `false` из вашего замыкания:

    $collection->each(function ($item, $key) {
        if (/* condition */) {
            return false;
        }
    });

<a name="method-eachspread"></a>
#### `eachSpread()` {#collection-method}

Метод `eachSpread` выполняет итерацию по элементам коллекции, передавая значение каждого вложенного элемента в заданный обратный вызов:

    $collection = collect([['John Doe', 35], ['Jane Doe', 33]]);

    $collection->eachSpread(function ($name, $age) {
        //
    });

Вы можете прекратить итерацию по элементам, вернув `false` из обратного вызова:

    $collection->eachSpread(function ($name, $age) {
        return false;
    });

<a name="method-every"></a>
#### `every()` {#collection-method}

Метод `every` может использоваться для проверки того, что все элементы коллекции проходят заданный тест истинности:

    collect([1, 2, 3, 4])->every(function ($value, $key) {
        return $value > 2;
    });

    // false

Если коллекция пуста, метод `every` вернет `true`:

    $collection = collect([]);

    $collection->every(function ($value, $key) {
        return $value > 2;
    });

    // true

<a name="method-except"></a>
#### `except()` {#collection-method}

Метод `except` возвращает все элементы в коллекции, кроме тех, которые имеют указанные ключи:

    $collection = collect(['product_id' => 1, 'price' => 100, 'discount' => false]);

    $filtered = $collection->except(['price', 'discount']);

    $filtered->all();

    // ['product_id' => 1]

Чтобы узнать об обратном к `except`, смотрите метод [only](#method-only).

> {tip} Поведение этого метода изменяется при использовании [Eloquent Collections](/docs/{{version}}/eloquent-collections#method-except).

<a name="method-filter"></a>
#### `filter()` {#collection-method}

Метод `filter` фильтрует коллекцию с помощью заданного обратного вызова, сохраняя только те элементы, которые проходят заданный тест на истинность:

    $collection = collect([1, 2, 3, 4]);

    $filtered = $collection->filter(function ($value, $key) {
        return $value > 2;
    });

    $filtered->all();

    // [3, 4]

Если обратный вызов не предоставлен, все записи коллекции, эквивалентные `false`, будут удалены:

    $collection = collect([1, 2, 3, null, false, '', 0, []]);

    $collection->filter()->all();

    // [1, 2, 3]

Для обратного к `filter`, смотрите метод [reject](#method-reject).

<a name="method-first"></a>
#### `first()` {#collection-method}

Метод `first` возвращает первый элемент в коллекции, который проходит заданную проверку истинности:

    collect([1, 2, 3, 4])->first(function ($value, $key) {
        return $value > 2;
    });

    // 3

Вы также можете вызвать метод `first` без аргументов, чтобы получить первый элемент в коллекции. Если коллекция пуста, возвращается `null`:

    collect([1, 2, 3, 4])->first();

    // 1

<a name="method-first-where"></a>
#### `firstWhere()` {#collection-method}

Метод `firstWhere` возвращает первый элемент коллекции с заданной парой ключ / значение:

    $collection = collect([
        ['name' => 'Regena', 'age' => null],
        ['name' => 'Linda', 'age' => 14],
        ['name' => 'Diego', 'age' => 23],
        ['name' => 'Linda', 'age' => 84],
    ]);

    $collection->firstWhere('name', 'Linda');

    // ['name' => 'Linda', 'age' => 14]

Вы также можете вызвать метод `firstWhere` с оператором сравнения:

    $collection->firstWhere('age', '>=', 18);

    // ['name' => 'Diego', 'age' => 23]

Подобно методу [where](#method-where), вы можете передать один аргумент методу `firstWhere`. В этом сценарии метод `firstWhere` вернет первый элемент, для которого значение данного ключа элемента является «истинным»:

    $collection->firstWhere('age');

    // ['name' => 'Linda', 'age' => 14]

<a name="method-flatmap"></a>
#### `flatMap()` {#collection-method}

Метод `flatMap` выполняет итерацию по коллекции и передает каждое значение заданному замыканию. Замыкание может изменить элемент и вернуть его, таким образом формируя новую коллекцию измененных элементов. Затем массив выравнивается на один уровень:

    $collection = collect([
        ['name' => 'Sally'],
        ['school' => 'Arkansas'],
        ['age' => 28]
    ]);

    $flattened = $collection->flatMap(function ($values) {
        return array_map('strtoupper', $values);
    });

    $flattened->all();

    // ['name' => 'SALLY', 'school' => 'ARKANSAS', 'age' => '28'];

<a name="method-flatten"></a>
#### `flatten()` {#collection-method}

Метод `flatten` сводит многомерную коллекцию в одно измерение:

    $collection = collect([
        'name' => 'taylor',
        'languages' => [
            'php', 'javascript'
        ]
    ]);

    $flattened = $collection->flatten();

    $flattened->all();

    // ['taylor', 'php', 'javascript'];

При необходимости вы можете передать методу `flatten` аргумент "глубины":

    $collection = collect([
        'Apple' => [
            [
                'name' => 'iPhone 6S',
                'brand' => 'Apple'
            ],
        ],
        'Samsung' => [
            [
                'name' => 'Galaxy S7',
                'brand' => 'Samsung'
            ],
        ],
    ]);

    $products = $collection->flatten(1);

    $products->values()->all();

    /*
        [
            ['name' => 'iPhone 6S', 'brand' => 'Apple'],
            ['name' => 'Galaxy S7', 'brand' => 'Samsung'],
        ]
    */

В этом примере вызов `flatten` без предоставления глубины также привел бы к сглаживанию вложенных массивов, что привело бы к `['iPhone 6S', 'Apple', 'Galaxy S7', 'Samsung']`. Предоставление глубины позволяет указать количество уровней, на которые будут сглажены вложенные массивы.

<a name="method-flip"></a>
#### `flip()` {#collection-method}

Метод `flip` меняет местами ключи коллекции на их соответствующие значения:

    $collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

    $flipped = $collection->flip();

    $flipped->all();

    // ['taylor' => 'name', 'laravel' => 'framework']

<a name="method-forget"></a>
#### `forget()` {#collection-method}

Метод `forget` удаляет элемент из коллекции по его ключу:

    $collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

    $collection->forget('name');

    $collection->all();

    // ['framework' => 'laravel']

> {note} В отличие от большинства других методов сбора, `forget` не возвращает новую измененную коллекцию; он изменяет вызываемую коллекцию.

<a name="method-forpage"></a>
#### `forPage()` {#collection-method}

Метод `forPage` возвращает новую коллекцию, содержащую элементы, которые будут присутствовать на заданном номере страницы. Метод принимает номер страницы в качестве первого аргумента и количество элементов, отображаемых на странице, в качестве второго аргумента:

    $collection = collect([1, 2, 3, 4, 5, 6, 7, 8, 9]);

    $chunk = $collection->forPage(2, 3);

    $chunk->all();

    // [4, 5, 6]

<a name="method-get"></a>
#### `get()` {#collection-method}

Метод `get` возвращает элемент по заданному ключу. Если ключ не существует, возвращается `null`:

    $collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

    $value = $collection->get('name');

    // taylor

При желании вы можете передать значение по умолчанию в качестве второго аргумента:

    $collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

    $value = $collection->get('age', 34);

    // 34

Вы даже можете передать обратный вызов в качестве значения метода по умолчанию. Результат обратного вызова будет возвращен, если указанный ключ не существует:

    $collection->get('email', function () {
        return 'taylor@example.com';
    });

    // taylor@example.com

<a name="method-groupby"></a>
#### `groupBy()` {#collection-method}

Метод `groupBy` группирует элементы коллекции по заданному ключу:

    $collection = collect([
        ['account_id' => 'account-x10', 'product' => 'Chair'],
        ['account_id' => 'account-x10', 'product' => 'Bookcase'],
        ['account_id' => 'account-x11', 'product' => 'Desk'],
    ]);

    $grouped = $collection->groupBy('account_id');

    $grouped->all();

    /*
        [
            'account-x10' => [
                ['account_id' => 'account-x10', 'product' => 'Chair'],
                ['account_id' => 'account-x10', 'product' => 'Bookcase'],
            ],
            'account-x11' => [
                ['account_id' => 'account-x11', 'product' => 'Desk'],
            ],
        ]
    */

Вместо передачи строкового ключа `key` вы можете передать обратный вызов. Обратный вызов должен вернуть значение, которое вы хотите задать для группы:

    $grouped = $collection->groupBy(function ($item, $key) {
        return substr($item['account_id'], -3);
    });

    $grouped->all();

    /*
        [
            'x10' => [
                ['account_id' => 'account-x10', 'product' => 'Chair'],
                ['account_id' => 'account-x10', 'product' => 'Bookcase'],
            ],
            'x11' => [
                ['account_id' => 'account-x11', 'product' => 'Desk'],
            ],
        ]
    */

Несколько критериев группировки могут быть переданы в виде массива. Каждый элемент массива будет применен к соответствующему уровню в многомерном массиве:

    $data = new Collection([
        10 => ['user' => 1, 'skill' => 1, 'roles' => ['Role_1', 'Role_3']],
        20 => ['user' => 2, 'skill' => 1, 'roles' => ['Role_1', 'Role_2']],
        30 => ['user' => 3, 'skill' => 2, 'roles' => ['Role_1']],
        40 => ['user' => 4, 'skill' => 2, 'roles' => ['Role_2']],
    ]);

    $result = $data->groupBy(['skill', function ($item) {
        return $item['roles'];
    }], $preserveKeys = true);

    /*
    [
        1 => [
            'Role_1' => [
                10 => ['user' => 1, 'skill' => 1, 'roles' => ['Role_1', 'Role_3']],
                20 => ['user' => 2, 'skill' => 1, 'roles' => ['Role_1', 'Role_2']],
            ],
            'Role_2' => [
                20 => ['user' => 2, 'skill' => 1, 'roles' => ['Role_1', 'Role_2']],
            ],
            'Role_3' => [
                10 => ['user' => 1, 'skill' => 1, 'roles' => ['Role_1', 'Role_3']],
            ],
        ],
        2 => [
            'Role_1' => [
                30 => ['user' => 3, 'skill' => 2, 'roles' => ['Role_1']],
            ],
            'Role_2' => [
                40 => ['user' => 4, 'skill' => 2, 'roles' => ['Role_2']],
            ],
        ],
    ];
    */

<a name="method-has"></a>
#### `has()` {#collection-method}

Метод `has` определяет, существует ли данный ключ в коллекции:

    $collection = collect(['account_id' => 1, 'product' => 'Desk', 'amount' => 5]);

    $collection->has('product');

    // true

    $collection->has(['product', 'amount']);

    // true

    $collection->has(['amount', 'price']);

    // false

<a name="method-implode"></a>
#### `implode()` {#collection-method}

Метод `implode` объединяет элементы в коллекцию. Его аргументы зависят от типа элементов в коллекции. Если коллекция содержит массивы или объекты, вы должны передать ключ атрибутов, которые вы хотите объединить, и «связующую» строку, которую вы хотите разместить между значениями:

    $collection = collect([
        ['account_id' => 1, 'product' => 'Desk'],
        ['account_id' => 2, 'product' => 'Chair'],
    ]);

    $collection->implode('product', ', ');

    // Desk, Chair

Если коллекция содержит простые строки или числовые значения, вы должны передать "glue" в качестве единственного аргумента метода:

    collect([1, 2, 3, 4, 5])->implode('-');

    // '1-2-3-4-5'

<a name="method-intersect"></a>
#### `intersect()` {#collection-method}

Метод `intersect` удаляет любые значения из исходной коллекции, которых нет в данном `array` или коллекции. Полученная коллекция сохранит ключи исходной коллекции:

    $collection = collect(['Desk', 'Sofa', 'Chair']);

    $intersect = $collection->intersect(['Desk', 'Chair', 'Bookcase']);

    $intersect->all();

    // [0 => 'Desk', 2 => 'Chair']

> {tip} Поведение этого метода изменяется при использовании [Eloquent Collections](/docs/{{version}}/eloquent-collections#method-intersect).

<a name="method-intersectbykeys"></a>
#### `intersectByKeys()` {#collection-method}

Метод `intersectByKeys` удаляет все ключи и соответствующие им значения из исходной коллекции, которые не присутствуют в данном `array` или коллекции:

    $collection = collect([
        'serial' => 'UX301', 'type' => 'screen', 'year' => 2009,
    ]);

    $intersect = $collection->intersectByKeys([
        'reference' => 'UX404', 'type' => 'tab', 'year' => 2011,
    ]);

    $intersect->all();

    // ['type' => 'screen', 'year' => 2009]

<a name="method-isempty"></a>
#### `isEmpty()` {#collection-method}

Метод `isEmpty` возвращает `true`, если коллекция пуста; в противном случае возвращается `false`:

    collect([])->isEmpty();

    // true

<a name="method-isnotempty"></a>
#### `isNotEmpty()` {#collection-method}

Метод `isNotEmpty` возвращает `true`, если коллекция не пуста; в противном случае возвращается `false`:

    collect([])->isNotEmpty();

    // false

<a name="method-join"></a>
#### `join()` {#collection-method}

Метод `join` объединяет значения коллекции в строку. Используя второй аргумент этого метода, вы также можете указать, как последний элемент должен быть добавлен к строке:

    collect(['a', 'b', 'c'])->join(', '); // 'a, b, c'
    collect(['a', 'b', 'c'])->join(', ', ', and '); // 'a, b, and c'
    collect(['a', 'b'])->join(', ', ' and '); // 'a and b'
    collect(['a'])->join(', ', ' and '); // 'a'
    collect([])->join(', ', ' and '); // ''

<a name="method-keyby"></a>
#### `keyBy()` {#collection-method}

Метод `keyBy` передает коллекцию заданным ключом. Если у нескольких элементов один и тот же ключ, в новой коллекции появится только последний:

    $collection = collect([
        ['product_id' => 'prod-100', 'name' => 'Desk'],
        ['product_id' => 'prod-200', 'name' => 'Chair'],
    ]);

    $keyed = $collection->keyBy('product_id');

    $keyed->all();

    /*
        [
            'prod-100' => ['product_id' => 'prod-100', 'name' => 'Desk'],
            'prod-200' => ['product_id' => 'prod-200', 'name' => 'Chair'],
        ]
    */

Вы также можете передать методу обратный вызов. Обратный вызов должен возвращать значение для ключа коллекции:

    $keyed = $collection->keyBy(function ($item) {
        return strtoupper($item['product_id']);
    });

    $keyed->all();

    /*
        [
            'PROD-100' => ['product_id' => 'prod-100', 'name' => 'Desk'],
            'PROD-200' => ['product_id' => 'prod-200', 'name' => 'Chair'],
        ]
    */

<a name="method-keys"></a>
#### `keys()` {#collection-method}

Метод `keys` возвращает все ключи коллекции:

    $collection = collect([
        'prod-100' => ['product_id' => 'prod-100', 'name' => 'Desk'],
        'prod-200' => ['product_id' => 'prod-200', 'name' => 'Chair'],
    ]);

    $keys = $collection->keys();

    $keys->all();

    // ['prod-100', 'prod-200']

<a name="method-last"></a>
#### `last()` {#collection-method}

Метод `last` возвращает последний элемент в коллекции, который проходит заданный тест на истинность:

    collect([1, 2, 3, 4])->last(function ($value, $key) {
        return $value < 3;
    });

    // 2

Вы также можете вызвать метод `last` без аргументов, чтобы получить последний элемент в коллекции. Если коллекция пуста, возвращается `null`:

    collect([1, 2, 3, 4])->last();

    // 4

<a name="method-macro"></a>
#### `macro()` {#collection-method}

Статический метод `macro` позволяет вам добавлять методы к классу `Collection` во время выполнения. Обратитесь к документации по [расширению коллекций](#extending-collections) для получения дополнительной информации.

<a name="method-make"></a>
#### `make()` {#collection-method}

Статический метод `make` создает новый экземпляр коллекции. Смотрите раздел [Создание коллекций](#creating-collections).

<a name="method-map"></a>
#### `map()` {#collection-method}

Метод `map` выполняет итерацию по коллекции и передает каждое значение в заданный обратный вызов. Обратный вызов может изменять элемент и возвращать его, тем самым формируя новую коллекцию измененных элементов:

    $collection = collect([1, 2, 3, 4, 5]);

    $multiplied = $collection->map(function ($item, $key) {
        return $item * 2;
    });

    $multiplied->all();

    // [2, 4, 6, 8, 10]

> {note} Как и большинство других методов коллекции, `map` возвращает новый экземпляр коллекции; он не изменяет вызываемую коллекцию. Если вы хотите преобразовать исходную коллекцию, используйте метод [`transform`](#method-transform) method.

<a name="method-mapinto"></a>
#### `mapInto()` {#collection-method}

Метод `mapInto()` выполняет итерацию по коллекции, создавая новый экземпляр данного класса, передавая значение в конструктор:

    class Currency
    {
        /**
         * Create a new currency instance.
         *
         * @param  string  $code
         * @return void
         */
        function __construct(string $code)
        {
            $this->code = $code;
        }
    }

    $collection = collect(['USD', 'EUR', 'GBP']);

    $currencies = $collection->mapInto(Currency::class);

    $currencies->all();

    // [Currency('USD'), Currency('EUR'), Currency('GBP')]

<a name="method-mapspread"></a>
#### `mapSpread()` {#collection-method}

Метод `mapSpread` выполняет итерацию по элементам коллекции, передавая значение каждого вложенного элемента в заданное замыкание. Замыкание может изменить элемент и вернуть его, таким образом формируя новую коллекцию измененных элементов:

    $collection = collect([0, 1, 2, 3, 4, 5, 6, 7, 8, 9]);

    $chunks = $collection->chunk(2);

    $sequence = $chunks->mapSpread(function ($even, $odd) {
        return $even + $odd;
    });

    $sequence->all();

    // [1, 5, 9, 13, 17]

<a name="method-maptogroups"></a>
#### `mapToGroups()` {#collection-method}

Метод `mapToGroups` группирует элементы коллекции по заданному замыканию. Замыкание должно возвращать ассоциативный массив, содержащий одну пару ключ / значение, таким образом формируя новую коллекцию сгруппированных значений:

    $collection = collect([
        [
            'name' => 'John Doe',
            'department' => 'Sales',
        ],
        [
            'name' => 'Jane Doe',
            'department' => 'Sales',
        ],
        [
            'name' => 'Johnny Doe',
            'department' => 'Marketing',
        ]
    ]);

    $grouped = $collection->mapToGroups(function ($item, $key) {
        return [$item['department'] => $item['name']];
    });

    $grouped->all();

    /*
        [
            'Sales' => ['John Doe', 'Jane Doe'],
            'Marketing' => ['Johnny Doe'],
        ]
    */

    $grouped->get('Sales')->all();

    // ['John Doe', 'Jane Doe']

<a name="method-mapwithkeys"></a>
#### `mapWithKeys()` {#collection-method}

Метод `mapWithKeys` выполняет итерацию по коллекции и передает каждое значение в заданный обратный вызов. Обратный вызов должен возвращать ассоциативный массив, содержащий одну пару ключ / значение:

    $collection = collect([
        [
            'name' => 'John',
            'department' => 'Sales',
            'email' => 'john@example.com',
        ],
        [
            'name' => 'Jane',
            'department' => 'Marketing',
            'email' => 'jane@example.com',
        ]
    ]);

    $keyed = $collection->mapWithKeys(function ($item) {
        return [$item['email'] => $item['name']];
    });

    $keyed->all();

    /*
        [
            'john@example.com' => 'John',
            'jane@example.com' => 'Jane',
        ]
    */

<a name="method-max"></a>
#### `max()` {#collection-method}

Метод `max` возвращает максимальное значение данного ключа:

    $max = collect([
        ['foo' => 10],
        ['foo' => 20]
    ])->max('foo');

    // 20

    $max = collect([1, 2, 3, 4, 5])->max();

    // 5

<a name="method-median"></a>
#### `median()` {#collection-method}

Метод `median` возвращает [среднее значение](https://en.wikipedia.org/wiki/Median) заданного ключа:

    $median = collect([
        ['foo' => 10],
        ['foo' => 10],
        ['foo' => 20],
        ['foo' => 40]
    ])->median('foo');

    // 15

    $median = collect([1, 1, 2, 4])->median();

    // 1.5

<a name="method-merge"></a>
#### `merge()` {#collection-method}

Метод `merge` объединяет данный массив или коллекцию с исходной коллекцией. Если строковый ключ в данных элементах совпадает со строковым ключом в исходной коллекции, значение данного элемента перезапишет значение в исходной коллекции:

    $collection = collect(['product_id' => 1, 'price' => 100]);

    $merged = $collection->merge(['price' => 200, 'discount' => false]);

    $merged->all();

    // ['product_id' => 1, 'price' => 200, 'discount' => false]

Если ключи данных элементов являются числовыми, значения будут добавлены в конец коллекции:

    $collection = collect(['Desk', 'Chair']);

    $merged = $collection->merge(['Bookcase', 'Door']);

    $merged->all();

    // ['Desk', 'Chair', 'Bookcase', 'Door']

<a name="method-mergerecursive"></a>
#### `mergeRecursive()` {#collection-method}

Метод `mergeRecursive` рекурсивно объединяет данный массив или коллекцию с исходной коллекцией. Если строковый ключ в данных элементах совпадает со строковым ключом в исходной коллекции, то значения этих ключей объединяются в массив, и это делается рекурсивно:

    $collection = collect(['product_id' => 1, 'price' => 100]);

    $merged = $collection->mergeRecursive([
        'product_id' => 2,
        'price' => 200,
        'discount' => false
    ]);

    $merged->all();

    // ['product_id' => [1, 2], 'price' => [100, 200], 'discount' => false]

<a name="method-min"></a>
#### `min()` {#collection-method}

Метод `min` возвращает минимальное значение данного ключа:

    $min = collect([['foo' => 10], ['foo' => 20]])->min('foo');

    // 10

    $min = collect([1, 2, 3, 4, 5])->min();

    // 1

<a name="method-mode"></a>
#### `mode()` {#collection-method}

Метод `mode` возвращает [среднюю величину](https://ru.wikipedia.org/wiki/Мода_(статистика)) заданного ключа:

    $mode = collect([
        ['foo' => 10],
        ['foo' => 10],
        ['foo' => 20],
        ['foo' => 40]
    ])->mode('foo');

    // [10]

    $mode = collect([1, 1, 2, 4])->mode();

    // [1]

    $mode = collect([1, 1, 2, 2])->mode();

    // [1, 2]

<a name="method-nth"></a>
#### `nth()` {#collection-method}

Метод `nth` создает новую коллекцию, состоящую из каждого n-го элемента:

    $collection = collect(['a', 'b', 'c', 'd', 'e', 'f']);

    $collection->nth(4);

    // ['a', 'e']

При желании вы можете передать начальное смещение в качестве второго аргумента:

    $collection->nth(4, 1);

    // ['b', 'f']

<a name="method-only"></a>
#### `only()` {#collection-method}

Метод `only` возвращает элементы в коллекции с указанными ключами:

    $collection = collect([
        'product_id' => 1,
        'name' => 'Desk',
        'price' => 100,
        'discount' => false
    ]);

    $filtered = $collection->only(['product_id', 'name']);

    $filtered->all();

    // ['product_id' => 1, 'name' => 'Desk']

Чтобы узнать об обратном к `only`, смотрите метод [except](#method-except).

> {tip} Поведение этого метода изменяется при использовании [Eloquent Collections](/docs/{{version}}/eloquent-collections#method-only).

<a name="method-pad"></a>
#### `pad()` {#collection-method}

Метод `pad` будет заполнять массив заданным значением, пока массив не достигнет заданного размера. Этот метод ведет себя как PHP функция [array_pad](https://secure.php.net/manual/en/function.array-pad.php).

Для прокладки влево следует указать отрицательный размер. Заполнение не произойдет, если абсолютное значение данного размера меньше или равно длине массива:

    $collection = collect(['A', 'B', 'C']);

    $filtered = $collection->pad(5, 0);

    $filtered->all();

    // ['A', 'B', 'C', 0, 0]

    $filtered = $collection->pad(-5, 0);

    $filtered->all();

    // [0, 0, 'A', 'B', 'C']

<a name="method-partition"></a>
#### `partition()` {#collection-method}

Метод `partition` может быть объединен с деструктуризацией массива PHP, чтобы отделить элементы, которые проходят данную проверку истинности, от тех, которые не проходят:

    $collection = collect([1, 2, 3, 4, 5, 6]);

    [$underThree, $equalOrAboveThree] = $collection->partition(function ($i) {
        return $i < 3;
    });

    $underThree->all();

    // [1, 2]

    $equalOrAboveThree->all();

    // [3, 4, 5, 6]

<a name="method-pipe"></a>
#### `pipe()` {#collection-method}

Метод `pipe` передает коллекцию данному замыканию и возвращает результат выполненного замыкания:

    $collection = collect([1, 2, 3]);

    $piped = $collection->pipe(function ($collection) {
        return $collection->sum();
    });

    // 6

<a name="method-pipeinto"></a>
#### `pipeInto()` {#collection-method}

Метод `pipeInto` создает новый экземпляр данного класса и передает коллекцию в конструктор:

    class ResourceCollection
    {
        /**
         * The Collection instance.
         */
        public $collection;

        /**
         * Create a new ResourceCollection instance.
         *
         * @param  Collection  $collection
         * @return void
         */
        public function __construct(Collection $collection)
        {
            $this->collection = $collection;
        }
    }

    $collection = collect([1, 2, 3]);

    $resource = $collection->pipeInto(ResourceCollection::class);

    $resource->collection->all();

    // [1, 2, 3]

<a name="method-pluck"></a>
#### `pluck()` {#collection-method}

Метод `pluck` извлекает все значения для данного ключа:

    $collection = collect([
        ['product_id' => 'prod-100', 'name' => 'Desk'],
        ['product_id' => 'prod-200', 'name' => 'Chair'],
    ]);

    $plucked = $collection->pluck('name');

    $plucked->all();

    // ['Desk', 'Chair']

Вы также можете указать, как вы хотите, чтобы полученная коллекция была привязана к ключу:

    $plucked = $collection->pluck('name', 'product_id');

    $plucked->all();

    // ['prod-100' => 'Desk', 'prod-200' => 'Chair']

Метод `pluck` также поддерживает получение вложенных значений с использованием "точечной" нотации:

    $collection = collect([
        [
            'speakers' => [
                'first_day' => ['Rosa', 'Judith'],
                'second_day' => ['Angela', 'Kathleen'],
            ],
        ],
    ]);

    $plucked = $collection->pluck('speakers.first_day');

    $plucked->all();

    // ['Rosa', 'Judith']

Если существуют повторяющиеся ключи, последний соответствующий элемент будет вставлен в собранную коллекцию:

    $collection = collect([
        ['brand' => 'Tesla',  'color' => 'red'],
        ['brand' => 'Pagani', 'color' => 'white'],
        ['brand' => 'Tesla',  'color' => 'black'],
        ['brand' => 'Pagani', 'color' => 'orange'],
    ]);

    $plucked = $collection->pluck('color', 'brand');

    $plucked->all();

    // ['Tesla' => 'black', 'Pagani' => 'orange']

<a name="method-pop"></a>
#### `pop()` {#collection-method}

Метод `pop` удаляет и возвращает последний элемент из коллекции:

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->pop();

    // 5

    $collection->all();

    // [1, 2, 3, 4]

<a name="method-prepend"></a>
#### `prepend()` {#collection-method}

Метод `prepend` добавляет элемент в начало коллекции:

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->prepend(0);

    $collection->all();

    // [0, 1, 2, 3, 4, 5]

Вы также можете передать второй аргумент, чтобы указать ключ добавляемого элемента:

    $collection = collect(['one' => 1, 'two' => 2]);

    $collection->prepend(0, 'zero');

    $collection->all();

    // ['zero' => 0, 'one' => 1, 'two' => 2]

<a name="method-pull"></a>
#### `pull()` {#collection-method}

Метод `pull` удаляет и возвращает элемент из коллекции по его ключу:

    $collection = collect(['product_id' => 'prod-100', 'name' => 'Desk']);

    $collection->pull('name');

    // 'Desk'

    $collection->all();

    // ['product_id' => 'prod-100']

<a name="method-push"></a>
#### `push()` {#collection-method}

Метод `push` добавляет элемент в конец коллекции:

    $collection = collect([1, 2, 3, 4]);

    $collection->push(5);

    $collection->all();

    // [1, 2, 3, 4, 5]

<a name="method-put"></a>
#### `put()` {#collection-method}

Метод `put` устанавливает заданный ключ и значение в коллекции:

    $collection = collect(['product_id' => 1, 'name' => 'Desk']);

    $collection->put('price', 100);

    $collection->all();

    // ['product_id' => 1, 'name' => 'Desk', 'price' => 100]

<a name="method-random"></a>
#### `random()` {#collection-method}

Метод `random` возвращает случайный элемент из коллекции:

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->random();

    // 4 - (retrieved randomly)

Вы можете передать целое число в `random`, чтобы указать, сколько элементов вы хотите получить случайным образом. Коллекция элементов всегда возвращается при явной передаче количества элементов, которые вы хотите получить:

    $random = $collection->random(3);

    $random->all();

    // [2, 4, 5] - (retrieved randomly)

Если в экземпляре коллекции меньше элементов, чем запрошено, метод `random` вызовет исключение `InvalidArgumentException`.

<a name="method-reduce"></a>
#### `reduce()` {#collection-method}

Метод `reduce` сокращает коллекцию до одного значения, передавая результат каждой итерации в следующую итерацию:

    $collection = collect([1, 2, 3]);

    $total = $collection->reduce(function ($carry, $item) {
        return $carry + $item;
    });

    // 6

Значение `$carry` на первой итерации равно `null`; однако вы можете указать его начальное значение, передав второй аргумент функции `reduce`:

    $collection->reduce(function ($carry, $item) {
        return $carry + $item;
    }, 4);

    // 10

Метод `reduce` также передает ключи массива в ассоциативных коллекциях заданному обратному вызову:

    $collection = collect([
        'usd' => 1400,
        'gbp' => 1200,
        'eur' => 1000,
    ]);

    $ratio = [
        'usd' => 1,
        'gbp' => 1.37,
        'eur' => 1.22,
    ];

    $collection->reduce(function ($carry, $value, $key) use ($ratio) {
        return $carry + ($value * $ratio[$key]);
    });

    // 4264

<a name="method-reject"></a>
#### `reject()` {#collection-method}

Метод `reject` фильтрует коллекцию, используя заданное замыкание. Замыкание должно вернуть `true`, если элемент должен быть удален из результирующей коллекции:

    $collection = collect([1, 2, 3, 4]);

    $filtered = $collection->reject(function ($value, $key) {
        return $value > 2;
    });

    $filtered->all();

    // [1, 2]

Обратный к методу `reject`, смотрите метод [`filter`](#method-filter).

<a name="method-replace"></a>
#### `replace()` {#collection-method}

Метод `replace` ведет себя аналогично `merge`; однако, помимо перезаписи совпадающих элементов, имеющих строковые ключи, метод `replace` также перезаписывает элементы в коллекции, у которых есть совпадающие числовые ключи:

    $collection = collect(['Taylor', 'Abigail', 'James']);

    $replaced = $collection->replace([1 => 'Victoria', 3 => 'Finn']);

    $replaced->all();

    // ['Taylor', 'Victoria', 'James', 'Finn']

<a name="method-replacerecursive"></a>
#### `replaceRecursive()` {#collection-method}

Этот метод работает как `replace`, но он будет повторяться в массивах и применять тот же процесс замены к внутренним значениям:

    $collection = collect([
        'Taylor',
        'Abigail',
        [
            'James',
            'Victoria',
            'Finn'
        ]
    ]);

    $replaced = $collection->replaceRecursive([
        'Charlie',
        2 => [1 => 'King']
    ]);

    $replaced->all();

    // ['Charlie', 'Abigail', ['James', 'King', 'Finn']]

<a name="method-reverse"></a>
#### `reverse()` {#collection-method}

Метод `reverse` меняет порядок элементов коллекции на обратный, сохраняя исходные ключи:

    $collection = collect(['a', 'b', 'c', 'd', 'e']);

    $reversed = $collection->reverse();

    $reversed->all();

    /*
        [
            4 => 'e',
            3 => 'd',
            2 => 'c',
            1 => 'b',
            0 => 'a',
        ]
    */

<a name="method-search"></a>
#### `search()` {#collection-method}

Метод `search` ищет в коллекции заданное значение и возвращает его ключ, если он найден. Если элемент не найден, возвращается `false`:

    $collection = collect([2, 4, 6, 8]);

    $collection->search(4);

    // 1

Поиск выполняется с использованием «свободного» сравнения, то есть строка с целым значением будет считаться равной целому числу того же значения. Чтобы использовать «строгое» сравнение, передайте `true` в качестве второго аргумента метода:

    collect([2, 4, 6, 8])->search('4', $strict = true);

    // false

В качестве альтернативы вы можете предоставить собственное замыкание для поиска первого элемента, который проходит заданный тест на истинность:

    collect([2, 4, 6, 8])->search(function ($item, $key) {
        return $item > 5;
    });

    // 2

<a name="method-shift"></a>
#### `shift()` {#collection-method}

Метод `shift` удаляет и возвращает первый элемент из коллекции:

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->shift();

    // 1

    $collection->all();

    // [2, 3, 4, 5]

<a name="method-shuffle"></a>
#### `shuffle()` {#collection-method}

Метод `shuffle` случайным образом перемешивает элементы в коллекции:

    $collection = collect([1, 2, 3, 4, 5]);

    $shuffled = $collection->shuffle();

    $shuffled->all();

    // [3, 2, 5, 1, 4] - (generated randomly)

<a name="method-skip"></a>
#### `skip()` {#collection-method}

Метод `skip` возвращает новую коллекцию с заданным количеством элементов, удаленных из начала коллекции:

    $collection = collect([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]);

    $collection = $collection->skip(4);

    $collection->all();

    // [5, 6, 7, 8, 9, 10]

<a name="method-skipuntil"></a>
#### `skipUntil()` {#collection-method}

Метод `skipUntil` пропускает элементы из коллекции до тех пор, пока данный обратный вызов не вернет `true`, а затем вернет оставшиеся элементы в коллекции как новый экземпляр коллекции:

    $collection = collect([1, 2, 3, 4]);

    $subset = $collection->skipUntil(function ($item) {
        return $item >= 3;
    });

    $subset->all();

    // [3, 4]

Вы также можете передать простое значение методу `skipUntil`, чтобы пропустить все элементы, пока не будет найдено заданное значение:

    $collection = collect([1, 2, 3, 4]);

    $subset = $collection->skipUntil(3);

    $subset->all();

    // [3, 4]

> {note} Если данное значение не найдено или обратный вызов никогда не возвращает `true`, метод `skipUntil` вернет пустую коллекцию.

<a name="method-skipwhile"></a>
#### `skipWhile()` {#collection-method}

Метод `skipWhile` пропускает элементы из коллекции, в то время как данный обратный вызов возвращает `true`, а затем возвращает оставшиеся элементы в коллекции как новую коллекцию:

    $collection = collect([1, 2, 3, 4]);

    $subset = $collection->skipWhile(function ($item) {
        return $item <= 3;
    });

    $subset->all();

    // [4]

> {note} Если обратный вызов никогда не возвращает `true`, метод `skipWhile` вернет пустую коллекцию.

<a name="method-slice"></a>
#### `slice()` {#collection-method}

Метод `slice` возвращает фрагмент коллекции, начиная с данного индекса:

    $collection = collect([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]);

    $slice = $collection->slice(4);

    $slice->all();

    // [5, 6, 7, 8, 9, 10]

Если вы хотите ограничить размер возвращаемого фрагмента, передайте желаемый размер в качестве второго аргумента метода:

    $slice = $collection->slice(4, 2);

    $slice->all();

    // [5, 6]

Возвращенный фрагмент по умолчанию сохранит ключи. Если вы не хотите сохранять исходные ключи, вы можете использовать метод [`values`](#method-values) для их переиндексации.

<a name="method-sole"></a>
#### `sole()` {#collection-method}

Метод `sole` возвращает первый элемент в коллекции, который проходит заданную проверку истинности, но только если проверка истинности соответствует ровно одному элементу:

    collect([1, 2, 3, 4])->sole(function ($value, $key) {
        return $value === 2;
    });

    // 2

Вы также можете передать пару ключ / значение методу `sole`, который вернет первый элемент в коллекции, соответствующий данной паре, но только если ему соответствует ровно один элемент:

    $collection = collect([
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Chair', 'price' => 100],
    ]);

    $collection->sole('product', 'Chair');

    // ['product' => 'Chair', 'price' => 100]

В качестве альтернативы вы также можете вызвать метод `sole` без аргументов, чтобы получить первый элемент в коллекции, если есть только один элемент:

    $collection = collect([
        ['product' => 'Desk', 'price' => 200],
    ]);

    $collection->sole();

    // ['product' => 'Desk', 'price' => 200]

Если в коллекции нет элементов, которые должны быть возвращены методом `sole`, будет выброшено исключение `\Illuminate\Collections\ItemNotFoundException`. Если необходимо вернуть более одного элемента, будет выброшено исключение `\Illuminate\Collections\MultipleItemsFoundException`.

<a name="method-some"></a>
#### `some()` {#collection-method}

Псевдоним для метода [`contains`](#method-contains).

<a name="method-sort"></a>
#### `sort()` {#collection-method}

Метод `sort` сортирует коллекцию. Сортированная коллекция сохраняет исходные ключи массива, поэтому в следующем примере мы будем использовать метод [`values`](#method-values) для сброса ключей на последовательно пронумерованные индексы:

    $collection = collect([5, 3, 1, 2, 4]);

    $sorted = $collection->sort();

    $sorted->values()->all();

    // [1, 2, 3, 4, 5]

Если ваши потребности в сортировке более сложные, вы можете передать обратный вызов `sort` с вашим собственным алгоритмом. Обратитесь к документации PHP по [`uasort`](https://secure.php.net/manual/en/function.uasort.php#refsect1-function.uasort-parameters), который является методом `sort` коллекции вызовы используют внутренне.

> {tip} Если вам нужно отсортировать коллекцию вложенных массивов или объектов, смотрите методы [`sortBy`](#method-sortby) и [`sortByDesc`](#method-sortbydesc).

<a name="method-sortby"></a>
#### `sortBy()` {#collection-method}

Метод `sortBy` сортирует коллекцию по заданному ключу. Сортированная коллекция сохраняет исходные ключи массива, поэтому в следующем примере мы будем использовать метод [`values`](#method-values) для сброса ключей на последовательно пронумерованные индексы:

    $collection = collect([
        ['name' => 'Desk', 'price' => 200],
        ['name' => 'Chair', 'price' => 100],
        ['name' => 'Bookcase', 'price' => 150],
    ]);

    $sorted = $collection->sortBy('price');

    $sorted->values()->all();

    /*
        [
            ['name' => 'Chair', 'price' => 100],
            ['name' => 'Bookcase', 'price' => 150],
            ['name' => 'Desk', 'price' => 200],
        ]
    */

Метод `sort` принимает [флаги сортировки](https://www.php.net/manual/en/function.sort.php) в качестве второго аргумента:

    $collection = collect([
        ['title' => 'Item 1'],
        ['title' => 'Item 12'],
        ['title' => 'Item 3'],
    ]);

    $sorted = $collection->sortBy('title', SORT_NATURAL);

    $sorted->values()->all();

    /*
        [
            ['title' => 'Item 1'],
            ['title' => 'Item 3'],
            ['title' => 'Item 12'],
        ]
    */

В качестве альтернативы вы можете передать собственное замыкание, чтобы определить, как сортировать значения коллекции:

    $collection = collect([
        ['name' => 'Desk', 'colors' => ['Black', 'Mahogany']],
        ['name' => 'Chair', 'colors' => ['Black']],
        ['name' => 'Bookcase', 'colors' => ['Red', 'Beige', 'Brown']],
    ]);

    $sorted = $collection->sortBy(function ($product, $key) {
        return count($product['colors']);
    });

    $sorted->values()->all();

    /*
        [
            ['name' => 'Chair', 'colors' => ['Black']],
            ['name' => 'Desk', 'colors' => ['Black', 'Mahogany']],
            ['name' => 'Bookcase', 'colors' => ['Red', 'Beige', 'Brown']],
        ]
    */

Если вы хотите отсортировать свою коллекцию по нескольким атрибутам, вы можете передать массив операций сортировки методу `sortBy`. Каждая операция сортировки должна быть массивом, состоящим из атрибута, по которому вы хотите выполнить сортировку, и направления желаемой сортировки:

    $collection = collect([
        ['name' => 'Taylor Otwell', 'age' => 34],
        ['name' => 'Abigail Otwell', 'age' => 30],
        ['name' => 'Taylor Otwell', 'age' => 36],
        ['name' => 'Abigail Otwell', 'age' => 32],
    ]);

    $sorted = $collection->sortBy([
        ['name', 'asc'],
        ['age', 'desc'],
    ]);

    $sorted->values()->all();

    /*
        [
            ['name' => 'Abigail Otwell', 'age' => 32],
            ['name' => 'Abigail Otwell', 'age' => 30],
            ['name' => 'Taylor Otwell', 'age' => 36],
            ['name' => 'Taylor Otwell', 'age' => 34],
        ]
    */

При сортировке коллекции по нескольким атрибутам вы также можете указать замыкания, которые определяют каждую операцию сортировки:

    $collection = collect([
        ['name' => 'Taylor Otwell', 'age' => 34],
        ['name' => 'Abigail Otwell', 'age' => 30],
        ['name' => 'Taylor Otwell', 'age' => 36],
        ['name' => 'Abigail Otwell', 'age' => 32],
    ]);

    $sorted = $collection->sortBy([
        fn ($a, $b) => $a['name'] <=> $b['name'],
        fn ($a, $b) => $b['age'] <=> $a['age'],
    ]);

    $sorted->values()->all();

    /*
        [
            ['name' => 'Abigail Otwell', 'age' => 32],
            ['name' => 'Abigail Otwell', 'age' => 30],
            ['name' => 'Taylor Otwell', 'age' => 36],
            ['name' => 'Taylor Otwell', 'age' => 34],
        ]
    */

<a name="method-sortbydesc"></a>
#### `sortByDesc()` {#collection-method}

Этот метод имеет ту же сигнатуру, что и метод [`sortBy`](#method-sortby), но сортирует коллекцию в обратном порядке.

<a name="method-sortdesc"></a>
#### `sortDesc()` {#collection-method}

Этот метод сортирует коллекцию в порядке, обратном методу [`sort`](#method-sort):

    $collection = collect([5, 3, 1, 2, 4]);

    $sorted = $collection->sortDesc();

    $sorted->values()->all();

    // [5, 4, 3, 2, 1]

В отличие от `sort`, вы не можете передавать замыкание в `sortDesc`. Вместо этого вы должны использовать метод [`sort`](#method-sort) и инвертировать ваше сравнение.

<a name="method-sortkeys"></a>
#### `sortKeys()` {#collection-method}

Метод `sortKeys` сортирует коллекцию по ключам нижележащего ассоциативного массива:

    $collection = collect([
        'id' => 22345,
        'first' => 'John',
        'last' => 'Doe',
    ]);

    $sorted = $collection->sortKeys();

    $sorted->all();

    /*
        [
            'first' => 'John',
            'id' => 22345,
            'last' => 'Doe',
        ]
    */

<a name="method-sortkeysdesc"></a>
#### `sortKeysDesc()` {#collection-method}

Этот метод имеет ту же сигнатуру, что и метод [`sortKeys`](#method-sortkeys), но сортирует коллекцию в обратном порядке.

<a name="method-splice"></a>
#### `splice()` {#collection-method}

Метод `splice` удаляет и возвращает часть элементов, начиная с указанного индекса:

    $collection = collect([1, 2, 3, 4, 5]);

    $chunk = $collection->splice(2);

    $chunk->all();

    // [3, 4, 5]

    $collection->all();

    // [1, 2]

Вы можете передать второй аргумент, чтобы ограничить размер результирующей коллекции:

    $collection = collect([1, 2, 3, 4, 5]);

    $chunk = $collection->splice(2, 1);

    $chunk->all();

    // [3]

    $collection->all();

    // [1, 2, 4, 5]

Кроме того, вы можете передать третий аргумент, содержащий новые элементы, чтобы заменить элементы, удаленные из коллекции:

    $collection = collect([1, 2, 3, 4, 5]);

    $chunk = $collection->splice(2, 1, [10, 11]);

    $chunk->all();

    // [3]

    $collection->all();

    // [1, 2, 10, 11, 4, 5]

<a name="method-split"></a>
#### `split()` {#collection-method}

Метод `split` разбивает коллекцию на заданное количество групп:

    $collection = collect([1, 2, 3, 4, 5]);

    $groups = $collection->split(3);

    $groups->all();

    // [[1, 2], [3, 4], [5]]

<a name="method-splitin"></a>
#### `splitIn()` {#collection-method}

Метод `splitIn` разбивает коллекцию на заданное количество групп, полностью заполняя нетерминальные группы перед выделением остатка последней группе:

    $collection = collect([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]);

    $groups = $collection->splitIn(3);

    $groups->all();

    // [[1, 2, 3, 4], [5, 6, 7, 8], [9, 10]]

<a name="method-sum"></a>
#### `sum()` {#collection-method}

Метод `sum` возвращает сумму всех элементов в коллекции:

    collect([1, 2, 3, 4, 5])->sum();

    // 15

Если коллекция содержит вложенные массивы или объекты, вы должны передать ключ, который будет использоваться для определения, какие значения следует суммировать:

    $collection = collect([
        ['name' => 'JavaScript: The Good Parts', 'pages' => 176],
        ['name' => 'JavaScript: The Definitive Guide', 'pages' => 1096],
    ]);

    $collection->sum('pages');

    // 1272

Кроме того, вы можете передать собственное замыкание, чтобы определить, какие значения коллекции нужно суммировать:

    $collection = collect([
        ['name' => 'Chair', 'colors' => ['Black']],
        ['name' => 'Desk', 'colors' => ['Black', 'Mahogany']],
        ['name' => 'Bookcase', 'colors' => ['Red', 'Beige', 'Brown']],
    ]);

    $collection->sum(function ($product) {
        return count($product['colors']);
    });

    // 6

<a name="method-take"></a>
#### `take()` {#collection-method}

Метод `take` возвращает новую коллекцию с указанным количеством элементов:

    $collection = collect([0, 1, 2, 3, 4, 5]);

    $chunk = $collection->take(3);

    $chunk->all();

    // [0, 1, 2]

Вы также можете передать отрицательное целое число, чтобы взять указанное количество элементов из конца коллекции:

    $collection = collect([0, 1, 2, 3, 4, 5]);

    $chunk = $collection->take(-2);

    $chunk->all();

    // [4, 5]

<a name="method-takeuntil"></a>
#### `takeUntil()` {#collection-method}

Метод `takeUntil` возвращает элементы в коллекции до тех пор, пока данный обратный вызов не вернет `true`:

    $collection = collect([1, 2, 3, 4]);

    $subset = $collection->takeUntil(function ($item) {
        return $item >= 3;
    });

    $subset->all();

    // [1, 2]

Вы также можете передать простое значение методу `takeUntil`, чтобы получать элементы, пока не будет найдено заданное значение:

    $collection = collect([1, 2, 3, 4]);

    $subset = $collection->takeUntil(3);

    $subset->all();

    // [1, 2]

> {note} Если данное значение не найдено или обратный вызов никогда не возвращает `true`, метод `takeUntil` вернет все элементы в коллекции.

<a name="method-takewhile"></a>
#### `takeWhile()` {#collection-method}

Метод `takeWhile` возвращает элементы в коллекции до тех пор, пока данный обратный вызов не вернет `false`:

    $collection = collect([1, 2, 3, 4]);

    $subset = $collection->takeWhile(function ($item) {
        return $item < 3;
    });

    $subset->all();

    // [1, 2]

> {note} Если обратный вызов никогда не возвращает `false`, метод `takeWhile` вернет все элементы в коллекции.

<a name="method-tap"></a>
#### `tap()` {#collection-method}

Метод `tap` передает коллекцию заданному обратному вызову, позволяя вам «коснуться» коллекции в определенной точке и сделать что-то с элементами, не затрагивая саму коллекцию. Затем коллекция возвращается методом `tap`:

    collect([2, 4, 3, 1, 5])
        ->sort()
        ->tap(function ($collection) {
            Log::debug('Values after sorting', $collection->values()->all());
        })
        ->shift();

    // 1

<a name="method-times"></a>
#### `times()` {#collection-method}

Статический метод `times` создает новую коллекцию, вызывая заданное замыкание указанное количество раз:

    $collection = Collection::times(10, function ($number) {
        return $number * 9;
    });

    $collection->all();

    // [9, 18, 27, 36, 45, 54, 63, 72, 81, 90]

<a name="method-toarray"></a>
#### `toArray()` {#collection-method}

Метод `toArray` преобразует коллекцию в простой массив PHP `array`. Если значениями коллекции являются модели [Eloquent](/docs/{{version}}/eloquent), модели также будут преобразованы в массивы:

    $collection = collect(['name' => 'Desk', 'price' => 200]);

    $collection->toArray();

    /*
        [
            ['name' => 'Desk', 'price' => 200],
        ]
    */

> {note} `toArray` также преобразует все вложенные объекты коллекции, которые являются экземпляром `Arrayable`, в массив. Если вы хотите получить необработанный массив, лежащий в основе коллекции, используйте вместо этого метод [`all`](#method-all).

<a name="method-tojson"></a>
#### `toJson()` {#collection-method}

Метод `toJson` преобразует коллекцию в сериализованную строку JSON:

    $collection = collect(['name' => 'Desk', 'price' => 200]);

    $collection->toJson();

    // '{"name":"Desk", "price":200}'

<a name="method-transform"></a>
#### `transform()` {#collection-method}

Метод `transform` выполняет итерацию по коллекции и вызывает заданный обратный вызов для каждого элемента в коллекции. Элементы в коллекции будут заменены значениями, возвращаемыми обратным вызовом:

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->transform(function ($item, $key) {
        return $item * 2;
    });

    $collection->all();

    // [2, 4, 6, 8, 10]

> {note} В отличие от большинства других методов сбора, `transform` изменяет саму коллекцию. Если вы хотите вместо этого создать новую коллекцию, используйте метод [`map`](#method-map).

<a name="method-union"></a>
#### `union()` {#collection-method}

Метод `union` добавляет заданный массив в коллекцию. Если данный массив содержит ключи, которые уже находятся в исходной коллекции, предпочтительнее будут значения исходной коллекции:

    $collection = collect([1 => ['a'], 2 => ['b']]);

    $union = $collection->union([3 => ['c'], 1 => ['d']]);

    $union->all();

    // [1 => ['a'], 2 => ['b'], 3 => ['c']]

<a name="method-unique"></a>
#### `unique()` {#collection-method}

Метод `unique` возвращает все уникальные элементы коллекции. Возвращенная коллекция сохраняет исходные ключи массива, поэтому в следующем примере мы будем использовать метод [`values`](#method-values) для сброса ключей на последовательно пронумерованные индексы:

    $collection = collect([1, 1, 2, 2, 3, 4, 2]);

    $unique = $collection->unique();

    $unique->values()->all();

    // [1, 2, 3, 4]

При работе с вложенными массивами или объектами вы можете указать ключ, используемый для определения уникальности:

    $collection = collect([
        ['name' => 'iPhone 6', 'brand' => 'Apple', 'type' => 'phone'],
        ['name' => 'iPhone 5', 'brand' => 'Apple', 'type' => 'phone'],
        ['name' => 'Apple Watch', 'brand' => 'Apple', 'type' => 'watch'],
        ['name' => 'Galaxy S6', 'brand' => 'Samsung', 'type' => 'phone'],
        ['name' => 'Galaxy Gear', 'brand' => 'Samsung', 'type' => 'watch'],
    ]);

    $unique = $collection->unique('brand');

    $unique->values()->all();

    /*
        [
            ['name' => 'iPhone 6', 'brand' => 'Apple', 'type' => 'phone'],
            ['name' => 'Galaxy S6', 'brand' => 'Samsung', 'type' => 'phone'],
        ]
    */

Наконец, вы также можете передать собственное замыкание методу `unique`, чтобы указать, какое значение должно определять уникальность элемента:

    $unique = $collection->unique(function ($item) {
        return $item['brand'].$item['type'];
    });

    $unique->values()->all();

    /*
        [
            ['name' => 'iPhone 6', 'brand' => 'Apple', 'type' => 'phone'],
            ['name' => 'Apple Watch', 'brand' => 'Apple', 'type' => 'watch'],
            ['name' => 'Galaxy S6', 'brand' => 'Samsung', 'type' => 'phone'],
            ['name' => 'Galaxy Gear', 'brand' => 'Samsung', 'type' => 'watch'],
        ]
    */

Метод `unique` использует «свободные» сравнения при проверке значений элементов, то есть строка с целым значением будет считаться равной целому числу того же значения. Используйте метод [`uniqueStrict`](#method-uniquestrict) для фильтрации с использованием «строгих» сравнений.

> {tip} Поведение этого метода изменяется при использовании [Eloquent Collections](/docs/{{version}}/eloquent-collections#method-unique).

<a name="method-uniquestrict"></a>
#### `uniqueStrict()` {#collection-method}

Этот метод имеет ту же сигнатуру, что и метод [`unique`](#method-unique); однако все значения сравниваются с использованием «строгих» сравнений.

<a name="method-unless"></a>
#### `unless()` {#collection-method}

Метод `unless` выполнит заданный обратный вызов, если первый аргумент, переданный методу, не будет иметь значение `true`:

    $collection = collect([1, 2, 3]);

    $collection->unless(true, function ($collection) {
        return $collection->push(4);
    });

    $collection->unless(false, function ($collection) {
        return $collection->push(5);
    });

    $collection->all();

    // [1, 2, 3, 5]

Для обратного `unless`, смотрите метод [`when`](#method-when).

<a name="method-unlessempty"></a>
#### `unlessEmpty()` {#collection-method}

Псевдоним для метода [`whenNotEmpty`](#method-whennotempty).

<a name="method-unlessnotempty"></a>
#### `unlessNotEmpty()` {#collection-method}

Псевдоним для метода [`whenEmpty`](#method-whenempty).

<a name="method-unwrap"></a>
#### `unwrap()` {#collection-method}

Статический метод `unwrap` возвращает базовые элементы коллекции из заданного значения, когда это применимо:

    Collection::unwrap(collect('John Doe'));

    // ['John Doe']

    Collection::unwrap(['John Doe']);

    // ['John Doe']

    Collection::unwrap('John Doe');

    // 'John Doe'

<a name="method-values"></a>
#### `values()` {#collection-method}

Метод `values` возвращает новую коллекцию с ключами, сброшенными на последовательные целые числа:

    $collection = collect([
        10 => ['product' => 'Desk', 'price' => 200],
        11 => ['product' => 'Desk', 'price' => 200],
    ]);

    $values = $collection->values();

    $values->all();

    /*
        [
            0 => ['product' => 'Desk', 'price' => 200],
            1 => ['product' => 'Desk', 'price' => 200],
        ]
    */

<a name="method-when"></a>
#### `when()` {#collection-method}

Метод `when` выполнит данный обратный вызов, когда первый аргумент, переданный методу, оценивается как `true`:

    $collection = collect([1, 2, 3]);

    $collection->when(true, function ($collection) {
        return $collection->push(4);
    });

    $collection->when(false, function ($collection) {
        return $collection->push(5);
    });

    $collection->all();

    // [1, 2, 3, 4]

Для обратного `when`, смотрите метод [`unless`](#method-unless).

<a name="method-whenempty"></a>
#### `whenEmpty()` {#collection-method}

Метод `whenEmpty` выполнит данный обратный вызов, когда коллекция пуста:

    $collection = collect(['Michael', 'Tom']);

    $collection->whenEmpty(function ($collection) {
        return $collection->push('Adam');
    });

    $collection->all();

    // ['Michael', 'Tom']


    $collection = collect();

    $collection->whenEmpty(function ($collection) {
        return $collection->push('Adam');
    });

    $collection->all();

    // ['Adam']

Второе замыкание может быть передано методу `whenEmpty`, который будет выполнен, когда коллекция не пуста:

    $collection = collect(['Michael', 'Tom']);

    $collection->whenEmpty(function ($collection) {
        return $collection->push('Adam');
    }, function ($collection) {
        return $collection->push('Taylor');
    });

    $collection->all();

    // ['Michael', 'Tom', 'Taylor']

Для обратного `whenEmpty`, смотрите метод [`whenNotEmpty`](#method-whennotempty).

<a name="method-whennotempty"></a>
#### `whenNotEmpty()` {#collection-method}

Метод `whenNotEmpty` выполнит данный обратный вызов, если коллекция не пуста:

    $collection = collect(['michael', 'tom']);

    $collection->whenNotEmpty(function ($collection) {
        return $collection->push('adam');
    });

    $collection->all();

    // ['michael', 'tom', 'adam']


    $collection = collect();

    $collection->whenNotEmpty(function ($collection) {
        return $collection->push('adam');
    });

    $collection->all();

    // []

Второе замыкание может быть передано методу `whenNotEmpty`, который будет выполняться, когда коллекция пуста:

    $collection = collect();

    $collection->whenNotEmpty(function ($collection) {
        return $collection->push('adam');
    }, function ($collection) {
        return $collection->push('taylor');
    });

    $collection->all();

    // ['taylor']

Для обратного `whenNotEmpty`, смотрите метод [`whenEmpty`](#method-whenempty).

<a name="method-where"></a>
#### `where()` {#collection-method}

Метод `where` фильтрует коллекцию по заданной паре ключ / значение:

    $collection = collect([
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Chair', 'price' => 100],
        ['product' => 'Bookcase', 'price' => 150],
        ['product' => 'Door', 'price' => 100],
    ]);

    $filtered = $collection->where('price', 100);

    $filtered->all();

    /*
        [
            ['product' => 'Chair', 'price' => 100],
            ['product' => 'Door', 'price' => 100],
        ]
    */

Метод `where` использует «свободные» сравнения при проверке значений элементов, то есть строка с целочисленным значением будет считаться равной целому числу того же значения. Используйте метод [`whereStrict`](#method-wherestrict) для фильтрации с использованием «строгих» сравнений.

При желании вы можете передать оператор сравнения в качестве второго параметра.

    $collection = collect([
        ['name' => 'Jim', 'deleted_at' => '2019-01-01 00:00:00'],
        ['name' => 'Sally', 'deleted_at' => '2019-01-02 00:00:00'],
        ['name' => 'Sue', 'deleted_at' => null],
    ]);

    $filtered = $collection->where('deleted_at', '!=', null);

    $filtered->all();

    /*
        [
            ['name' => 'Jim', 'deleted_at' => '2019-01-01 00:00:00'],
            ['name' => 'Sally', 'deleted_at' => '2019-01-02 00:00:00'],
        ]
    */

<a name="method-wherestrict"></a>
#### `whereStrict()` {#collection-method}

Этот метод имеет ту же сигнатуру, что и метод [`where`](#method-where); однако все значения сравниваются с использованием «строгих» сравнений.

<a name="method-wherebetween"></a>
#### `whereBetween()` {#collection-method}

Метод `whereBetween` фильтрует коллекцию, определяя, находится ли указанное значение элемента в заданном диапазоне:

    $collection = collect([
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Chair', 'price' => 80],
        ['product' => 'Bookcase', 'price' => 150],
        ['product' => 'Pencil', 'price' => 30],
        ['product' => 'Door', 'price' => 100],
    ]);

    $filtered = $collection->whereBetween('price', [100, 200]);

    $filtered->all();

    /*
        [
            ['product' => 'Desk', 'price' => 200],
            ['product' => 'Bookcase', 'price' => 150],
            ['product' => 'Door', 'price' => 100],
        ]
    */

<a name="method-wherein"></a>
#### `whereIn()` {#collection-method}

Метод `whereIn` удаляет элементы из коллекции, у которых нет указанного значения элемента, содержащегося в данном массиве:

    $collection = collect([
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Chair', 'price' => 100],
        ['product' => 'Bookcase', 'price' => 150],
        ['product' => 'Door', 'price' => 100],
    ]);

    $filtered = $collection->whereIn('price', [150, 200]);

    $filtered->all();

    /*
        [
            ['product' => 'Desk', 'price' => 200],
            ['product' => 'Bookcase', 'price' => 150],
        ]
    */

Метод `whereIn` использует «свободные» сравнения при проверке значений элементов, то есть строка с целым значением будет считаться равной целому числу того же значения. Используйте метод [`whereInStrict`](#method-whereinstrict) для фильтрации с использованием «строгих» сравнений.

<a name="method-whereinstrict"></a>
#### `whereInStrict()` {#collection-method}

Этот метод имеет ту же сигнатуру, что и метод [`whereIn`](#method-wherein); однако все значения сравниваются с использованием «строгих» сравнений.

<a name="method-whereinstanceof"></a>
#### `whereInstanceOf()` {#collection-method}

Метод `whereInstanceOf` фильтрует коллекцию по заданному типу класса:

    use App\Models\User;
    use App\Models\Post;

    $collection = collect([
        new User,
        new User,
        new Post,
    ]);

    $filtered = $collection->whereInstanceOf(User::class);

    $filtered->all();

    // [App\Models\User, App\Models\User]

<a name="method-wherenotbetween"></a>
#### `whereNotBetween()` {#collection-method}

Метод `whereNotBetween` фильтрует коллекцию, определяя, находится ли указанное значение элемента вне заданного диапазона:

    $collection = collect([
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Chair', 'price' => 80],
        ['product' => 'Bookcase', 'price' => 150],
        ['product' => 'Pencil', 'price' => 30],
        ['product' => 'Door', 'price' => 100],
    ]);

    $filtered = $collection->whereNotBetween('price', [100, 200]);

    $filtered->all();

    /*
        [
            ['product' => 'Chair', 'price' => 80],
            ['product' => 'Pencil', 'price' => 30],
        ]
    */

<a name="method-wherenotin"></a>
#### `whereNotIn()` {#collection-method}

Метод `whereNotIn` удаляет элементы из коллекции, которые имеют указанное значение элемента, не содержащееся в данном массиве:

    $collection = collect([
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Chair', 'price' => 100],
        ['product' => 'Bookcase', 'price' => 150],
        ['product' => 'Door', 'price' => 100],
    ]);

    $filtered = $collection->whereNotIn('price', [150, 200]);

    $filtered->all();

    /*
        [
            ['product' => 'Chair', 'price' => 100],
            ['product' => 'Door', 'price' => 100],
        ]
    */

Метод `whereNotIn` использует «свободные» сравнения при проверке значений элементов, то есть строка с целым значением будет считаться равной целому числу того же значения. Используйте метод [`whereNotInStrict`](#method-wherenotinstrict) для фильтрации с использованием «строгих» сравнений.

<a name="method-wherenotinstrict"></a>
#### `whereNotInStrict()` {#collection-method}

Этот метод имеет ту же сигнатуру, что и метод [`whereNotIn`](#method-wherenotin); однако все значения сравниваются с использованием «строгих» сравнений.

<a name="method-wherenotnull"></a>
#### `whereNotNull()` {#collection-method}

Метод `whereNotNull` возвращает элементы из коллекции, для которых заданный ключ не равен `null`:

    $collection = collect([
        ['name' => 'Desk'],
        ['name' => null],
        ['name' => 'Bookcase'],
    ]);

    $filtered = $collection->whereNotNull('name');

    $filtered->all();

    /*
        [
            ['name' => 'Desk'],
            ['name' => 'Bookcase'],
        ]
    */

<a name="method-wherenull"></a>
#### `whereNull()` {#collection-method}

Метод `whereNull` возвращает элементы из коллекции, для которых заданный ключ равен `null`:

    $collection = collect([
        ['name' => 'Desk'],
        ['name' => null],
        ['name' => 'Bookcase'],
    ]);

    $filtered = $collection->whereNull('name');

    $filtered->all();

    /*
        [
            ['name' => null],
        ]
    */


<a name="method-wrap"></a>
#### `wrap()` {#collection-method}

Статический метод `wrap` оборачивает данное значение в коллекцию, если применимо:

    use Illuminate\Support\Collection;

    $collection = Collection::wrap('John Doe');

    $collection->all();

    // ['John Doe']

    $collection = Collection::wrap(['John Doe']);

    $collection->all();

    // ['John Doe']

    $collection = Collection::wrap(collect('John Doe'));

    $collection->all();

    // ['John Doe']

<a name="method-zip"></a>
#### `zip()` {#collection-method}

Метод `zip` объединяет значения данного массива со значениями исходной коллекции по их соответствующему индексу:

    $collection = collect(['Chair', 'Desk']);

    $zipped = $collection->zip([100, 200]);

    $zipped->all();

    // [['Chair', 100], ['Desk', 200]]

<a name="higher-order-messages"></a>
## Сообщения высшего порядка

Коллекции также обеспечивают поддержку «сообщений более высокого порядка», которые являются сокращениями для выполнения общих действий с коллекциями. Методы сбора, которые предоставляют сообщения более высокого порядка: [`average`](#method-average), [`avg`](#method-avg), [`contains`](#method-contains), [`each`](#method-each), [`every`](#method-every), [`filter`](#method-filter), [`first`](#method-first), [`flatMap`](#method-flatmap), [`groupBy`](#method-groupby), [`keyBy`](#method-keyby), [`map`](#method-map), [`max`](#method-max), [`min`](#method-min), [`partition`](#method-partition), [`reject`](#method-reject), [`skipUntil`](#method-skipuntil), [`skipWhile`](#method-skipwhile), [`some`](#method-some), [`sortBy`](#method-sortby), [`sortByDesc`](#method-sortbydesc), [`sum`](#method-sum), [`takeUntil`](#method-takeuntil), [`takeWhile`](#method-takewhile) и [`unique`](#method-unique).

К каждому сообщению более высокого порядка можно получить доступ как к динамическому свойству экземпляра коллекции. Например, давайте использовать сообщение более высокого порядка `each` для вызова метода для каждого объекта в коллекции:

    use App\Models\User;

    $users = User::where('votes', '>', 500)->get();

    $users->each->markAsVip();

Точно так же мы можем использовать сообщение высшего порядка `sum`, чтобы собрать общее количество «голосов» для набора пользователей:

    $users = User::where('group', 'Development')->get();

    return $users->sum->votes;

<a name="lazy-collections"></a>
## Отложенные коллекции

<a name="lazy-collection-introduction"></a>
### Введение

> {note} Прежде чем узнать больше о отложенных коллекциях Laravel, потратьте некоторое время на то, чтобы ознакомиться с [генераторами PHP](https://www.php.net/manual/en/language.generators.overview.php).

Чтобы дополнить и без того мощный класс `Collection`, класс `LazyCollection` использует [генераторы PHP](https://www.php.net/manual/en/language.generators.overview.php), чтобы вы могли работать с очень большие наборы данных при низком уровне использования памяти.

Например, представьте, что ваше приложение должно обрабатывать файл журнала размером в несколько гигабайт, используя при этом методы сбора данных Laravel для анализа журналов. Вместо того, чтобы читать весь файл в память сразу, можно использовать отложенные коллекции, чтобы сохранить в памяти только небольшую часть файла в данный момент:

    use App\Models\LogEntry;
    use Illuminate\Support\LazyCollection;

    LazyCollection::make(function () {
        $handle = fopen('log.txt', 'r');

        while (($line = fgets($handle)) !== false) {
            yield $line;
        }
    })->chunk(4)->map(function ($lines) {
        return LogEntry::fromLines($lines);
    })->each(function (LogEntry $logEntry) {
        // Process the log entry...
    });

Или представьте, что вам нужно перебрать 10 000 моделей Eloquent. При использовании традиционных коллекций Laravel все 10 000 моделей Eloquent должны быть загружены в память одновременно:

    use App\Models\User;

    $users = User::all()->filter(function ($user) {
        return $user->id > 500;
    });

Однако метод `cursor` построителя запросов возвращает экземпляр `LazyCollection`. Это позволяет вам по-прежнему выполнять только один запрос к базе данных, но при этом одновременно загружать в память только одну модель Eloquent. В этом примере обратный вызов `filter` не выполняется до тех пор, пока мы фактически не перебираем каждого пользователя индивидуально, что позволяет резко сократить использование памяти:

    use App\Models\User;

    $users = User::cursor()->filter(function ($user) {
        return $user->id > 500;
    });

    foreach ($users as $user) {
        echo $user->id;
    }

<a name="creating-lazy-collections"></a>
### Создание отложенных коллекций

Чтобы создать экземпляр отложенной коллекции, вы должны передать функцию генератора PHP методу `make` коллекции:

    use Illuminate\Support\LazyCollection;

    LazyCollection::make(function () {
        $handle = fopen('log.txt', 'r');

        while (($line = fgets($handle)) !== false) {
            yield $line;
        }
    });

<a name="the-enumerable-contract"></a>
### Перечисляемый контракт

Почти все методы, доступные в классе `Collection`, также доступны в классе `LazyCollection`. Оба этих класса реализуют контракт `Illuminate\Support\Enumerable`, который определяет следующие методы:

<div id="collection-method-list" markdown="1">

[all](#method-all)
[average](#method-average)
[avg](#method-avg)
[chunk](#method-chunk)
[chunkWhile](#method-chunkwhile)
[collapse](#method-collapse)
[collect](#method-collect)
[combine](#method-combine)
[concat](#method-concat)
[contains](#method-contains)
[containsStrict](#method-containsstrict)
[count](#method-count)
[countBy](#method-countBy)
[crossJoin](#method-crossjoin)
[dd](#method-dd)
[diff](#method-diff)
[diffAssoc](#method-diffassoc)
[diffKeys](#method-diffkeys)
[dump](#method-dump)
[duplicates](#method-duplicates)
[duplicatesStrict](#method-duplicatesstrict)
[each](#method-each)
[eachSpread](#method-eachspread)
[every](#method-every)
[except](#method-except)
[filter](#method-filter)
[first](#method-first)
[firstWhere](#method-first-where)
[flatMap](#method-flatmap)
[flatten](#method-flatten)
[flip](#method-flip)
[forPage](#method-forpage)
[get](#method-get)
[groupBy](#method-groupby)
[has](#method-has)
[implode](#method-implode)
[intersect](#method-intersect)
[intersectByKeys](#method-intersectbykeys)
[isEmpty](#method-isempty)
[isNotEmpty](#method-isnotempty)
[join](#method-join)
[keyBy](#method-keyby)
[keys](#method-keys)
[last](#method-last)
[macro](#method-macro)
[make](#method-make)
[map](#method-map)
[mapInto](#method-mapinto)
[mapSpread](#method-mapspread)
[mapToGroups](#method-maptogroups)
[mapWithKeys](#method-mapwithkeys)
[max](#method-max)
[median](#method-median)
[merge](#method-merge)
[mergeRecursive](#method-mergerecursive)
[min](#method-min)
[mode](#method-mode)
[nth](#method-nth)
[only](#method-only)
[pad](#method-pad)
[partition](#method-partition)
[pipe](#method-pipe)
[pluck](#method-pluck)
[random](#method-random)
[reduce](#method-reduce)
[reject](#method-reject)
[replace](#method-replace)
[replaceRecursive](#method-replacerecursive)
[reverse](#method-reverse)
[search](#method-search)
[shuffle](#method-shuffle)
[skip](#method-skip)
[slice](#method-slice)
[some](#method-some)
[sort](#method-sort)
[sortBy](#method-sortby)
[sortByDesc](#method-sortbydesc)
[sortKeys](#method-sortkeys)
[sortKeysDesc](#method-sortkeysdesc)
[split](#method-split)
[sum](#method-sum)
[take](#method-take)
[tap](#method-tap)
[times](#method-times)
[toArray](#method-toarray)
[toJson](#method-tojson)
[union](#method-union)
[unique](#method-unique)
[uniqueStrict](#method-uniquestrict)
[unless](#method-unless)
[unlessEmpty](#method-unlessempty)
[unlessNotEmpty](#method-unlessnotempty)
[unwrap](#method-unwrap)
[values](#method-values)
[when](#method-when)
[whenEmpty](#method-whenempty)
[whenNotEmpty](#method-whennotempty)
[where](#method-where)
[whereStrict](#method-wherestrict)
[whereBetween](#method-wherebetween)
[whereIn](#method-wherein)
[whereInStrict](#method-whereinstrict)
[whereInstanceOf](#method-whereinstanceof)
[whereNotBetween](#method-wherenotbetween)
[whereNotIn](#method-wherenotin)
[whereNotInStrict](#method-wherenotinstrict)
[wrap](#method-wrap)
[zip](#method-zip)

</div>

> {note} Методы, которые изменяют коллекцию (такие как `shift`, `pop`, `prepend` и т. д.) **недоступны** в классе `LazyCollection`.

<a name="lazy-collection-methods"></a>
### Методы отложенных коллекций

В дополнение к методам, определенным в контракте `Enumerable`, класс `LazyCollection` содержит следующие методы:

<a name="method-takeUntilTimeout"></a>
#### `takeUntilTimeout()` {#collection-method}

Метод `takeUntilTimeout` возвращает новую отложенную коллекцию, которая будет перечислять значения до указанного времени. По истечении этого времени коллекция перестанет перечислять:

    $lazyCollection = LazyCollection::times(INF)
        ->takeUntilTimeout(now()->addMinute());

    $lazyCollection->each(function ($number) {
        dump($number);

        sleep(1);
    });

    // 1
    // 2
    // ...
    // 58
    // 59

Чтобы проиллюстрировать использование этого метода, представьте себе приложение, которое отправляет счета из базы данных с помощью курсора. Вы можете определить [запланированную задачу](/docs/{{version}}/scheduling), которая запускается каждые 15 минут и обрабатывает счета максимум 14 минут:

    use App\Models\Invoice;
    use Illuminate\Support\Carbon;

    Invoice::pending()->cursor()
        ->takeUntilTimeout(
            Carbon::createFromTimestamp(LARAVEL_START)->add(14, 'minutes')
        )
        ->each(fn ($invoice) => $invoice->submit());

<a name="method-tapEach"></a>
#### `tapEach()` {#collection-method}

В то время как метод `each` вызывает данный обратный вызов для каждого элемента в коллекции сразу, метод `tapEach` вызывает данный обратный вызов только по мере того, как элементы извлекаются из списка один за другим:

    // Пока ничего не сдампили...
    $lazyCollection = LazyCollection::times(INF)->tapEach(function ($value) {
        dump($value);
    });

    // Дамп трёх элементов...
    $array = $lazyCollection->take(3)->all();

    // 1
    // 2
    // 3

<a name="method-remember"></a>
#### `remember()` {#collection-method}

Метод `remember` возвращает новую отложенную коллекцию, которая запоминает любые значения, которые уже были перечислены, и не будет извлекать их снова при последующих перечислениях коллекции:

    // Запрос еще не выполнен...
    $users = User::cursor()->remember();

    // Запрос выполняется...
    // Первые 5 пользователей гидратированы из базы данных...
    $users->take(5)->all();

    // Первые 5 пользователей приходят из кеша коллекции...
    // Остальные гидратированы из базы данных...
    $users->take(20)->all();
