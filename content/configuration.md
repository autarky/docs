# Configuration

## Configuration data

Autarky comes with a cascading environment-based config data system that makes it easy to manage a global and environment-specific configurations.

First of all, take a look at the closure that is `$env` in the default skeleton `app/start.php` file. The return value of the closure passed in here should be a string, and this will be the name of the environment the application runs under. This can be strings such as 'production', 'staging', 'local' and so on.

All the files located directly in `app/config` are always loaded first. Then, if the directory `app/config/{ENVIRONMENT}` exists, files located there are loaded and override the global values. The config arrays are merged using a simple `array_merge`, so only top-level array keys can be overriden.

If you're using any packages that have service providers that add their own config files via `$config->addNamespace('my-namespace', $path)`, you can override this package's config files by putting files into `app/config/my-namespace` or `app/config/{ENVIRONMENT}/my-namespace`.

Out of the box, Autarky supports PHP array-based config files as well as YML/YAML files. You can have PHP and YML files in the same directory and they will both load fine. You usually want to use PHP files where you need some sort of programming capabilities (like re-using variables, grabbing environment variables, or finding the root path of the project using PHP constants like `__DIR__`), whereas YAML is well suited for static data.

> Note that the "path" config file currently cannot be YML.

## Runtime configuration

In addition to the config data available in the `app/config` directory, there is another type of configuration which we will call runtime configuration.

There are many ways to do runtime configuration of your application, including passing a callback to `$app->config()` either in `app/start.php` or a service provider, but the easiest is to create a configurator.

1. Create a class that implements the interface `Autarky\ConfiguratorInterface`.
2. Define the classes the configurator needs to configure as type-hinted constructor arguments.
3. Execute the configuration in the `configure()` method.
4. Add the name of the class to the `configurators` array in `config/app`.

Autarky comes with a couple of configurators you can look at for reference. The [route configurator](https://github.com/autarky/framework/blob/ce4f88b3454f5375b23909d11d24574a41b49ab7/classes/Routing/DefaultRouteConfigurator.php) is very simple and a good starting point, while the [log configurator](https://github.com/autarky/framework/blob/ce4f88b3454f5375b23909d11d24574a41b49ab7/classes/Logging/DefaultLogConfigurator.php) is more intricate.

Note that you can add configurators via code by calling `$app->config('NameOfConfiguratorClass')`. This can be useful if you need to load a configurator only in specific environments, for example.
