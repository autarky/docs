# Configuration

## Configuration data

Autarky comes with a cascading environment-based config data system that makes it easy to manage a global and environment-specific configurations.

First of all, take a look at the closure that is `$env` in the default skeleton `app/start.php` file. The return value of the closure passed in here should be a string, and this will be the name of the environment the application runs under. This can be strings such as 'production', 'staging', 'local' and so on.

The config store loads files from the `app/config` directory by default. Each file represents a top-level dictionary key - for example, `$config->get('foo')` will attempt to read from `app/config/foo.*`.

> The config arrays are merged using a simple `array_merge`, so only top-level array keys can be overriden.

Packages can "mount" their own config directory or file(s) onto the config store. For example, a package may mount their config files as the name "foo". These files can then be accessed via `$config->get('foo/bar')`. This will first look for the file(s) `foo.*` in the package's config directory and load that. Then, if the file(s) `app/config/foo/bar.*` exists, those will be loaded and merged, allowing you to easily overwrite package config.

Out of the box, Autarky supports PHP array-based config files as well as YML/YAML files. You can have PHP and YML files in the same directory and they will both load fine. You usually want to use PHP files where you need some sort of programming capabilities (like re-using variables, grabbing environment variables, or finding the root path of the project using PHP constants like `__DIR__`), whereas YAML is well suited for static data.

> Note that the "path" config file currently cannot be YML.

To use the config store, either retrieve it with `$app->getConfig()` or add it as a type-hinted constructor argument:

```php
use Autarky\Config\ConfigInterface;

class MyClass {
	public function __construct(ConfigInterface $config)
```

## Environment configuration

In some cases, you may not want to keep certain configuration data in `app/config`. Sensitive data such as API keys, database passwords, password salts are best kept separate from the rest of the config data.

This is best achieved by using the `.env` file in your project's root directory. This file is added to the project's `.gitignore` by default, and by utilizing the [vlucas/phpdotenv package](https://github.com/vlucas/phpdotenv), we can load the values into the process's environment variables.

To use an environment variable in PHP, simply do `getenv('ENV_VAR_KEY')`. The [getenv() function](http://php.net/manual/en/function.getenv.php) will return false if the environment variable does not exist, or the value of the variable if it does exist.

## Runtime configuration

In addition to the config data available in the `app/config` directory, there is another type of configuration which we will call runtime configuration.

There are many ways to do runtime configuration of your application, including passing a callback to `$app->config()` either in `app/start.php` or a service provider, but the easiest is to create a configurator.

1. Create a class that implements the interface `Autarky\ConfiguratorInterface`.
2. Define the classes the configurator needs to configure as type-hinted constructor arguments.
3. Execute the configuration in the `configure()` method.
4. Add the name of the class to the `configurators` array in `config/app`.

Autarky comes with a couple of configurators you can look at for reference. The [route configurator](https://github.com/autarky/framework/blob/ce4f88b3454f5375b23909d11d24574a41b49ab7/classes/Routing/DefaultRouteConfigurator.php) is very simple and a good starting point, while the [log configurator](https://github.com/autarky/framework/blob/ce4f88b3454f5375b23909d11d24574a41b49ab7/classes/Logging/DefaultLogConfigurator.php) is more intricate.

Note that you can add configurators via code by calling `$app->config('NameOfConfiguratorClass')`. This can be useful if you need to load a configurator only in specific environments, for example.
