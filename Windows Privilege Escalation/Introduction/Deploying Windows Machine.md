
---
## ✅ **Remote Access to TryHackMe Windows VM: Full Guide (with Commands)**

---

### 🔌 **1. Connect to TryHackMe VPN**

**Only if you're using your own Kali VM or Linux box.**  
(If using the TryHackMe browser Kali, skip this step — VPN is already connected.)

```bash
sudo openvpn your-file.ovpn
```

- Replace `your-file.ovpn` with the one you downloaded from TryHackMe.
    
- Wait for “Initialization Sequence Completed” before continuing.
    

---

### 🌐 **2. Verify RDP Port (3389) is Open**

Use `nmap` to check if the target VM is accepting RDP connections:

```bash
nmap -p 3389 MACHINE_IP
```

- Replace `MACHINE_IP` with the IP of your assigned Windows machine.
    

---

### 🧪 **3. Connect via RDP — Choose one of the two methods below**

---

## 💻 **Method A: Using `xfreerdp` (Command-line)**

```bash
xfreerdp /u:user /p:password321 /cert:ignore /v:MACHINE_IP
```

### 🔍 Flag Breakdown:

|Flag|Description|
|---|---|
|`/u:user`|Login with username `user`|
|`/p:password321`|Password is `password321`|
|`/cert:ignore`|Ignore self-signed certificate warnings|
|`/v:MACHINE_IP`|The VM’s IP address|

> 🪟 This opens a graphical RDP window showing the Windows desktop.

---

## 🖥️ **Method B: Using Remmina (GUI-based)**

### 📥 **Install Remmina** (if not installed):

```bash
sudo apt update && sudo apt install remmina -y
```

### 🧭 **Steps to Use Remmina:**

1. Run Remmina:
    
    ```bash
    remmina
    ```
    
2. Click the **"+" (New Connection)** icon.
    
3. Fill in:
    
    - **Protocol**: RDP
        
    - **Server**: `MACHINE_IP`
        
    - **Username**: `user`
        
    - **Password**: `password321`
        
    - (Optional settings):
        
        - Resolution: Fit to window
            
        - Color depth: 32-bit
            
        - Security: RDP/Negotiate
            
        - Ignore cert errors: Enable if available
            
4. Save and double-click to connect.
    

> 🪟 This will launch a remote desktop session in a GUI window — ideal for users who prefer not to use terminal commands.

---

### 🛠️ **4. Perform Privilege Escalation Tasks**

- Once inside, follow the lab instructions to run tools like:
    
    - `whoami /priv`
        
    - `winPEAS.exe`
        
    - `PowerUp.ps1`
        
- Escalate privileges by exploiting the given scenario (e.g., service misconfigurations, weak permissions, stored credentials).
    

---

### 🔁 **5. Exit Elevated Shell After Each Task**

- After gaining SYSTEM/Admin access in each task, **log off or close that session**.
    
- **Reconnect again as the `user` account** (repeat steps 3A or 3B) before starting the next lab technique.
    

---
