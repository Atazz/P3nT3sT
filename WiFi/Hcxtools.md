# hcxtools

https://github.com/ZerBea/hcxtools

Small set of tools convert packets from captures (h = hash, c = capture, convert and calculate candidates, x = different hashtypes) for the use with latest hashcat or John the Ripper. The tools are 100% compatible to hashcat and John the Ripper and recommended by hashcat. This branch is pretty closely synced to hashcat git and John the Ripper git.

Support of hashcat hash-modes: 4800, 5500, 2200x, 16100, 250x (deprecated), 1680x (deprecated)

Support of John the Ripper hash-modes: WPAPSK-PMK, PBKDF2-HMAC-SHA1, chap, netntlm, tacacs-plus

Support of gzip (.gz) single file compression.

Main purpose is to detect weak points within own WiFi networks by analyzing the hashes. Therefore convert the dump file to WPA-PBKDF2-PMKID+EAPOL hash file and check if wlan-key or plainmasterkey was transmitted unencrypted. Or upload the "uncleaned" dump file (pcapng, pcap, cap) here [https://wpa-sec.stanev.org/?submit](https://wpa-sec.stanev.org/?submit) to find out if your ap or the client is vulnerable by using common wordlists or a weak password generation algorithm.

## [](https://github.com/ZerBea/hcxtools#brief-description)Brief description

Multiple stand-alone binaries - designed to run on Arch Linux, but other Linux distributions should work, too.

All of these utilities are designed to execute only one specific function.

hcxdumptool moved to: [https://github.com/ZerBea/hcxdumptool](https://github.com/ZerBea/hcxdumptool)

Read this post: hcxtools - solution for capturing wlan traffic and conversion to hashcat formats ([https://hashcat.net/forum/thread-6661.html](https://hashcat.net/forum/thread-6661.html))

Read this post: New attack on WPA/WPA2 using PMKID ([https://hashcat.net/forum/thread-7717.html](https://hashcat.net/forum/thread-7717.html))

Read this post: Hash mode 22000 explained ([https://hashcat.net/forum/thread-10253.html](https://hashcat.net/forum/thread-10253.html))

Read this wiki: [https://hashcat.net/wiki/doku.php?id=cracking_wpawpa2](https://hashcat.net/wiki/doku.php?id=cracking_wpawpa2)

Unsupported: Windows OS, macOS, Android, emulators or wrappers and NETLINK!