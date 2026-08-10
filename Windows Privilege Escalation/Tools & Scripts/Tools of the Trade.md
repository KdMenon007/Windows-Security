
---
### 🔍 **Why Use These Tools?**

Manually checking everything on a system takes time. These scripts/tools automate the process and can quickly point you toward known **privilege escalation vectors** — misconfigurations, vulnerable software, weak permissions, etc

---

#### 🧪 **1. WinPEAS (Windows Privilege Escalation Awesome Script)**

- A powerful enumeration tool written in C++ and also available as a `.bat` file.
    
- **What it checks:**
    
    - Services, registry, environment variables
        
    - Installed software
        
    - Weak folder/file permissions
        
    - Scheduled tasks
        
- **How to use:**
    
    ```cmd
    winpeas.exe > output.txt
    ```
    
- **Tip:** Run as a low-privileged user, then check output for red/yellow highlights (common issues).
    

---

#### 📜 **2. PrivescCheck**

- A PowerShell alternative to WinPEAS.
    
- **Less noisy**, no need to upload an EXE.
    
- **How to use:**
    
    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process -Force
    . .\PrivescCheck.ps1
    Invoke-PrivescCheck
    ```
    
- **Use it** when AV blocks binaries or you want to stay stealthy.
    

---

#### 🐍 **3. WES-NG (Windows Exploit Suggester - NG)**

- Runs **on your own machine**, not the target.
    
- Looks at missing patches and known exploits.
    
- **Steps:**
    
    1. On the **target**, run:
        
        ```cmd
        systeminfo > systeminfo.txt
        ```
        
    2. On your **Kali/attacker machine**:
        
        ```bash
        wes.py --update
        wes.py systeminfo.txt
        ```
        
    
    - Helps find known exploits for outdated Windows versions or patches.
        

---

#### 🎯 **4. Metasploit’s Local Exploit Suggester**

- If you already have a **Meterpreter session**, use this to auto-scan for vulnerabilities.
    
- **How to use in Meterpreter:**
    
    ```bash
    use post/multi/recon/local_exploit_suggester
    set SESSION <id>
    run
    ```
    
- Lists matching exploits and privilege escalation paths.
    

---
