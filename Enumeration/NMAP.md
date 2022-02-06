# Nmap Scanning

## Quick scanning of open ports for further scanning


```bash  
ports=$(nmap -p- --min-rate=1000 -T4 <IP> | grep '^[0-9]' | cut -d '/' -f 1 | tr '\n' ',' | sed s/,$//) 
```

```bash
nmap -p$ports -sC -sV <IP>
```

## Ping Scan

```bash
nmap -sn 192.168.68.0/24

nmap -sn 192.168.68.1-15

nmap -sn 192.168.68.*

nmap -sn 192.168.*.1

nmap -sn 192.168.68-69.*

nmap -sn -iL hostlist.txt
```

`-iL` = `Input list for host scanning`

# OS Fingerprinting

```bash
nmap -Pn -O <targets>
```

