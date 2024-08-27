#TryHackMe UltraTech writeup - Medium
The basics of pentration testing, enumeration, privlilege escaltion, and webapp testing

##Part 1
The first thing im going to do is run an nmap scan on the target IP address (10.10.208.199).
```
Starting Nmap 7.60 ( https://nmap.org ) at 2024-08-27 18:11 BST
Nmap scan report for ip-10-10-208-199.eu-west-1.compute.internal (10.10.208.199)
Host is up (0.00089s latency).
Not shown: 997 closed ports
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
8081/tcp open  blackice-icecap
MAC Address: 02:3C:7E:9D:8D:85 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 1.80 seconds
```


Which software is using the port 8081?

Whcih other Non-standard port is used?

Which software using this port?

Which GNU/Linux distrobution seems to be used?

The software using the port 8081 is a RESTapi, how many of its routes are used by the web application?
