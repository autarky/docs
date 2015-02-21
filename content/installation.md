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
