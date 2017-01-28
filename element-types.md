Element Types
=============

Plugins can provide custom element types by creating a class that implements `craft\base\ElementInterface` and `craft\base\ElementTrait`.

As a convenience, you can extend `craft\base\Element`, which provides a base element type implementation.

You can refer to Craft’s own element classes for examples. They are located in `vendor/craftcms/cms/src/elements/`.

## Element Query Classes

If you need to make any modifications to queries for your element type, such as joining in a custom database table, or supporting custom query parameters, you will need to create a custom element query class.

Element query classes should extend `craft\elements\db\ElementQuery`.

You can refer to Craft’s own element query classes for examples. They are locaed in `vendor/craftcms/cms/src/elements/db/`.

Once you have created your element query class, you’ll need to update your element class’s static `find()` method to return new instances of it:

```php
public static function find(): ElementQueryInterface
{
    return new MyElementQuery(get_called_class());
}
```
