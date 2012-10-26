Soflomo\Log
===
`Soflomo\Log` is a small module that provides a drop-in configuration, instantiation and initialization of a `Zend\Log\Logger` object. Its purpose it to have logging enabled in your Zend Framework 2 application with only a small configuration file. The module is opinionated with the selection of writers, but you are free to configure your own.

Installation
---
`Soflomo\Log` is available through composer. Add "soflomo/log" to your composer.json list. During development of `Soflomo\Log`, you can specify the latest available version:

```
"soflomo/log": "dev-master"
```

Enable the module in your `config/application.config.php` file. Add an entry `Soflomo\Log` to the list of enabled modules. Logging should work out of the box.

Usage
---
This module provides a pre-configured instance of `Zend\Log\Logger`. The logger is connected through various writers. If you log something with the logger, it will push the message to all the writers. The writers itself check the type ("priority") of the message in order to determine whether it actually needs to perform the write. With this setup, you can write all messages (including debug messages) into a console writer (Fire-PHP or Chrome-PHP) and only write critical errors into a file stream.

To get the logger injected, make your service implement `Zend\Log\LoggerAwareInterface'. Then pull your service from the service manager.

```php
namespace MyModule\Service;

use Zend\Log\Logger;
use Zend\Log\LoggerAwareInterface;

class MyService implements LoggerAwareInterface
{
    protected $logger;

    public function setLogger(Logger $logger)
    {
        $this->logger = $logger;
    }

    public function doSomething()
    {
        // Do your work

        if (null !== $this->logger) {
            $this->logger->info('Something done here!');
        }
    }
}
```

If the service is registered into the service manager, fetch the service and the logger is injected.

Configuration
---
The default configuration is a logger object with three different writers:

1. A Fire-PHP writer, used when Fire-PHP is installed;
2. A Chrome-PHP writer, used when Chrome-PHP is installed;
3. A stream writer, writing to `data/log/application.log`.

The stream writes everything from `INFO` (so only debug messages are ignored) up to `EMERG`. However, it waits for logging until an `ERROR` occurs. This means, all messages with `INFO`, `NOTICE` and `WARN` are not logged until an `ERROR` happens. This causes a slim log file with high priority messages and only informative messages when a real error occured.

Configure the behaviour of the three writers in the "writers" key. For example, to disable firephp logging and modify the path of the stream writer, put the following in a configuration file:

```php
return array(
    'soflomo_log' => array(
        'writers' => array(
            'firephp' => array(
                'enabled' => false,
            ),
            'stream'  => array(
                'stream'  => 'data-something/log-else/application.log',
            ),
        ),
    ),
);
```

For all options, check the `config/module.config.php` file of this module.