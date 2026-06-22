

---


#### Sherlock Scenario

We have been hot on the trail for a political dissident. They jump from café to café using the Wi-Fi making it hard to nab them. During one of their trips, they unknowingly sat next to one of our agents and we captured them with their laptop on. We need to know where they have been and what they have been doing. Analyze the KAPE output and see if you can get us some answers.


----


Task 1

What is the Computer name of the machine?
InvisibleChains

Import SYSTEM hive into Registry Explorer and go to HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Param eters\Interfaces

![](Pasted%20image%2020260608194250.png)



Task 2

What is the first Wi-Fi SSID(Decoded) they connected to on May 30th 2025?

ArboretumCoffee

Import SOFTWARE hive into Registry Explorer and go to Software/Microsoft/Windows NT/Current Version/NetworkList

![](Pasted%20image%2020260608194937.png)



Task 3

When did the system obtain a lease for the network?
2025-05-30 18:22:48

Load SYSTEM hive into Registry Explorer and go to ControlSet001\Services\Tcpip\Parameters\Interfaces and the DCHPNetworkHints tab
![](Pasted%20image%2020260608201720.png)




Task 4

What IP address did the device receive when connecting to the café?
172.16.100.16
![](Pasted%20image%2020260608202200.png)






Task 5

What was the BSSID (MAC address) of the access point they connected to at the café?
E4-D1-24-96-A5-D1

![](Pasted%20image%2020260608202602.png)





Task 6

It looks like they started some sort of manifesto at the café, what is the name of the file they started to write?
The Chains Not Seen.txt


Load in ntuser.dat and check RecentDocs
![](Pasted%20image%2020260608203650.png)



Task 7

What is the last sentence of the manifesto?

Freedom is a perspective away.







Task 8

They started their research by watching a YouTube video of a speech, what is the name of the speech?

Import Edge browser history into DB Browser and you can find the answer in url´s 
![](Pasted%20image%2020260608222852.png)


Task 9

They continued their research by looking up a book on Wikipedia, what was the title of the book?
The Iron Heel




Import Edge browser history into DB Browser
![](Pasted%20image%2020260608222620.png)

Task 10

What was the last thing they downloaded before leaving the café?

BraveBrowserSetup.exe

Import Edge browser history into DB Browser and go to Downloads tab
![](Pasted%20image%2020260608223022.png)


Task 11

When investigating changes to network profiles on a Windows system, which event log would you examine to find entries related to these profile-specific events, using event IDs such as 10000 or 10001?
Microsoft-Windows-NetworkProfile/Operational



Task 12

Using the logs from the previous answer, when did they disconnect and leave the first café?

2025-05-30 18:55:45

Use chainsaw to search through the Microsoft-Windows-NetworkProfile/Operational logs
```
.\chainsaw.exe search -e "10001" C:\Users\*\Desktop\KAPEOUT\C\Windows\System32\winevt\logs\Microsoft-Windows-NetworkProfile%4Operational.evtx
```
![](Pasted%20image%2020260608225148.png)


Task 13

Using the same logs, when did the user arrive at the second café?

2025-05-30 19:05:26

Use chainsaw to search through the Microsoft-Windows-NetworkProfile/Operational logs
```
.\chainsaw.exe search -e "10000" C:\Users\*\Desktop\KAPEOUT\C\Windows\System32\winevt\logs\Microsoft-Windows-NetworkProfile%4Operational.evtx
```
![](Pasted%20image%2020260608225527.png)




Task 14

What is the SSID(decoded) of the second Wi-Fi they connected to on May 30th 2025?

Happy Trails Guest

![](Pasted%20image%2020260608230852.png)





Task 15

What IP address did the device receive when connecting to the second café?
192.168.10.100

![](Pasted%20image%2020260608230950.png)


Task 16

When did the system obtain a lease for the second network?

2025-05-30 19:06:02

![](Pasted%20image%2020260608231025.png)



Task 17

What was the BSSID (MAC address) of the access point they connected to at the second café?

4C-BA-7D-E1-8C-30

![](Pasted%20image%2020260608231435.png)




Task 18

What was the first thing the user downloaded at the second café?

VirtualBox-7.1.8-168469-Win.exe



Import Brave browser history into DB Browser and go to Downloads
C\Users\Ernes\AppData\Local\BraveSoftware\Brave-Browser\User Data\Default/History
![](Pasted%20image%2020260608231909.png)





Task 19

What online forum/social media site did they visit?

Import Brave browser history into DB Browser and go to url´s

C\Users\Ernes\AppData\Local\BraveSoftware\Brave-Browser\User Data\Default/History
![](Pasted%20image%2020260608231624.png)




Task 20

What was their username on the site?



Import this into DB Browser and go to logins tab
\C\Users\Ernes\AppData\Local\BraveSoftware\Brave-Browser\User Data\Default\Login Data
![](Pasted%20image%2020260608232324.png)


Task 21

What was the name of the VM they created?

LastHope


Search through the drive with Chainsaw for any Virtualbox instance
```
.\chainsaw.exe search -e "VirtualBox" C:\Users\tim\Desktop\KAPEOUT\C\
```
![](Pasted%20image%2020260608232708.png)




Task 22

What street was the first café on?
W Prospect Rd

Search up the cafe 
![](Pasted%20image%2020260608233132.png)


Task 23

Investigators may want to follow up on the Wi-Fi credentials used at the first café the suspect visited. Which file stores the authentication details (including the encrypted password) for the first network?

{BAC95378-DC6B-4464-918E-4E005F747786}.xml



Task 24

What authentication method was used to connect to the first café's Wi-Fi?

WPA2PSK

