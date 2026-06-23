
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
<img width="2463" height="1296" alt="image" src="https://github.com/user-attachments/assets/7cf12bf6-d3b6-4d1e-aeaa-4cb095d716b9" />









Task 2

Employees should be trained not to leave their accounts unlocked. What is the username of the logged in user?

```
MrManj
```


<img width="806" height="405" alt="image" src="https://github.com/user-attachments/assets/af99e9e0-2cd6-4628-9b65-0267632b781b" />










Task 3

How many USB storage devices were attached to this host in total?

```
3
```


<img width="504" height="245" alt="image" src="https://github.com/user-attachments/assets/0606e80b-b7e5-464a-9855-cae9567f52a3" />













Task 4

What is the attach timestamp for the USB in UTC?


```
2024-02-23 11:37:50
```


We know from Sherlock description we are looking for a Toshiba usb. To find its installation date we can check USBSTOR in SYSTEM hive
<img width="2922" height="347" alt="image" src="https://github.com/user-attachments/assets/eb615155-0121-48b7-bb4f-70343855ae0c" />









Task 5

What is the detach timestamp for the USB in UTC?

```
2024-02-23 11:39:12
```


To find its last detach we can check USBSTOR in SYSTEM hive
<img width="2975" height="254" alt="image" src="https://github.com/user-attachments/assets/23089ee2-2e79-4e47-8557-6dfaaf899c78" />












Task 6

Which folder did he copy to the USB?


```
Documents
```


Extract usrclass.dat from
```
C:\Users\MrManj\AppData\Local\Microsoft\Windows\
```
<img width="1853" height="380" alt="image" src="https://github.com/user-attachments/assets/d7562b55-3751-4185-b03e-f0e75c0a20d6" />


Then import it into Shellbags explorer

<img width="1763" height="550" alt="image" src="https://github.com/user-attachments/assets/10a292d3-ac59-4662-a99f-1793e1ce63b2" />






Task 7

There were subfolders in the folder that was copied. What is the name of the first subfolder? (Alphabetically)

```
Business Proposals
```



<img width="1510" height="922" alt="image" src="https://github.com/user-attachments/assets/58cf4b3a-8374-40b6-90b9-ca28d6f8277a" />










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
<img width="1071" height="889" alt="image" src="https://github.com/user-attachments/assets/985ae46c-90e1-4497-9682-7ab8bc2e90e2" />






Task 9

Eddie opens some files after copying them to the USB. What is the name of the file with the .docx extension Eddie opens?


```
Proposal Brnrdr ltd.docx
```



We can find it in the same jumplist data as in Q8
<img width="1055" height="330" alt="image" src="https://github.com/user-attachments/assets/9914e891-0566-4bef-b20a-e296b60c9b22" />








Task 10

What was the volume name of the USB?


```
RVT-9J
```


We can find USB name in SOFTWARE\Microsoft\Windows Portable Devices\Devices
<img width="1479" height="1431" alt="image" src="https://github.com/user-attachments/assets/376fe324-b67a-47ab-b049-39081aa602b4" />









Task 11

What was the drive letter of the USB?


```
E
```


<img width="961" height="330" alt="image" src="https://github.com/user-attachments/assets/5cd8a0da-b44c-4389-bb6d-10d54567455a" />










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
<img width="1576" height="287" alt="image" src="https://github.com/user-attachments/assets/325f4ff2-e16f-44e1-a670-0d21652874c9" />


Correlate it to serial number in USB and in the same row you have the vendor id in Key Name
<img width="2312" height="381" alt="image" src="https://github.com/user-attachments/assets/9368ac56-1879-4fca-a116-a95c75f0f89e" />

in our case "346D"
use this site to locate the vendor
https://the-sz.com/products/usbid/
apply "0x" before the ID and search it up
```
0x346D
```
<img width="1951" height="1330" alt="image" src="https://github.com/user-attachments/assets/9175ac8a-36f7-4b46-bfc6-a42d199428ad" />
