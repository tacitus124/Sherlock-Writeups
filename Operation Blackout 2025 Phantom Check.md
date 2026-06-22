

-----

#### Sherlock Scenario

Talion suspects that the threat actor carried out anti-virtualization checks to avoid detection in sandboxed environments. Your task is to analyze the event logs and identify the specific techniques used for virtualization detection. Byte Doctor requires evidence of the registry checks or processes the attacker executed to perform these checks.



We are given two logs:
Powershell log and Powershell operational


For Powershell operational, filter by event id 4104 to filter for executed code 





Task 1

Which WMI class did the attacker use to retrieve model and manufacturer information for virtualization detection?: Win32_ComputerSystem
<img width="2830" height="1774" alt="image" src="https://github.com/user-attachments/assets/e6fc2b2e-cb9e-4ebb-92da-d32c7b7cdff7" />



Task 2

Which WMI query did the attacker execute to retrieve the current temperature value of the machine?: SELECT * FROM MSAcpi_ThermalZoneTemperature
<img width="2830" height="1781" alt="image" src="https://github.com/user-attachments/assets/15932e13-4c6f-4747-b724-5187c334df4e" />



Task 3

The attacker loaded a PowerShell script to detect virtualization. What is the function name of the script?: Check-VM
<img width="2842" height="1773" alt="image" src="https://github.com/user-attachments/assets/05129b05-e7a2-42a1-9e18-2414f0759a0f" />



Task 4

Which registry key did the above script query to retrieve service details for virtualization detection?:
HKLM:\SYSTEM\ControlSet001\Services
<img width="2796" height="1737" alt="image" src="https://github.com/user-attachments/assets/ce0bc64d-e625-44a0-98e9-a9549770a22f" />



Task 5

The VM detection script can also identify VirtualBox. Which processes is it comparing to determine if the system is running VirtualBox?: vboxservice.exe, vboxtray.exe
<img width="2814" height="1782" alt="image" src="https://github.com/user-attachments/assets/3247c7be-f7e7-446e-90ce-1b4cdf6611d2" />


Task 6

The VM detection script prints any detection with the prefix 'This is a'. Which two virtualization platforms did the script detect?: Hyper-V, Vmware
<img width="2794" height="1767" alt="image" src="https://github.com/user-attachments/assets/10f4f6a1-f530-4b71-acdb-191781ad0d2a" />

