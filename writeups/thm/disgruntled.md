# Disgruntled
A TryHackMe box focusing on Linux forensics.

This box is created around the scenario of a disgruntled IT user conducting suspicious activity on the network. My job is to discover what they did and stop it. 

### 1. What commands did they run?

The user installed a package on the machine with elevated privledges. In order to find out what commands they ran I will be looking in the authentication logs or auth.log located in /var/log.

<p><span style="color:red"><em>cat auth.log | grep -i COMMAND</em></span></p>

The package they installed was named dokuwiki, which is an opensource wiki software written in PHP. The present owkring directory was /home/cybert. This user also created a user and a script file. This information can still be gathered by using the command above. 

```
User created - it-admin
Date sudo was given - Dec 28 06:27:34
Script file name - bomb.sh

```
To find more information on bomb.sh, I will be looking in the .bash_history file. 
