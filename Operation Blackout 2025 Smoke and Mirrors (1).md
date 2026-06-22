
---
# Operation Blackout 2025: Smoke and Mirrors — Writeup

## Scenario

I investigated a Windows host where several expected security logs and Microsoft Defender alerts appeared to be missing. The goal was to identify how the attacker tampered with Windows security controls to avoid detection.

The provided evidence consisted of three event log sources: PowerShell logs, PowerShell Operational logs, and Sysmon Operational logs.

---

## Initial Defender Tampering

The first clear sign of defense evasion occurred at:

```text
2025-04-10T06:31:35.2182888Z
```

The attacker executed:

```powershell
Set-MpPreference -DisableIOAVProtection $true -DisableEmailScanning $true -DisableBlockAtFirstSeen $true
```

This command disables several Microsoft Defender protections, including scanning of downloaded files, email scanning, and block-at-first-seen protection. This was the first PowerShell command found that directly weakened Defender.

<img width="2818" height="1779" alt="image" src="https://github.com/user-attachments/assets/1551fff3-e989-4b35-9998-551ad87e331c" />


---

## LSA Protection Disabled

At:

```text
2025-04-10T06:28:47.4471034Z
```

the attacker modified the LSA protection registry setting:

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Control\LSA /v RunAsPPL /t REG_DWORD /d 0 /f
```

This disables LSA protection by setting `RunAsPPL` to `0`. LSA protection helps protect credential-related processes, so disabling it is a strong sign of credential-access preparation.

The registry key path was:

```text
HKLM\SYSTEM\CurrentControlSet\Control\LSA
```

<img width="2820" height="1756" alt="image" src="https://github.com/user-attachments/assets/451824d5-2c81-42b0-b4fe-daf3418a4f4f" />


---

## Defender Definitions Removed

At:

```text
2025-04-10T06:29:28.4792162Z
```

the attacker used Microsoft Defender’s command-line utility:

```powershell
C:\Program Files\Windows Defender\MpCmdRun.exe -RemoveDefinitions -All
```

This removes Defender definitions, reducing Defender’s ability to detect known malware. The same activity was also seen through `cmd.exe /c`, showing that the attacker intentionally used Defender’s own tool to weaken protection.

<img width="2810" height="1742" alt="image" src="https://github.com/user-attachments/assets/c165bd44-052a-4f9f-b485-8bd36b25cbef" />


---

## AMSI Bypass Loaded

At:

```text
2025-04-10T06:37:49Z
```

the attacker staged and executed a PowerShell function named:

```powershell
Disable-Protection
```

The script embedded C# code inside PowerShell using `Add-Type`. The C# code imported Windows API functions from `kernel32.dll`, including:

```text
GetProcAddress
GetModuleHandle
VirtualProtect
```

The purpose of the script was to locate and patch:

```text
AmsiScanBuffer
```

inside:

```text
amsi.dll
```

The attacker also obfuscated suspicious strings by splitting them with `+`, for example:

```powershell
"a" + "m" + "s" + "i" + ".dll"
```

This reconstructs `amsi.dll` at runtime and avoids simple string-based detections. The same method was used for `AmsiScanBuffer`.

The key patched function was:

```text
AmsiScanBuffer
```

<img width="2822" height="1451" alt="image" src="https://github.com/user-attachments/assets/7cabc020-599f-4c2a-a0c0-7c3f3310de50" />



---

## Runtime C# Compilation Evidence

Sysmon showed that PowerShell’s `Add-Type` caused the .NET C# compiler to run:

```text
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\csc.exe
```

It created a temporary DLL:

```text
C:\Users\User\AppData\Local\Temp\10atty35\10atty35.dll
```

This confirms that the embedded C# AMSI bypass was compiled during execution. The likely chain was:

```text
PowerShell Add-Type
→ csc.exe runs
→ temporary DLL created
→ PowerShell loads DLL
→ C# calls kernel32.dll
→ AmsiScanBuffer is patched
```


<img width="3140" height="1777" alt="image" src="https://github.com/user-attachments/assets/a43088b9-1e7d-4e2e-ae96-f64224437652" />


---

## Additional Defender Disable Command

At:

```text
2025-04-10T06:37:05.6297757Z
```

the attacker ran a broader Defender disable command:

```powershell
Set-MpPreference -DisableRealtimeMonitoring $true -DisableScriptScanning $true -DisableBehaviorMonitoring $true -DisableIOAVProtection $true -DisableIntrusionPreventionSystem $true
```

This disabled multiple Defender protections, including real-time monitoring, script scanning, behavior monitoring, IOAV protection, and intrusion prevention.

<img width="2828" height="1726" alt="image" src="https://github.com/user-attachments/assets/01019c25-99f0-4e45-bebf-8e64893733cf" />


---

## Safe Mode Persistence / Reboot Preparation

At:

```text
2025-04-10T06:38:35.4020795Z
```

the attacker ran:

```powershell
bcdedit /set safeboot network
```

This configures the machine to boot into Safe Mode with networking. This can be used to weaken security controls and make further tampering easier after reboot.

The command used to restart the machine in Safe Mode was:

```text
bcdedit.exe /set safeboot network
```
<img width="2831" height="1754" alt="image" src="https://github.com/user-attachments/assets/43ffc815-2e83-4045-b998-53891b7879e7" />



---

## PowerShell History Disabled

At:

```text
2025-04-10T06:38:43.5490019Z
```

the attacker disabled PowerShell command history logging with:

```powershell
Set-PSReadlineOption -HistorySaveStyle SaveNothing
```

This prevents commands from being saved in the PowerShell history file, making later investigation harder.

<img width="2815" height="1739" alt="image" src="https://github.com/user-attachments/assets/93dcdb6a-3d3e-4afb-83ba-163afa55d57a" />


---

## Timeline Summary

|Time UTC|Activity|
|---|---|
|06:28:47|Disabled LSA protection by modifying `RunAsPPL`|
|06:29:28|Removed Defender definitions with `MpCmdRun.exe -RemoveDefinitions -All`|
|06:31:35|Disabled Defender IOAV, email scanning, and block-at-first-seen|
|06:37:05|Disabled real-time monitoring, script scanning, behavior monitoring, IOAV, and IPS|
|06:37:49|Staged and executed AMSI bypass function|
|06:37:50|`csc.exe` compiled temporary DLL from embedded C#|
|06:38:35|Configured system to boot into Safe Mode with networking|
|06:38:43|Disabled PowerShell command history logging|

---

## Answers

**Task 1:**  
The attacker disabled LSA protection by modifying this registry key:

```text
HKLM\SYSTEM\CurrentControlSet\Control\LSA
```

**Task 2:**  
The first PowerShell command used to disable Windows Defender was:

```powershell
Set-MpPreference -DisableIOAVProtection $true -DisableEmailScanning $true -DisableBlockAtFirstSeen $true
```

**Task 3:**  
The AMSI function patched by the script was:

```text
AmsiScanBuffer
```

**Task 4:**  
The command used to restart the machine in Safe Mode was:

```powershell
bcdedit.exe /set safeboot network
```

**Task 5:**  
The command used to disable PowerShell command history logging was:

```powershell
Set-PSReadlineOption -HistorySaveStyle SaveNothing
```

---

## Conclusion

The attacker performed a clear defense evasion sequence. They weakened Microsoft Defender, removed Defender definitions, disabled LSA protection, patched AMSI in memory, configured Safe Mode boot, and disabled PowerShell history logging.

The strongest evidence was the AMSI bypass chain: PowerShell used `Add-Type` to compile embedded C# code with `csc.exe`, created a temporary DLL, and attempted to patch `AmsiScanBuffer`. Combined with the later Defender tampering commands, this shows the attacker was actively trying to blind security controls and reduce forensic visibility.
