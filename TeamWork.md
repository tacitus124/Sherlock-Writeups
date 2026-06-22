

----

#### Sherlock Scenario

It is Friday afternoon and the SOC at Edny Consulting Ltd has received alerts from the workstation of Jason Longfield, a software engineer on the development team, regarding the execution of some discovery commands. Jason has just gone on holiday and is not available by phone.The workstation appears to have been switched off, so the only evidence we have at the moment is an export of his mailbox containing today's messages. As the company was recently the victim of a supply chain attack, this case is being taken seriously and the Cyber Threat Intelligence team is being called in to determine the severity of the threat.


---


Task 1

Identify the sender of the suspicious email.

Looking through the emails, one stands out here with a link to a suspicious domain



<img width="1087" height="625" alt="image" src="https://github.com/user-attachments/assets/1aeb3567-4597-4f28-83a7-761884fac16a" />







Task 2

The suspicious email came from a custom domain, identify its creation date.
  
2025-01-31





We can find the relevant information here

https://web.archive.org/web/20250219112815/https://www.whois.com/whois/developingdreams.site


Task 3

The domain was registered shortly before the suspicious email was received, which likely corresponds to the time when the threat actor was planning this campaign. Which MITRE ATT&CK sub-technique of the Resource Development tactic corresponds to this activity?

T1583.001


https://attack.mitre.org/techniques/T1583/001/








Task 4

The previously identified domain appears to belong to a company, what is the full URL of the company's page on X (formerly Twitter)?


https://x.com/Develop_Dreams

https://web.archive.org/web/20250203105719/https://www.developingdreams.site/ then we can hover over the Twitter logo at the bottom to see the URL



Task 5

Reading the suspicious email carefully, it appears that the threat actor first contacted the victim using the previously identified social media profile. Which MITRE ATT&CK sub-technique of the Resource Development tactic corresponds to this activity?
T1585.001


https://attack.mitre.org/techniques/T1585/001/


Task 6

What is the name of the game the threat actor would like us to collaborate on?
### DeTankWar

https://web.archive.org/web/20250204120033/https://developingdreams.site/




Task 7

What is the SHA-256 hash of the executable shared by the threat actor?
56554117d96d12bd3504ebef2a8f28e790dd1fe583c33ad58ccbf614313ead8c

Paste the download URL in the email in waybackmachine and you will get get the file
https://web.archive.org/web/20250204120233/https://developingdreams.site/release/beta_release_v.1.32.zip

Then get hash with powershell
```
Get-FileHash .\beta_release_v.1.32.exe -Algorithm SHA256
```


Task 8

As part of the preparation of the tools for the attack, the threat actor hosted this file, presumably malware, on its infrastructure. Which MITRE ATT&CK sub-technique of the Resource Development tactic corresponds to this activity?

T1608.001

https://attack.mitre.org/techniques/T1608/001/


Task 9

Based on the information you have gathered so far, do some research to identify the name of the threat actor who may have carried out this attack.

Moonstone Sleet

Looking up the hash we got earlier on Virustotal, a comment mentions a research report on a new APT named Moonstone sleet
https://www.virustotal.com/gui/file/56554117d96d12bd3504ebef2a8f28e790dd1fe583c33ad58ccbf614313ead8c/community

https://www.microsoft.com/en-us/security/blog/2024/05/28/moonstone-sleet-emerges-as-new-north-korean-threat-actor-with-new-bag-of-tricks/

The report lines up with what we discovered so far





Task 10

What nation is the threat actor believed to be associated with?
North Korea

https://www.microsoft.com/en-us/security/blog/2024/05/28/moonstone-sleet-emerges-as-new-north-korean-threat-actor-with-new-bag-of-tricks/

Task 11

Another campaign from this threat actor used a trojanized version of a well-known software to infect victims. What is the name of this tool?
Putty


https://www.microsoft.com/en-us/security/blog/2024/05/28/moonstone-sleet-emerges-as-new-north-korean-threat-actor-with-new-bag-of-tricks/

Task 12

Which MITRE ATT&CK technique corresponds to the activity of deploying trojanized/manipulated software?
T1195.002

https://attack.mitre.org/techniques/T1195/002/




Task 13

Our company wants to protect itself from other supply chain attacks, so in documenting more about this threat actor, the CTI team found that other security researchers were also tracking a group whose techniques closely match the threat actor we identified, and discovered a new supply chain campaign around the end of July 2024. What technology is this campaign targeting?

npm


https://zero.checkmarx.com/july-2024-in-software-supply-chain-security-cb960b598b62

https://zero.checkmarx.com/a-year-long-campaign-of-north-korean-actors-targeting-developers-via-malicious-npm-packages-dbf7a6761361


Task 14

We now need some indicators to be able to rule out that other systems have been compromised. What is the name and version of the lastest malicious package published? (Format: package-name vX.X.X)

harthat-hash v1.3.3

https://securitylabs.datadoghq.com/articles/stressed-pungsan-dprk-aligned-threat-actor-leverages-npm-for-initial-access/


Task 15

The malicious packages downloaded an additional payload from a C2 server, what is its IP address?
142.111.77.196

https://securitylabs.datadoghq.com/articles/stressed-pungsan-dprk-aligned-threat-actor-leverages-npm-for-initial-access/

Task 16

The payload, after being renamed, is finally executed by a legitimate Windows binary to evade defenses. Which MITRE ATT&CK technique corresponds to this activity?
T1218.011

https://attack.mitre.org/techniques/T1218/011/


