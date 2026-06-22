

---


#### Sherlock Scenario

Wowza Enterprise (Wowza Inc.) has hired you to investigate an incident involving a workstation used by Otello John, which is believed to have been compromised by a threat actor. The attacker reportedly changed the desktop wallpaper to a questionable image. The IT team has already provided you with initial information, and it is your duty to report back your findings.


---

Task 1

Identify the phishing URL that the user clicked on, resulting in credential harvesting.

```
http://login.wowzalnc.co.th/logon.php
```


Import Chrome history located at "C\Users\otello.j\AppData\Local\Google\Chrome\User Data\Default/History" into DB Browser and go to urls. If we look closely we can see one domain that differentiates from the benign domain. 

Notice the i in wowzainc and then the other domain using l in wowzalnc. This is known as typosquatting
![](Pasted%20image%2020260619142044.png)











Task 2

When did the threat actor gain access to the victim's computer via RDP for the first time?

```
2025-05-27 11:59:57
```



Extract login events from Security folder with chainsaw
```
.\chainsaw.exe search -e "4624" C:\Users\*\Desktop\C\Windows\System32\winevt\logs\Security.evtx > events.txt
```
Then filter for events with LogonType 10 indicating an RDP login

![](Pasted%20image%2020260619151325.png)






Task 3

The threat actor accessed several sensitive files on the victim's work-related folder. What is the full path of the PowerPoint presentation file opened by the attacker?



```
C:\Users\otello.j\Desktop\Working\Proposal to CFO.pptx
```




Import MFT into Timeline Explorer search for "pptx"

![](Pasted%20image%2020260619152014.png)





Task 4

The threat actor discovered a privilege that allows specific volume-level management operations and could be exploited to get full control over the C drive. What is this special privilege?

```
SeManageVolumePrivilege
```


We can find this download in the Chrome downloads. This exploits the _SeManageVolumePrivilege_ privilege
![](Pasted%20image%2020260619153140.png)











Task 5

What is the name of the executable downloaded by the threat actor to exploit previously found privilege?

```
SeManageVolumeExploit.exe
```



![](Pasted%20image%2020260619153324.png)











Task 6

What is the full URL from where the threat actor tried to download a DLL file?


```
http://freehackingtool.com/tools/PrintConfig.dll
```



Still looking in the Chrome browser downloads, we find this DLL
![](Pasted%20image%2020260619154503.png)







Task 7


The malicious DLL file was not successfully downloaded, as the download was interrupted by the safe browsing safety feature. Research Browser forensics and find the description of the interrupt reason that caused the download to be disrupted.


```
The user shut down the browser
```


In the same Downloads tab as in Q6 we can see a tab called "interrupt_reason" with a value of "41" for the DLL file. Looking it up means user shut down the browser


![](Pasted%20image%2020260619163713.png)


https://dfir.blog/chrome-values-lookup-tables/





Task 8

Since the download was not successful from the browser directly, which LOLBIN did the threat actor use to download this file successfully?

```
certutil.exe
```





Searching for the earliest sign of the DLL with chainsaw shows this result
```
.\chainsaw.exe search -e "PrintConfig.dll" 'C:\Users\*\Desktop\C' --skip-errors | findstr "SystemTime"
```
![](Pasted%20image%2020260619165834.png)

Tools like certutil,powershell etc drop downloaded files at this location "\C\Users\otello.j\AppData\LocalLow\Microsoft\CryptnetUrlCache\MetaData"

We can find a file downloaded at the same time as the earliest sign of the DLL, ignore my time difference
![](Pasted%20image%2020260619170219.png)

If we run strings on it, we see its URL
![](Pasted%20image%2020260619170249.png)










Task 9

When was the malicious DLL file successfully downloaded using this LOLBIN?

```
2025-05-28 12:45:37
```




We can find earliest creation date in USN journal output. Use usnparser to extract the data and import it into Timeline Explorer and search for the DLL
![](Pasted%20image%2020260619174148.png)










Task 10

To gain System privileges, the threat actor replaced an existing DLL with the same name. What is the original path of the legitimate DLL?


```
C:\Windows\System32\spool\drivers\x64\3\Printconfig.dll
```



regsvr32.exe trying to register the dll
![](Pasted%20image%2020260619175310.png)


Defender flagged this file as malicious
![](Pasted%20image%2020260619175235.png)









Task 11

The threat actor removed the legitimate DLL before replacing it with the malicious DLL. When was the legit DLL deleted?

