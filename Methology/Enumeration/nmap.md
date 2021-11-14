# Scanning all IP quick.


```bash  
ports=$(nmap -p- --min-rate=1000 -T4 <IP> | grep '^[0-9]' | cut -d '/' -f 1 | tr '\n' ',' | sed s/,$//) 
```

```bash
nmap -p$ports -sC -sV <IP>
```