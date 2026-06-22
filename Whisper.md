

---


#### Sherlock Scenario

The SOC team received an alert on 21st January 2025 about suspicious activity originating from an employee's system (Alpha). Alpha was not authorized to engage in any offensive hacking activities within the network. The Incident Response (IR) team quickly acted, isolating the system so you can perform a thorough triage and investigation to determine the scope of the breach and potential impact on the network.



----


Im going to start by extracting Powershell console history for each user

Two users have the file available at /C/Users/asd/AppData/Roaming/Microsoft/Windows/PowerShell/PSReadline

/C\Users\A7md-NaS3eR\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline

Both contain malicious activity and files which gives me a good overview of what happened

<img width="990" height="604" alt="image" src="https://github.com/user-attachments/assets/1c74647c-7752-4b93-b2d1-bcc67fdd8cd9" />


<img width="870" height="629" alt="image" src="https://github.com/user-attachments/assets/9d88cb84-d612-40c5-a8f6-bf8ae24e0f10" />











Task 1

What is the hostname of the company computer involved in the unauthorized activity?
HelpDesk

Load up system hive into registry explorer and navigate to HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\ComputerName\ComputerName
<img width="870" height="629" alt="image" src="https://github.com/user-attachments/assets/9d89bdb8-04e5-40ae-b435-6dc3b7f7b766" />







Task 2

What is the IP address associated with the machine?
192.168.159.199

You can find IP adress information in
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Param eters\Interfaces
<img width="1833" height="373" alt="image" src="https://github.com/user-attachments/assets/b146ac79-de5b-4852-a556-58c6d4aead2b" />



Task 3

What is the Security Identifier (SID) of the computer involved in the incident?

```
S-1-5-21-953115734-2025219997-3674921352
```


Task 4

What was the timezone setting of the machine?

```
Pacific Standard Time
```

timezone information in system hive

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Time Zones
<img width="1786" height="546" alt="image" src="https://github.com/user-attachments/assets/f2718196-a456-4fb0-af6d-66ecfa99237b" />


Task 5

Which tool did the attacker use to escalate privileges and extract sensitive credentials from the system?
MIMIKATZ.EXE

<img width="830" height="568" alt="image" src="https://github.com/user-attachments/assets/76ed3325-4ee2-4a72-862b-6de4fe55b070" />




Task 6

What is the timestamp of the last execution of the credential extraction tool on the system used by the attacker?

2025-01-21 00:21:41


Extract prefetch with PECmd to find last run
```
.\PECmd.exe -f "C:\Users\*\Desktop\Whisper\C\Windows\prefetch\MIMIKATZ.EXE-7A038744.pf"
```

<img width="1231" height="582" alt="image" src="https://github.com/user-attachments/assets/c1cdadf8-9403-40ce-a6cf-ca55af520129" />



Task 7

What is the timestamp when the attacker first downloaded the credential extraction tool onto the system?
2025-01-21 00:20:15

<img width="1869" height="768" alt="image" src="https://github.com/user-attachments/assets/2d57daa4-c979-4918-929c-409a99f94aad" />




Task 8

After using the previously mentioned tool, the attacker downloaded a script to scan the domain controller. What is the URL from which the attacker downloaded this script?

|                                                                           |
| ------------------------------------------------------------------------- |
| https://mega.nz/file/I31zBZTb#H5aUJamEj2xMroi9baoTKCMjQF1jiV8TK9gbjoNGgkw |

Import Microsoft Edge history into DB Browser. C:\Alpha\\AppData\Local\Microsoft\Edge\User Data\Default\History

<img width="1028" height="630" alt="image" src="https://github.com/user-attachments/assets/f49b3661-dfb9-4cd4-a190-4f73801f99e8" />


Task 9

What is the filename of the script? (Format - Full path of file, starting with drive letter)
C:\Users\Alpha\Downloads\DC-Scan.ps1

Use the same tool as above and check downloads table

<img width="1031" height="460" alt="image" src="https://github.com/user-attachments/assets/1e157828-5acb-4d78-87d7-05b985cb2205" />



Task 10

What is the timestamp indicating when the attacker deleted the script after it was downloaded?

2025-01-21 01:27:47



Extract USN journal and import it into Timeline Explorer and search for the file and look for FILE_DELETE
```
python .\usn.py -f "C:\Users\*\Desktop\Whisper\C\`$Extend\`$J" -o C:\Users\tim\Desktop\usn_output.csv -c
```

<img width="1122" height="469" alt="image" src="https://github.com/user-attachments/assets/730d3739-cde7-4b15-8919-6b6857446ff4" />







Task 11

What is the name of the shared file that the attacker accessed?

\\CYBERUP\Users\Administrator\Desktop\Top-Secret\Secret.xlsx

Check the properties of Secret.xlsx in \C\Users\Alpha\AppData\Roaming\Microsoft\Windows\Recent
<img width="803" height="498" alt="image" src="https://github.com/user-attachments/assets/3e84372a-6e87-40b9-b42d-2c0630678994" />



Task 12

To persist, the attacker created a new user account. What is the SID of the newly created account?

```
S-1-5-21-953115734-2025219997-3674921352-1002
```

Search for account creation with event id "4720" in Security logs. It shows one account created

<img width="835" height="813" alt="image" src="https://github.com/user-attachments/assets/f02e36a5-7f9a-40c9-b1ea-72b4589f56e3" />




Task 13

How many times has the newly created user logged in?

0


We know the users SID from last task so we can search up everything about it in the Security logs. The logs shows no login/event id 4624. 
```
.\chainsaw.exe search -e "S-1-5-21-953115734-2025219997-3674921352-1002" C:\Users\*\Desktop\Whisper\C\Windows\System32\winevt\Logs\Security.evtx
```


Task 14

What is the password of the newly created user?
Password123


