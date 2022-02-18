## Joomla Version
Most recent versions of Joomla contains a "joomla.xml" file within the "/administrator/manifests/files" directory, which is readable without authentication to the application.

```bash
curl -s http://<target-joomla>/administrator/manifests/files/joomla.xml | grep "<version>" | grep -Po "(\d+\.)+\d+"
```