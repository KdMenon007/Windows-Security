
---
### 🔎 **1. [winPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS)**

A powerful enumeration tool that scans a Windows system for common privilege escalation vectors like:

- Service misconfigurations
    
- Registry issues
    
- Unquoted service paths
    
- Stored credentials
    
- Token privileges, etc.
    

📌 **Use case:** Automated, comprehensive system audit for privilege escalation opportunities. 
**Syntax:**

```powershell
.\winPEASx64.exe
```

Or, if running from PowerShell:

```powershell
iwr -uri https://github.com/peass-ng/PEASS-ng/releases/latest/download/winPEASx64.exe -Outfile winPEASx64.exe
.\winPEASx64.exe
```

---

### 📋 **2. [Windows Privilege Escalation Checklist](https://book.hacktricks.xyz/windows/checklist-windows-privilege-escalation)**

A **step-by-step manual guide** from HackTricks that outlines what to check during a Windows privesc engagement.

📌 **Use case:** Helps organize and follow through manual enumeration systematically.

---

### 🧪 **3. [Sherlock](https://github.com/rasta-mouse/Sherlock)**

A PowerShell script that detects missing patches and vulnerable Windows binaries (e.g., MS16-032) for **known local privilege escalation exploits**.

📌 **Use case:** Identifies unpatched vulnerabilities that can be exploited locally.
**Syntax:**

```powershell
Import-Module .\Sherlock.ps1
Find-AllVulns
```

---

### 🧠 **4. [Watson](https://github.com/rasta-mouse/Watson)**

A C# tool that performs similar checks to Sherlock, but also:

- Checks Windows build version
    
- Detects vulnerable settings
    
- Lists suitable LPE exploits for that system.
    

📌 **Use case:** Finding privilege escalation paths based on OS version and system configs.
**Syntax:**

```powershell
.\Watson.exe
```

---

### ⚡ **5. [PowerUp](https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc)**

A PowerShell-based scanner that checks:

- Weak service permissions
    
- DLL hijacking opportunities
    
- Unquoted service paths
    
- Vulnerable registry keys
    

📌 **Use case:** Detects local misconfigurations that can be exploited for privilege escalation.
**Syntax:**

```powershell
iwr -uri https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1 -Outfile PowerUp.ps1
. .\PowerUp.ps1
Invoke-AllChecks
```

---

### 🕵️‍♂️ **6. [JAWS](https://github.com/411Hall/JAWS)**

Just Another Windows (Enum) Script – written in PowerShell, JAWS checks for:

- Antivirus status
    
- Interesting files
    
- Registry configs
    
- User privileges and policies
    

📌 **Use case:** Quick enumeration of post-exploitation data with privesc potential.
**Syntax:**

```powershell
powershell -ExecutionPolicy Bypass -File .\JAWS.ps1
```

---

### 💥 **7. [Windows Exploit Suggester](https://github.com/AonCyberLabs/Windows-Exploit-Suggester)**

Python tool that parses the system's patch level (via `systeminfo`) and checks against a local database of known exploits.

📌 **Use case:** Suggests missing patches and associated LPE exploits.
**Syntax:**

```bash
python windows-exploit-suggester.py --update
systeminfo > systeminfo.txt
python windows-exploit-suggester.py --database 2025-04-04-mssb.xls --systeminfo systeminfo.txt
```

---

### 🛠️ **8. [Metasploit Local Exploit Suggester](https://blog.rapid7.com/2015/08/11/metasploit-local-exploit-suggester-do-less-get-more/)**

A **Metasploit module** that analyzes a session and suggests known exploits based on system info.

📌 **Use case:** Automated in-session LPE enumeration within Metasploit.
**Syntax (within MSF):**

```bash
use post/multi/recon/local_exploit_suggester
set SESSION <session_id>
run
```

---

### 🔐 **9. [Seatbelt](https://github.com/GhostPack/Seatbelt)**

A C# post-exploitation enumeration tool (like WinPEAS but modular). Pulls:

- Token privileges
    
- Interesting files
    
- UAC status
    
- Credential manager secrets
    
- Browser data
    

📌 **Use case:** Stealthy, modular collection of juicy privilege escalation indicators.
**Syntax:**

```powershell
.\Seatbelt.exe all
```

Or for specific checks:

```powershell
.\Seatbelt.exe Browser
.\Seatbelt.exe Tokens
```

---

### 🧵 **10. [SharpUp](https://github.com/GhostPack/SharpUp)**

Also part of GhostPack. Checks for:

- UAC bypass options
    
- Insecure file/folder permissions
    
- Registry vulnerabilities
    
- Token privileges
    

📌 **Use case:** .NET-based enumeration tool for identifying local privilege escalation vectors.
**Syntax:**

```powershell
.\SharpUp.exe --help
.\SharpUp.exe --all
```

---
