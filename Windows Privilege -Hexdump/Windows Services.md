
---
### **1. Windows Services**

- **Windows Services** are specialized processes that run in the background, often without user interaction, and continue running even after the user logs off.
    
- They can be configured and managed using the **Services Control Manager** (services.msc).
    
- Different types of services:
    
    - **Local Services**: Run with limited privileges.
        
    - **Network Services**: Typically used for network-related tasks.
        
    - **System Services**: Essential services for the system's core functions.
        
    - **Third-party Application Services**: Services related to installed software.
        

Windows Services present a **significant attack surface** because they can often run with elevated privileges or in the background without user intervention.

---

### **2. Manage Services**

Here are some useful PowerShell and command line commands to interact with Windows services:

- **Show all current services**:
    
    ```powershell
    Get-Service
    ```
    
- **Display specific properties for each service**:
    
    ```powershell
    Get-Service | Select-Object Displayname,Status,ServiceName,Can*
    ```
    
- **Get the binary path for each running service**:
    
    ```powershell
    Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}
    ```
    
- **Get a list of all service names**:
    
    ```powershell
    sc.exe query
    sc.exe query | select-string service_name
    ```
    
- **Stop a service**:
    
    ```powershell
    sc.exe stop <SERVICE>
    ```
    
- **Start a service**:
    
    ```powershell
    sc.exe start <SERVICE>
    ```
    
- **Check the configuration of a service**:
    
    ```powershell
    sc.exe qc <SERVICE>
    ```
    
- **Change service configuration (e.g., malicious binary path)**:
    
    ```powershell
    sc.exe config <SERVICE> binPath="C:\path\to\malicious.exe"
    ```
    
- **Check service permissions**:
    
    ```powershell
    sc.exe sdshow <SERVICE>
    ```
    
- **Convert SDDL string to a PowerShell object**:
    
    ```powershell
    ConvertFrom-SddlString -Sddl <SDDL>
    ```
    
- **Change permissions of a service**:
    
    ```powershell
    sc.exe sdset <SERVICE> <SDDL>
    ```
    
- **Get executable paths for all processes**:
    
    ```powershell
    wmic process list full | select-string 'executablepath=C:'
    wmic process list full | select-string 'executablepath=C:' | select-  string -notmatch 'system32|syswow'
    ```
    

---
### **3. Add New Service**

To create a new service, you need an executable that implements the **Windows Service API**. You can use `sc.exe` to create and manage services.

#### **Creating a Simple Service**:

