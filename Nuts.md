

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
![](Pasted%20image%2020260625135014.png)








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
![](Pasted%20image%2020260625152317.png)








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
![](Pasted%20image%2020260625154142.png)








Task 8

When tampering with the system's security settings, what command did the attacker employ?

```
Set-MpPreference -DisableRealtimeMonitoring $true
```



Checking the content of the ps1 file wee can see it disables Defender´s monitoring
![](Pasted%20image%2020260625154520.png)








Task 9

Following the security settings alteration, the attacker downloaded a malicious file to ensure continued access to the system. Provide the SHA1 hash of this file.

```
57b7acf278968eaa53920603c62afd8b305f98bb
```


We know from the ps1 script that it downloads and runs a file named "uninstall.exe"

We can find that Defender detected the file in its logs at \C\ProgramData\Microsoft\Windows Defender\Support. Everytime Defender makes a detection, it also hashes the file
![](Pasted%20image%2020260625161302.png)








Task 10

Identify the framework utilised by the malicious file for command and control communication.


```
Sliver
```


Continuing down the log, we see further information about the file
![](Pasted%20image%2020260625161247.png)








Task 11

At what precise moment was the malicious file executed?

```
2024-03-19 19:23:36
```


We can check its prefetch file to find when it ran
![](Pasted%20image%2020260625162127.png)











Task 12

The attacker made a mistake and didn’t stop all the features of the security measures on the machine. When was the malicious file detected? Provide the timestamp in UTC.


```
2024-03-19 19:33:32
```


We can look up the event in Defender to find its detection time
```
\C\Windows\System32\winevt\logs\Microsoft-Windows-Windows Defender%4Operational
```

![](Pasted%20image%2020260625162813.png)







Task 13

After establishing a connection with the C2 server, what was the first action taken by the attacker to enumerate the environment? Provide the name of the process.


```
whoami.exe
```



Going back to the MFT output in Timeline Explorer, follow the flow after uninstall.exe execution
![](Pasted%20image%2020260625163201.png)







Task 14

To ensure continued access to the compromised machine, the attacker created a scheduled task. What is the name of the created task?


```
MicrosoftSystemDailyUpdates
```




One task stands out here and its contents disables Defender
![](Pasted%20image%2020260625164437.png)

![](Pasted%20image%2020260625164507.png)




Task 15

When was the scheduled task created? Provide the timestamp in UTC.

```
2024-03-19 19:24:05
```



![](Pasted%20image%2020260625164542.png)









Task 16

Upon concluding the intrusion, the attacker left behind a specific file on the compromised host. What is the name of this file?

```
file.exe
```




After converting prefetch directory into a timeline and importing it into Timeline Explorer we can see this suspious file
![](Pasted%20image%2020260625170930.png)







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
![](Pasted%20image%2020260625173800.png)




https://github.com/PoorBillionaire/USN-Journal-Parser/tree/master






Task 18

Identify the malware family associated with the file mentioned in the previous question (17).

```
Impala
```


We can find its location by searching for it in the MFT data
![](Pasted%20image%2020260625174522.png)



![](Pasted%20image%2020260625175019.png)

```
Get-FileHash .\Updater.exe -Algorithm SHA256
```
Then search it up on virustotal

![](Pasted%20image%2020260625175121.png)

Impala is the malware family related to this attack.
https://jfrog.com/blog/impala-stealer-malicious-nuget-package-payload/



Task 19

When was the file dropped onto the system? Provide the timestamp in UTC.

```
2024-03-19 19:30:04
```


Going back to the MFT data, we can see its creation date
![](Pasted%20image%2020260625175405.png)