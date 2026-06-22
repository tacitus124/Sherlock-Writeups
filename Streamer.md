

---


#### Sherlock Scenario

Simon Stark is a dev at forela who recently planned to stream some coding sessions with colleagues on which he received appreciation from CEO and other colleagues too. He unknowingly installed a well known streaming software which he found by google search and was one of the top URL being promoted by google ads. Unfortunately things took a wrong turn and a security incident took place. Analyze the triaged artifacts provided to find out what happened exactly.


----
Task 1

What's the original name of the malicious zip file which the user downloaded thinking it was a legit copy of the software?


```
OBS-Studio-28.1.2-Full-Installer-x64.zip
```





Task 2

Simon Stark renamed the downloaded zip file to something else. What's the renamed Name of the file alongside the full path?


```
C:\Users\Simon.stark\Documents\Streaming Software\Obs Streaming Software.zip
```





Task 3

What's the timestamp when the file was renamed?

```
2023-05-05 10:22:23
```






Task 4

What's the Full URL from where the software was downloaded?

```
http://obsproicet.net/download/v28_23/OBS-Studio-28.1.2-Full-Installer-x64.zip
```






Task 5

Dig down deeper and find the IP Address on which the malicious domain was being hosted.

```
13.232.96.186
```






Task 6

Multiple Source ports connected to communicate and download the malicious file from the malicious website. Answer the highest source port number from which the machine connected to the malicious website.

```
50045
```






Task 7

The zip file had a malicious setup file in it which would install a piece of malware and a legit instance of OBS studio software so the user has no idea they got compromised. Find the hash of the setup file.

```
35e3582a9ed14f8a4bb81fd6aca3f0009c78a3a1
```






Task 8

The malicious software automatically installed a backdoor on the victim's workstation. What's the name and filepath of the backdoor?

```
C:\Users\Simon.stark\Miloyeki ker konoyogi\lat takewode libigax weloj jihi quimodo datex dob cijoyi mawiropo.exe
```






Task 9

Find the prefetch hash of the backdoor.

```
D8A6D943
```






Task 10

The backdoor is also used as a persistence mechanism in a stealthy manner to blend in the environment. What's the name used for persistence mechanism to make it look legit?


```
COMSurrogate
```





Task 11

What's the bogus/invalid randomly named domain which the malware tried to reach?


```
oaueeewy3pdy31g3kpqorpc4e.qopgwwytep
```





Task 12

The malware tried exfiltrating the data to a s3 bucket. What's the url of s3 bucket?


```
bbuseruploads.s3.amazonaws.com
```





Task 13

What topic was simon going to stream about in week 1? Find a note or something similar and recover its content to answer the question.

```
FileSystem Security
```






Task 14

What's the name of Security Analyst who triaged the infected workstation?



```
CyberJunkie
```







Task 15


What's the network path from where acquisition tools were run?


```
\\DESKTOP-887GK2L\Users\CyberJunkie\Desktop\Forela-Triage-Workstation\Acquisiton and Triage tools
```