
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
![](Pasted%20image%2020260615212802.png)












Task 2

What is the IP address of the attacker from whom the files were downloaded?

```
94.156.177.109
```


![](Pasted%20image%2020260615213156.png)




Task 3

Which malicious file appears to be the first one downloaded?

```
sh
```
![](Pasted%20image%2020260615213210.png)










Task 4

What is the name of the function that the attacker used to download the payload?

```
dlr
```



Follow the HTTP stream of the malicious request
![](Pasted%20image%2020260615213407.png)






Task 5

Which port does the attacker's server use?


```
80
```

![](Pasted%20image%2020260615213501.png)








Task 6

The script checks which directories it can write to by attempting to create test files. What is the size of the second test file? (Size in MB)

```
2
```


![](Pasted%20image%2020260615213627.png)








Task 7

What is the full command that the script uses to identify the CPU architecture?

```
uname -mp
```


![](Pasted%20image%2020260615214329.png)









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
![](Pasted%20image%2020260615215413.png)







Task 10

Apparently, the attacker used a packer to compress the malware. Which version of this packer was used? (Format X.XX)


```
4.23
```



Export the x86_64 file from File > Export Objects > HTTP > x86_64. Then run strings on it to find the version

![](Pasted%20image%2020260615220304.png)







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
![](Pasted%20image%2020260615225335.png)

https://attack.mitre.org/techniques/T1496/