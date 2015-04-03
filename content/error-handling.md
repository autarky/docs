# Error handling

By default, once the framework has been booted, all uncaught exceptions and PHP errors, warnings, notices etc. will be caught by the framework's error handler.

The error handler is highly configurable and makes it easy to define a global behaviour for exceptions of different types.

## How it works

The error handler (`Autarky\Errors\ErrorHandlerManager`) keeps a doubly linked list of handlers. When an exception is thrown anywhere in your application and isn't caught by a try/catch block, it's passed to the the manager.

When the manager is passed an exception, it iterates through its list of handlers. It invokes each handler in order and checks the return value. If the handler returns null, the manager moves on to the next handler. If the handler returns anything other than null, the manager tries converting this value into a HTTP response and returns it to the end user - no further handlers are called.

Typically your handlers will be split into two types - the ones returning null and the ones returning a response. An example of handlers that return null would be a handler that writes information about the exception to a log file. An example of handlers taht return a value would be a handler that renders a pretty page telling the user what went wrong and what they can do about it.

When the manager reaches the end of the list, if no handler has returned a non-null value, the **default error handler** is invoked. This handler is Symfony's error handler, which renders a grey page saying "Whoops, something went wrong". If debug mode is enabled in the `config/app` config file, you also get information about the exception that was thrown.

> If you install the package `filp/whoops`, the default error handler will use Whoops instead of Symfony's error handler as the default (but only in debug mode).

## Adding error handlers

The easiest and recommended way to add your own error handlers is via the `app.error_handlers` array in the `config/app` file. The handler classes listed here are added to the manager's list in the order you specify them.

The classes must implement the `Autarky\Errors\ErrorHandlerInterface`. This interface contains a single method:

`handle(Exception $exception)` - handles the exception and returns either a response-like value or null.

You can also add handlers programmatically. Since the handler list is doubly linked, you can either insert handlers to the beginning (top) of the list, corresponding with high priority, or the end (bottom), corresponding with low priority.

	$manager = $app->getErrorHandler();
	$manager->prependHandler($myHandler); // high priority handler
	$manager->appendHandler($myHandler); // low priority handler
