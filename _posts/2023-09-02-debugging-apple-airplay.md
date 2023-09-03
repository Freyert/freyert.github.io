---
layout: default
author: Fulton byrne
title: "Debugging Apple Airplay"
---

Problem: airplay very rarely is able to connect to my samsung tv.

Resources
* [_Unofficial Airplay Specification_](https://nto.github.io/AirPlay)
* [_nmap.org: Script broadcast-dns-service-discovery_](https://nmap.org/nsedoc/scripts/broadcast-dns-service-discovery.html)



First, discover the IP address being broadcast via mDNS:

```
nmap --script=broadcast-dns-service-discovery
Starting Nmap 7.94 ( https://nmap.org ) at 2023-09-02 18:53 EDT
Pre-scan script results:
| broadcast-dns-service-discovery: 
|   224.0.0.251
|     8296/tcp amzn-alexa
|       Address=192.168.1.50
|     37247/tcp airplay
|       acl=0
|       deviceid=D0:C2:4E:B6:B7:D6
|       features=0x7F8AD0,0x38BCB46
|       rsf=0x3
|       fv=p20.T-NKM2AKUC-2141.2
|       flags=0x4c
|       model=QAQ70
|       manufacturer=Samsung
|       serialNumber=0BFE3CYT300520M
|       protovers=1.1
|       srcvers=377.25.06
|       pi=0F:6B:09:DB:20:90
|       psi=00000000-0000-0000-0000-0F6B09DB2090
|       gid=00000000-0000-0000-0000-0F6B09DB2090
|       Address=192.168.1.50
|     38008/tcp spotify-connect
|       Address=192.168.1.50
|     58247/tcp companion-link
|_      Address=192.168.1.215 fe80::69:9ed1:9d20:da5d
```
From this I can see that the TV runs Alexa, Airplay, and Spotify connect.
Since this is coming from multicast DNS I will assume the TV is up and broadcasting this information.


Attempting a `ping` shows I can not reach the host.

```
ping 192.168.1.50
PING 192.168.1.50 (192.168.1.50): 56 data bytes
Request timeout for icmp_seq 0
Request timeout for icmp_seq 1
Request timeout for icmp_seq 2
^C
--- 192.168.1.50 ping statistics ---
4 packets transmitted, 0 packets received, 100.0% packet loss
```

Checking on the router I can see that the TV is indeed provisioned the 192.168.1.50 address.

Unfortunately this is where the path goes dark. My TV and router are both locked down so I can't 
get more information directly from them to figure out why (intermettently) I can't use airplay with the TV.
However, I now know that when I can `ping` the TV it means I can use airplay. Which I am going to deduce is related to the router and not the TV.
