

----


#### Sherlock Scenario

As the team was nearing the deadline for pushing their project to production, one of the engineers, Alex, took charge of the deployment process. Alex had been exploring ways to expedite the deployment process and had recently discovered a package that promised to streamline the process significantly. Excited about the prospect of saving time, Alex decided to utilise this package for the deployment. As Alex began the deployment process using the package, he noticed that the CPU usage on the deployment server started to spike unusually high. Concerned about this anomaly, he decided to ask for help. A Triage image has been acquired for your examination. Your primary objective is to ascertain the means by which this malware infiltrated our organisation, successfully evading our existing security measures.



----

Task 1

What action did Alex take to integrate the purported time-saving package into the deployment process? (provide the full command)

```
nuget install PublishIgnor -Version 1.0.11-beta
```



We can find ran commands in C\Users\Administrator\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history
<img width="2132" height="521" alt="image" src="https://github.com/user-attachments/assets/c5199d87-70c2-4ddd-88e3-367231e0bcb3" />









Task 2

Identify the URL from which the package was downloaded.


```
https://www.nuget.org/packages/PublishIgnor/
```



Import Chrome Browser history into DB Browser and go to URL´s
C\Users\Administrator\AppData\Local\Google\Chrome\User Data\Default/History








Task 3

Who is the threat actor responsible for publishing the malicious package? (the name of the package publisher)

```
a1l4m
```













Task 4

When did the attacker initiate the download of the package? Provide the timestamp in UTC format (YYYY-MM-DD HH:MM).


```
2024-03-19 18:41
```




Import MFT into Timeline Explorer and search for the package download
<img width="3766" height="1016" alt="image" src="https://github.com/user-attachments/assets/b6785a55-c0c8-4338-8ad0-5cd4c49cb526" />









Task 5

Despite restrictions, the attacker successfully uploaded the malicious file to the official site by altering one key detail. What is the modified package ID of the malicious package?


```
PublishIgnor
```



The official package is named PublishIgnore and not PublishIgnor

https://www.nuget.org/packages/PublishIgnore#readme-body-tab










Task 6

Which deceptive technique did the attacker employ during the initial access phase to manipulate user perception? (technique name)

```
typosquatting
```


https://www.kaspersky.com/resource-center/definitions/what-is-typosquatting










Task 7

Determine the full path of the file within the package containing the malicious code.


```
C:\Users\Administrator\.nuget\packages\publishignor\1.0.11-beta\tools\init.ps1
```


In the package we see a init.ps1 file containing suspicious activity
<img width="2623" height="1465" alt="image" src="https://github.com/user-attachments/assets/8c979222-71d7-4a99-92e9-2676bf711fe2" />








Task 8

When tampering with the system's security settings, what command did the attacker employ?

```
Set-MpPreference -DisableRealtimeMonitoring $true
```



Checking the content of the ps1 file wee can see it disables Defender´s monitoring
<img width="1332" height="1550" alt="image" src="https://github.com/user-attachments/assets/fe1c5176-a9f2-4b36-8f3a-07c88435dd77" />









Task 9

Following the security settings alteration, the attacker downloaded a malicious file to ensure continued access to the system. Provide the SHA1 hash of this file.

```
57b7acf278968eaa53920603c62afd8b305f98bb
```


We know from the ps1 script that it downloads and runs a file named "uninstall.exe"

We can find that Defender detected the file in its logs at \C\ProgramData\Microsoft\Windows Defender\Support. Everytime Defender makes a detection, it also hashes the file
<img width="2213" height="961" alt="image" src="https://github.com/user-attachments/assets/ce7bcc92-7128-4703-a041-5bbc65764a34" />









Task 10

Identify the framework utilised by the malicious file for command and control communication.


```
Sliver
```


Continuing down the log, we see further information about the file
<img width="2049" height="1524" alt="image" src="https://github.com/user-attachments/assets/4e312901-1338-434e-8437-06f775d07aaa" />









Task 11

At what precise moment was the malicious file executed?

```
2024-03-19 19:23:36
```


