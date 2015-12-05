# Routing

Autarky comes with a very simplistic router out of the box, based on top of [nikic's FastRoute](https://github.com/nikic/FastRoute). Much of the syntax and rules are the same, especially those relating to route parameters.

Routes can be added in two ways - either programatically somewhere that won't be invoked until the application is booted (like a configurator class, or a config callback):

```php
$router->addRoute('GET', '/', ['MyController', 'exampleAction'], 'first.route');
```

Or by adding them to the routes.yml config file:

```yaml
second.route:
  method:  'GET'
  path:    '/bar/{v1}'
  handler: ['MyController', 'otherAction']
```

The method you map the route to in your controller should accept the route parameters as arguments.

Keep in mind that the route parameter names *have* to match exactly - `{foo}` means the controller method must take a `$foo` argument.

This is not true the other way around - your controller method can take an argument that isn't a route parameter, as long as the argument has a default value.

In addition to route paramters, your controller methods can type-hint any argument to the `Symfony\Component\HttpFoundation\Request` class to receive the request instance. This can be useful if you need to access the request headers, query strings or something else.

## Events

The router has a few useful events you can add listeners to: The "match", "before" and "after" events.

"Before" and "after" event listeners are the most common ones. These have to be given a name and manually added to a route:

```php
$config = [
	'before' => ['before_name'],
	'after'  => ['after_name']
];
$router->group($config, function($router) {
	$router->addRoute(/* ... */);
});
```

Or in your `routes.yml` config:

```yaml
my_route:
  method:  'GET'
  path:    '/foo/bar/baz'
  handler: ['MyController', 'myAction']
  before:  ['before_action']
```

The "before" event is fired before a route's controller/handler is invoked. In this event listener it is possible to stop the router from invoking the controller at all by setting a response, or replacing the controller.

The event object gives you access to the route object, controller, and request object via the methods `getRoute()`, `getController()`, and `getRequest()`. You can set a response or the controller via the `setResponse()` and `setController()` methods.

```php
$router->addBeforeHook('before_name', function($event) {
	if (something()) {
		$event->setResponse(new Response('replacing the original response'));
	} elseif (somethingelse()) {
		$event->setController(['SomeOtherController', 'someAction']);
	}
});
```

The "after" event is fired after a route's controller has been invoked, and can be used to manipulate or replace the response object.

The event object gives you the access to the route and request as in "before" listeners, but also the respone object via `getResponse()` and `setResponse()`.

```php
$router->addAfterHook('after_name', function($event) {
	$response = $event->getResponse();
	if (something()) {
		$response->headers->set('foo', 'bar');
	} else if (somethingelse()) {
		$event->setResponse(new Response('replacing the original response'));
	}
});
```

There are also **global** before/after listeners, which don't need a name and are run on every route.

```php
$router->addGlobalBeforeHook(function($event) {});
$router->addGlobalAfterHook(function($event) {});
```

The "match" event is the least useful one - it is fired when a route is matched with an URL and can be used for manipulation of the route object if you really know what you're doing.
