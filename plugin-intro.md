Intro to Plugin Dev
===================

- [What are Plugins?](#what-are-plugins)
- [Getting Started](#getting-started)
  - [Setting up the basic file structure](#setting-up-the-basic-file-structure)
  - [composer.json](#composerjson)
  - [`src/Plugin.php`](#srcpluginphp)
  - [Loading your plugin into Craft](#loading-your-plugin-into-craft)
- [Plugin Icons](#plugin-icons)
- [Plugin Changelogs](#plugin-changelogs)

## What are Plugins?

Plugins are mini applications that run alongside Craft’s core code. They can be simple, serving a single purpose like providing a new Dashboard widget type, or they can be complex, introducing entirely new concepts to the system, like an e-commerce application. Craft’s plugin architecture provides a solid foundation for building just about anything.

Technically, plugins are a superset of [Yii Modules](http://www.yiiframework.com/doc-2.0/guide-structure-modules.html), which means they can have [models](http://www.yiiframework.com/doc-2.0/guide-structure-models.html), [active record classes](http://www.yiiframework.com/doc-2.0/guide-db-active-record.html), [controllers](http://www.yiiframework.com/doc-2.0/guide-structure-controllers.html), [application components](http://www.yiiframework.com/doc-2.0/guide-structure-application-components.html), and other things. It wouldn’t hurt to take some time to read up on those concepts if you are new to Yii.

The main benefits of Craft Plugins over Yii Modules are:

- Plugins can be installed, updated, and uninstalled. 
- Plugins can have their own migrations, managed separately from Craft and content migrations.
- Plugins can have a global template variable, accessed from `craft.pluginhandle`.
- Plugins can have their own section in the Control Panel.

## Getting Started

### Setting up the basic file structure

To create a plugin, create a new directory wherever you like to work on development projects. Give it the following structure:

```
base_dir/
  composer.json
  src/
    Plugin.php
```

> {tip} It doesn’t matter what name you give the root directory. Choose something that makes sense.

### composer.json

Whether or not you wish to make your plugin available as a Composer dependency (you should), your plugin must have a `composer.json` file. Craft will check this file to get basic information about the plugin.

Use this template as a starting point for your `composer.json` file:

```json
{
  "name": "package/name",
  "description": "Your plugin’s package description",
  "type": "craft-plugin",
  "minimum-stability": "dev",
  "require": {
    "craftcms/cms": "^3.0.0-alpha.1"
  },
  "autoload": {
    "psr-4": {
      "root\\namespace\\": "src/"
    }
  },
  "extra": {
    "handle": "pluginhandle",
    "name": "Plugin Name",
    "developer": "Developer Name",
    "developerUrl": "https://developer-url.com"
  }
}
```

Replace:

- `package/name` with your desired Composer [package name](https://getcomposer.org/doc/04-schema.md#name).
- `root\\namespace\\` with the root PHP namespace your plugin’s PHP classes will use. (Use double-backslashes because JSON.)
- `pluginhandle` with your plugin’s handle.
- `Plugin Name` with the name your plugin should have within the Control Panel.
- `Developer Name` with your name, or the organization name that the plugin should be attributed to.
- `https://developer-url.com` with the URL to the website the developer name should link to in the Control Panel.

> {note} Don’t use `pixelandtonic\\*` or `craft\\*` as your namespace root. Those should only be used for first party code.

> {note} Your plugin’s name and handle must both be unique to be included in the Craft Plugin Store.

### `src/Plugin.php`

The `src/Plugin.php` file is your plugin’s primary class. It will get instantiated at the beginning of every request. Its `init()` method is the best place to register event listeners, and any other steps it needs to take to initialize itself.

Use this template as a starting point for your `Plugin.php` file:

```php
<?php
namespace root\namespace;

use Craft;

class Plugin extends \craft\base\Plugin
{
    public function init()
    {
        parent::init();
        
        // Custom initialization code goes here...
    }
}
```

Replace `root\namespace` with the namespace you chose in your `composer.json` file (with single-backslashes and without any trailing backslashes).

### Loading your plugin into Craft

There are two ways to make your plugin visible to Craft:
 
1. Move/symlink the plugin into your `plugins/` folder
2. Set your plugin up as a Composer dependency

#### Move/symlink the plugin into your `plugins/` folder

This route is simpler, but it’s only practical if your plugin isn’t going to have any of its own Composer dependencies (besides Craft itself).

In your terminal, go to your Craft project’s `plugins/` folder and create a symlink to your plugin’s root directory. The symlink should be named identically to your plugin’s handle.

```
> cd ~/dev/my-craft-project/plugins
> ln -s ~/dev/my-plugin pluginhandle
```

#### Set your plugin up as a Composer dependency

Composer supports a [`path`](https://getcomposer.org/doc/05-repositories.md#path) repository type, which can be used to symlink your plugin into the `vendor/` folder right alongside your project’s other dependencies (like Craft itself). This is the best way to go if your plugin has any of its own dependencies, as Composer will still load those into the `vendor` folder like normal.

To set this up, open your Craft project’s `composer.json` file and add a new `path` repository record, pointed at your plugin’s root directory.

```json
{
  "repositories": [
      {
        "type": "path",
        "url": "../my-plugin"
      }
    ]
}
```

In your terminal, go to your Craft project and tell Composer to require your plugin. (Use the same package name you gave your plugin in its `composer.json` file.)

```
> cd ~/dev/my-craft-project
> composer.phar require package/name
```

> {note} One caveat of `path` Composer repositories is that Composer is not too smart about keeping their dependencies updated when calling `composer update`. You may need to remove and re-require your plugin in your Craft project each time its dependencies change. 

## Plugin Icons

Plugins can provide an icon, which will be visible on the Settings → Plugins page.
 
<img src="assets/plugin-index.png" width="1159" alt="The Settings → Plugins page in Craft’s Control Panel.">

Plugin icons must be square SVG files, saved as `icon.svg` at the root of your plugin’s source directory (e.g `src/`).

If your plugin has a [Control Panel section](cp-templates.md), you can also give its global nav item a custom icon by saving an `icon-mask.svg` file in the root of your plugin’s source directory. Note that this icon cannot contain strokes, and will always be displayed in a solid color (respecting alpha transparency).

## Plugin Changelogs

If you keep a changelog for your plugin (you should), you can configure your plugin with its URL. Whenever Craft is checking for updates, it will download and parse your changelog to see if any updates to your plugin are available.

Your changelog should be saved as `CHANGELOG.md` at the root of your plugin’s repo. (That’s not a technical requirement, but putting it there will make it easier to find for people viewing your plugin on GitHub or within their `vendor/` directory.) It should be a Markdown-formatted document that loosely follows the suggestions on [keepachangelog.com](http://keepachangelog.com/).

Within the changelog, releases should be listed in descending order (newest on top). Craft will stop parsing the changelog as soon as it hits a version that is older than or equal to the installed version.

The version headers in your changelog must follow the general format:

```markdown
## X.Y.Z - YYYY-DD-MM
```

with the following possible deviations:

- A 4th version number is allowed (e.g. `1.2.3.4`).
- Pre-release versions are allowed (e.g. `1.0.0-alpha.1`).
- The version can start with `v` (e.g. `v1.2.3`).
- The version can be hyperlinked (e.g. `[1.2.3]`).
- A `[CRITICAL]` flag can be appended after the date to indicate a critical release.

As for the release notes, Craft will parse all content between version headings as [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/#GitHub-flavored-markdown). You can have H[3-6] headings, paragraphs, lists, code blocks, etc. The only thig that is *not* allowed is actual HTML code, which will be escaped.
  
You can also define **notes** and **tips**, using this syntax:

```markdown
> {note} A word of warning.

> {tip} A helpful tip.
```

<img src="assets/plugin-update.png" width="1060" alt="The Utilities → Updates page in Craft’s Control Panel, with an available update for the “Foo” plugin.">

Once your plugin’s changelog is set up and available publicly somewhere, you can configure your plugin with its URL by setting its `$changelogUrl` property from its [`init()`](http://www.yiiframework.com/doc-2.0/yii-base-object.html#init()-detail) method:

```php
public function init()
{
    parent::init();

    $this->changelogUrl = 'https://raw.githubusercontent.com/pixelandtonic/foo/master/CHANGELOG.md';
    
    // ...
}
```
