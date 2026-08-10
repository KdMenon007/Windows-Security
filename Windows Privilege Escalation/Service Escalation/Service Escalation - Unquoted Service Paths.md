
---

## 🔍 **Detection Phase (Finding the Vulnerability)**

### **Windows VM**

#### 1. `sc qc unquotedsvc`

- **Purpose**: Displays configuration info about the `unquotedsvc` Windows service.
    
- **Key Output Field**:
    
    ```bash
    BINARY_PATH_NAME : C:\Program Files\Unquoted Path Service\common.exe
    ```
    
- **What's Wrong?**  
    The path **does not use quotation marks** even though it includes spaces.
    

#### ⚠️ Why This Is a Problem:

Windows parses service paths without quotes **from left to right**, assuming each segment is potentially an executable:

- It will first look for: `C:\Program.exe`
    
- Then: `C:\Program Files\Unquoted.exe`
    
- Then finally: `C:\Program Files\Unquoted Path Service\common.exe`
    

If any of the earlier paths exist and are executable, **Windows might run the wrong file**, especially if that file was planted by an attacker. This is a misconfiguration and can be exploited.

---

## 💥 **Exploitation Phase (Gaining Privileges)**

### **Kali VM (Attacker Machine)**

#### 1. Generate a malicious executable:

```bash
msfvenom -p windows/exec CMD='net localgroup administrators user /add' -f exe-service -o common.exe
```

- **`msfvenom`**: A tool from Metasploit for generating payloads.
    
- **`-p windows/exec`**: Payload that executes a Windows command.
    
- **`CMD='net localgroup administrators user /add'`**: This adds `user` to the local administrators group.
    
- **`-f exe-service`**: Generates a Windows service executable format.
    
- **`-o common.exe`**: Output file name.
    

👉 **Result**: A malicious executable (`common.exe`) that, when run, elevates a normal user to admin by modifying the local group.

---

### **Back to Windows VM (Victim Machine)**

#### 1. Place `common.exe` in this directory:

```
C:\Program Files\Unquoted Path Service\
```

- This location is critical because it's part of the service path, and **Windows will search for executables along this path if unquoted**.
    

#### 2. Start the vulnerable service:

```bash
sc start unquotedsvc
```

- When the service starts, Windows tries to execute its binary.
    
- Due to unquoted path, it might run your `common.exe` if:
    
    - You placed it in a part of the path Windows searches.
        
    - You have permission to write in that directory (or a subdirectory like `C:\Program Files\` — this is often simulated in CTF labs).
        

#### 3. Check if the exploit worked:

```bash
net localgroup administrators
```

- This command will list users in the `Administrators` group.
    
- If you see the `user` account there, it means **the payload executed successfully and elevated privileges**.
    

---
