# clue/promise-stream-react [![Build Status](https://travis-ci.org/clue/php-promise-stream-react.svg?branch=master)](https://travis-ci.org/clue/php-promise-stream-react)

The missing link between Promise-land and Stream-land, 
built on top of [React PHP](http://reactphp.org/).

**Table of Contents**

* [Usage](#usage)
  * [buffer()](#buffer)
  * [unwrapReadable()](#unwrapreadable)
* [Install](#install)
* [License](#license)

> Note: This project is in beta stage! Feel free to report any issues you encounter.

## Usage

This lightweight library consists only of a few simple functions.
All functions reside under the `Clue\React\Promise\Stream` namespace.

The below examples assume you use an import statement similar to this:

```php
use Clue\React\Promise\Stream;

Stream\buffer(…);
```

Alternatively, you can also refer to them with their fully-qualified name:

```php
\Clue\React\Promise\Stream\buffer(…);
``` 

### buffer()

The `buffer(ReadableStreamInterface $stream)` function can be used to create
a `Promise` which resolves with the stream data buffer.

```php
$stream = accessSomeJsonStream();

Stream\buffer($stream)->then(function ($contents) {
    var_dump(json_decode($contents));
});
```

### unwrapReadable()

The `unwrapReadable(PromiseInterface $promise)` function can be used to unwrap
a `Promise` which resolves with a `ReadableStreamInterface`.

This function returns a readable stream instance (implementing `ReadableStreamInterface`)
right away which acts as a proxy for the future promise resolution.
Once the given Promise resolves with a `ReadableStreamInterface`, its data will
be piped to the output stream.

```php
//$promise = someFunctionWhichResolvesWithAStream();
$promise = startDownloadStream($uri);

$stream = Stream\unwrapReadable($promise);

$stream->on('data', function ($data) {
   echo $data;
});

$stream->on('end', function () {
   echo 'DONE';
});
```

If the given promise is either rejected or fulfilled with anything but an
instance of `ReadableStreamInterface`, then the output stream will emit
an `error` event and close:

```php
$promise = startDownloadStream($invalidUri);

$stream = Stream\unwrapReadable($promise);

$stream->on('error', function (Exception $error) {
    echo 'Error: ' . $error->getMessage();
});
```

The given `$promise` SHOULD be pending, i.e. it SHOULD NOT be fulfilled or rejected
at the time of invoking this function.
If the given promise is already settled and does not resolve with an
instance of `ReadableStreamInterface`, then you will not be able to receive
the `error` event.

## Install

The recommended way to install this library is [through Composer](https://getcomposer.org).
[New to Composer?](https://getcomposer.org/doc/00-intro.md)

This will install the latest supported version:

```bash
$ composer require clue/promise-stream:^0.1
```

See also the [CHANGELOG](CHANGELOG.md) for details about version upgrades.

## License

MIT
