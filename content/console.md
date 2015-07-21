# Console (CLI)

Autarky ships with the Symfony 2 Console component for making CLI interfaces to your application. The console application is invoked by running `php app/console.php`, which will show a list of available commands.

If you have an existing Command class, you can add it by adding a line to your `app/console.php` file:

```php
$console->add(new MyNamespace\MyCommand(/* ... */));
```

If your command has dependencies that should be resolved from the container, you can do the following:

```php
$container = $app->getContainer();
$command = $container->resolve('MyNamespace\MyCommand');
$console->add($command);
```

If you want your commands to lazily resolve dependencies for performance reasons, it's best to pass the `$container` instance to the command and then call `$container->resolve()` inside the command's `fire()` method.

Service providers can configure the console application via their `bootConsole(ConsoleApplication $app)` method:

```php
use Symfony\Component\Console\Application as ConsoleApplication;
public function bootConsole(ConsoleApplication $app) {}
```
