

---


#### Sherlock Scenario

John is an employee at a mid-sized tech company. He works as a Senior IT support specialist, but his true passion is finding ways to make extra money. John is always on the lookout for giveaways, discounts, and any opportunity to earn a quick buck. He’s not particularly tech-savvy when it comes to cybersecurity, but he’s resourceful and knows how to follow online tutorials.

Recently, John came across an enticing giveaway that promised exciting rewards. However, when he opened the giveaway, he didn’t find or win anything. This made him suspicious that something might have gone wrong with his machine. Concerned about the unusual behavior, John has reached out to you, the investigator, to uncover what happened and whether his system has been compromised.



---


Task 1

At what exact time did the user execute the malicious shortcut file?



```
2025-01-26 16:17:15
```



Import ntuser.dat into Registry Explorer and go to UserAssist. We know from the Sherlock description it has something to do with giveaways
![](Pasted%20image%2020260616123412.png)








Task 2

The previous malicious file executed an initial payload. What is the full path of this payload?


```
C:\Temp\svch0st.exe
```


Start by importing pecmd into timeline explorer
```
.\PECmd.exe -d C:\Users\*\Desktop\C\Windows\prefetch\ --csv C:\Users\*\Desktop\
```

Then look around the time of when the lnk was executed "2025-01-26 16:17:15". We can see a file called svch0st masquerading as the benign windows process svchost. Notice the benign svchost process above svch0st.
![](Pasted%20image%2020260616133805.png)


Now we use chainsaw to find out more about this svch0st
```
.\chainsaw.exe search "svch0st.exe" C:\Users\*\Desktop\C\
```

We can see a svch0st file getting downloaded via powershell and gets placed in C:\Temp\ as svch0st.exe
![](Pasted%20image%2020260616134308.png)



Task 3

At what timestamp did the payload execute and grant the attacker shell access?


```
2025-01-26 16:17:54
```


Find programs last run in the pecmd output
![](Pasted%20image%2020260616135107.png)







Task 4

What is the command line the attacker used to enumerate installed packages on the system?

```
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -Command Get-Package
```


This chainsaw command filters for powershell logs with event id 600 and extracts the command line so you can easily sift through it for any malicious doings
```
.\chainsaw.exe search 600 C:\Users\*\Desktop\C\Windows\System32\winevt\ | findstr "Command"
```

In this case, we find this interesting command that retrieves installed packages
![](Pasted%20image%2020260616141551.png)




Task 5

Which application did the attacker identify as vulnerable?

```
YandexBrowser
```



 import NTuser.dat into Registry Explorer go to Uninstall tab to find applications and versions
![](Pasted%20image%2020260616143700.png)





Task 6

What version of that vulnerable application did the attacker identify?

```
24.4.5.498
```


![](Pasted%20image%2020260616144010.png)

The specific Yandex Browser version **24.4.5** isn't listed with a unique exploit by that exact string. However, versions prior to **24.4.0.682** are affected by a **Use After Free** memory corruption flaw ([CVE-2023-26226](https://nvd.nist.gov/vuln/detail/CVE-2023-26226)), and later versions are tied to a **DLL Search Order Hijacking** flaw ([CVE-2024-6473](https://nvd.nist.gov/vuln/detail/CVE-2024-6473)). [[1](https://yandex.com/bugbounty/i/hall-of-fame-browser/), [2](https://yandex.com/bugbounty/i/hall-of-fame-browser/), [3](https://news.drweb.com/show/?i=14899)]






Task 7

What is the CVE associated with this vulnerability?


```
CVE-2024-6473
```










Task 8

What is the name of the legitimate binary that the attacker used to deliver the malicious payload and establish persistence on the compromised system?


```
certutil.exe
```




We know the TA is targeting Yandex so lets search up everything related to Yandex in Timeline explorer with the MFT data imported.

Researching how the YandexBrowser CVE works is that is sideloads a malicious DLL. Therefore, lets filter by DLL extension
![](Pasted%20image%2020260616155013.png)
One stands out here and doesnt belong to YandexBrowser. "wldp.dll" is a Windows DLL and not a YandexBrowser DLL so there is no reason for it be there. 


Now, lets search it up. We can see it belonged to an inetcache folder before which indicates it was downloaded by some tool. TA's likes to use binaries like certutil,powershell etc
![](Pasted%20image%2020260616155215.png)


We can confirm this by checking the certutil prefetch and see what files it interacted with
```
.\PECmd.exe -f C:\Users\*\Desktop\C\Windows\prefetch\CERTUTIL.EXE-28F1E0C1.pf
```
![](Pasted%20image%2020260616162434.png)






Task 9

What is the name of the malicious Portable Executable (PE) file that enabled him to accomplish his objective?


```
wldp.dll
```

Evidence is in Question 8








Task 10

What is the SHA-256 hash of that malicious file?

```
A1A17EBD90610D808E761811D17DA3143F3DE0D4CC5EE92BD66000DCA87D9270
```


Go to \C\Users\Administrator\AppData\LocalLow\Microsoft\CryptnetUrlCache\Content to retrieve the contents/hashes of the file
Check the time when the wldp file was downloaded in MFT and compare it to the times in the Content folder
![](Pasted%20image%2020260616163313.png)
You can see two files matching when wldp.dll was downloaded

```
Get-FileHash .\A16B2E6DE64B13EDF2C00F32C4559930 -Algorithm SHA256
```





Task 11

How many milliseconds of cumulative coded sleep delays occurred before the C2 binary provided a shell after the vulnerable application was launched?


```
11000
```

Sleep gets called 2x for a total of 11000 ms
![](Pasted%20image%2020260616165902.png)







Task 12

What is the mutex name used to ensure only one instance of the C2 binary runs at a time?T


```
Global\\YandaExeMutex
```

![](Pasted%20image%2020260616170153.png)







Task 13

What is the full path of the Command and Control (C2) Binary?



```
C:\Users\Administrator\AppData\Local\Temp\yanda.tmp
```


![](Pasted%20image%2020260616171502.png)






Task 14

What is the name of the C2 framework used by the attacker?


```
sliver
```










Task 15

What is the IP address and port number of the malicious C2 server used by the attacker?

```
18.192.12.126:8888
```