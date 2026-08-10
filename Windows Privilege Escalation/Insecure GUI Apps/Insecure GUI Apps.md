
---

### 🔑 **Step 1: Start RDP Session**

First, log in to the target machine using the **user** account credentials via RDP:

```bash
rdesktop -u user -p password321 MACHINE_IP
```

- Replace `user` with the user account, `password321` with the password, and `MACHINE_IP` with the target system’s IP address.
    

---

### 🎨 **Step 2: Launch AdminPaint**

- Double-click the **AdminPaint** shortcut on the Desktop. This is likely a version of Paint that's configured to run with **admin privileges**.
    

---

### 🔍 **Step 3: Verify Paint Runs with Admin Privileges**

Once Paint is open, open a **command prompt** inside Paint’s environment and run:

```cmd
tasklist /V | findstr mspaint.exe
```

- **What it does:** This checks if **Paint** (mspaint.exe) is running with **admin privileges** (SYSTEM or elevated).
    
- **What you should see:** A line with `mspaint.exe` running, possibly showing elevated privileges.
    

---

### 🖼️ **Step 4: Use Paint to Launch a Command Prompt**

- Inside Paint, click on **File**, then **Open**.
    
- In the **File Open dialog**, paste this into the file path:
    
    ```
    file://c:/windows/system32/cmd.exe
    ```
    
- **What it does:** It opens **cmd.exe** using **Paint**, effectively running it with the same elevated privileges as Paint.
    
- **Why it works:** Since Paint is running with **admin privileges**, the command prompt you launch will inherit those privileges.
    

---
