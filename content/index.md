# Autarky

Autarky is a minimalist framework that doesn't hold your hand and doesn't put restrictions on you, aimed at experienced developers or quick learners. It's designed for the perfect balance between flexibility, enabling rapid development, and clean object oriented programming.

Heavily inspired by Python's Flask, Autarky is a stripped down implementation of all the favourite things from various other PHP frameworks. I hope you'll find that it's less invasive, more loosely coupled and less bloated than other frameworks, but still makes it easy to get started quickly.

> Autarky is currently at version 0.8, which means breaking changes can happen as according to [SemVer](http://semver.org/)'s 4th rule. However, the API can be considered relatively stable at this point (february 2015), with the worst breaking changes being classes or config keys renaming.

## Goals and vision

The main goal of the framework is to provide a thin, unopiniated routing and dependency injection layer that wraps around Symfony's HttpFoundation component. Anything that goes on beyond the routing is entirely up to the programmer - you simply map URLs to classes and methods, the configurable dependency injecting container resolves these classes and calls the methods, and the return value is turned into a HTTP response.

The framework does not come with a database layer, mail library, cache layer, authentication service, queue services, translation service and so on. The implementation and integration of these are entirely left up to you, whether you want to write them yourself or pick a third party library, all you should need is a service provider class that binds the services to the container and you're good to go.

Key features and design concepts include:

- No procedural code, everything can be wrapped in classes and callbacks
- Intelligent service/dependency injection container - automatic dependency injection via typehinting in constructor parameters, powerful factory definitions
- Utilizes [Composer](https://getcomposer.org/) for package management and autoloading
- Utilizes [Symfony's HttpFoundation](http://symfony.com/doc/current/components/http_foundation/introduction.html) for easy handling of responses and requests as well as [session management](http://symfony.com/doc/current/components/http_foundation/sessions.html)
- Comes with an implementation of nikic's [FastRoute](https://github.com/nikic/FastRoute), a [very performant router](http://nikic.github.io/2014/02/18/Fast-request-routing-using-regular-expressions.html)
- Application-level middleware using [StackPHP](http://stackphp.com/)
- Seamless integration with [Twig](http://twig.sensiolabs.org/), the most robust and mature templating engine available in PHP
- Connection manager for management and lazy instantiation of PDO objects
- Easily testable - implementing HttpKernelInterface means you can utilize Symfony's BrowserKit and [DomCrawler](http://symfony.com/doc/current/components/dom_crawler.html) components, which makes system-level/functional testing a breeze. [Check out an example.](https://github.com/autarky/skeleton/blob/master/tests/ExampleTest.php)

## Creating a project

This repository contains the framework's core classes and tests. To create a new project using Autarky, we need to use [Composer](https://getcomposer.org/):

	composer create-project autarky/skeleton -s dev --prefer-dist /path/to/project

This will set up a minimalist project for you to build on top of. You can show the demo page in your browser by setting up PHP's built-in web server and open "localhost:8000" in your favourite browser:

	php -S localhost:8000 -t /path/to/project/web

Keep in mind that during version 0.x, breaking changes may happen as the minor version increments. The [Github releases](https://github.com/autarky/framework/releases) page contains upgrade instructions and changelogs.

## Contributing

Autarky is [hosted on Github](https://github.com/autarky). The main repository is [autarky/framework](https://github.com/autarky/framework) - please use this repository for reporting issues. See the repository/repositories CONTRIBUTING file for more information.

## Contact

Feel free to open an issue on Github if you have any problems or suggestions. Alternatively (or additionally), you may want to ask on [Stack Overflow](http://stackoverflow.com) using the ["autarky" tag](http://stackoverflow.com/questions/ask?tags=autarky,php).

Contact [Andreas Lutro](https://github.com/anlutro) personally for potential security issues.

## License

The contents of this repository is released under the [MIT license](http://opensource.org/licenses/MIT).
