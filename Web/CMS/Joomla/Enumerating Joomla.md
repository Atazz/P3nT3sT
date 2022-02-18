## Joomla Version
Most recent versions of Joomla contains a "joomla.xml" file within the "/administrator/manifests/files" directory, which is readable without authentication to the application.

```bash
curl -s http://<target-joomla>/administrator/manifests/files/joomla.xml | grep "<version>" | grep -Po "(\d+\.)+\d+"
```

## Joomla PHP version
```bash
curl -sSL -D - http://<target-joomla>/index.php -o /dev/null | grep "X-Powered-By"
```