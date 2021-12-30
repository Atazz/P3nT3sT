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

