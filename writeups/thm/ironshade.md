# IronShade - Medium - Compromise assessment on a linux host

## Task 1 - Linux Challenge

### What is the Machine ID of the machine we are investigating?

To find this we can go to the /etc folder and cat the machine-id file. 

<p><span style="color:red">
dc7c8ac5c09a4bbfaf3d09d399f10d96
</span></p>

### What backdoor user account was created on the server?

To find this we can look at what users we have in the home folder, since we are ubuntu, the only other user would be microservice.

### What ist he cronjob that was set up by the attaker for persistence?



### Examine the running processes on the machine. Can you identify the suspicous-looking hidden process from the backdoor account?

### How many processes are found to be running from the backdoor account's directory?

### What is the name of the hidden file in memory from the root directory?

### What suspicious services were installed on the server? Format is service a, service b in alphabetical order.

### Examine the logs; when was the backdoor account created on this infected system?

### From which IP address were multiple ssh connections observed against the suspicious backdoor account?

### How many failed SSH login attempts were observed on the backdoor account?

### Which malicious package was installed on the host?

### What is the secret code found in the metadata of the suspicious package?
