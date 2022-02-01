# Normal Commands for Linux

## Tar
How to extract tar / tar.gz

```bash
tar -xzf tarfile
```

There are also a few flags you can add to the command to have it perform slightly different functions:

-v: Enables verbose mode, showing the progress of the command
-x: Extract
-z: Uses gzip, omit this if you just have a .tar
-f: specifies file input, rather than STDIN

## Bash Specielt Characters

Data between '' or $() will be evaluated before the whole statement and will become part of this statement

```bash
file `ls /etc/*.conf`
```

