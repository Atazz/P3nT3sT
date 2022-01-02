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
````bash
impacket-GetUserSPNs <domain>/<username> -dc-ip <DC-IP>
````
