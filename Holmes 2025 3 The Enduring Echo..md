
----


#### Sherlock Scenario

LeStrade passes a disk image to Holmes. It's one of the identified breach points, now showing abnormal CPU activity and anomalies in process logs.



----




We are given a C drive to investigate

Im going to be using Autopsy for an overview and ease of navigation

Registry Explorer for general forensic work

Event logs for deeper forensic work





Task 1

What was the first (non cd) command executed by the attacker on the host?
Follow his WmiPrvSE process execution and you will find his next commands after "cd" will be "systeminfo"

<img width="1894" height="1314" alt="image" src="https://github.com/user-attachments/assets/4afc6fc0-5ead-4b99-b8f9-2fb47bad089f" />



Task 2

Which parent process (full path) spawned the attacker’s commands:
C:\Windows\System32\wbem\WmiPrvSE.exe

Search for process creation events in Security log with event id 4688


<img width="2817" height="1415" alt="image" src="https://github.com/user-attachments/assets/0f20ed0d-69b5-43c0-934a-86d37221f64a" />




Task 3

Which remote-execution tool was most likely used for the attack: wmiexec.py

The command captures the classic behaviour of wmiexec.py

`wmiexec.py`:

- executes commands through WMI
- captures stdout/stderr
- redirects output into a file over `ADMIN$`
- reads the file back remotely
- deletes it afterward

https://www.crowdstrike.com/en-us/blog/how-to-detect-and-prevent-impackets-wmiexec/




Task 4

What was the attacker’s IP address:
10.129.242.110

We can filter out every unique IP in the security logs with this script and then we can make out the suspicious one

```
Get-WinEvent -Path "C:\Users\topsa\Desktop\echo\C\Windows\System32\winevt\Logs\Security.evtx" |
Where-Object { $_.Id -eq 4624 } |
ForEach-Object {

    $xml = [xml]$_.ToXml()

    $eventData = @{}
    foreach ($data in $xml.Event.EventData.Data) {
        $eventData[$data.Name] = $data.'#text'
    }

    [PSCustomObject]@{
        User      = $eventData.TargetUserName
        IPAddress = $eventData.IpAddress
        LogonType = $eventData.LogonType
    }
} |
Where-Object {
    $_.IPAddress -and
    $_.IPAddress -ne "-" -and
    $_.IPAddress -ne "::1" -and
    $_.IPAddress -ne "127.0.0.1"
} |
Sort-Object User, IPAddress, LogonType -Unique |
Format-Table -AutoSize
```

<img width="1639" height="787" alt="image" src="https://github.com/user-attachments/assets/2939307b-d870-496a-afaa-24f37db66392" />



Task 5

The attacker established multiple persistence mechanisms. What is set as the name of the earliest one created:
SysHelper Update

Still following his WMIPrvSE trace we can see him creating a scheuled task named Syshelper Update

<img width="2680" height="1419" alt="image" src="https://github.com/user-attachments/assets/a318a90e-3d22-431b-8416-d83b759bc9b2" />




Task 6

Identify the script executed by the persistence mechanism:
C:\Users\Werni\Appdata\Local\JM.ps1
We can find a scheduled task called "Syshelper Update" which is out of the ordinary, its contents executes a malicious powershell script called "JM.ps1"


<img width="2680" height="1419" alt="image" src="https://github.com/user-attachments/assets/87367afe-eff8-4463-86b8-96da99be5d5d" />




Task 7

What local account did the attacker create:
svc_netupd

Checking the contents of the persistence file, we can see how it checks for predefined usernames and if one doesnt exist it creates one of the predefined usernames


<img width="2405" height="1572" alt="image" src="https://github.com/user-attachments/assets/6cd70f27-fae5-4767-aa6b-3ed62f45e98b" />







Task 8

What domain name did the attacker use for credential exfiltration?
http://NapoleonsBlackPearl.htb

We can check the script in the persistence script to find out its extracting a created username/password to the domain

<img width="2405" height="1572" alt="image" src="https://github.com/user-attachments/assets/dbd07196-30df-4fc8-933e-dc3c55351d3a" />




Task 9

What password did the attacker's script generate for the newly created user:
Watson_20250824160509



Task 10

What was the IP address of the internal system the attacker pivoted to?
192.168.1.101

Tracing the TA through his WmiPrv commands we can see a proxy.bat getting created. In the following logs we can see what proxy.bat executes

<img width="2763" height="1699" alt="image" src="https://github.com/user-attachments/assets/9b7eac1c-4bba-4170-964f-dc3497ba1b90" />


<img width="2817" height="1713" alt="image" src="https://github.com/user-attachments/assets/8de653ab-0c85-4022-b42d-f378f3c9fa93" />



Task 11

Which TCP port on the victim was forwarded to enable the pivot?
9999

<img width="2817" height="1713" alt="image" src="https://github.com/user-attachments/assets/1d4d8140-02ac-44a6-9af4-4cb090c61be2" />


Task 12

What is the full registry path that stores persistent IPv4→IPv4 TCP listener-to-target mappings:

HKLM\SYSTEM\CurrentControlSet\Services\PortProxy\v4tov4\tcp




Task 13

What is the MITRE ATT&CK ID associated with the previous technique used by the attacker to pivot to the internal system: T1090.001

https://attack.mitre.org/versions/v13/techniques/T1090/001/




Task 14

Before the attack, the administrator configured Windows to capture command line details in the event logs. What command did they run to achieve this: 
```
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\Audit" /v ProcessCreationIncludeCmdLine_Enabled /t REG_DWORD /d 1 /f
```
Open the administrators Powershell console history and you can see what commands he ran

<img width="2397" height="1560" alt="image" src="https://github.com/user-attachments/assets/614cbab3-9dc7-417a-9334-7fd963875703" />