- Write a simple service code (example: `simpleService.c`).
  
    ```bash
  #include <windows.h>
#include <stdio.h>

#define SERVICE_NAME L"simpleService"

SERVICE_STATUS ServiceStatus;
SERVICE_STATUS_HANDLE ServiceStatusHandle;
char TempFile[MAX_PATH] = {0};

void WriteToLog(char *s) {
    static int pathInitialized = 0;
    if (!pathInitialized) {
        GetTempPathA(sizeof TempFile, TempFile);
        strcat(TempFile, "TempLogger.log");
        pathInitialized = 1;
    }
    FILE *log = fopen(TempFile, "a+");
    if (log == NULL) { return; }
    fprintf(log, "%s\n", s);
    fclose(log);
    
    OutputDebugStringA(s); // Debugging support
}

void ServiceControlHandler(DWORD control) {
    switch (control) {
        case SERVICE_CONTROL_PAUSE:
            ServiceStatus.dwCurrentState = SERVICE_PAUSED;
            break;
        case SERVICE_CONTROL_CONTINUE:
            ServiceStatus.dwCurrentState = SERVICE_RUNNING;
            break;
        case SERVICE_CONTROL_STOP:
        case SERVICE_CONTROL_SHUTDOWN:
            ServiceStatus.dwCurrentState = SERVICE_STOPPED;
            SetServiceStatus(ServiceStatusHandle, &ServiceStatus);
            return;  // Exit the handler
    }
    SetServiceStatus(ServiceStatusHandle, &ServiceStatus);
}

void ServiceMain(DWORD argc, LPWSTR *argv) {
    // Setup service
    ServiceStatus.dwServiceType = SERVICE_WIN32_OWN_PROCESS;
    ServiceStatus.dwCurrentState = SERVICE_START_PENDING;
    ServiceStatus.dwControlsAccepted = SERVICE_ACCEPT_PAUSE_CONTINUE | SERVICE_ACCEPT_STOP | SERVICE_ACCEPT_SHUTDOWN;
    ServiceStatus.dwWin32ExitCode = NO_ERROR;
    ServiceStatus.dwServiceSpecificExitCode = 0;
    ServiceStatus.dwCheckPoint = 0;
    ServiceStatus.dwWaitHint = 0;

    ServiceStatusHandle = RegisterServiceCtrlHandler(SERVICE_NAME, ServiceControlHandler);
    if (ServiceStatusHandle == 0) {
        return;
    }

    ServiceStatus.dwCurrentState = SERVICE_RUNNING;
    SetServiceStatus(ServiceStatusHandle, &ServiceStatus);

    // Service main loop
    while (ServiceStatus.dwCurrentState != SERVICE_STOPPED) {
        Sleep(5000);
        if (ServiceStatus.dwCurrentState == SERVICE_RUNNING) {
            WriteToLog("Please Subscribe!!");
        }
    }
}

// ------------------------------------------------------

int wWinMain(HINSTANCE instance, HINSTANCE previnstance, LPWSTR cmdline, int showcmd) {
    SERVICE_TABLE_ENTRY StartTable[] = {{SERVICE_NAME, ServiceMain}, {NULL, NULL}};
    StartServiceCtrlDispatcher(StartTable);
    return 0;
}
   ``` 
- Compile the code using MinGW:
    
    ```bash
    x86_64-w64-mingw32-gcc -mwindows -municode -O2 -s -o simpleService.exe simpleService.c
    ```
    
- After compiling, transfer the `simpleService.exe` to the target machine and create the service:
    
    ```powershell
    sc.exe create SimpleService binPath="C:\path\to\simpleService.exe"
    ```
    
- **Get configuration of the service**:
    
    ```powershell
    sc.exe qc SimpleService
    ```
    
- **Start the service**:
    
    ```powershell
    sc.exe start SimpleService
    ```
    
- **Stop the service**:
    
    ```powershell
    sc.exe stop SimpleService
    ```
    
- **Delete the service**:
    
    ```powershell
    sc.exe delete SimpleService
    ```
    

#### **Running Arbitrary `.bat` or `.exe` as a Service**:

- You can use **NSSM (Non-Sucking Service Manager)** to run arbitrary programs as services.
    
    - Download from [NSSM](https://nssm.cc/)
        
    - Install the service:
        
        ```bash
        nssm.exe install <SERVICE-NAME>
        ```
        

---

### **4. References**

- [Windows Local Privilege Escalation](https://xorond.com/posts/2021/04/windows-local-privilege-escalation/)
    
- [ConvertFrom-SddlString](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/convertfrom-sddlstring)
    
- [Security Descriptor Definition Language (SDDL)](https://learn.microsoft.com/en-us/windows/win32/secauthz/security-descriptor-definition-language)
    
- [StackOverflow: Create Windows Service from Executable](https://stackoverflow.com/questions/3582108/create-windows-service-from-executable)
    
- [NSSM - Non-Sucking Service Manager](https://nssm.cc/)
    
- [Basic Windows Service Template in C](https://gist.github.com/mmmunk/0b0adbccb6b91e778e3a6c6b47908c9c)
    
- [Cygwin and MinGW Options](https://gcc.gnu.org/onlinedocs/gcc/Cygwin-and-MinGW-Options.html)
    
- [Working with SDDL](https://www.advancedinstaller.com/forums/viewtopic.php?t=49990)
    

---
