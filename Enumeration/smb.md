# # SMB
## smbclient
````basb
smbclient -L \\<IP>\ -N
````
**`-L`** -- Listing shares
**`-N`** -- Anonymous login

When logged into the smbclient without the **`-L`**-flag. You can enable **RECURSE ON** and **PROMPT OFF** and list every file available with **`mget *`** 