```
2025-05-28 12:47:06
```




Check for FILE_DELETE event in usn output
![](Pasted%20image%2020260619175712.png)









Task 12

When was the malicious DLL detected as malware?


```
2025-05-28 15:19:35
```



Search for all events related to PrintConfig.dll using chainsaw and look for Defender logs
```
.\chainsaw.exe search -e "PrintConfig.dll" 'C:\Users\*\Desktop\C' --skip-errors
```
![](Pasted%20image%2020260619175820.png)








Task 13

The threat actor initiated a Windows component to load this DLL. What is the CLSID of this component?


```
{854A20FB-2D44-457D-992F-EF13785D2B51}
```




The exploit uses Powershell, therefore we can go to the users Powershell history file and see the command
C\Users\otello.j\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history
![](Pasted%20image%2020260619181457.png)







Task 14

What is the name of the service/object associated with this CLSID?


```
PrintNotify
```



We can find the service name in the same chainsaw output from earlier
![](Pasted%20image%2020260619181748.png)









Task 15

What is the SHA1 hash of the malicious DLL file?T


```
916564984e38f8bb91921cd4e40b64156a72142b
```


Defender produces hash of flagged files, you can find them in the MPlogs located at \C\ProgramData\Microsoft\Windows Defender\Support\MPLog*
![](Pasted%20image%2020260619183019.png)










Task 16

A Second DLL file was downloaded from the same malicious domain. Where was it downloaded on the filesystem?

```
C:\windows\system32\wbem\tzres.dll
```


Going back to the certutil downloaded files, we see something was downloaded around the same time as the first dll
![](Pasted%20image%2020260619184052.png)

If we check its contents we see this DLL downloaded
![](Pasted%20image%2020260619184155.png)


Then we search all events related to the DLL with chainsaw to find its location
```
.\chainsaw.exe search -e "tzres.dll" C:\Users\*\Desktop\C\ --skip-errors
```
![](Pasted%20image%2020260619184933.png)







Task 17

When was this DLL downloaded on the system?

```
2025-05-28 12:54:23
```

Search through MFT output for "tzres.dll"
![](Pasted%20image%2020260619185237.png)












Task 18

The threat actor downloaded a VBScript for command execution to facilitate the DLL execution of the second malicious DLL. What is the full path of this script after it was moved to a new location?


```
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp\a.vbs
```



Going back to the Chrome downloads, we can see a.vbs getting downloaded
![](Pasted%20image%2020260619185813.png)


We can find its location using chainsaw
```
.\chainsaw.exe search -e "a.vbs" C:\Users\*\Desktop\C\ --skip-errors
```
![](Pasted%20image%2020260619185735.png)






Task 19

What is the full command that the script is configured to execute?

```
cmd.exe /c systeminfo
```


![](Pasted%20image%2020260619185953.png)











Task 20

The threat actor configured the VBS script to be hidden from the Windows GUI (File Explorer). When was this attribute set on the file?


```
2025-05-28 12:56:11
```


We can the change in USN journal output
![](Pasted%20image%2020260619190636.png)










Task 21

Which process loads the previously identified DLL with this command? The command executed by the VBS script ultimately facilitates loading and execution of the Second malicious DLL, providing persistence and execution capabilities to the attacker.


```
wmiprvse.exe
```


Research shows wmiprvse is a common process associated with this DLL
![](Pasted%20image%2020260619191455.png)










Task 22

The threat actor downloaded an image file to change the desktop wallpaper. What is the full path of this file?


```
C:\Users\Public\Pictures\gg.bmp
```



Going back to the users Powershell history we find this image
![](Pasted%20image%2020260619191846.png)











Task 23

The threat actor then proceeded to change the desktop wallpaper of the compromised user to the newly downloaded image. Find the time when the wallpaper was altered?


```
2025-05-28 12:59:30
```





We can use chainsaw to search for the command run in Q22 and we will find the Powershell event and time
```
.\chainsaw.exe search -e "HKCU\\control panel\\desktop" C:\Users\*\Desktop\C\ --skip-errors
```
![](Pasted%20image%2020260619192606.png)







Task 24

When did the victim user log in to their workstation after the compromise?



```
2025-05-28 15:04:41
```



Use the same logs you extracted in Q2 and look for local login events related to the user after the wallpaper was changed
![](Pasted%20image%2020260619195027.png)













Task 25

What is the message on the new wallpaper?

```
HACKED BY ANARCHY
```

