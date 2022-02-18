# WPScan
Other tools like WPScan: https://github.com/iniqua/plecost

## Updating WPScan
Updating the vulnerability database etc.
```bash
wpscan --update
```

## Enumerating WordPress
Using WPScan you can easily gather a lot of information about the WordPress site.
Avoid risking being caught by IDS / firewall use `--uger-agent` and well know ones.
`Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.169 Safari/537.36`

```bash
wpscan --url http://<target>
target=10.0.0.1; wpscan --url http://$target:80 --enumerate u,t,p | tee $target-wpscan-enum
```