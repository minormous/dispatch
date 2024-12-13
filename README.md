Minormous Dispatch
==============

[![Latest Stable Version](https://img.shields.io/packagist/v/minormous/dispatch.svg)](https://packagist.org/packages/minormous/dispatch)
[![License](https://img.shields.io/packagist/l/minormous/dispatch.svg)](https://github.com/minormous/dispatch/blob/master/LICENSE)
[![Build Status](https://travis-ci.org/minormous/dispatch.svg)](https://travis-ci.org/minormous/dispatch)
[![Code Coverage](https://scrutinizer-ci.com/g/minormous/dispatch/badges/coverage.png?b=master)](https://scrutinizer-ci.com/g/minormous/dispatch/?branch=master)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/minormous/dispatch/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/minormous/dispatch/?branch=master)

An HTTP Interop compatible middleware dispatcher in [Minormous](http://minormous.github.io/).
Attempts to be [PSR-1](http://www.php-fig.org/psr/psr-1/), [PSR-2](http://www.php-fig.org/psr/psr-2/),
[PSR-4](http://www.php-fig.org/psr/psr-4/), [PSR-7](http://www.php-fig.org/psr/psr-7/),
and [PSR-15](http://www.php-fig.org/psr/psr-15/) compliant.

Heavily influenced by the design of [Tari by ircmaxwell](https://github.com/ircmaxell/Tari-PHP).

For more information, see [the documentation](http://minormousframework.readthedocs.org/en/latest/dispatch).

## Install

```
composer require minormous/dispatch
```

## Usage

The `MiddlewareCollection` is a container for middleware that acts as the entry point.
It takes two arguments:

- An array of `$middleware` which must be instances of server middleware.
- A callable `$default` that acts as the terminator for the collection and returns
  an empty response.

Once the collection is prepared it can dispatched with a server request and will return
the response for output.

### Example

```php
use Minormous\Dispatch\MiddlewareCollection;

// Any implementation of PSR-15 MiddlewareInterface
$middleware = [
    new FooMiddleware(),
    // ...
];

// Default handler for end of collection
$default = function (ServerRequestInterface $request) {
    // Any implementation of PSR-7 ResponseInterface
    return new Response();
};

$collection = new MiddlewareCollection($middleware);

// Any implementation of PSR-7 ServerRequestInterface
$request = ServerRequest::fromGlobals();
$response = $collection->dispatch($request, $default);
```

### Nested Collections

The `MiddlewareCollection` also implements the `MiddlewareInterface` to allow
collections to be nested:

```php
use Minormous\Dispatch\MiddlewareCollection;

// Any implementation of PSR-15 MiddlewareInterface
$middleware = [
    new FooMiddleware(),

    // A nested collection
    new MiddlewareCollection(...),

    // More middleware
    new BarMiddleware(),
    // ...
];

$collection = new MiddlewareCollection($middleware);

// HTTP factories can also be used
$default = [$responseFactory, 'createResponse'];
$request = $serverRequestFactory->createRequest($_SERVER);

$response = $collection->dispatch($request, $default);
```
