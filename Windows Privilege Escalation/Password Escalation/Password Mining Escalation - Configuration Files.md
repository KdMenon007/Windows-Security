
---

## 🛠️ **Password Mining via Configuration Files (Unattend.xml)**

### 🖥️ **Windows VM**

---

### **1. Open the Unattend.xml file**

```cmd
notepad C:\Windows\Panther\Unattend.xml
```

- **Unattend.xml** is used during **Windows automated installation** or **Sysprep** to preconfigure system settings, user accounts, etc.
    
- It's often located in:
    
    - `C:\Windows\Panther\`
        
    - `C:\Windows\System32\Sysprep\`
        
    - `C:\Windows\Panther\Unattend\`
        

> 🧠 _Why important?_ These files **sometimes contain plaintext or base64-encoded passwords** — often for administrator or local accounts — due to misconfigured deployment scripts.

---

### **2. Look for credentials in the XML**

- Inside the file, search for something like:
    

```xml
<Password>
    <Value>UGFzc3dvcmQxMjM=</Value>
    <PlainText>true</PlainText>
</Password>
```

- **`<Value>`** contains a Base64-encoded password.
    
- **`<PlainText>true</PlainText>`** confirms it’s not encrypted — it’s just encoded.
    

> ⚠️ _Base64 is not encryption_ — it’s just encoding. Anyone with access to this file can decode the password easily.

---

### 🐱‍💻 **Kali VM**

---

### **1. Decode the password using base64**

```bash
echo UGFzc3dvcmQxMjM= | base64 -d
```

- Replace `UGFzc3dvcmQxMjM=` with the string you copied.
    
- This will output the **cleartext password** (e.g., `Password123`).
    

> 🔓 _You’ve now obtained valid credentials_, which can be used to log in or elevate privileges if that account is privileged.

---
