
---


#### Sherlock Scenario

A memory dump from a connected Linux machine reveals covert network connections, fake services, and unusual redirects. Holmes investigates further to uncover how the attacker is manipulating the entire network!



---

We are given a memory capture to analyze

I will be using volatility 3 for analyzing



---

Task 1

What is the Linux kernel version of the provided image:
5.10.0-35-amd64

We can use this command to identify OS banners

```
vol3 -f memdump.mem banners.Banners
```
<img width="2342" height="303" alt="image" src="https://github.com/user-attachments/assets/b94b2dd5-ebad-40cd-8079-2fe0c0f3b660" />



Task 2

The attacker connected over SSH and executed initial reconnaissance commands. What is the PID of the shell they used:
13608

Run vol3 -s ~/vol3symbols -f memdump.mem linux.psaux.PsAux and you will find two SSH connections/shells. One of the SSH connections is benign and is used for forensic extraction

<img width="3418" height="1571" alt="image" src="https://github.com/user-attachments/assets/42ba6860-da95-4da0-acc9-99fd00ed2a4d" />




Task 3

After the initial information gathering, the attacker authenticated as a different user to escalate privileges. Identify and submit that user's credentials: jm:WATSON0

We know from previous questions he switched user to JM

```
remnux@remnux:~/Downloads/Tunnel$ strings -a memdump.mem | grep -Ei "passwd"
```
We can then use this command to search through the dump for any mentions of password, we will get a hit on a hash which we can crack with john

<img width="2451" height="481" alt="image" src="https://github.com/user-attachments/assets/2f9843f6-41c7-47a8-a11f-bbe5f2d7057f" />


<img width="902" height="320" alt="image" src="https://github.com/user-attachments/assets/5aa95b89-e2b7-46ba-bae4-fa0890ca7cb5" />



Task 4

The attacker downloaded and executed code from Pastebin to install a rootkit. What is the full path of the malicious file:

/usr/lib/modules/5.10.0-35-amd64/kernel/lib/Nullincrevenge.ko



Check for hidden modules with 
```
vol3 -s ~/vol3symbols -f memdump.mem linux.malware.hidden_modules.Hidden_modules
```

You will find a suspicious module
<img width="1488" height="367" alt="image" src="https://github.com/user-attachments/assets/5e7ba64d-f830-4eeb-a0cf-ccc0db941cd4" />


take note of the offset adress and dump the file
```
vol3 -s ~/vol3symbols -f memdump.mem \
     -o extracted_modules \
     linux.module_extract.ModuleExtract \
     --base 0xffffc0aa0040
```

Then run strings on it to reveal a rootkit
<img width="596" height="1755" alt="image" src="https://github.com/user-attachments/assets/2e71ebc3-daf5-4edf-88a1-491b87ae59cb" />


To find its location, grep for it in the pagecache files you dumped
<img width="3207" height="535" alt="image" src="https://github.com/user-attachments/assets/a21478c6-3955-4e8d-9cdc-adfb2c8fc56c" />


Task 5

What is the email account of the alleged author of the malicious file:
i-am-the@network.now

We can dump the Nullincrevenge.ko and run strings on it while filtering for @


<img width="1010" height="110" alt="image" src="https://github.com/user-attachments/assets/7c42c6c8-aae6-4b88-b4c3-9d4d576dd5d8" />




Task 6

The next step in the attack involved issuing commands to modify the network settings and installing a new package. What is the name and PID of the package? (package name,PID): dnsmasq:38687

Checking bash history we see he installed dnsmasq

<img width="2040" height="781" alt="image" src="https://github.com/user-attachments/assets/1bd35dcd-1215-42e6-ba5e-aebc76f200ed" />


We can then run pslist and grep for dnsmasq

<img width="1552" height="266" alt="image" src="https://github.com/user-attachments/assets/3b9450ae-07e8-431f-ba13-2fe1a3fe8306" />



Task 7

Clearly, the attacker's goal is to impersonate the entire network. One workstation was already tricked and got its new malicious network configuration. What is the workstation's hostname:

Parallax-5-WS-3

Searching for cogwork strings in the memory dump shows the hostname
<img width="639" height="1414" alt="image" src="https://github.com/user-attachments/assets/ba31eb6c-74be-4af4-9277-0b9585afff78" />



Task 8

After receiving the new malicious network configuration, the user accessed the City of CogWork-1 internal portal from this workstation. What is their username: 

Extract network traffic out of the memory dump with bulk extractor "bulk_extractor -o networktraffic memdump.mem". Load it into wireshark and filter for http traffic

<img width="1812" height="1659" alt="image" src="https://github.com/user-attachments/assets/9e225493-da8e-4a2a-a3ee-ae94d6022ef3" />



Task 9

Finally, the user updated a software to the latest version, as suggested on the internal portal, and fell victim to a supply chain attack. From which Web endpoint was the update downloaded:

/win10/update/CogSoftware/AetherDesk-v74-77.exe


Dump the .json log files from the malicious container and run strings on them
```
strings inode_0x9b339df93420.dmp
```

<img width="2886" height="31" alt="image" src="https://github.com/user-attachments/assets/256a6083-a528-4a02-a980-f80b30513012" />




Task 10

To perform this attack, the attacker redirected the original update domain to a malicious one. Identify the original domain and the final redirect IP address and port. (domain,IP:port) :
updates.cogwork-1.net,13.62.49.86:7477


Search for "jm_proxy" strings in the memory dump and you will eventually up finding the config content as below

<img width="1362" height="688" alt="image" src="https://github.com/user-attachments/assets/78f37314-49b6-4230-9820-414e53f1ad3a" />





