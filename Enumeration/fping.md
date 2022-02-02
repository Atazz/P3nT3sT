# Fping

It's like ping but for ping sweeps.

fping is a program to send ICMP echo probes to network hosts, similar to ping, but much better performing when pinging multiple hosts. fping has a very long history: Roland Schemers did publish a first version of it in 1992 and it has established itself since then as a standard tool for network diagnostics and statistics.

```bash
fping -a -g 192.168.82.0/24 2>/dev/null
```