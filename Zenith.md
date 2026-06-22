

---

#### Sherlock Scenario

We are contacting you with an urgent request concerning a potentially suspicious email that was recently received and unfortunately opened by one of our team members. As a construction company (Caymine builders), we regularly engage in project discussions with clients, and this email appeared to contain a project plan in PDF format. However, after further review, we have reason to believe this email and its attachment could be malicious. Despite our usual security protocols, the PDF was opened on one of our systems, which has raised significant concern regarding the security of our network.


---

Task 1

When did the client receive the email?

2024-09-19 17:44:11


![](Pasted%20image%2020260609145301.png)





Task 2

When was the malicious PDF file created? (UTC)

2024-09-18 13:57:04



Extract the attachment out of the email, you can use this script


```
import os
from email import policy
from email.parser import BytesParser

eml = "Submission of Project Plan for Your Review - john@skyline.com - 2024-09-19 1845.eml"
out = "attachments"
os.makedirs(out, exist_ok=True)

with open(eml, "rb") as f:
    msg = BytesParser(policy=policy.default).parse(f)

count = 0
for part in msg.iter_parts():
    filename = part.get_filename()
    if filename:
        payload = part.get_payload(decode=True)
        if payload:
            path = os.path.join(out, filename)
            with open(path, "wb") as o:
                o.write(payload)
            print(f"[+] Extracted {filename} ({len(payload)} bytes)")
            count += 1

print(f"[+] Total attachments: {count}")

```
This will extract the attachment into a "attachments" folder. Then run strings on the file in attachments folder


![](Pasted%20image%2020260609155031.png)






Task 3

What is the embedded file name with extension inside the malicious PDF?

```
downtown_construction_project_plan.pdf)
```

![](Pasted%20image%2020260609155521.png)





Task 4

When was the Windows PE malware compiled?

2024-09-18 21:19:18






Task 5

What was the original project name the attacker gave to their malware Windows PE project?
exit




Task 6

To which new location in the system is the malware copying itself?
C:\Users\Public\test.exe







Task 7

What is the name of the registry value key that the malware is creating inside the Run folder?
WindowsPooler







Task 8

What is the name of the process being targeted for injection by the malware?
explorer.exe







Task 9

Which operating system is the client using?

Windows7




Task 10

What is the full name of the Adobe PDF program?
Adobe Reader 9









Task 11

When did the attacker use the GetSystem service installation to gain NT Authority/SYSTEM access?
2024-09-19 17:48:31

search through logs for service installation events with chainsaw
```
.\chainsaw.exe search -e "7045" C:\Users\*\Desktop\C\Target\C\Windows\System32\winevt\logs\
```
![](Pasted%20image%2020260609165849.png)





Task 12

When did the attacker establish their final persistence by installing the WindowsPooler service?
2024-09-19 17:50:17

search through logs for service installation events with chainsaw
```
.\chainsaw.exe search -e "7045" C:\Users\*\Desktop\C\Target\C\Windows\System32\winevt\logs\
```
![](Pasted%20image%2020260609165424.png)