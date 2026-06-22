

---

#### Sherlock Scenario

Happy Grunwald, the CEO of Forela, decided to expand the company's business in Lahore, Pakistan, and brought along his IT Administrator, Alonzo Spire, to help set up the new office and ensure the company's IT infrastructure was running smoothly. However, they faced some challenges due to the language barrier and unreliable power supply in the area. Despite these challenges, they worked closely with local vendors to set up the new office, and Alonzo ensured the IT infrastructure was secure and reliable. They also made an effort to learn about the local culture and customs, which helped them build relationships with the locals. After a few days, Happy received a call from the UK security team, informing him that his workstation had been compromised, despite having received security awareness training and not opening any suspicious emails or links. A memory dump was retrieved and provided to you as the forensic analyst. Your task is to analyze the memory artefact and provide insight into the threat actor who compromised the workstation.


---



Task 1

What is the PID of the malicious process that gave the threat actor initial access?


```
9620
```



Dump the command line arguments and decode the base64 encoded command
```
vol3 -f Dump.mem windows.cmdline
```


![](Pasted%20image%2020260616222630.png)




![](Pasted%20image%2020260616222733.png)
**Summary (stager):**

- **Purpose**: Downloads an encrypted payload from http://43.204.228.96:24/news.php and executes it in memory via IEX.
- **Obfuscation**: Hides C2 address in Base64 (UTF-16), uses fake IE User-Agent + static cookie.
- **Encryption**: RC4 with hardcoded key y_NMoPemWH/&<R?4Y,7!paV%)~9bZj:J (first 4 bytes = IV).
- **Behavior**: Common red team / malware pattern for loading second-stage C2 implant.







Task 2

There is no evidence of an email client being used. Which application did Happy Grunwald use to read the emails?

```
firefox
```



To get email related information we can run bulk extractor on the memory. Then we grep for grunwald to find out he is using an app called Zoho Mail which is primarily a web browser app
```
cat email.txt | grep "grunwald"
```
![](Pasted%20image%2020260617132253.png)
We also know from image in Q1 Firefox was used.






Task 3

Happy told the security team that the email was from a System Administrator, so he immediately opened the attachment. What is the email address that sent the phishing email?

```
alonzo.spire@forela.co.uk 
```

Search for admin related emails 
```
cat email.txt | grep "admin"
```
![](Pasted%20image%2020260617135538.png)











Task 4

What was the subject/topic of the phishing email?


```
Meeting with The New Operations team
```











Task 5

Happy was going through a document at the time of the incident. What's the full path of the document?

```
C:\Users\happy.grunwald\Documents\C-level\Budget-Plan_Pakistan.docx
```



extract processes and look for document related processes
```
vol3 -f Dump.mem Windows.pstree
```
![](Pasted%20image%2020260617141434.png)







Task 6

Recover the document and find the Topic/heading of the document.


```
Budget plan For 2023-Asia Expansion
```

dump the pid associated with the process in Q5
```
vol3 -f Dump.mem windows.dumpfiles --pid 9888 
```

then open file.0xdc81d8415100.0xdc81d82a6490.DataSectionObject.Budget-Plan_Pakistan.docx.dat
and open /word/document.xml

![](Pasted%20image%2020260617153707.png)




Task 7

What's the expected date of start of business in Pakistan?

```
2023-09-15
```


in same document as in Q6
![](Pasted%20image%2020260617154052.png)







Task 8

What's the name of the malicious attachment that led to the initial infection?

```
SystemHealthCheck.zip
```


The phishing email mentioned a zip
![](Pasted%20image%2020260617155518.png)

We can look for any zip files in the process output
![](Pasted%20image%2020260617155448.png)







Task 9

What's the name of the malicious stager file that established C2 communication, giving the attacker a foothold?

```
SystemHealthCheck.hta
```



Grep for "System" in the bulk extractor zip output
```
cat zip.txt | grep "System"
```
![](Pasted%20image%2020260617161405.png)
hta files are scripts used to execute payloads and are commonly used in phishing attacks