We can check its prefetch file to find when it ran
<img width="1192" height="740" alt="image" src="https://github.com/user-attachments/assets/4ae508d7-4a1a-441c-8dc4-9277de960fe7" />











Task 12

The attacker made a mistake and didn’t stop all the features of the security measures on the machine. When was the malicious file detected? Provide the timestamp in UTC.


```
2024-03-19 19:33:32
```


We can look up the event in Defender to find its detection time
```
\C\Windows\System32\winevt\logs\Microsoft-Windows-Windows Defender%4Operational
```

<img width="1366" height="1762" alt="image" src="https://github.com/user-attachments/assets/bc2db9fc-d61e-4caa-9ccc-93124c6b8101" />








Task 13

After establishing a connection with the C2 server, what was the first action taken by the attacker to enumerate the environment? Provide the name of the process.


```
whoami.exe
```



Going back to the MFT output in Timeline Explorer, follow the flow after uninstall.exe execution
<img width="3610" height="950" alt="image" src="https://github.com/user-attachments/assets/8b80de9d-dd1d-423b-abdd-271246c3eceb" />








Task 14

To ensure continued access to the compromised machine, the attacker created a scheduled task. What is the name of the created task?


```
MicrosoftSystemDailyUpdates
```




One task stands out here and its contents disables Defender
<img width="877" height="426" alt="image" src="https://github.com/user-attachments/assets/873c871d-8577-4a22-9f02-d5499b2f9a0f" />


<img width="1210" height="1453" alt="image" src="https://github.com/user-attachments/assets/84c099e6-df04-494e-96cc-809dfc5d2b89" />





Task 15

When was the scheduled task created? Provide the timestamp in UTC.

```
2024-03-19 19:24:05
```



<img width="1367" height="1417" alt="image" src="https://github.com/user-attachments/assets/7bc8698b-caaf-4522-963d-b2fdd06e0aae" />










Task 16

Upon concluding the intrusion, the attacker left behind a specific file on the compromised host. What is the name of this file?

```
file.exe
```




After converting prefetch directory into a timeline and importing it into Timeline Explorer we can see this suspious file
<img width="2683" height="1076" alt="image" src="https://github.com/user-attachments/assets/03de31e9-27e3-42cb-bc07-24c6563ef474" />








Task 17

As an anti-forensics measure. The threat actor changed the file name after executing it. What is the new file name?


```
Updater.exe
```




Usnjournal keeps track of renamed files so lets extract it
```
python .\usn.py -f '..\..\C\$Extend\$J' -o C:\Users\*\Desktop\usn.csv --csv
```
Then import into Timeline Explorer and look for file.exe

We can see file.exe getting renamed to Updater.exe
<img width="3479" height="584" alt="image" src="https://github.com/user-attachments/assets/be6cec4a-492f-4ea7-ab34-3b04cca1828a" />





https://github.com/PoorBillionaire/USN-Journal-Parser/tree/master






Task 18

Identify the malware family associated with the file mentioned in the previous question (17).

```
Impala
```


We can find its location by searching for it in the MFT data
<img width="3501" height="421" alt="image" src="https://github.com/user-attachments/assets/98be340b-7d3f-49cd-a7f7-cbbc4255997d" />




<img width="1134" height="580" alt="image" src="https://github.com/user-attachments/assets/b941142c-0a1f-4669-b305-62d93a6e368a" />


```
Get-FileHash .\Updater.exe -Algorithm SHA256
```
Then search it up on virustotal

<img width="1564" height="384" alt="image" src="https://github.com/user-attachments/assets/115ca37b-ab8e-4bb1-93f9-ab6b4e9b453a" />


Impala is the malware family related to this attack.
https://jfrog.com/blog/impala-stealer-malicious-nuget-package-payload/



Task 19

When was the file dropped onto the system? Provide the timestamp in UTC.

```
2024-03-19 19:30:04
```


Going back to the MFT data, we can see its creation date
<img width="3704" height="373" alt="image" src="https://github.com/user-attachments/assets/523c1318-4bc8-4d64-ad98-472c97c1fa04" />
