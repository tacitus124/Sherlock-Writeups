

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


<img width="1899" height="799" alt="image" src="https://github.com/user-attachments/assets/bb209ff5-e94f-4dca-a5cd-60cdf2c64d3e" />





<img width="1528" height="822" alt="image" src="https://github.com/user-attachments/assets/69256bdd-58cd-4819-b9cf-cafb6913ed62" />

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
<img width="1287" height="815" alt="image" src="https://github.com/user-attachments/assets/4ad5fe31-fde7-44d0-9758-aecb76daf6ad" />

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
<img width="1067" height="435" alt="image" src="https://github.com/user-attachments/assets/fe491b8f-b006-4d1d-b9ae-651ce9197d49" />












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
<img width="1401" height="556" alt="image" src="https://github.com/user-attachments/assets/70ddf99c-0ff0-461f-894e-f3e74425cc77" />







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

<img width="1187" height="541" alt="image" src="https://github.com/user-attachments/assets/61f44d99-593b-43d8-8b8f-3db2e12cf7c4" />





Task 7

What's the expected date of start of business in Pakistan?

```
2023-09-15
```


in same document as in Q6
<img width="1255" height="248" alt="image" src="https://github.com/user-attachments/assets/da0af91f-5fbc-41ba-a67a-badfa79ea748" />








Task 8

What's the name of the malicious attachment that led to the initial infection?

```
SystemHealthCheck.zip
```


The phishing email mentioned a zip
<img width="1904" height="297" alt="image" src="https://github.com/user-attachments/assets/32a77d2a-abe0-499c-a509-8dcd4072a771" />


We can look for any zip files in the process output
<img width="629" height="139" alt="image" src="https://github.com/user-attachments/assets/9b4834d4-63ba-420c-8a07-9f1b24f821ad" />








Task 9

What's the name of the malicious stager file that established C2 communication, giving the attacker a foothold?

```
SystemHealthCheck.hta
```



Grep for "System" in the bulk extractor zip output
```
cat zip.txt | grep "System"
```
<img width="1388" height="233" alt="image" src="https://github.com/user-attachments/assets/3143021a-ac4e-470f-8c28-c7be59d0c616" />

hta files are scripts used to execute payloads and are commonly used in phishing attacks






Task 10

To bypass email filters the attacker password protected the both of the zip files and provided the passwords to Happy. You are given the malicious attachment, but sadly Happy doesn't remember the passwords. Recover the passwords and provide them in the order given by the attacker, separated by a comma and a space.


```
forela1234, forela123
```


```
strings Dump.mem | grep "alonzo.spire@forela.co.uk"
```
<img width="1515" height="296" alt="image" src="https://github.com/user-attachments/assets/0c775a6b-b970-47ee-aa44-85de803d3fb1" />








Task 11

Submit the MD5 hash of the stager file you retrieved.

```
dd46189c153f8e3f4d34868df3f6511f
```



unzip the First.zip > Second.zip with the passwords found earlier and md5sum the file for hash
<img width="628" height="180" alt="image" src="https://github.com/user-attachments/assets/c83a58da-c0b6-45f2-8095-9fc6df924b0f" />







Task 12

Which C2 Framework was used by the attacker?


```
Empire
```



Several labels show signs of Empire being used
<img width="1805" height="487" alt="image" src="https://github.com/user-attachments/assets/794959d7-32e1-4a0f-9de2-37265a3dd87a" />

https://www.virustotal.com/gui/file/2567f2b05822f4d78b049a0bf42e2ead2ee6aa110311e3f3c27249ab7f8f28a1






Task 13

What is the PID of the process the threat actor abused for privilege escalation?


```
4352
```


Checking the process list we can find this process chain which is highly suspicious
<img width="1112" height="161" alt="image" src="https://github.com/user-attachments/assets/ac4deb24-c9eb-4a15-a69c-4d27cda917ce" />








Task 14

What is the path of the malicious file that resulted in privilege escalation?

```
C:\Users\happy.grunwald\AppData\Local\Temp\Winsrvc.dll
```



with the suspicious chain we found in last task, we continue to investigate the svchost process that spawned the chain, lets find out what DLL's it interacted with

```
vol3 -f Dump.mem windows.dlllist --pid "4352"
```

<img width="1516" height="135" alt="image" src="https://github.com/user-attachments/assets/de376a63-4ce1-42f3-a2a2-d34207fa5959" />


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
<img width="1213" height="312" alt="image" src="https://github.com/user-attachments/assets/760e7077-55cc-4873-8102-6b22de14214b" />









Task 17

Hunt for the second C2 Address to which connection was made by a LOLBIN after escalating privileges.


```
13.233.33.88:4444
```


rundll32 making a network connection
<img width="1268" height="441" alt="image" src="https://github.com/user-attachments/assets/f7543b60-1176-4017-95e9-be3a4811972a" />







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
<img width="938" height="479" alt="image" src="https://github.com/user-attachments/assets/9ed01a66-5512-4d0d-b395-31f9cdc797ac" />










Task 20

The threat actor archived the collected data using tools like WinRAR, RAR, etc., before exfiltrating it. What is the password with which the TA archived the collected data?


```
rwajnkhgbdtyalmd
```


<img width="938" height="479" alt="image" src="https://github.com/user-attachments/assets/d79bdb7a-bd92-43ab-b3f8-3a7dccc66efc" />








Task 21

The threat actor is known to set up and use Remote Access tools. Which tool did the attacker set up for Persistence?


```
Teamviewer
```



<img width="1679" height="175" alt="image" src="https://github.com/user-attachments/assets/778fe089-5ff5-496f-8957-0efa45771ad7" />








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
