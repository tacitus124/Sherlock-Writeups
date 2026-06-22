
----

#### Sherlock Scenario

Recently, a user triggered multiple alerts after downloading several potentially unwanted applications (PUAs), prompting concern from the security team. To gain deeper insight into the user's activity, the team began monitoring network traffic from their workstation. Their objective is to assess whether the downloads are linked to more serious malware threats.



----



Task 1

What is the victim's IP address?


```
192.168.1.152
```


Filter for HTTP traffic and you will notice a malicious looking request
<img width="1093" height="807" alt="image" src="https://github.com/user-attachments/assets/925266cf-b828-4b2c-8072-d648f7053d2d" />













Task 2

What is the IP address of the attacker from whom the files were downloaded?

```
94.156.177.109
```


<img width="928" height="724" alt="image" src="https://github.com/user-attachments/assets/2f83ec49-f57d-4a19-9950-27d0faa80952" />





Task 3

Which malicious file appears to be the first one downloaded?

```
sh
```
<img width="928" height="724" alt="image" src="https://github.com/user-attachments/assets/18c5e3d8-c3be-4480-9ef4-64099d7aaa96" />











Task 4

What is the name of the function that the attacker used to download the payload?

```
dlr
```



Follow the HTTP stream of the malicious request
<img width="1280" height="458" alt="image" src="https://github.com/user-attachments/assets/e812cbc8-a268-487a-8fb2-5204d6b081e2" />







Task 5

Which port does the attacker's server use?


```
80
```

<img width="603" height="489" alt="image" src="https://github.com/user-attachments/assets/64d600b1-3f42-42b1-95aa-5c6f732682a0" />









Task 6

The script checks which directories it can write to by attempting to create test files. What is the size of the second test file? (Size in MB)

```
2
```


<img width="1088" height="169" alt="image" src="https://github.com/user-attachments/assets/322f9396-8126-468a-9648-0f0b2b88a580" />









Task 7

What is the full command that the script uses to identify the CPU architecture?

```
uname -mp
```


<img width="676" height="276" alt="image" src="https://github.com/user-attachments/assets/ef2c9e8b-4de4-49b0-a495-6b6295a9e19b" />










Task 8

What is the name of the file that is downloaded after the CPU architecture is compared with reference values?


```
x86_64
```










Task 9

What is the full command that the attacker used to disable any existing mining service?

```
systemctl disable c3pool_miner
```




```
ip.src_host== 192.168.1.152 and ip.dst_host == 94.156.177.109
```
Filter for the communicating IP's and go to Statistics > Conversations  > TCP > and then follow any of the streams until you find this
<img width="944" height="466" alt="image" src="https://github.com/user-attachments/assets/d41a2bfc-a4b3-4264-98ed-e3660ad667b6" />








Task 10

Apparently, the attacker used a packer to compress the malware. Which version of this packer was used? (Format X.XX)


```
4.23
```



Export the x86_64 file from File > Export Objects > HTTP > x86_64. Then run strings on it to find the version

<img width="853" height="576" alt="image" src="https://github.com/user-attachments/assets/2e351fd0-c4c0-46c0-be20-28b545aa673f" />








Task 11

What is the entropy value of unpacked malware?

```
6.488449
```











Task 12


What is the file name with which the unpacked malware was submitted on VirusTotal for the first time?


```
redtail.cuidado
```


Grab the hash of the file and look it up virustotal under "Names" section

https://www.virustotal.com/gui/file/61c128f1e5225e9d230d7fffa66468286c16fe622a7edee661fefe115698656c/details









Task 13

What MITRE ATT&CK technique ID is associated with the main purpose of the malware?

```
T1496
```


Lots of the analysis points to the malware being a miner
<img width="1780" height="844" alt="image" src="https://github.com/user-attachments/assets/3fed34f9-f04e-42b0-b0d7-dbbb50077c5f" />


https://attack.mitre.org/techniques/T1496/
