# Extension

Autarky provides a pretty bare minimum of what is needed to get you going with PHP development. You, the user, are meant to provide most of the meat for it.

## Adding functionality

As an example, let's say you want to use Eloquent, the Laravel ORM, in your application. The recommended course of action here is to write your own service provider, in which you share an instance of the class `Illuminate\Database\Capsule\Manager`. An official example on how to use this class can be found [here](https://github.com/illuminate/database#usage-instructions).

All we need to do is bind the manager class as a shared instance onto the container inside the service provider's `register()` method, like this:

	use Illuminate\Database\Capsule\Manager;

	class EloquentProvider extends \Autarky\Providers\AbstractProvider
	{
		public function register()
		{
			$container = $this->app->getContainer();

			$container->define('Illuminate\Database\Capsule\Manager', function() {
				$capsule = new Capsule;
				$capsule->addConnection([...]);
				return $capsule;
			});

			$container->share('Illuminate\Database\Capsule\Manager');
		}
	}

If we want Eloquent to be available we also need to run a special method. We do this by adding a `config()` callback to the `register()` method block:

	$this->app->config(function($app) {
		$this->app->resolve('Illuminate\Database\Capsule\Manager')
			->bootEloquent();
	});

## Replacing functionality

You might also want to replace core components of the framework. Most of them are registered by service providers, so you can simply remove the service provider from your `app/start.php` file and replace it with your own.

In some cases, the core classes implement an interface in order to decouple with other parts of the framework. If the class you want to replace implements an interface, you too should implement this interface and alias your class as the default implementation of this interface in the container.
