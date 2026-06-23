
---


#### Sherlock Scenario

The incident happened around 4:30 PM on Friday, "The Last day of the week" at the Accounts/Marketing department of "Finance XYZ" company. There weren't many people in the department, and the remaining were not paying much attention to their surroundings as they were getting ready to head home.

After the weekend on Monday while reviewing the security cam footage member of an IT team saw "Eddie" a new intern in the Accounts/Marketing department, plugging a USB into an unauthorized computer (containing sensitive financial and marketing documents), interacting with computer and unplugging the USB before heading out. As the incident happened 2 days ago, and not having enough knowledge of what Eddie did the security team use caution while asking around and gathering intel to avoid causing suspicion. The only information they were able to find out was that Eddie had a "Toshiba" USB. You are provided with a partial image of the “unauthorized computer" as well as a list of important documents, to investigate what he did and if he stole something sensitive or not?


---


First, open the ad1 image in FTK Imager and then extract SYSTEM AND SOFTWARE hives

Then import the hives into Registry Explorer


Task 1

Find out the time zone of victim PC. (UTC+xx:xx)

```
UTC+05:00
```


We can find the timezone in TimeZoneInformation
![](Pasted%20image%2020260622182648.png)









Task 2

Employees should be trained not to leave their accounts unlocked. What is the username of the logged in user?

```
MrManj
```


![](Pasted%20image%2020260622182912.png)










Task 3

How many USB storage devices were attached to this host in total?

```
3
```


![](Pasted%20image%2020260622230911.png)












Task 4

What is the attach timestamp for the USB in UTC?


```
2024-02-23 11:37:50
```


We know from Sherlock description we are looking for a Toshiba usb. To find its installation date we can check USBSTOR in SYSTEM hive
![](Pasted%20image%2020260622231152.png)








Task 5

What is the detach timestamp for the USB in UTC?

```
2024-02-23 11:39:12
```


To find its last detach we can check USBSTOR in SYSTEM hive
![](Pasted%20image%2020260622231548.png)











Task 6

Which folder did he copy to the USB?


```
Documents
```


Extract usrclass.dat from
```
C:\Users\MrManj\AppData\Local\Microsoft\Windows\
```
![](Pasted%20image%2020260622234010.png)

Then import it into Shellbags explorer

![](Pasted%20image%2020260622233948.png)





Task 7

There were subfolders in the folder that was copied. What is the name of the first subfolder? (Alphabetically)

```
Business Proposals
```



![](Pasted%20image%2020260622234051.png)









Task 8

Eddie opens some files after copying them to the USB. What is the name of the file with the .xlsx extension Eddie opens?

```
Business Leads.xlsx
```


extract jumplist data from the ad1, this can be found at C:\Users\<username>\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations


Then analyze it with JLEC
```
JLECmd.exe -f "<path-to-Jumplist-file>" --csv "<path-to-save-csv>"
```
![](Pasted%20image%2020260623122654.png)





Task 9

Eddie opens some files after copying them to the USB. What is the name of the file with the .docx extension Eddie opens?


```
Proposal Brnrdr ltd.docx
```



We can find it in the same jumplist data as in Q8
![](Pasted%20image%2020260623122828.png)







Task 10

What was the volume name of the USB?


```
RVT-9J
```


We can find USB name in SOFTWARE\Microsoft\Windows Portable Devices\Devices
![](Pasted%20image%2020260623123945.png)








Task 11

What was the drive letter of the USB?


```
E
```


![](Pasted%20image%2020260623123414.png)









Task 12



I hope we can find some more evidence to tie this all together. What is Eddie's last name?

```
Homer
```



















Task 13

There was an unbranded USB in the USB list, can you identify it's manufacturer’s name?


``` 
Shenzhen SanDiYiXin Electronic Co.,LTD
```


Find the Serial Number in USBSTOR
![](Pasted%20image%2020260623131959.png)

Correlate it to serial number in USB and in the same row you have the vendor id in Key Name
![](Pasted%20image%2020260623132121.png)
in our case "346D"
use this site to locate the vendor
https://the-sz.com/products/usbid/
apply "0x" before the ID and search it up
```
0x346D
```
![](Pasted%20image%2020260623132312.png)