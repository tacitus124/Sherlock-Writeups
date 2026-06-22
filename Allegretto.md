
---

#### Sherlock Scenario

We have been conducting surveillance on a suspected drug dealer known as Shadow. Recently, we obtained intelligence regarding his location, which led us to a flat in London. Upon executing a raid, we encountered a violent situation resulting in multiple casualties. During the operation, Shadow attempted to delete critical information from his computer to evade capture. However, we successfully apprehended him before he could cause significant damage. We have since recovered important files from his computer and would like you to analyze this data to determine his activities.



---

Task 1

Which version of QGIS is being used by Shadow?
```
3.34.11
```



Extract MFT data and import into Timeline Explorer and search for QGIS
```
.\MFTECmd.exe -f '..\investigation\Target\C\$MFT' --csv C:\Users\*\Desktop\investigation\
```
<img width="1239" height="337" alt="image" src="https://github.com/user-attachments/assets/58b8bc97-06a5-4bb0-ac18-7b1cd7e7bce9" />






Task 2

What is the Shadow's Darkmail email address?


```
shadow152535@darkmail.htb
```


We can find the email in \Important collections\Thunderbird\Profiles\ka48xic2.default-esr\ImapMail\mail.darkmail.htb\INBOX
<img width="1241" height="380" alt="image" src="https://github.com/user-attachments/assets/30c3715f-7dcd-4ef9-87a8-18a361b7b544" />





Task 3

What is Shadow's Bitcoin address?

```
39tjYdCgsmko1uqW1uiE7sKEZZxhHo2UmK
```

We can find it in the same file as in Q2
<img width="718" height="587" alt="image" src="https://github.com/user-attachments/assets/6e342a20-81f3-41ed-8dab-48588745d879" />







Task 4

What is the onion URL for the SilkRoad where Shadow sells drugs?

```
http://silkroadm5oqzuz3yq7pvwvinvyzeg2wnpnw3t4cyrsggrcgqfy2ptid.onion
```

We can find the relevant information in \Important collections\Thunderbird\Profiles\ka48xic2.default-esr\Mail\Local Folders\Drafts
<img width="1767" height="795" alt="image" src="https://github.com/user-attachments/assets/6f22107a-452c-4290-bfde-1c91a6dfb513" />









Task 5

Based on the cookie's last access, when was the last time Shadow visited the Silk Road website using its onion domain?


```
2024-09-22 18:33:40
```




Import \Important collections\Tor Browser\Browser\TorBrowser\Data\Browser\profile.default\cookies.sqlite into DB Browser and check LastAccessed for the found domain in moz_cookies. 
<img width="1603" height="349" alt="image" src="https://github.com/user-attachments/assets/e209dc4a-0519-4e70-a04f-638293985d50" />

then convert the timestamp into UTC https://www.unixtimestamp.com/


Task 6

What is Shadow's Hotmail email address?

```
shadow152535@hotmail.com
```



do a recursive string search for hotmail in the Thunderbird browser
```
Get-ChildItem -Path \Important collections\Thunderbird -Recurse -File |
>> Select-String -Pattern "hotmail"
```

<img width="1055" height="523" alt="image" src="https://github.com/user-attachments/assets/7f7e8ae1-bf52-4156-aaf8-170a59f9c857" />




Task 7

When was Shadow supposed to meet his mysterious boss?

```
2024-12-21 21:00:00
```


If the user has been using the calendar function within Thunderbird, the relevant information can be found in Thunderbird\Profiles\ka48xic2.default-esr\calendar-data\local.sqlite. 

Here we can see an event named "Meet the boss"
<img width="1598" height="230" alt="image" src="https://github.com/user-attachments/assets/c5649004-100d-4e74-abe5-f873680394f5" />
)
Convert it into UTC with https://www.unixtimestamp.com/




Task 8

When was the "Contacts Due Amounts" spreadsheet created?

```
2024-09-22 15:44:22
```


In the file we found in Q2 an attachment called "contacts_due_amounts.xlsx". Extract the base64 into a file and run exiftool to extract metadata from the file
<img width="906" height="547" alt="image" src="https://github.com/user-attachments/assets/fae5b09a-3453-480b-a548-906ff8dc0770" />






Task 9

Which file did Shadow delete from the encrypted USB drive?

```
E:\Locations\Item_Dropoff tracking.kml
```

Decrypt the shadow.E01 file and mount it, then you can read the file





Task 10

What are Stash Roman's coordinates?

```
53°57'42"N 1°04'54"W
```
