### Python pty module

````
python -c 'import pty; pty.spawn("/bin/bash")'
````

### Upgrading Netcat with magic
Once bash is running in the PTY, background the shell with ``