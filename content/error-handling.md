# Error handling

Autarky provides a way to manage a stack of error handlers.

At the core of this system is the `Autarky\Errors\ErrorHandlerManager` class, with which you'll make most of your interactions. You can get the instance of this class via `$app->getErrorHandler()` - for example, in a service provider:

	$errorHandlerManager = $this->app->getErrorHandler();

The manager keeps a doubly linked list of error handlers. This means you can add handlers to the end of the list or at the start. When an exception is thrown, the manager goes through the list and invokes all the appropriate handlers until something is returned from one of them.

If nothing is returned from any handlers, the grey default Symfony "Whoops, something went wrong" page will be displayed.

## Adding error handlers

The easiest and recommended way to add error handlers is via the `app/config` file. Each handler added here will be called in the order they are listed. The first one to return true from `$handler->handles($exception)` and return a a non-null value from `$handler->handle($exception)` will be the value returned to the end user.

If the handler either doesn't handle the specific type of exception or returns null from `handle()`, the next handler in the stack will be called. If none of the handlers return a non-null value, the default error handler will be invoked.

You can easily create your own error handlers - just implement the `Autarky\Errors\ErrorHandlerInterface` and add the class name to the `error_handlers` config array and you're set.
