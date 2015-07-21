# Dependency injection

Dependency injection is one of the core values of the framework. It is one of the main tools in languages such as PHP to write pure, clean OOP, and in combination with auto-injection via reflection on typehinted dependencies, allows for rapid development with little need for configuration.

## The container

At the core of any dependency injection system lies some sort of a container. These can have many names - IoC container, service container, service locator - which one of these the Autarky one is we'll leave to the pedants to find out, we'll simply call it "the container".

The container is an intelligent class capable of figuring out how to construct or locate dependencies to any class you may have in your application.

If you have a main class that has a dependency that is another class, if you type-hint against the dependency class in the main class's constructor, the dependency will automatically be resolved when you ask the container to provide an instance of the main class.

This resolving works recursively, so you can have deep hierarchies of cascading classes, and by using shared instances you can have the same instance of a particular class shared across the entire application.

As a basic example of how you can use the container's powers to your advantage: Controllers (classes mapped to routes) in Autarky are always resolved from the container, which means that any type-hinted argument in the controller's constructor will be automatically resolved. If the dependency has dependencies, the same process is repeated for those.

```php
class MyController extends Controller {
	protected $dependency;
	public function __construct(MyDependency $dependency) {
		$this->dependency = $dependency;
	}
}
```

This functionality is not just limited to controllers. Almost any time you pass the name of a class as a string to something inside the framework, it will be resolved from the container. This includes, but is not limited to:

- Controllers
- Event listeners
- Configurators
- Error handlers

## The `new` keyword

The `new` keyword is the enemy of dependency injection and you should almost never use it for anything other than simple data objects (entities, Symfony HTTP response/cookie/etc objects, exceptions...).

Any time you use the `new` keyword to instantiate another class, keep in mind that you're making the part of your code where `new` is called entirely untestable, you're making it hard to replace/mock/stub the class you're instantiating, and you're preventing the class you're instantiating from having its dependencies auto-resolved.

You're also making it near impossible to have shared instances of a class in your application - for example, you don't want to open more than 1 database connection, so you'd like to have a shared PDO instance between all your application classes, but without dependency injection you will have to do `new PDO` in several places, or rely on nasty global/static variables.

Consider the following example:

```php
class MyService {
	public function __construct() {
		$this->pdo = new PDO('some dsn');
		$this->validator = new MyValidator();
	}
}

class MyController extends Controller {
	public function __construct() {
		$this->service = new MyService();
	}
	public function someAction() {
		// do things with $this->service
	}
}
```

This can easily be rewritten to use dependency injection, with the added bonus of being unit testable:

```php
class MyService {
	public function __construct(PDO $pdo, MyValidator $validator) {
		$this->pdo = $pdo;
		$this->validator = $validator;
	}
}

class MyController extends Controller {
	public function __construct(MyService $service) {
		$this->service = $service;
	}
}
```

## Configuring dependency injection

The above example will work fine if MyDependency has no dependencies, or all its dependencies can be automatically resolved without configuration. However, if your constructors type-hint an interface, abstract class or non-class parameter, you will need to configure the IoC container.

The most common configuration is to define a factory for a class, so that each time a specific class is asked for via the container, that factory is called. This is done via the `define()` method.

```php
$container->define('MyNamespace\MyClass', function($container) {
	return new MyClass(/* ... */);
});
$container->define('MyNamespace\MyClass', ['MyClassFactory', 'makeMyClass']);
```

If you have a class that you want to act like a singleton, in that it will only be constructed once and the same instance will be re-used across your entire application, use the `share($class)` method. This can be used with or without a factory as shown above.

```php
$container->share('MyNamespace\MySingleton');
```

Sometimes you want to type-hint against an interface in your constructor parameters and let the container decide which implementation to use. This is done via the `alias($original, $alias)` method. This method can be used to swap different implementations as well, it does not need to be restricted to interfaces or abstract classes.

```php
$container->alias('MyNamespace\MyImplementation', 'MyNamespace\MyInterface');
```

The most useful one is `$container->params($class, array $params)`, which let you configure what classes/variables are passed to a specific class's constructor method.

```php
$container->params('MyNamespace\MySpecificClass', [
	'MyNamespace\MyInterface' => 'MyNamespace\OtherImplementation',
	'$nonClassArgument' => 'foobar',
]);
```

## Factory definitions (new in 0.7)

In your application, some classes will have multiple instances that you need to keep track of. A good example of this may be PDO objects. You may want to use different PDO connections for different things. Autarky's container makes this easy out of the box - assuming you have the connections configured in `app/config/database`, you can do the following:

```php
$container->resolve('MyClass', [
	'PDO' => $container->getFactory('PDO', ['$connection' => 'custom']),
]);
```

Under the hood, this still utilises reflection. It does not matter which position the PDO argument is in MyClass's constructor - in fact, the PDO argument could be dropped from the constructor altogether and you could still resolve it like this without anything breaking.

The reason this works is there is a ConnectionManager class with a `makePdo` method that takes a `$connection` argument. The ConnectionManager stores any number of PDO instances, and will return the one corresponding to the connection name passed to `makePdo`. The `makePdo` method is then registered as the factory for the PDO class, so that any time you try to resolve the PDO class from the container, this factory method is called. Here's a simplified code snippet of how it's done:

```php
$container->define('ConnectionManager', function() {
	return new ConnectionManager(/* ... */);
});
$container->share('ConnectionManager');
$container->define('PDO', ['ConnectionManager', 'makePdo']);
```

## Avoiding reflection (new in 0.7)

If you define a factory that isn't a closure, by default, reflection is going to be used to look up the function/method and its parameters, so that the correct arguments can be resolved and passed to the callable by the container.

Reflection can cause some overhead, or may not be suitable in every situation. It is possible to bypass it entirely by creating factory objects manually.

```php
$factory = $container->makeFactory('MyClass');
$factory = $container->makeFactory(['MyClassFactory', 'makeObject']);
$factory = $container->makeFactory(function(/* ... */) {});
```

Once you have a factory object, you can add arguments to it.

```php
$factory->addClassArgument('$object', 'OtherClass', $required);
$factory->addScalarArgument('$scalar', 'string', $required, $default);
```

Once you've added all your arguments, add it to the container, and you can now resolve your class out of it, which will call the factory.

```php
$container->define('MyClass', $factory);
```