Task 10

To bypass email filters the attacker password protected the both of the zip files and provided the passwords to Happy. You are given the malicious attachment, but sadly Happy doesn't remember the passwords. Recover the passwords and provide them in the order given by the attacker, separated by a comma and a space.


```
forela1234, forela123
```


```
strings Dump.mem | grep "alonzo.spire@forela.co.uk"
```
![](Pasted%20image%2020260617162304.png)







Task 11

Submit the MD5 hash of the stager file you retrieved.

```
dd46189c153f8e3f4d34868df3f6511f
```



unzip the First.zip > Second.zip with the passwords found earlier and md5sum the file for hash
![](Pasted%20image%2020260617165521.png)






Task 12

Which C2 Framework was used by the attacker?


```
Empire
```



Several labels show signs of Empire being used
![](Pasted%20image%2020260617165651.png)
https://www.virustotal.com/gui/file/2567f2b05822f4d78b049a0bf42e2ead2ee6aa110311e3f3c27249ab7f8f28a1






Task 13

What is the PID of the process the threat actor abused for privilege escalation?


```
4352
```


Checking the process list we can find this process chain which is highly suspicious
![](Pasted%20image%2020260617172551.png)







Task 14

What is the path of the malicious file that resulted in privilege escalation?

```
C:\Users\happy.grunwald\AppData\Local\Temp\Winsrvc.dll
```



with the suspicious chain we found in last task, we continue to investigate the svchost process that spawned the chain, lets find out what DLL's it interacted with

```
vol3 -f Dump.mem windows.dlllist --pid "4352"
```

![](Pasted%20image%2020260617173914.png)

We see this malicious DLL being loaded



Task 15

What is the Mitre sub-technique ID with which the attacker escalated their privileges?


```
T1055.001
```


https://attack.mitre.org/techniques/T1055/001/








Task 16

The attacker connected to 2 different C2 addresses. What was the C2 IP and port number pair which facilitated initial remote access to the threat actor?

```
43.204.228.96:24
```


We know the pid of the powershell process from task 1 so lets use that to search for network connections related to the process

```
vol3 -f Dump.mem windows.netscan | grep "9620"
```
![](Pasted%20image%2020260617180821.png)








Task 17

Hunt for the second C2 Address to which connection was made by a LOLBIN after escalating privileges.


```
13.233.33.88:4444
```


rundll32 making a network connection
![](Pasted%20image%2020260617181051.png)







Task 18

The attacker directly downloaded a script from a web server. What is the full URL from where it was downloaded?

```
http://13.127.18.201/SystemIntegrityCheck.bat
```












Task 19

The threat actor that performed the attack is known for automating the collection of documents and files from compromised systems. The threat actor named their operation against Forela "PK-OPR24." Our reverse engineer said that she found this string commented on multiple occurrences in the analysis of the malicious stager. Reverse the script and provide the file extensions that it collects, in the order they are presented in the code.

```
docx, docm, pdf, xls, txt, ppt, xlsx, pptx
```


Run a recursive string scan in the bulk extractor output
![](Pasted%20image%2020260618123625.png)










Task 20

The threat actor archived the collected data using tools like WinRAR, RAR, etc., before exfiltrating it. What is the password with which the TA archived the collected data?


```
rwajnkhgbdtyalmd
```


![](Pasted%20image%2020260618124041.png)







Task 21

The threat actor is known to set up and use Remote Access tools. Which tool did the attacker set up for Persistence?


```
Teamviewer
```



![](Pasted%20image%2020260617181457.png)







Task 22

The threat actor created a backdoor user to establish a backup persistence mechanism. What are the credentials of this account?


```
helpdesk:BILA@hud
```


use windows.hashdump to dump hashes and then crack them












Task 23

What Threat Actor seems to be in question? External Research is required, so keep in mind things like some of the techniques you identified so far, the Region in which they operate, etc.


```
Mustang Panda
```


https://attack.mitre.org/groups/G0129/