
---

#### Sherlock Scenario

The Cyber Crime Investigation Unit (CCIU) has been tracking a famous cybercriminal organization known as "CrewCrow" for several years. The group is responsible for numerous high-profile cyber-attacks, targeting financial institutions, government agencies, and private corporations worldwide. The elusive leader of CrewCrow, known by the alias "Nefarious," is a master hacker, who has managed to evade the authorities for years. In a major breakthrough, CCIU intercepted communications indicating that Nefarious was planning a significant cyber-attack. Acting swiftly, the unit launched a coordinated operation, to arrest CrewCrow members and seize their equipment. During the raid, agents confiscated several devices, including Nefarious's personal computer.As the top digital forensics analyst in the country, you have been tasked with analyzing the disk image of Nefarious's computer. Your objective is to uncover critical information that could provide insights into CrewCrow's operations, reveal the details of their planned attack, and ultimately bring Nefarious to justice.

---


Task 1

Identify the conferencing application used by CrewCrow members for their communications.


```
Zoom
```




Import ntuser.dat into Registry Explorer and find the userassist key which shows program execution. We can see it shows Zoom, a popular conference program
<img width="1459" height="658" alt="image" src="https://github.com/user-attachments/assets/04fb40c5-b80c-4f74-b18f-87ea583e20bb" />











Task 2

Determine the last time Nefarious used the conferencing application.

```
2024-07-16 09:02:02
```





We can find a programs last run via its prefetch file. We can use PECmd to extract the relevant information
```
.\PECmd.exe -f "..\crewcrow\C\Windows\prefetch\ZOOM.EXE-F882A381.pf"
```
<img width="1529" height="877" alt="image" src="https://github.com/user-attachments/assets/8a3db215-2e14-4e20-96a5-908590323bdf" />










Task 3

Where is the conferencing application's data stored?


```
C:\Users\Nefarious\AppData\Roaming\Zoom\data
```




We can find the answer in the same prefetch information as in Q2
<img width="996" height="776" alt="image" src="https://github.com/user-attachments/assets/159f54ca-bf67-4e27-90f9-b7d5d15f8910" />










Task 4

Which Windows data protection service is used to secure the conferencing application's database files?

```
DPAPI
```




https://github.com/sepinf-inc/IPED/issues/2708











Task 5

Determine the sign-in option used by Nefarious.


```
Password
```















Task 6

Retrieve the password used by Nefarious

```
ohsonefarious92
```



use hashcat to crack the password












Task 7

Find the key derivation function iterations used in the encryption process of the conferencing application's database.

```
4000
```




https://github.com/sepinf-inc/IPED/issues/2708











Task 8

Find the key derivation function page size used in the encryption process.

```
1024
```


https://github.com/sepinf-inc/IPED/issues/2708













Task 9

Identify Nefarious email address.

```
nefarious92@outlook.com
```
















Task 10

What is the Meeting ID?


```
86233834426
```















Task 11

Retrieve the password used to encrypt the plan PDF file from the meeting chat.



```
EOztYmVeUxp6TmV
```


















Task 12

Discover the location from which the upcoming cyber-attack will be launched.


```
Eastern Europe
```
