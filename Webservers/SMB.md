# SMB
## Impacket SMBServer
Impacket is my preferred method of setting up a file transfer over SMB because it does not run in the background or involve modifying configuration files. We can use smbserver.py specifically. If other people attempt to connect to the service, it has the bonus that it will display a NetNTLMv2 hash that, if successfully cracked, will reveal the password.

### Impacket SMBServer - Syntax
````bash
/usr/share/doc/python3-impacket/examples/smbserver.py -smb2support <share name> <location> #or impacket-smbserver -smb2support <share name> <location>
````
Note: Prefix your command with sudo python3 depending on system

It is essential always to have a separate order for file transfers to prevent unauthorized persons from gaining access to our local data. For example, if we would use our home directory to transfer the desired data and an unauthorized person accesses it, he will be able to navigate to the ".ssh" directory and find the SSH keys, which can be used against us.

We can set up an SMB share on our box called "FileTransfer", but we create our folder for it before. Now we can share our current working directory.

### Impacket SMBServer - Syntax
````bash
mkdir Transfers && cd Transfers
/usr/share/doc/python3-impacket/examples/smbserver.py -smb2support FileTransfer $(pwd)
````
To verify the share was created and accessible, we can run:

### Impacket SMBServer - Syntax
````bash
Ryohei@htb[/htb]$ sudo smbclient -L 127.0.0.1

Enter WORKGROUP\root's password: 

	Sharename       Type      Comment
	---------       ----      -------
	IPC$            Disk      
	FileTransfer    Disk      
SMB1 disabled -- no workgroup available
````
Going to our Impacket terminal, we can see the connection and if we had not known the user's password before. We could use the line under "Authenticated Successfully" to Hashcat and attempt to crack it.

### Impacket SMBServer - Listening

````bash
[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
[*] Incoming connection (127.0.0.1,38928)
[*] AUTHENTICATE_MESSAGE (WORKGROUP\root,PARROT)
[*] User PARROT\root authenticated successfully
[*] root::WORKGROUP:4141414141414141:926ebbd136be706117721fbcad821410:01010000000000008039147afabcd601a3099ada8208fd2c00000000010010004a006e00640046006700460075007000030010004a006e0064004600670046007500700002001000640062005a0054005300490054004b0004001000640062005a0054005300490054004b00070008008039147afabcd6010600040002000000080030003000000000000000000000000000000054af210c79b6f4bd986d404d7769e730cecdf6b2feec6c6d5cd8a765d98034bb0a0010000000000000000000000000000000000009001c0063006900660073002f003100320037002e0030002e0030002e00310000000000
[*] Connecting Share(1:IPC$)
[*] NetrShareEnum Level: 1
[*] Disconnecting Share(1:IPC$)
[*] Closing down connection (127.0.0.1,38928)
[*] Remaining connections []
````
In some cases, computers will not allow anonymous SMB connections. In this case, we may want to use the user/password flags to allow authentication on our SMB server. This can be done with the following command:

### Impacket SMBServer - Listening
````bash
Ryohei@htb[/htb]$ /usr/share/doc/python3-impacket/examples/smbserver.py -user USERNAME -password PASSWORD FileTransfer $(pwd)
````
As we have seen, there are a few quick and easy ways to transfer files over HTTP and SMB. Aside from their real-world applications, these examples can be used on the provided Pwnbox during any module that requires a file transfer (i.e., transferring a zip file containing BloodHound data collected during the Active Directory BloodHound module.
