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
## Install RSAT
````Powershell
Get-WindowsCapability -Name Rsat.ActiveDirectory.DS-LDS.Tools* -Online | Add-WindowsCapability -Online
````
1. If the command does not work look at the following Group Policies:
1.1 Computer Configuration > Policies > Administrative Templates > System > Specify settings for optional component installation and component repair
1.2 EN: Download repair content and optional features directly from Windows Updates instead of Windows Server Updates Services
2. Computer Configuration > Administrative Templates > Windows Components> Windows Update
2.1 EN: Do not connect to Windows Update Internet addresses
2.2 DA: Unlad at oprette forbindelse ti lnogen Windows Update-relaterede intrnetplaceringer

## SharpHound
````Powershell
.\SharpHound.exe -c All,GPOLocalGroup 
.\SharpHound.exe -c Session --Loop --LoopInterval 00:10:00 --LoopDuration 24:00:0
````
 ## Powershell & command logging
Computer Configuration > Policies > Administrative Settings > Windows Components > Windows PowerShell

## Check SMB version
````powershell
Get-SmbServerConfiguration
````
## Computers trusted for delegation
````powershell
Get-ADComputer -server $logonserver -Filter {TrustedForDelegation-eq $true -and primarygroupid-eq 515} -Properties trustedfordelegation,serviceprincipalname,description
````

## Administrators not in Protected Users group
````powershell
$Domain = ”domain.local”; $Group = Get-ADGroup "Protected Users" -Server $Domain; Get-ADUser -Server $Domain -Filter '(AdminCount -eq 1) -and (Enabled -eq $True)' -Properties MemberOf | Where-Object {$_.MemberOf -notcontains $Group.DistinguishedName} | Select SamAccountName, Name
````
## SMB Signing
````powershell
reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\ /v EnableSecuritySignature
````
REG_DWORD = 1

## LDAP Require signing /sealing
````powershell
reg query HKLM\SYSTEM\CurrentControlSet\Services\LDAP\ /v LDAPClientIntegrity
````
REG_DWORD = 2,

## Network authentication level set to NTLMv2 and refuse LM and NTLM
````powershell
reg query HKLM\System\CurrentControlSet\Control\Lsa\ /v LmCompatibilityLevel
````
REG_DWORD = 5
## Deny write access to removable drives not protected by BitLocker
````powershell
reg query HKLM:\System\CurrentControlSet\Policies\Microsoft\FVE /v RDVDenyWriteAccess
````
REG_DWORD = 1

## Is Wdigest Authentication disabled
````powershell
reg query HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest /v UseLogonCredential
````
REG_DWORD = 0
## Is LAPS enabled
````powershell
req query HKLM\Software\Policies\Microsoft Services\AdmPwd /v AdmPwdEnabled
````
REG_DWORD = 1

## Prevent the storage of the LAN Manager hash of passwords.
````powershell
reg query HKLM\SYSTEM\CurrentControlSet\Control\Lsa\ /value NoLMHash
````
REG_DWORD 1
