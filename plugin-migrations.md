Plugin Migrations
=================

If your schema changes over the life of your plugin, you can write a [migration](http://www.yiiframework.com/doc-2.0/guide-db-migrations.html) to keep existing installations updated with the latest schema. Craft automatically checks for new migrations whenever a plugin’s schema version number changes.

- [Creating Migrations](#creating-migrations)
  - [Logging](#logging)
- [Executing Migrations](#executing-migrations)
- [Install Migrations](#installation-migrations)

## Creating Migrations

To create a new migration, open up your terminal and go to a Craft project that your plugin is installed in:

    cd /path/to/project

Then run the following command to generate a new migration file for your plugin (replacing `MIGRATION_NAME` and `PLUGIN_HANDLE` with your migration name and plugin handle, respectively):

    ./craft migrate/create MIGRATION_NAME --plugin=PLUGIN_HANDLE

> {tip} If your Craft install is running from a Vagrant box, you will need to SSH into the box to run this command.

> {note} Migration names must be valid PHP class names, though we recommend sticking with `snake_case` rather than `StudlyCase` as a convention.

Enter `yes` at the prompt, and a new migration file will be created in a `migrations/` subfolder within your plugin’s source directory.

The migration file contains a class with two methods: `safeUp()` and `saveDown()`. `safeUp()` is where you should put the main migration code. If you want to make it possible to revert your migration, `safeDown()` is where the reversion code goes.

### Logging

If you want to log any messages in your migration code, echo it out rather than calling `Craft::info()`:
 
```php
echo "    > some note\n";
```

If the migration is being run from a console request, this will ensure the message is seen by whoever is executing the migration, as the message will be output into the terminal. If it’s a web request, Craft will capture it and log it to `storage/logs/` just as if you had used `Craft::info()`.

> {tip} Your migration’s base classes, `craft\db\Migration` and `yii\db\Migration`, provide lots of handy methods for making changes to the database schema. Use these whenever possible, rather than calling `Craft::$app->db->createCommand()`, as they will automatically echo out a message about the command, and how long it took to execute.

## Executing Migrations

To execute your plugin’s migrations, you’ll need to increase its schema version. (If you haven’t already explicitly defined your plugin’s schema version, it will be `1.0.0` by default.)

```php
class Plugin extends \craft\base\Plugin
{
    public $schemaVersion = '1.0.1';
    
    // ...
}
```

With that in place, go to your Control Panel, and Craft will prompt you to run any pending plugin migrations. Click “Finish up” to do that.

Alternatively, you can run pending migrations from your terminal with the `migrate/up` command:

    ./craft migrate/up --plugin=PLUGIN_HANDLE

## Install Migrations

Plugins can have a special “Install” migration which handles the installation and uninstallation of the plugin. If your plugin has any database changes it needs to make when installed/uninstalled, you can create an Install migration by running the `migrate/create` command using the migration name `install`:

    ./craft migrate/create install --plugin=PLUGIN_HANDLE

The migration will be created in your plugin’s `migrations/` folder just like other migrations, but unlike the others, it will not be named with a timestamp – just `Install.php`. Also unlike the others, you cannot execute the Install migration with the `migrate/up` command. The only time it gets run is when your plugin is being installed (`safeUp()`) or uninstalled (`safeDown()`), courtesy of `craft\base\Plugin::install()` and `uninstall()`.
