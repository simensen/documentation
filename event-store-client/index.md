---
outputFileName: index.html
---

# Overview

This part covers async event store client connections and what to take into consideration.

Prooph Event Store Client supports async non-blocking TCP connection to EventStore.

All operations are handled fully asynchronously, returning an `Amp\Promise`.

The `EventStoreConnection` maintains a full-duplex connection between the client and the Event Store server.

## PHP Extensions

You need the [allegro/php-protobuf](https://github.com/allegro/php-protobuf/) PHP extension.

The protobuf extension from Google will not work, because it doesn't support proto2, only proto3.

Additional extensions are only needed if your app necessitates a high numbers of concurrent socket connections.

- [ev](https://pecl.php.net/package/ev)
- [event](https://pecl.php.net/package/event)
- [php-uv](https://github.com/bwoebi/php-uv) (experimental fork)

## Async Example

An example using Amp's event loop:

```php
namespace Prooph\EventStoreClient;

use Amp\Loop;

Loop::run(function () {
    $connection = EventStoreConnectionFactory::createFromEndPoint(
        new EndPoint('eventstore', 1113)
    );

    yield $connection->connectAsync();

    $slice = yield $connection->readStreamEventsForwardAsync(
        'foo-bar',
        0,
        10,
        true
    );

    \var_dump($slice);
});
```

### Amp 

This library is written using [Amp](https://github.com/amphp/amp/) and works out of the box with it.

### ReactPHP

There is an [adapter](https://github.com/amphp/react-adapter) to make any [ReactPHP](https://github.com/reactphp/react/) library compatible with Amp.

### Swoole

There is a [driver](https://github.com/swoole/library/blob/master/src/Swoole/Driver/Amp.php) to be compatible with [swoole](https://github.com/swoole/swoole-src).

## Client Installation

```bash
composer require prooph/event-store-client
```

To install allegro/php-protobuf:

    git clone https://github.com/allegro/php-protobuf
    cd php-protobuf
    git checkout v0.12.3
    phpize
    ./configure
    make
    sudo make install

    add "extension=protobuf.so" to your php.ini

## Unit tests

Run the server with memory database

```bash
./run-node.sh --run-projections=all --mem-db
```

You need to exclude the ignore group

```bash
./vendor/bin/phpunit --exclude-group=ignore
```

Those are tests that only work against an empty database and can only be run manually.

Before next run, restart the server. This way you can always start with a clean server.

* * *

## Next Step

The next part covers reading events from a stream.

-   [Step 1 - Install, run, and write your first event](~/getting-started/install.md)
