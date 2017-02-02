Element Queries
===============

Element queries are [query builders](http://www.yiiframework.com/doc-2.0/guide-db-query-builder.html) that are tuned for fetching elements in Craft. They have several custom parameters, and they abstract away all the complexities of the actual SQL query needed to fetch the elements. Rather than raw data, they return element models.

## Creating Element Queries

You can create element queries in both PHP and Twig code. Here’s how:

Element Type  | PHP                                  | Twig
------------- | ------------------------------------ | ----------------------
Assets        | `craft\elements\Asset::find()`       | `craft.assets()`
Categories    | `craft\elements\Category::find()`    | `craft.categories()`
Entries       | `craft\elements\Entry::find()`       | `craft.entries()`
Matrix blocks | `craft\elements\MatrixBlock::find()` | `craft.matrixBlocks()`
Tags          | `craft\elements\Tag::find()`         | `craft.tags()`
Users         | `craft\elements\User::find()`        | `craft.users()`

## Setting Parameters

Once you’ve created an element query, you can set parameters on it.

The available parameters varies by element type. Here are the lists of parameters supported by Craft’s built-in element types:

- [Assets](asset-query-params.md)
- [Categories](category-query-params.md)
- [Entries](entry-query-params.md)
- [Matrix blocks](matrix-block-query-params.md)
- [Tags](tag-query-params.md)
- [Users](user-query-params.md)

The parameters should be set with chained method calls, like so:

#### PHP

```php
use craft\elements\Entry;

$query = Entry::find()
    ->section('news')
    ->limit(10);
```

#### Twig

```twig
{% set query = craft.entries()
    .section('news')
    .limit(10) %}
```

### Batch-Setting Parameters

You can also batch-set parameters like so:

#### PHP

```php
use craft\elements\Entry;

$query = Entry::find();
Craft::configure($query, [
    'section' => 'news',
    'limit' => 10
]);
```

#### Twig

```twig
{% set query = craft.entries({
    section: 'news',
    limit: 10
}) %}
```

## Executing Element Queries

Once you’ve defined your parameters on the query, there are multiple methods available to execute it, depending on the data you need back.

### `exists()`

Returns whether any elements match the query.

#### PHP

```php
use craft\elements\Entry;

$exists = Entry::find()
    ->section('news')
    ->slug('hello-world')
    ->exists();
```

#### Twig

```twig
{% set exists = craft.entries()
    .section('news')
    .slug('hello-world')
    .exists() %}
```

### `count()`

Returns the total number of elements that are matched by the query.

#### PHP

```php
use craft\elements\Entry;

$count = Entry::find()
    ->section('news')
    ->count();
```

#### Twig

```twig
{% set count = craft.entries()
    .section('news')
    .count() %}
```

### `all()`

Returns all of the elements in an array.

> {note} If the `asArray` param was set to `true`, then the elements will be represented as raw arrays, rather than element objects.

#### PHP

```php
use craft\elements\Entry;

$entries = Entry::find()
    ->section('news')
    ->limit(10)
    ->all();
```

#### Twig

```twig
{% set entries = craft.entries()
    .section('news')
    .limit(10)
    .all() %}
```

> {tip} If you loop through an element query as if it were an array, `all()` will be called automatically for you, and you will actually be looping through its results.

### `one()`

Returns the first matching element, or `null` if there isn’t one.

> {note} If the `asArray` param was set to `true`, then the element will be represented as a raw array, rather than an element object.

#### PHP

```php
use craft\elements\Entry;

$entry = Entry::find()
    ->section('news')
    ->slug('hello-world')
    ->one();
```

#### Twig

```twig
{% set entry = craft.entries()
    .section('news')
    .slug('hello-world')
    .one() %}
```

### `nth()`

Returns the `n`th matching element, or `null` if there isn’t one. Note that `n` is 0-indexed, so `nth(0)` will give you the first element, `nth(1)` will give you the second, etc.

> {note} If the `asArray` param was set to `true`, then the element will be represented as a raw array, rather than an element object.

#### PHP

```php
use craft\elements\Entry;

$entry = Entry::find()
    ->section('news')
    ->nth(4);
```

#### Twig

```twig
{% set entry = craft.entries()
    .section('news')
    .nth(4) %}
```

### `ids()`

Returns an array of the IDs of the matching elements.

#### PHP

```php
use craft\elements\Entry;

$entryIds = Entry::find()
    ->section('news')
    ->ids();
```

#### Twig

```twig
{% set entryIds = craft.entries()
    .section('news')
    .ids() %}
```

### `column()`

Returns an array of all the first column’s values.

> {note} By default the first column will be the elements’ IDs, but you can customize that with the `select()` param.

#### PHP

```php
use craft\elements\Entry;

$uris = Entry::find()
    ->section('news')
    ->select('uri')
    ->column();
```

#### Twig

```twig
{% set uris = craft.entries()
    .section('news')
    .select('uri')
    .column() %}
```

### `scalar()`

Returns the first column’s value of the first matching element.

> {note} By default the first column will be the element’s ID, but you can customize that with the `select()` param.

#### PHP

```php
use craft\elements\Entry;

$uri = Entry::find()
    ->section('news')
    ->slug('hello-world')
    ->select('uri')
    ->scalar();
```

#### Twig

```twig
{% set uri = craft.entries()
    .section('news')
    .slug('hello-world')
    .select('uri')
    .scalar() %}
```

### Aggregate Methods

The following methods will run an aggregate method on the first column of matching elements, and return the result:

- `sum()` – Returns the sum of all the values in the first column
- `average()` – Returns the average number of all the values in the first column
- `min()` – Returns the minimum number of all the values in the first column
- `max()` – Returns the maximum number of all the values in the first column

> {note} By default the first column will be the elements’ IDs, but you can customize that with the `select()` param.

#### PHP

```php
use craft\elements\Entry;

$sum = Entry::find()
    ->section('news')
    ->select('field_someNumberField')
    ->sum();
```

#### Twig

```twig
{% set sum = craft.entries()
    .section('news')
    .select('field_someNumberField')
    .sum() %}
```
