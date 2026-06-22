

----


#### Sherlock Scenario

With help from D.I. Lestrade, Holmes acquires logs from a compromised MSP connected to the city’s financial core. The MSP’s AI helpdesk bot looks to have been manipulated into leaking remote access keys - an old trick of Moriarty’s.


----


We are given three files:
C drive
acquired file.kdbx
msp helpdesk network traffic capture


----

Start of by opening the network traffic file in Wireshark, then Statistics > HTTP > Request Sequences to get an overview of the traffic, we can see HTTP requests to MSP helpdesk
<img width="1857" height="1111" alt="image" src="https://github.com/user-attachments/assets/d51b5973-c2b1-44e0-9bf0-38436e32fa6c" />


Then filter for HTTP traffic and go through the requests until you find the malicious prompts by the attacker, then follow the HTTP stream to get the relevant answers to the questions below



Task 1

What was the IP address of the decommissioned machine used by the attacker to start a chat session with MSP-HELPDESK-AI: 10.0.69.45





Task 2

What was the hostname of the decommissioned machine?
WATSON-ALPHA-2

Filter IP address of the compromised machine and the first packet shows a host announcement:
ip.addr == 10.0.69.45
<img width="2396" height="517" alt="image" src="https://github.com/user-attachments/assets/f26fb5f1-db12-40a2-bf3d-2b095d85d38d" />



Task 3

What was the first message the attacker sent to the AI chatbot:
Hello Old Friend






Task 4

When did the attacker's prompt injection attack make MSP-HELPDESK-AI leak remote management tool info: 2025-08-19 12:02:06






Task 5

What is the Remote management tool Device ID and password:
565963039:CogWork_Central_97&65
<img width="2939" height="1341" alt="image" src="https://github.com/user-attachments/assets/1192dcee-d4dd-456c-9531-df1517acb923" />




Task 6

What was the last message the attacker sent to MSP-HELPDESK-AI:
JM WILL BE BACK

<img width="2976" height="1736" alt="image" src="https://github.com/user-attachments/assets/2b68b244-d9ed-423e-a60c-ddf99957f35e" />






Task 7

When did the attacker remotely access Cogwork Central Workstation:
2025-08-20 09:58:25


We can find the relevant information in a Teamviewer auth log in the C:\Program Files\Teamviewer directory

<img width="2310" height="1375" alt="image" src="https://github.com/user-attachments/assets/5e73631e-2e3e-465f-a43d-7dda0476cdf9" />



Task 8

What was the RMM Account name used by the attacker:
James Moriarty


In the C:\Program Files\Teamviewer is a txt file with incoming connections, there is one user that logged in around the time of the compromise seen in the network traffic

<img width="2310" height="1375" alt="image" src="https://github.com/user-attachments/assets/df7d0a83-2a52-4d7b-98d7-2b5e8d9dd654" />



Task 9

What was the machine's internal IP address from which the attacker connected:
192.168.69.213


We can extract the TeamViewer15_Logfile.log and search for IP addresses around the time when the attacker accessed the workstation. Keep in mind that you will have to look 1 hour forward

<img width="1828" height="1547" alt="image" src="https://github.com/user-attachments/assets/e93a69cc-9cce-4f14-8d84-073acd69c339" />





Task 10

The attacker brought some tools to the compromised workstation to achieve its objectives. Under which path were these tools staged: C:\Windows\Temp\safe\

Run through the C drive with Chainsaw
```
.\chainsaw.exe hunt "C:\Users\*\Desktop\Holmes2025_Chapter2 The Watchman's Residue\TRIAGE_IMAGE_COGWORK-CENTRAL\C\Windows\System32\winevt\logs" --sigma .\sigma\rules --mapping .\mappings\sigma-event-logs-all.yml
```

<img width="2952" height="1128" alt="image" src="https://github.com/user-attachments/assets/21a90b75-82b6-46c9-89fd-f28f8f2df078" />



Task 11

The attacker staged a browser credential harvesting tool on the compromised system. How long did this tool run before it was terminated? (Provide your answer in milliseconds, rounded to the nearest thousand):
8000

We can get runtimes by loading ntuser.dat into registry explorer and going to the following reg key "/Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist". Now check the focus time which tracks how long an application has been in focus

<img width="3021" height="433" alt="image" src="https://github.com/user-attachments/assets/1e7e8c6c-c9e9-4db7-b7e9-59d7b2444f4d" />



Task 12

The attacker executed a OS Credential dumping tool on the system. When was the tool executed:
2025-08-20 10:07:08

Extract USN journal data with MFTECmd and ingest the data into Timeline explorer


<img width="3582" height="1654" alt="image" src="https://github.com/user-attachments/assets/7d2f8e8a-c235-44c5-8a8c-edcbb5a90673" />




Task 13

The attacker exfiltrated multiple sensitive files. When did the exfiltration start? (UTC):
2025-08-20 10:12:07


Teamviewer can be used to upload and download files so if you keep following the attackers session in the Teamviewer log you can find when he started extracting files. Keep in mind the timezone difference (Teamviewer is 1 hour ahead)


<img width="2569" height="692" alt="image" src="https://github.com/user-attachments/assets/10db728f-83ac-4218-9724-c7a6a05a32b7" />







Task 14

Before exfiltration, several files were moved to the staged folder. When was the Heisen-9 facility backup database moved to the staged folder for exfiltration:
2025-08-20 10:11:09

<img width="3476" height="743" alt="image" src="https://github.com/user-attachments/assets/02c66a79-bbd7-46f9-becf-59cf047bad86" />

Judging by the Timestamps and Update Reasons in the USN journal we can assume the file was copied/moved



Task 15

When did the attacker access and read a txt file, which was probably the output of one of the tools they brought, due to the naming convention of the file:
2025-08-20 10:08:06


Use JumpLists to analyze this, JumpLists shows what the user opened,  when they opened it,  
and sometimes from where
```
JLECmd.exe -d "C:\Users\*\Desktop\Holmes2025_Chapter2 The Watchman's Residue\TRIAGE_IMAGE_COGWORK-CENTRAL\C\Users\Cogwork_Admin\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations" --csv <path-to-save-csv>
```
<img width="3313" height="1036" alt="image" src="https://github.com/user-attachments/assets/ff38c2e7-5492-4da3-b0df-745fabb5c9cb" />

Here we see how he opened dump.txt with Notepad



Task 16

The attacker created a persistence mechanism on the workstation. When was the persistence setup?
2025-08-20 10:13:57


We can load the SOFTWARE hive into Registry Explorer and go to the Winlogon keys which handles logins/startup etc... We can see the Winlogon key was modified around the time of when the TA was extracting. We can see he modified Userinit to include his malicious JM.exe program

<img width="3440" height="755" alt="image" src="https://github.com/user-attachments/assets/85b87bf5-80a0-4a40-9acf-7c4f450af757" />



Task 17

What is the MITRE ID of the persistence subtechnique:
[T1547.004](https://attack.mitre.org/techniques/T1547/004/)




Task 18

When did the malicious RMM session end:
2025-08-20 10:14:27

We can find the relevant information in a Teamviewer auth log in the C:\Program Files\Teamviewer directory

<img width="2015" height="1391" alt="image" src="https://github.com/user-attachments/assets/a613ff79-edcd-4eb2-9745-e508a5c306f1" />



Task 19

The attacker found a password from exfiltrated files, allowing him to move laterally further into CogWork-1 infrastructure. What are the credentials for Heisen-9-WS-6:
Werni:Quantum1!
