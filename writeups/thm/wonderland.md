Wonderland - Fall down the rabbit hole and enter wonderland - Medium

Objectives - Find the flag in user.txt and root.txt

I started by running a basic nmap scan on the target IP address
```
nmap -A 10.10.97.29
```
With this scan we found that port 22 (ssh) and 80 (HTTP) are open. My next step was to curl the webpage to see if that would revel any vulnerabilities, it did not. Never fear, the next thing I did was run dirbuster on the webpage. Eventully I run down the web directories to: 
```
http://10.10.97.29/r/a/b/b/i/t/
```
Next I inspected the webpage for more information and found what looks to be like credentials for alice.
```
alice:HowDothTheLittleCrocodileImproveHisShiningTail
```
Next Im going to try those credentials to access the SSH server. 
```
ssh alice@10.10.97.29
```
Im in!! The first thing we see once we log into alices computer is root.txt and a .py file. This is what the code for that file looks like:
```
py code that can be exploited
```

