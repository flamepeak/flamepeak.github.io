---
title: PHP Debugging 
date: 2019-12-16 15:07:04
tags: [PHP]
categories: [Web前端]
---


### Dumping variables to stdout

- **var_dump ($var)** dumps the variable type and value to stdout.
- **print_r ($var)** prints the variable value in human-readable form to stdout.
- **get_defined_vars()** gets all the defined variables including built-ins and custom variables (print_r to view them).
- **debug_zval_dump ($var)** dumps the variable with its reference counts. This is useful when there are multiple paths to update a single reference.
- **debug_print_backtrace()** prints a backtrace that shows the current function call-chain.
- **debug_backtrace()** gets the backtrace. You can print_r, log it to a file, or send it to a logging endpoint asynchronously.



eg:  

```php
<?php
$myVar = "hello world!";

var_dump($myVar);
print_r($myVar);

$allVars = get_defined_vars();
print_r($allVars);
debug_zval_dump($allVars);

function sayHello($hello) {
    echo $hello;
    debug_print_backtrace();
}

sayHello($myVar);
?>
```

[Test PHP online](https://paiza.io/en/projects/new?language=php)


### Switching error reporting level

PHP has a few ways to configure error reporting. You can use the `php.ini` file, if you have access to it. Otherwise, you might use the `htaccess` configuration. If you can’t use configuration files, you have the option of changing the values via a script.

- **error_reporting** sets the level of logging. **E_NOTICE** is useful during development since it will tell you about defects such as unassigned variables.
- **display_errors** tells PHP if and where to display error messages.
- **display_startup_errors** should only be used when debugging.
- **log_errors** and **error_log** work together to send errors to a log file. Do this in production rather than displaying them to end users.



### PHP debugging tools

 You can debug PHP using one of many debugging tools to attach a debugger client. [PhpStorm](https://www.jetbrains.com/help/phpstorm/php-debugging-session.html) works with debug utilities like [Xdebug](https://xdebug.org/docs/) and [ZendDebugger](http://www.zend.com/en/products/studio/downloads). 

 Being a polyglot, I need an IDE that supports multiple languages, so I’m opting for [VS Code](https://code.visualstudio.com/) these days. 

1)  [Install XDebug](https://xdebug.org/docs/install) 

I highly recommend you make a simple `test.php` file, put a `phpinfo();` statement in there, then copy the output and paste it into the [XDebug installation wizard](https://xdebug.org/wizard.php). It will analyze it and give you tailored installation instructions for your environment.



2)  [Configure PHP to use XDebug](https://xdebug.org/docs/install#configure-php)

 [Configure PHP to use XDebug](https://xdebug.org/docs/install#configure-php) by adding `zend_extension=path/to/xdebug` to your php.ini. The path of your php.ini is shown in your `phpinfo()` output under "Loaded Configuration File". 

3)  Enable remote debugging in your `php.ini`: 

```
; allow remote debugging
[XDebug]
xdebug.remote_enable = 1
xdebug.remote_autostart = 1
```

4) If you are doing web development, don't forget to restart your webserver to reload the settings.

5) Verify your installation by checking your `phpinfo()` output for an XDebug section.



### VScode Error

1) **setting php.validate.executablePath**

 Go to File> Preferences > Settings ...  

输入框中输入php，修改 `PHP › Validate: Executable Path` 下`Eidit in settings.json`

```
{
"php.validate.executablePath": "C:\\xampp\\php\\php.exe",
"php.executablePath": "C:\\php7.1\\php.exe"
}
```











参考：

[A Detailed Guide to PHP Debugging]( https://stackify.com/php-debugging-guide/ )

