


---


#### Sherlock Scenario

Forela carry out penetration testing of their internal networks utilising an internal team within their security department. The security team have notes from tests in addition to company critical credentials. It seems their host may have been compromised. Please verify how this occurred using the retrospective collection provided.



-----







Task 1

What is the name of the repository utilized by the Pen Tester within Forela that resulted in the compromise of his host?

```
autoenum
```

Checking the bash history of the user shows this script
![](Pasted%20image%2020260612135217.png)


If we go to the Git and look through the script we find this suspicious function "do_wget_and_run"
![](Pasted%20image%2020260612135333.png)















Task 2

What is the name of the malicious function within the script ran by the Pen Tester?

```
do_wget_and_run
```

Follow steps above
![](Pasted%20image%2020260612135503.png)









Task 3

What is the password of the zip file downloaded within the malicious function?

```
superhacker
```


The script encodes the part 1 and part 2 strings in base64. If you decode them together, you get the answer
![](Pasted%20image%2020260612140009.png)


![](Pasted%20image%2020260612140202.png)








Task 4

What is the full URL of the file downloaded by the attacker?

```
https://www.dropbox.com/scl/fi/uw8oxug0jydibnorjvyl2/blob.zip?rlkey=zmbys0idnbab9qnl45xhqn257&st=v22geon6&dl=1
```


The "FILE_URL" function combines the two URL's in functions f1 and f2
![](Pasted%20image%2020260612140346.png)









Task 5

When did the attacker finally take out the real comments for the malicious function?

```
2024-12-23 22:27:58
```

git clone the repository and inspect the commit "7d203152c5a3a56af3d57eb1faca67a3ec54135f"







Task 6

The attacker changed the URL to download the file, what was it before the change?




```
https://www.dropbox.com/scl/fi/wu0lhwixtk2ap4nnbvv4a/blob.zip?rlkey=gmt8m9e7bd02obueh9q3voi5q&st=em7ud3pb&dl=1
```

![](Pasted%20image%2020260612141600.png)








Task 7

What is the MITRE technique ID utilized by the attacker to persist?


```
T1053.003
```


https://attack.mitre.org/techniques/T1053/003/


The script places the downloaded file in crontab so it executes on startup
![](Pasted%20image%2020260612141720.png)



Task 8

What is the Mitre Att&ck ID for the technique relevant to the binary the attacker runs?

```
T1496
```

Download the script from the URL and get the file hash of the "blob" file

Search it up on virustotal and its labeled as a crypto miner
https://www.virustotal.com/gui/file/b0e1ae6d73d656b203514f498b59cbcf29f067edf6fbd3803a3de7d21960848d/detection


https://attack.mitre.org/techniques/T1496/


We can further confirm it by checking the config file included in the download
![](Pasted%20image%2020260612142905.png)