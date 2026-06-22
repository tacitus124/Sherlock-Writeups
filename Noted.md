

---


#### Sherlock Scenario

Simon, a developer working at Forela, notified the CERT team about a note that appeared on his desktop. The note claimed that his system had been compromised and that sensitive data from Simon's workstation had been collected. The perpetrators performed data extortion on his workstation and are now threatening to release the data on the dark web unless their demands are met. Simon's workstation contained multiple sensitive files, including planned software projects, internal development plans, and application codebases. The threat intelligence team believes that the threat actor made some mistakes, but they have not found any way to contact the threat actors. The company's stakeholders are insisting that this incident be resolved and all sensitive data be recovered. They demand that under no circumstances should the data be leaked. As our junior security analyst, you have been assigned a specific type of DFIR (Digital Forensics and Incident Response) investigation in this case. The CERT lead, after triaging the workstation, has provided you with only the Notepad++ artifacts, suspecting that the attacker created the extortion note and conducted other activities with hands-on keyboard access. Your duty is to determine how the attack occurred and find a way to contact the threat actors, as they accidentally locked out their own contact information.



----



Task 1

What is the full path of the script used by Simon for AWS operations?

```
C:\Users\Simon.stark\Documents\Dev_Ops\AWS_objects migration.pl
```
Located in /Noted/C/Users/Simon.stark/AppData/Roaming/Notepad++/config.xml
<img width="1094" height="274" alt="image" src="https://github.com/user-attachments/assets/8b10e926-68c2-4764-b61e-cb86318cdfc9" />




Task 2

The attacker duplicated some program code and compiled it on the system, knowing that the victim was a software engineer and had all the necessary utilities. They did this to blend into the environment and didn't bring any of their tools. This code gathered sensitive data and prepared it for exfiltration. What is the full path of the program's source file?

```
C:\Users\Simon.stark\Desktop\LootAndPurge.java
```

The JAVA@2023-07-24_145332 file in Backup folder is a ransom script.
<img width="1406" height="630" alt="image" src="https://github.com/user-attachments/assets/14fe224b-07ef-45ef-83cc-487b19c181bd" />


We can find the files location in session.xml
<img width="1676" height="443" alt="image" src="https://github.com/user-attachments/assets/bc13a860-8c41-4e21-947f-858a0c7a1716" />




Task 3

What's the name of the final archive file containing all the data to be exfiltrated?

```
Forela-Dev-Data.zip
```

<img width="1026" height="596" alt="image" src="https://github.com/user-attachments/assets/9ea81082-1c2f-4660-8acb-343ac53ed965" />




Task 4

What's the timestamp in UTC when attacker last modified the program source file?

```
2023-07-24 09:53:23
```

https://www.infralin.com/convertwindowsfiletime.htm


Task 5

The attacker wrote a data extortion note after exfiltrating data. What is the crypto wallet address to which attackers demanded payment?

```
0xca8fa8f0b631ecdb18cda619c4fc9d197c8affca
```

use the password "sdklY57BLghvyh5FJ#fion_7" found in the ransom note to unlock the text at 
[Forela-Extortion - Pastebin.com](https://pastebin.com/xmTkajd5)


Task 6

What's the email address of the person to contact for support?

```
CyberJunkie@mail2torjgmxgexntbrmhvgluavhj7ouul5yar6ylbvjkxwqf6ixkwyd.onion
```
https://pastebin.com/xmTkajd5
