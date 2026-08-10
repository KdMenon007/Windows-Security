
---
### **1. What is Windows Credential Manager?**

Credential Manager is a built-in Windows feature that stores and manages credentials such as:

- **Usernames**
    
- **Passwords**
    
- **Certificates**
    

It allows users to save login details for applications, websites, and network resources, so they don’t have to enter them every time. The credentials are encrypted using **DPAPI (Data Protection API)**.

---

### **2. Why is Credential Manager Dangerous?**

Even though stored credentials are encrypted, they can still be **used** (but not directly read) by an attacker who gains access to the session.

==session is compromised => all credentials saved in the manager can be used==

For example, if an attacker gets access to your computer while you're logged in, they could:

- Use stored credentials to access other systems or applications.
    
- Run commands as another user without knowing the actual password.
    
- Extract and misuse authentication tokens.
    

⚠️ **Tip:** Be cautious when saving sensitive credentials on shared or insecure machines.

---

### **3. How to Use Credential Manager?**

You can access Credential Manager through:

- **Control Panel → User Accounts → Credential Manager**
    
- Here, you will see stored credentials under **Web Credentials** and **Windows Credentials**.
    

#### 🔍 **Command Line Methods**

##### **🔹 View Stored Credentials**

```powershell
cmdkey /list
```

This will display all credentials saved in the system.

##### **🔹 Add New Credentials**

```powershell
cmdkey /add:MyServer /user:MyUser /pass:MyPassword
```

This command stores login credentials for "MyServer."

##### **🔹 Remove Credentials**

```powershell
cmdkey /delete:MyServer
cmdkey /delete:Domain:interactive=WORKGROUP\Administrator
```

These commands delete specific saved credentials.

##### **🔹 Running a Program with Saved Credentials**

```powershell
runas /savecred /user:computername\username powershell.exe
```

- The **first time**, it asks for a password.
    
- **Next time**, it runs the command without asking for a password.
    

---

### **4. What is Windows Vault?**

Windows Vault is the storage system behind Credential Manager. It securely holds encrypted credentials.

#### 🔍 **Command Line Methods for Windows Vault**

##### **🔹 List Vaults**

```powershell
vaultcmd /list
```

##### **🔹 Show Stored Vault Credentials**

```powershell
vaultcmd /listcreds:"Web Credentials" /all
```

##### **🔹 Dump Vault Credentials using Mimikatz**

```powershell
mimikatz.exe vault::list
```

⚠️ This last command extracts stored credentials, which can be a major security risk.

---

### **5. References**

For further details:

- [Microsoft Credential Manager Store](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh994565\(v=ws.11\))
    
- [cmdkey Official Documentation](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/cmdkey)
    

---
