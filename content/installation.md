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

## Trimming dependencies

The skeleton project comes with a few dependencies that are not necessary for the framework to function, but have been included as a convenience.

`autarky/twig-templating` provides Twig integration into the framework. If you want to use a different templating engine or no templating altogether, you can remove this.

`monolog/monolog` provides logging features. If you want to use a different logger or don't want logging at all, you can remove this.

`symfony/browser-kit` enables the BrowserKit client in PHPUnit WebTestCases. If you don't plan to use this feature, you can remove this.

`symfony/yaml` enables YAML config files. If you convert all your config files to PHP, you can remove this.

`vlucas/dotenv` makes it easy to store environment-specific variables outside of your codebase. If you don't want to do this, you can remove the package.
