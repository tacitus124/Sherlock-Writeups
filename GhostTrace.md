

----

#### Sherlock Scenario

You are a blue team analyst tasked with investigating a suspected breach in an Active Directory environment named Main.local. The network includes a Domain Controller (DC01 and two client machines (Client02 and Client03). A user on Client03 received a phishing email, leading to a series of attacks that compromised the domain. Your job is to analyze the provided Windows Event Logs and Sysmon logs from Client02, Client03, and DC01 to reconstruct the attack chain, identify the attacker’s actions, and uncover critical artifacts such as credentials, hashes, and persistence mechanisms.



----


Task 1

What is the name of the malicious phishing attachment downloaded by the user on Client02?

Extract $MFT with MFTECmd 

```
.\MFTECmd.exe -f 'C:\Users\*\Desktop\GhostTrace\Logs-Client02\C\$MFT' --csv 'C:\Users\*\Desktop\
```
Import into Timeline Explorer and
Filter for .docm and you will find the file coming from a suspicious url

<img width="3772" height="1631" alt="image" src="https://github.com/user-attachments/assets/54476e85-322e-407d-9149-085bf8eff7c4" />


Task 2

What is the IP address from which the malicious attachment was downloaded?
```
192.168.204.152
```
<img width="3772" height="1631" alt="image" src="https://github.com/user-attachments/assets/5fc4c4ad-8d89-4731-8dec-986834ee2bcc" />



Task 3

After the victim opened the file the malware initiated a network connections to a remote IP address. What is the IP address and the port number?
192.168.204.152:4444



After using chainsaw to search up the docm source
```
.\chainsaw.exe search -e "Profits.docm" C:\Users\topsa\Desktop\GhostTrace\Logs-Client02\
```
<img width="2054" height="954" alt="image" src="https://github.com/user-attachments/assets/9f3fba0d-e44f-466d-bab6-873df62fa1fe" />

We can use the parent GUID to collect more information on what the process is doing


```
.\chainsaw.exe search -e "0FD50764-8E3C-6832-6D01-000000002300" C:\Users\topsa\Desktop\GhostTrace\Logs-Client02\
```







Task 4

What is the name of the second-stage payload uploaded to Client02?
UpdatePolicy.exe


```
 .\chainsaw.exe search -e "0FD50764-8F16-6832-7E01-000000002300" C:\Users\topsa\Desktop\GhostTrace\Logs-Client02\
```
Keep following the TA trace with the above commands

<img width="2000" height="812" alt="image" src="https://github.com/user-attachments/assets/a1ea1b1b-5604-43f7-b304-9aed28f63b37" />




Task 5

What port was used for the reverse shell connection from the second-stage payload on Client02?
1337


```
.\chainsaw.exe search -e "0FD50764-8F47-6832-8301-000000002300" C:\Users\topsa\Desktop\GhostTrace\Logs-Client02\
```
<img width="802" height="916" alt="image" src="https://github.com/user-attachments/assets/729287c9-22a1-4373-92b3-0c38ccf7abc8" />



Task 6

The attacker subsequently downloaded a tool to enumerate the Active Directory environment. What is the name of this tool?

PowerView.ps1

<img width="2317" height="1112" alt="image" src="https://github.com/user-attachments/assets/c9bea106-14f3-4d97-b7b3-767cebc52bad" />


```
.\chainsaw.exe hunt C:\Users\*\Desktop\GhostTrace\Logs-Client02\ --sigma .\rules --mapping mappings\sigma-event-logs-all.yml
```

Task 7

What is the username of the targeted service account?
sqlsvc


Since we know TA used powerview we can search for specific commands executed with powerview
```
.\chainsaw.exe search -e "Get-DomainSPNTicket" C:\Users\*\Desktop\GhostTrace\Logs-Client02\
```
<img width="1554" height="913" alt="image" src="https://github.com/user-attachments/assets/4ec4b971-1dc4-4166-9781-83f0305250a6" />



Task 8

After acquiring the account credentials, the attacker was able to crack the ticket. When did the attacker first use them to log in? (UTC)

2025-05-25 04:03:47


Search for "4624" login events for sqlsvc user in Security logs for Client 03

<img width="2038" height="1723" alt="image" src="https://github.com/user-attachments/assets/b7d395bb-822f-4e5a-9638-56de32058e06" />


Task 9

What is the executable associated with the first service created by a Sysinternals tool on the target system following the attacker's initial login attempt?

VgYTbFEK.exe



Task 10

On Client03, what was the file name of the executable used to dump cleartext credentials from memory?

search for a popular tool named mimikatz
```
.\chainsaw.exe search -e "mimikatz" C:\Users\topsa\Desktop\GhostTrace\Logs-Client03\
```

Logs show netdiag disguised as mimikatz
<img width="2079" height="891" alt="image" src="https://github.com/user-attachments/assets/277026ec-0230-4e82-a342-3274bca5dbcf" />


Task 11

What is the username of the account whose cleartext password was found on Client03?
Lucas

After extracting the relevant info in the image, lucas is the most proabable user
```
.\chainsaw.exe search -e 4624 "C:\Users\*\Desktop\GhostTrace\Logs-Client03\" |
>> Select-String "SystemTime|TargetUserName|IpAddress|LogonType"
```
<img width="754" height="1023" alt="image" src="https://github.com/user-attachments/assets/9a3fafc9-65a3-443d-a6ea-fec0521875e7" />



Task 12

After obtaining the cleartext password of this account, the attacker carried out a domain-level credential extraction attack. At what time did the compromised account perform this attack on the domain? (UTC)
2025-05-25 04:26:36

We can use this chainsaw command to search for DC-Sync attacks which is a technique to get all domain hashes.

```
.\chainsaw.exe search -e 4662 "C:\Users\*\Desktop\GhostTrace\Logs-DC\" |
Select-String "SystemTime|SubjectUserName|SubjectDomainName|ObjectName|Properties|1131f6aa|1131f6ad|89e95b76"
```
<img width="2094" height="1785" alt="image" src="https://github.com/user-attachments/assets/6fbe8ef1-7ee1-4662-886f-72f5400f4bb8" />




Task 13

At what time did the attacker initially authenticate using the administrator account? (UTC)
2025-05-25 04:34:01



Task 14

What is the name of the service created by the attacker on DC01 for persistence?
WindowsUpdateSvc

```
.\chainsaw.exe search -e "sc.exe" C:\Users\topsa\Desktop\GhostTrace\Logs-DC\
```
<img width="1965" height="951" alt="image" src="https://github.com/user-attachments/assets/d2c2e1ec-93de-4c80-bd6f-c1cbab0d7a84" />



Task 15

What is the name of the scheduled task created by the attacker on DC01 for persistence?
WindowsUpdateCheck


Search for schtasks creation with chainsaw in DC logs 

```
.\chainsaw.exe search -e "schtasks" C:\Users\topsa\Desktop\GhostTrace\Logs-DC\
```
<img width="2233" height="1026" alt="image" src="https://github.com/user-attachments/assets/9e0e9dac-f8ec-472e-974d-ac5b9a9cde61" />






Task 16

What is the registry key name created by the attacker on DC01 for persistence?
xcvafctr


search for persistence in Run key 
```
.\chainsaw.exe search -e "CurrentVersion\\Run" C:\Users\topsa\Desktop\GhostTrace\Logs-DC\
```
<img width="1953" height="956" alt="image" src="https://github.com/user-attachments/assets/9f5187e9-d634-4171-b831-e074ad0eb605" />

