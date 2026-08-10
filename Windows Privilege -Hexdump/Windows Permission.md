
---

## **1. Authentication, Authorization, and Session Management**

Whenever you use a secure system (like Windows), three key processes take place:

- **Authentication** – Proving your identity (e.g., logging in with a password).
    
- **Authorization** – Determining what you can do (e.g., whether you can install programs).
    
- **Session Management** – Keeping track of your login session while you use the system.
    

---

## **2. Security Principals and Security Identifiers (SIDs)**

A **Security Principal** is anything that Windows can recognize as a user or group. This includes:

- User accounts (e.g., JohnDoe)
    
- Computer accounts
    
- Processes or services
    

Each security principal gets a **Security Identifier (SID)**, which is a unique code used by Windows to manage permissions.

### **2.1 SID Structure**

SIDs follow a specific format:

```
S-R-X-Y1-Y2-Yn-1-Yn
```

- **S** → Stands for "SID".
    
- **R** → Revision (almost always "1").
    
- **X** → Identifier authority (who created the SID, e.g., NT Authority).
    
- **Y** → Subauthorities (parts of the SID that identify domains and accounts).
    

For example:

```
S-1-5-32-544
```

- **1** → Revision version.
    
- **5** → Identifier Authority (NT Authority).
    
- **32** → Built-in Group.
    
- **544** → Administrators group.
    

Another example:

```
S-1-5-21-1004336348-1177238915-682003330-512
```

- **21-1004336348-1177238915-682003330** → Domain Identifier (each domain gets a unique ID).
    
- **512** → Relative Identifier (RID) for "Domain Admins".
    

### **2.2 Well-Known SIDs**

Some SIDs are **always the same** on every Windows system. These are called **Well-Known SIDs**.

Examples:

- **S-1-5-18** → Local System account
    
- **S-1-5-32-544** → Administrators group
    
- **S-1-5-32-545** → Users group
    

### **2.3 Enumerating SIDs (Finding SIDs on a System)**

To find your current user’s SID:

```sh
whoami /user
```

To list all user accounts and their SIDs:

```sh
wmic useraccount get domain,name,sid
```

Example output:

```
Domain           Name                SID
--------------------------------------------------------
MY-PC           Administrator       S-1-5-21-XXXXXXXXX-500
MY-PC           Guest               S-1-5-21-XXXXXXXXX-501
MY-PC           MyUser              S-1-5-21-XXXXXXXXX-1000
```

---

## **3. Access Tokens**

An **Access Token** is created when you log in and contains information about your account’s permissions.

Each access token includes:

- Your **SID**
    
- The **groups** you belong to
    
- Your **privileges** (like the ability to install software)
    
- Whether you are using an **admin token or a regular user token**
    

When you start a program, Windows assigns it an **Access Token**, which controls what it can do.

To get access tokens, Windows provides API functions like:

- `OpenProcessToken()` → Gets the token of a process.
    
- `OpenThreadToken()` → Gets the token of a thread.
    

---

## **4. File Permissions (icacls Command)**

Windows manages file security using **Access Control Lists (ACLs)**, which contain **Access Control Entries (ACEs)**.

To check permissions on a file:

```sh
icacls file.txt
```

Example output:

```
file.txt NT AUTHORITY\SYSTEM:(I)(F)
         BUILTIN\Administrators:(I)(F)
         MY-PC\MyUser:(I)(M)
```

### **Permission Types**

- **F** → Full control
    
- **M** → Modify
    
- **RX** → Read & Execute
    
- **R** → Read-only
    
- **W** → Write-only
    

### **Inheritance Flags (For Folders)**

- **OI** → Object Inherit (applies to files inside the folder)
    
- **CI** → Container Inherit (applies to subfolders)
    
- **I** → Inherited from the parent folder
    

To **change** file permissions:

```sh
icacls file.txt /grant MyPC\User:R /t /c
```

- `/t` → Apply to all subfolders
    
- `/c` → Continue even if errors occur
    

---

## **5. Mandatory Integrity Control (MIC)**

MIC adds **an extra layer of security** by assigning an **Integrity Level** to every process and file.

### **Integrity Levels**

1. **System** → Used by critical Windows services.
    
2. **High** → Admin-level programs.
    
3. **Medium** → Normal users.
    
4. **Low** → Restricted processes (e.g., Internet Explorer in Protected Mode).
    

To check your current integrity level:

```sh
whoami /groups
```

Example output:

```
Mandatory Label\High Mandatory Level S-1-16-12288
```

To **change** the integrity level of a file:

```sh
icacls file.txt /setintegritylevel high
```

---

## **6. User Account Control (UAC)**

**UAC** is a security feature that prevents users (even admins) from making system changes without explicit permission.

### **How UAC Works**

When an administrator logs in, Windows **creates two access tokens**:

- **Standard User Token** (used for everyday tasks).
    
- **Administrator Token** (only activated when needed).
    

When you try to run an administrative task, you see a **UAC prompt**, asking for permission to continue.

---

## **7. References**

Here are useful links to learn more:

- **Security Identifiers (SIDs)**
    
    - [Microsoft Docs - SIDs](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-identifiers)
        
    - [Well-Known SIDs](https://learn.microsoft.com/en-us/windows/win32/api/winnt/ne-winnt-well_known_sid_type)
        
- **Access Tokens**
    
    - [Microsoft Docs - Access Tokens](https://learn.microsoft.com/en-us/windows/win32/secauthz/access-tokens)
        
- **Mandatory Integrity Control**
    
    - [MIC Explained](https://learn.microsoft.com/en-us/windows/win32/secauthz/mandatory-integrity-control)
        
- **File Permissions (icacls)**
    
    - [Microsoft Docs - icacls](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753525\(v=ws.10\)?redirectedfrom=MSDN)
        
- **Windows API and Impersonation**
    
    - [Windows API and Impersonation](https://0x00-0x00.github.io/research/2018/10/17/Windows-API-and-Impersonation-Part1.html)
        

---

This should make everything much easier to understand while covering all the details! 🚀 Let me know if you need any further explanation.