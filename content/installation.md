# Installation

## Prerequisites

- PHP 5.4 or higher

## Install a new project

Using either a local or global installation of composer:

	composer create-project autarky/skeleton -s dev --prefer-dist /path/to/project

This will set up a sample project for you and install all dependencies. If you configure your webserver to have a docroot of the web directory, or fire up the PHP internal webserver:

	php -S localhost -t /path/to/project/web

... you should be able to go to "localhost" in your browser and see the extremely simple welcome page.

Read some of the example files located in the skeleton project and you should be able to figure out a lot just based on that.

## Project structure

The default project structure looks like this:

	myproject
	├── app
	│   ├── config
	│   │   └── testing
	│   └── templates
	├── bin
	├── src
	│   └── Web
	├── tests
	├── var
	│   ├── logs
	│   ├── session
	│   ├── twig
	│   └── yaml
	└── web
	    └── assets

The `app` directory is entirely voluntary. It contains config files, templates and a couple of procedural scripts. It's meant for any code or data related to your app that isn't PHP classes. You can create as many new directories inside this directory as you want, or you can move the `config` and `templates` directory to the `myproject` root instead if you like.

The `bin` directory contains executable scripts, named after the UNIX `/bin` directory. This directory and its contents can also be removed if you don't want them.

The `src` directory contains only PHP classes. It's configured as a PSR-4 autoloaded directory in the default composer.json. Again, you're free to rename or remove this directory if you want.

The `tests` directory is meant for PHPUnit tests. The default phpunit.xml file has this directory configured as the application test suite. This directory, too, can be renamed or removed.

The `var` directory contains various cache or state data. The `logs` directory obviously contains application log files, the `storage` directory contains serialized session data if you're using the file session driver, the `twig` directory is for Twig's generated template files, and `yaml` contains cached data of parsed YAML files. If you don't use logging, file-based sessions, Twig templating or YAML config files, the `var` directory can safely be removed.

Finally, the `web` directory is meant to be the docroot of your webserver. It contains an `index.php` which serves as a front controller to your web application. Keeping Javascript and CSS files in an `assets` directory is a good practice, but entirely optional.

If you want to rename or move any of these directories, make sure to update the `app/config/path.php` file. If you want to move the `config` directory, you should also update `app/start.php`. The ideal would be to have the directory of the config files placed in the `path.php` config file - but the config loader obviously can't load the "path" config before it knows where the config files are.

In the end, you don't actually need any of these files to run a simple Autarky application. If you want to, you can easily create a single-PHP-file application using Autarky by creating an `index.php` that looks like this:

	<?php
	require_once __DIR__.'/vendor/autoload.php';

	$app = new Autarky\Application('production', [
		new Autarky\Container\ContainerProvider,
		new Autarky\Routing\RoutingProvider,
	]);
	$app->boot();

	$app->route('GET', '/', function() {
		return 'Hello world!';
	});

	$app->run();

This shows how easy it is to adjust the framework to your likings, with as much or little scaffolding and bootstrapping as you feel is optimal for your application.

## Trimming dependencies

The skeleton project comes with a few third-party dependencies that are not necessary for the framework to function, but have been included as a convenience.

`autarky/twig-templating` provides Twig integration into the framework. If you want to use a different templating engine or no templating altogether, you can remove this.

`monolog/monolog` provides logging features. If you want to use a different logger or don't want logging at all, you can remove this.

`symfony/browser-kit` enables the BrowserKit client in PHPUnit WebTestCases. If you don't plan to use this feature, you can remove this.

`symfony/yaml` enables YAML config files. If you convert all your config files to PHP, you can remove this.

`vlucas/dotenv` makes it easy to store environment-specific variables outside of your codebase. If you don't want to do this, you can remove the package.
