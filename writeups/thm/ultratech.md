# TryHackMe UltraTech writeup - Medium
The basics of pentration testing, enumeration, privlilege escaltion, and webapp testing

## Part 1 - Enumeration

The first thing im going to do is run an nmap scan on the target IP address (10.10.208.199).

```.sh
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


### Which software is using the port 8081?

- To find this I did an nmap scan looking for the service and version (-sV) specificly on port 8081.
- nmap -sV 10.10.208.199

```.sh
PORT     STATE SERVICE VERSION
8081/tcp open  http    Node.js Express framework
MAC Address: 02:3C:7E:9D:8D:85 (Unknown)
```
From this we can see that the software running is Node.js.

### Which other Non-standard port is used?
- To find this, I ran an nmap scan that scans all ports rather than just the top 1000
    `{ nmap 10.10.208.199 -p- }`

```.sh
PORT      STATE SERVICE
21/tcp    open  ftp
22/tcp    open  ssh
8081/tcp  open  blackice-icecap
31331/tcp open  unknown
```
From this we can see that 31331 is the other non standard port running.

### Which software using this port?
- To find this, I ran an nmap scan that looks for the service and version (-sV) on port 31331.
- nmap -sV 10.10.208.199 -p 31331

```.sh
PORT      STATE SERVICE VERSION
31331/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
MAC Address: 02:3C:7E:9D:8D:85 (Unknown)
```
From this we can see that Apache is the software using this port.

### Which GNU/Linux distribution seems to be used?
- To find this we can use the nmap scan from above.
- We can see that Ubuntu is the GNU/Linux distribution being used.

### The software using the port 8081 is a RESTapi, how many of its routes are used by the web application?
- To find this we can use an nmap scan that goes into more detail, -A.
- nmap -A 10.10.208.199 -p 8081.

```.sh
PORT     STATE SERVICE VERSION
8081/tcp open  http    Node.js Express framework
|_http-cors: HEAD GET POST PUT DELETE PATCH
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
MAC Address: 02:3C:7E:9D:8D:85 (Unknown)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.13 (97%), Linux 3.8 (96%), ASUS RT-N56U WAP (Linux 3.4) (95%), Linux 3.16 (95%), Linux 3.1 (93%), Linux 3.2 (93%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (92%), Linux 3.10 (92%), Linux 3.19 (92%), Linux 3.2 - 4.8 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop

TRACEROUTE
HOP RTT     ADDRESS
1   0.56 ms ip-10-10-208-199.eu-west-1.compute.internal (10.10.208.199)
```
From this we can see there are 2 Rest api's; _http-cors, and _http-title. 

## Part 2 - Explotation

### There is a database lying around, what is its filename?
- To start off, I opened my web browser and went to http://10.10.208.199:8081. The only thing to pop up is UltraTech API v0.1.3, which might be vulnerable. Im going to look at the other port before attempting to exploit this one.
- When looking at http://10.10.208.199:31331, we find a somewhat sophisticated looking webpage. Upon futher inspection on the Aboutus page we find a list of potential users

```.sh
John McFamicom | r00t
Francois LeMytho | P4c0
Alvaro Squalo | Sq4l
```


### What is the first user's password hash?

### what is the password associated with this hash?




  

