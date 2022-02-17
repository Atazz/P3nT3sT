# LDAP
## ldapsearch
### Print out all data from server with ldap
````bash
ldapsearch -h <IP-ADDRESS> -p 389 -x -b "dc=<DOMAIN>,dc=<DOMAIN>"
````
### Identification of configured SPNs and extraction of hash
Kerberos authentication uses Service Principal Names (SPNs) to identify the account associated
with a particular service instance. ldapsearch can be used to identify accounts that are
configured with SPNs.
````bash
ldapsearch -x -h <IP-ADDRESS> -p 389 -D '<username>' -w '<Password>' -b ""dc=<DOMAIN>,dc=<DOMAIN>" -s sub "(&(objectCategory=person)(objectClass=user)(!(useraccountcontrol:1.2.840.113556.1.4.803:=2))(serviceprincipalname=*/*))"
````
## windapsearch
First git clone from this repository: (https://github.com/ropnop/windapsearch)

### Installation
windapsearch requires the python-ldap module. You should be able to get up and running with two commands:
````bash
git clone https://github.com/ropnop/windapsearch.git
python3 -m pip install -r requirements.txt #or pip install python-ldap #or apt-get install python-ldap or 
````

### Usage of Windapsearch
````bash
./windapsearch.py -d <domain> --dc-ip <IP-ADDRESS-DC -U
````
The latest version is designed to be used with Python 3, but if you are stuck with Python 2, you can use the windapsearch_py2.py script.

### Specifying Domain and Account
To begin you need to specify a Domain Controller to connect to with --dc-ip, or a domain with -d. If no Domain Controller IP address is specified, the script will attempt to do a DNS host lookup on the domain and take the top result.

A valid domain username and password are required for most lookups. If none are specififed the script will attempt an anonymous bind and enumerate the default namingContext, but most additional queries will fail. The username needs to include the full domain, e.g. ropnop@lap.example.com or EXAMPLE\ropnop

The password can be specified on the command line with -p or if left out it will be prompted for.

### Enumerate Users
The -U option performs an LDAP search for all entries where objectCategory=user. By default, it will only display the commonName and the userPrincipalName. The --attrs option can be used to specify custom or additional attributes to display, or the --full option will display everythin for all users.

**WARNING: in a large domain this can get very big, very fast**

## Enumerate Groups and Group Memberships
Use the -G option to enumerate all entries where objectCategory=group. This will output the DN and CN of all groups.

To query group membership, use the -m option with either the DN or CN of the group you wish to query. The tool supports fuzzy search matching so even a partial CN will work. If it matches more than one group, the tool will specify which group to query.

## Impacket-GetADUsers
````bash
GetADUsers.py <domain-name>/ -dc-ip <IP-ADDRESS-DC> -debug
````
