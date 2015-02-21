# Templating

Autarky comes with an integrated implementation of Twig, the most mature and fully-featured PHP templating library at the moment.

> Twig integration is a separate package since version 0.8. `composer require autarky/twig-templating` if you don't already have it installed.

The templating engine can be accessed via the class `Autarky\TwigTemplating\TemplatingEngine`, but the easiest way is to use the `render($template, array $data)` method which comes with the base controller class.

Simply do `return $this->render('path/to/view.twig', [..])` from a controller, and the view's contents will be rendered and put into a HTTP response with status code 200.

By default the framework comes with a Twig environment loading view files from your server's filesystem. Put a file called `hello.twig` in `app/templates` and you can do `$this->render('hello.twig')`. The path to the template is relative to the templates directory.

## Twig functions

In addition to Twig's built-in functionality, a few Autarky-specific functions are added.

`url('route.name', [param1, param2, ...])` will return the URL to the given route with the given parameters.

`asset('path/to/asset.css.js')` will return the URL to the asset relative to the public directory.

`partial(['MyPartialController', 'myMethod'])` will resolve MyPartialController from the container, call myMethod() and print the returned value as a string.

## Twig globals

`flash` is a global variable, being an array of flash messages passed from a controller via the `$this->flashMessages($messageOrMessages)` method. What this array contains is entirely up to the user of the framework - it can be simple strings or simple data objects, depending on what you need/want.

## Events

You can listen for events and modify template data at run-time.

	use Autarky\TwigTemplating\Event\CreatingTemplateEvent;
	use Autarky\TwigTemplating\Event\RenderingTemplateEvent;

	$engine = $app->resolve('Autarky\TwigTemplating\TemplatingEngine');
	$engine->creating('path/to/template.twig', ['MyListener', 'creating']);
	$engine->rendering('path/to/template.twig', ['MyListener', 'rendering']);

	class MyListener
	{
	    public function creating(CreatingTemplateEvent $event)
	    {
	        $context = $event->getTemplate()->getContext();
	        $context->foo = 'bar';
	    }
	    public function rendering(RenderingTemplateEvent $event) {}
	}

The type-hinting for the events is optional. You can also get the context by doing `$event->getContext()`. After setting `$context->foo = 'bar'`, the variable `foo` is now available in the Twig template.
