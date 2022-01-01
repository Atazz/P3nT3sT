# Group Policy Preferences
Group Policy Preferences (GPP) was introduced in Windows Server 2008, and among many other
features, allowed administrators to modify users and groups across their network.
An example use case is where a company’s gold image had a weak local administrator password,
and administrators wanted to retrospectively set it to something stronger. The defined password
was AES-256 encrypted and stored in Groups.xml. However, at some point in 2012 Microsoft
published the AES key on MSDN, meaning that passwords set using GPP are now trivial to crack
and considered low hanging fruit.
## Installation
````bash
sudo apt install gpp-decrypt
````
## Crack GPP password
````bash
gpp-decrypt <encrypted_password>
````
