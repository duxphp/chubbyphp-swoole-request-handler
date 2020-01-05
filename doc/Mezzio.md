# Mezzio

## Requirements

* [mezzio/mezzio][1]: ^3.2.1

## Example

```php
<?php

declare(strict_types=1);

namespace App;

use Chubbyphp\SwooleRequestHandler\OnRequest;
use Chubbyphp\SwooleRequestHandler\PsrRequestFactory;
use Chubbyphp\SwooleRequestHandler\SwooleResponseEmitter;
use Laminas\Diactoros\ServerRequestFactory;
use Laminas\Diactoros\StreamFactory;
use Laminas\Diactoros\UploadedFileFactory;
use Mezzio\Application;
use Swoole\Http\Server;

$loader = require __DIR__.'/vendor/autoload.php';

/** @var Application $app*/
$app = ...;

$http = new Server('localhost', 8080);

$http->on('start', function (Server $server): void {
    echo 'Swoole http server is started at http://localhost:8080'.PHP_EOL;
});

$http->on('request', new OnRequest(
    new PsrRequestFactory(
        new ServerRequestFactory(),
        new StreamFactory(),
        new UploadedFileFactory()
    ),
    new SwooleResponseEmitter(),
    $app
));

$http->start();
```

### with blackfire

```php
<?php

declare(strict_types=1);

namespace App;

use Blackfire\Client;
use Chubbyphp\SwooleRequestHandler\Adapter\BlackfireOnRequestAdapter;
use Chubbyphp\SwooleRequestHandler\OnRequest;

/** @var OnRequest $onRequest */
$onRequest = ...;

if (extension_loaded('blackfire') {
    $onRequest = new BlackfireOnRequestAdapter($onRequest, new Client());
}

$http->on('request', $onRequest);
```

### with newrelic

```php
<?php

declare(strict_types=1);

namespace App;

use Chubbyphp\SwooleRequestHandler\Adapter\NewRelicOnRequestAdapter;
use Chubbyphp\SwooleRequestHandler\OnRequest;

/** @var OnRequest $onRequest */
$onRequest = ...;

if (extension_loaded('newrelic') && false !== $name = ini_get('newrelic.appname')) {
    $onRequest = new NewRelicOnRequestAdapter($onRequest, $name);
}

$http->on('request', $onRequest);
```

[1]: https://packagist.org/packages/mezzio/mezzio