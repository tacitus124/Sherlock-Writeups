


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
<img width="1197" height="570" alt="image" src="https://github.com/user-attachments/assets/ee9cb154-485f-45c5-9d08-fd6b26c5798c" />



If we go to the Git and look through the script we find this suspicious function "do_wget_and_run"
<img width="1124" height="924" alt="image" src="https://github.com/user-attachments/assets/b6b24d28-6f10-46ec-b50d-2489f2021851" />
















Task 2

What is the name of the malicious function within the script ran by the Pen Tester?

```
do_wget_and_run
```

Follow steps above
<img width="1167" height="839" alt="image" src="https://github.com/user-attachments/assets/aa102ace-dccf-4422-baca-14cff67296b6" />










Task 3

What is the password of the zip file downloaded within the malicious function?

```
superhacker
```


The script encodes the part 1 and part 2 strings in base64. If you decode them together, you get the answer
<img width="647" height="540" alt="image" src="https://github.com/user-attachments/assets/9ef85ca7-024e-474a-85ba-6c015b44150f" />



<img width="1452" height="716" alt="image" src="https://github.com/user-attachments/assets/9c38d3d0-e7d9-4d0b-9935-d9fe7f2fee48" />









Task 4

What is the full URL of the file downloaded by the attacker?

```
https://www.dropbox.com/scl/fi/uw8oxug0jydibnorjvyl2/blob.zip?rlkey=zmbys0idnbab9qnl45xhqn257&st=v22geon6&dl=1
```


The "FILE_URL" function combines the two URL's in functions f1 and f2
<img width="1078" height="636" alt="image" src="https://github.com/user-attachments/assets/4304a68d-3257-442f-8c88-464a6a3a26ae" />










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

<img width="1571" height="695" alt="image" src="https://github.com/user-attachments/assets/28c000cb-075b-407e-8da4-9e0cb6d1780d" />









Task 7

What is the MITRE technique ID utilized by the attacker to persist?


```
T1053.003
```


https://attack.mitre.org/techniques/T1053/003/


The script places the downloaded file in crontab so it executes on startup
<img width="1016" height="791" alt="image" src="https://github.com/user-attachments/assets/9a34c23f-93d6-43ac-9b66-bacc25e03a0b" />




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
<img width="1409" height="618" alt="image" src="https://github.com/user-attachments/assets/4f4bcca1-7186-4863-a87d-ac79e8a4d8bf" />
