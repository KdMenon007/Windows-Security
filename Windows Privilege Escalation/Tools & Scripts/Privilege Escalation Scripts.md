
---

### **1. winPEASany.exe**

- **Description**: `winPEAS` is a popular tool that helps identify privilege escalation vectors on Windows systems. It scans for insecure configurations, misconfigurations, vulnerabilities, and weaknesses that can be leveraged for privilege escalation.
    
- **Key Features**:
    
    - Checks for insecure service permissions.
        
    - Looks for writable files and directories that could be used for code execution.
        
    - Checks for unquoted service paths, weak file permissions, and registry misconfigurations.
        
- **Download Link**: [winPEAS GitHub Repository](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS)
    

---

### **2. Seatbelt.exe**

- **Description**: `Seatbelt` is a C# tool that performs security enumeration and checks for Windows privilege escalation opportunities. It provides a comprehensive set of checks for system information, user information, services, registry keys, and file permissions.
    
- **Key Features**:
    
    - Scans for writable files and services.
        
    - Identifies unquoted service paths and weak registry keys.
        
    - Detects local admin group membership.
        
- **Download Link**: [Seatbelt GitHub Repository](https://github.com/GhostPack/Seatbelt)
    

---

### **3. PowerUp.ps1**

- **Description**: `PowerUp` is a PowerShell script designed to identify privilege escalation opportunities on Windows systems. It is part of the **PowerSploit** collection of tools and checks for common misconfigurations that may be leveraged for privilege escalation.
    
- **Key Features**:
    
    - Checks for unquoted service paths.
        
    - Detects weak registry permissions.
        
    - Identifies writable files, folders, and executables.
        
    - Checks for vulnerable services, scheduled tasks, and weak access control entries (ACEs).
        
- **Download Link**: [PowerUp GitHub Repository](https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc)
    

---

### **4. SharpUp.exe**

- **Description**: `SharpUp` is a tool written in C# that helps identify potential privilege escalation vectors on Windows machines. It checks for common misconfigurations and vulnerabilities related to services, file permissions, registry permissions, and user permissions.
    
- **Key Features**:
    
    - Scans for unquoted service paths, weak registry permissions, and unsafe configurations.
        
    - Identifies writable files and directories.
        
    - Performs detailed checks for common Windows privilege escalation techniques.
        
- **Download Link**: [SharpUp GitHub Repository](https://github.com/itm4n/SharpUp)
    

---

### **Additional Tools for Windows Privilege Escalation**

Here are additional tools you might find useful for Windows privilege escalation:

---

### **5. LinPEAS.exe**

- **Description**: The Windows version of `LinPEAS` performs a similar function as `winPEAS`. It is part of the PEASS-ng project and looks for privilege escalation vectors on Linux, but there's a version available for Windows as well.
    
- **Download Link**: [LinPEAS GitHub Repository](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS)
    

---

### **6. WinEnum.exe**

- **Description**: `WinEnum` is a tool that enumerates Windows system information, identifying potential privilege escalation opportunities. It checks for unquoted service paths, weak file permissions, and other misconfigurations.
    
- **Download Link**: [WinEnum GitHub Repository](https://github.com/3gstudent/WinEnum)
    

---

### **7. Sherlock.ps1**

- **Description**: `Sherlock` is a PowerShell script designed to identify potential privilege escalation vulnerabilities in Windows environments. It checks for unquoted service paths, file permissions, registry permissions, and more.
    
- **Download Link**: [Sherlock GitHub Repository](https://github.com/rasta-mouse/Sherlock)
    

---

### **8. PoshC2 (PowerShell C2)**

- **Description**: `PoshC2` is a PowerShell-based Command and Control framework that also includes functionality for post-exploitation and privilege escalation. It has several modules to escalate privileges and execute commands on remote systems.
    
- **Download Link**: [PoshC2 GitHub Repository](https://github.com/nettitude/PoshC2)
    

---

### **9. Mimikatz**

- **Description**: `Mimikatz` is one of the most popular post-exploitation tools used to extract credentials from Windows memory, including plaintext passwords, NTLM hashes, Kerberos tickets, and more. It is also useful for interacting with Windows authentication protocols.
    
- **Download Link**: [Mimikatz GitHub Repository](https://github.com/gentilkiwi/mimikatz)
    

---

### **10. Rubeus**

- **Description**: `Rubeus` is a powerful C# tool that interacts with Kerberos tickets in Windows environments. It can be used for ticket extraction, ticket renewal, and ticket forging, among other things.
    
- **Download Link**: [Rubeus GitHub Repository](https://github.com/GhostPack/Rubeus)
    

---

### **11. Impacket**

- **Description**: `Impacket` is a collection of Python classes for working with network protocols. It is useful for interacting with SMB, MSRPC, LDAP, and other Windows services and can be leveraged for lateral movement, post-exploitation, and privilege escalation.
    
- **Download Link**: [Impacket GitHub Repository](https://github.com/SecureAuthCorp/impacket)
    

---

### **12. PTH-Winexe**

- **Description**: `PTH-Winexe` allows you to use Pass-the-Hash (PTH) attacks to authenticate on a Windows system without needing to crack the password hash.
    
- **Download Link**: [PTH-Winexe GitHub Repository](https://github.com/byt3bl33d3r/PTH-Winexe)
    

---

### **13. Empire**

- **Description**: `Empire` is a PowerShell and Python-based post-exploitation tool. It has many built-in modules that assist with privilege escalation and lateral movement.
    
- **Download Link**: [Empire GitHub Repository](https://github.com/EmpireProject/Empire)
    

---

### **14. Evil-WinRM**

- **Description**: `Evil-WinRM` is a PowerShell-based tool used for remote management and post-exploitation on Windows systems via WinRM (Windows Remote Management).
    
- **Download Link**: [Evil-WinRM GitHub Repository](https://github.com/Hackplayers/evil-winrm)
    

---

### **15. PsExec**

- **Description**: `PsExec` is a command-line tool that allows you to execute processes remotely on Windows machines. It can be useful for executing commands under different user accounts, such as SYSTEM or Local Service.
    
- **Download Link**: [PsExec GitHub Repository](https://github.com/microsoft/psps)
    

---

### **16. ROPgadget**

- **Description**: `ROPgadget` is a tool for finding Return-Oriented Programming (ROP) gadgets. It can be useful for certain exploit development, which can help you escalate privileges in some scenarios.
    
- **Download Link**: [ROPgadget GitHub Repository](https://github.com/JonathanSalwan/ROPgadget)
    

---

