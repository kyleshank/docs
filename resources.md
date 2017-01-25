Front End Resources
===================

If your plugin has any CSS, Javascript, images, or other front-end resources, you can place them in a `resources/` directory within your plugin’s source directory.

There are multiple ways to work with these files.

## Including CSS and JS Resources

If you need to include one of your CSS or JS resources on the currently rendering page, use `craft\web\View::registerCssResource()` or `registerJsResource()`.

#### PHP

```php
\Craft::$app->view->registerCssResource('pluginHandle/css/styles.css');
\Craft::$app->view->registerJsResource('pluginHandle/js/script.js');
```

#### Twig

```twig
{% do view.registerCssResource('pluginHandle/css/styles.css') %}
{% do view.registerJsResource('pluginHandle/js/script.js') %}
```

> {note} Everything after `pluginHandle/` should be the resource path, relative to your `resources/` folder. 

Those methods will automatically define an [asset bundle](http://www.yiiframework.com/doc-2.0/guide-structure-assets.html#asset-bundles) for you, whose base path is set to your plugin’s `resources/` directory. When it’s time to include your CSS/JS file on the page, your entire `resources/` directory will get published into the web root somewhere (if it wasn’t already published), and the published resource URL will be used in the HTML include tag.

Craft provides a helper function, `UrlHelper::getResourceUrl('path/to/file.ext')`, which returns the URL to a resource file. Templates have a similar function: `{{ resourceUrl('path/to/file.ext') }}`. The URL returned by these functions will work even if the craft/ folder is placed above the web root.

## Obtaining the URL to a Published Resource

If you need to get the published URL of one of your resources, you can do that with the help of `craft\web\AssetManager::getPublishedUrl()`.

```php
$dirUrl = \Craft::$app->assetManager->getPublishedUrl('@ns/prefix/resources', true);
$resourceUrl = $dirUrl.'/images/foo.svg';
```

> {note} Replace `ns/prefix` with your plugin’s namespace prefix, with forward-slashes instead of backslashes.
