

---


#### Sherlock Scenario

We have been hot on the trail for a political dissident. They jump from café to café using the Wi-Fi making it hard to nab them. During one of their trips, they unknowingly sat next to one of our agents and we captured them with their laptop on. We need to know where they have been and what they have been doing. Analyze the KAPE output and see if you can get us some answers.


----


Task 1

What is the Computer name of the machine?
InvisibleChains

Import SYSTEM hive into Registry Explorer and go to HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Param eters\Interfaces

<img width="1275" height="486" alt="image" src="https://github.com/user-attachments/assets/cd73e76a-cdbd-4c66-ad66-f430d3cc3850" />




Task 2

What is the first Wi-Fi SSID(Decoded) they connected to on May 30th 2025?

ArboretumCoffee

Import SOFTWARE hive into Registry Explorer and go to Software/Microsoft/Windows NT/Current Version/NetworkList

<img width="1834" height="350" alt="image" src="https://github.com/user-attachments/assets/e6bcd4e5-8f74-4c33-a06b-3f7c7c5d3749" />




Task 3

When did the system obtain a lease for the network?
2025-05-30 18:22:48

Load SYSTEM hive into Registry Explorer and go to ControlSet001\Services\Tcpip\Parameters\Interfaces and the DCHPNetworkHints tab
<img width="1395" height="360" alt="image" src="https://github.com/user-attachments/assets/ec5d67f9-e82c-48f5-9e80-9d667fc0d831" />





Task 4

What IP address did the device receive when connecting to the café?
172.16.100.16
<img width="1267" height="255" alt="image" src="https://github.com/user-attachments/assets/145c550e-c577-495d-85f3-6fb554271b23" />






Task 5

What was the BSSID (MAC address) of the access point they connected to at the café?
E4-D1-24-96-A5-D1


<img width="1296" height="332" alt="image" src="https://github.com/user-attachments/assets/c418e885-a186-4763-86af-a03db2c12a24" />









Task 6

It looks like they started some sort of manifesto at the café, what is the name of the file they started to write?
The Chains Not Seen.txt


Load in ntuser.dat and check RecentDocs

<img width="1460" height="558" alt="image" src="https://github.com/user-attachments/assets/b079ac37-cbc3-4f97-a9b2-5f81709f8114" />




Task 7

What is the last sentence of the manifesto?

Freedom is a perspective away.







Task 8

They started their research by watching a YouTube video of a speech, what is the name of the speech?

Import Edge browser history into DB Browser and you can find the answer in url´s 
<img width="1144" height="359" alt="image" src="https://github.com/user-attachments/assets/b943aa02-9fd5-4ea6-b50f-2545758e3e0b" />



Task 9

They continued their research by looking up a book on Wikipedia, what was the title of the book?
The Iron Heel




Import Edge browser history into DB Browser
<img width="1530" height="804" alt="image" src="https://github.com/user-attachments/assets/32431f05-69c1-4cda-8c24-b54f46376530" />


Task 10

What was the last thing they downloaded before leaving the café?

BraveBrowserSetup.exe

Import Edge browser history into DB Browser and go to Downloads tab
<img width="1868" height="370" alt="image" src="https://github.com/user-attachments/assets/c359a8ee-2060-4b72-9f7e-0afdf96b8e4c" />



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
<img width="854" height="563" alt="image" src="https://github.com/user-attachments/assets/db7dab72-2798-4c16-b32e-56c8ca201010" />



Task 13

Using the same logs, when did the user arrive at the second café?

2025-05-30 19:05:26

Use chainsaw to search through the Microsoft-Windows-NetworkProfile/Operational logs
```
.\chainsaw.exe search -e "10000" C:\Users\*\Desktop\KAPEOUT\C\Windows\System32\winevt\logs\Microsoft-Windows-NetworkProfile%4Operational.evtx
```
<img width="1109" height="580" alt="image" src="https://github.com/user-attachments/assets/1c6f78e2-fef3-45fa-9709-18a0242efa71" />




Task 14

What is the SSID(decoded) of the second Wi-Fi they connected to on May 30th 2025?

Happy Trails Guest

<img width="1320" height="397" alt="image" src="https://github.com/user-attachments/assets/9b96b59b-d7a8-4c57-b984-e55da0f985a6" />






Task 15

What IP address did the device receive when connecting to the second café?
192.168.10.100

<img width="1056" height="357" alt="image" src="https://github.com/user-attachments/assets/a9ac0548-9d14-43f8-86d1-6b5c855527eb" />



Task 16

When did the system obtain a lease for the second network?

2025-05-30 19:06:02

<img width="1150" height="348" alt="image" src="https://github.com/user-attachments/assets/d9cdcb0a-0912-426f-a90f-5a86c20d06b5" />




Task 17

What was the BSSID (MAC address) of the access point they connected to at the second café?

4C-BA-7D-E1-8C-30

<img width="1220" height="335" alt="image" src="https://github.com/user-attachments/assets/628db9fe-343e-47ce-9020-c7f0ee355818" />





Task 18

What was the first thing the user downloaded at the second café?

VirtualBox-7.1.8-168469-Win.exe



Import Brave browser history into DB Browser and go to Downloads
C\Users\Ernes\AppData\Local\BraveSoftware\Brave-Browser\User Data\Default/History
<img width="1920" height="525" alt="image" src="https://github.com/user-attachments/assets/3c3e398b-58d0-4aed-abf8-bfb8c55f9e83" />






Task 19

What online forum/social media site did they visit?

Import Brave browser history into DB Browser and go to url´s

C\Users\Ernes\AppData\Local\BraveSoftware\Brave-Browser\User Data\Default/History
<img width="1254" height="741" alt="image" src="https://github.com/user-attachments/assets/d6b5e6d1-00fa-48b3-a044-b2960e75b634" />




Task 20

What was their username on the site?



Import this into DB Browser and go to logins tab
\C\Users\Ernes\AppData\Local\BraveSoftware\Brave-Browser\User Data\Default\Login Data
<img width="1736" height="472" alt="image" src="https://github.com/user-attachments/assets/1ce9d4af-ef8d-49b6-83d5-3d3b33de13d3" />



Task 21

What was the name of the VM they created?

LastHope


Search through the drive with Chainsaw for any Virtualbox instance
```
.\chainsaw.exe search -e "VirtualBox" C:\Users\tim\Desktop\KAPEOUT\C\
```
<img width="753" height="492" alt="image" src="https://github.com/user-attachments/assets/d96c17cb-95e2-406b-98b9-c488b4654a6e" />





Task 22

What street was the first café on?
W Prospect Rd

Search up the cafe 
<img width="1123" height="555" alt="image" src="https://github.com/user-attachments/assets/834aa10c-f1be-4064-ba3f-1caace070ba8" />



Task 23

Investigators may want to follow up on the Wi-Fi credentials used at the first café the suspect visited. Which file stores the authentication details (including the encrypted password) for the first network?

{BAC95378-DC6B-4464-918E-4E005F747786}.xml



Task 24

What authentication method was used to connect to the first café's Wi-Fi?

WPA2PSK

