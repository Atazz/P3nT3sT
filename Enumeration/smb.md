# # SMB
## smbclient
````basb
smbclient -L \\<IP>\ -N
````
**`-L`** -- Listing shares
**`-N`** -- Anonymous login

When logged into the smbclient without the **`-L`**-flag. You can enable **RECURSE ON** and **PROMPT OFF** and list every file available with **`mget *`** 

## smbmap
`smbmap - SMB enumeration tool`
SMBMap  allows  users to enumerate samba share drives across an entire domain. List share drives, drive permissions, share contents, upload/download functionality, file
name auto-download pattern matching, and even execute remote commands. This tool was designed with pen testing in mind, and is intended to simplify searching for poten‐
tially sensitive data across large networks.

````bash
smbmap -R <share-directory> -H <IP of Host> -A <PATTERN>
````
