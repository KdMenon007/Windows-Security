
To gain access to another user’s account, one of the easiest ways is by looking for saved passwords on the machine you have access to. Sometimes, users leave passwords in places where they shouldn’t, or software automatically stores them. Here’s where to look:

---

### 2. **Unattended Windows Installations**

When Windows is set up on multiple computers automatically (called **unattended installation**), the system might store sensitive information like administrator credentials in certain files. These files are used during the installation process to avoid user interaction.

- **Where to look**:
    
    - `C:\Unattend.xml`
        
    - `C:\Windows\Panther\Unattend.xml`
        
    - `C:\Windows\Panther\Unattend\Unattend.xml`
        
    - `C:\Windows\system32\sysprep.inf`
        
    - `C:\Windows\system32\sysprep\sysprep.xml`
        
- **What’s inside?**  
    These files might store credentials like the administrator's username and password:
    
    ```xml
    <Credentials>
        <Username>Administrator</Username>
        <Domain>thm.local</Domain>
        <Password>MyPassword123</Password>
    </Credentials>
    ```
    

If you find these files, they could contain the administrator's username and password.

---

### 3. **Powershell History**

Windows stores the commands you run in **Powershell** in a file. This file can contain passwords if you typed them directly into Powershell.

- **Where to look for the history**:  
    The Powershell command history is stored in a file at:
    
    ```
    %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
    ```
    
    - To read this file, use the following command in **cmd.exe** (not Powershell):
        
    
    ```
    type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
    ```
    
    - If you're in Powershell, you must replace `%userprofile%` with `$Env:userprofile`.
        
- **Why this matters?**  
    If someone ran a command with a password (e.g., `Set-Password mypassword123`), you could find it in this history file.
    

---

### 4. **Saved Windows Credentials**

Windows allows users to save credentials (like usernames and passwords) for later use. These credentials are stored on the system and can be listed using a special command.

- **To see saved credentials**:
    
    ```
    cmdkey /list
    ```
    
    - This command shows the saved credentials, but **you can’t see the actual passwords**.
        
- **How to use them**:  
    If you find useful credentials, you can run commands using those credentials. For example:
    
    ```
    runas /savecred /user:admin cmd.exe
    ```
    
    - This will let you run commands as another user without needing to type the password every time.
        

---

### 5. **IIS Configuration (Web Servers)**

**IIS (Internet Information Services)** is a web server used on Windows. Websites running on IIS can have passwords stored for database connections or authentication settings. You can find these passwords in a file called **web.config**.

- **Where to look** for the `web.config` file:
    
    - `C:\inetpub\wwwroot\web.config`
        
    - `C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config`
        
- **How to search for database credentials**:
    
    - Use this command to find connection strings (which may contain passwords):
        
    
    ```
    type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString
    ```
    
- **Why this matters?**  
    These files could contain sensitive information like database usernames and passwords used by the web server.
    

---

### 6. **PuTTY (SSH Client)**

**PuTTY** is a program that helps users connect to other systems via **SSH**. It can store settings for these connections, but it doesn't store passwords directly. However, it can store proxy settings, which might include cleartext (unprotected) credentials.

- **Where to look**:  
    You can search the registry for stored proxy credentials:
    
    ```
    reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s
    ```
    
    - This will show any saved proxy usernames and passwords used in PuTTY.
        
- **Why this matters?**  
    Even though PuTTY doesn’t store SSH passwords directly, it might store **proxy credentials** or other information you can use to access systems.
    

---

### 7. **Retrieve Credentials from Other Software**

Any software that saves passwords, like web browsers, email clients, or FTP clients, can be a place to look for stored credentials. This includes software like:

- **Browsers** (e.g., Chrome, Firefox)
    
- **Email clients** (e.g., Outlook)
    
- **FTP clients** (e.g., FileZilla)
    
- **VNC software** (used for remote desktop connections)
    

These applications often store passwords in unprotected forms, and there are methods to extract them, depending on the software used.

---

### Summary

- **Unattended Installations**: Look for files that store credentials used during Windows setup.
    
- **Powershell History**: Check for commands that might include passwords.
    
- **Saved Credentials**: Use `cmdkey /list` to find saved credentials for different users.
    
- **IIS Configuration**: Look for `web.config` files that might store database credentials.
    
- **PuTTY**: Check for saved proxy credentials in the registry.
    
- **Other Software**: Many programs save passwords, so they’re another place to look for credentials.
    

These are some of the common places on Windows systems where you can look for passwords or credentials to escalate your access.