
---

### **Summary of Windows OS History & Versions**

- **Windows History**: Windows has been around since 1985 and is the most widely used OS for both home and business use, making it a major target for hackers and malware.
    
- **Windows XP**: Very popular and long-lasting. When its end-of-life was announced, many organizations rushed to upgrade.
    
- **Windows Vista**: Major redesign but had many issues and was not well received.
    
- **Windows 7**: Seen as a stable replacement for XP. Many companies switched to it after XP support ended. It also received an end-of-support date early.
    
- **Windows 8.x**: Short-lived and not widely adopted, similar to Vista.
    
- **Windows 10**: Became the standard desktop OS. Comes in **Home** and **Pro** editions. Microsoft will support at least one version of it until **October 14, 2025**.
    
- **Windows 11**: Officially released on **October 5, 2021**, as the current version for end-users.
    
- **Windows Server 2019**: Current version of the Windows Server OS (the VM mentioned runs **Windows Server 2019 Standard**).
    
---

### **Windows 10 Desktop Overview**

The **Windows Desktop (GUI)** is what you see after logging in. It includes several key components:

---

### 🔐 **Login Screen**

- Requires a valid username and password.
    
- Can be a local Windows account or a domain account (Active Directory).
    

---

### 🖥️ **Main Desktop Components**

1. **Desktop**
    
    - Contains shortcuts to apps, files, folders.
        
    - Right-click for options like icon size, arrangement, and creating new items.
        
    - Change display settings and wallpaper under **Display settings** and **Personalize**.
        
    - Multi-monitor setup can also be configured here.
        
2. **Start Menu**
    
    - Accessed via the Windows logo at the bottom-left.
        
    - Divided into:
        
        - **User/account shortcuts**: Lock, sign out, Documents, Pictures, Settings, Power.
            
        - **Recently added & installed apps**: Listed alphabetically.
            
        - **Tiles**: App tiles for quick access. Right-click to pin/unpin or resize.
            
3. **Search Box (Cortana)**
    
    - Lets you search apps, files, or use voice commands (if enabled).
        
4. **Task View**
    
    - Shows all open windows and allows virtual desktop management.
        
5. **Taskbar**
    
    - Shows running apps/programs.
        
    - Hovering gives previews.
        
    - Items disappear when closed unless pinned.
        
6. **Toolbars**
    
    - Optional. Can be enabled for quick access to folders/apps.
        
7. **Notification Area (System Tray)**
    
    - Bottom-right corner: shows date/time, volume, network, etc.
        
    - Customize via **Taskbar Settings > Notification Area**.
        

---

### **Windows File System: NTFS Overview**

#### 📁 **File System Types**

- **Current**: NTFS (New Technology File System) – used in modern Windows systems.
    
- **Older**: FAT16, FAT32, and HPFS – used in legacy systems and still seen in USBs, SD cards, etc.
    

---

### ✅ **Why NTFS Is Better**

- **Journaling file system**: Can recover data after crashes using log files.
    
- **Supports**:
    
    - Files **larger than 4GB**
        
    - **File/folder permissions**
        
    - **Compression**
        
    - **Encryption** via EFS (Encrypting File System)
        

---

### 🔒 **NTFS Permissions**

Set to allow or deny access to files and folders:

- **Full Control**
    
- **Modify**
    
- **Read & Execute**
    
- **List Folder Contents**
    
- **Read**
    
- **Write**
    

**To view/edit permissions:**

1. Right-click file/folder → **Properties**
    
2. Go to **Security** tab
    
3. Select a user/group to view their permissions
    

---

### 🧪 **Alternate Data Streams (ADS)**

- NTFS allows multiple data streams in one file.
    
- Main stream = `$DATA`, but ADS can store hidden data.
    
- **Not visible in File Explorer**
    
- Can be used for:
    
    - Hiding malware (malicious use)
        
    - Marking downloaded files from the internet (legit use)
        
- Use **PowerShell or 3rd party tools** to view ADS.
    

---

### 🔍 **How to Check File System Type**

- Right-click **C:\ drive** → **Properties** → Look for **File System: NTFS**
    
---

### **📂 C:\Windows Folder Overview**

- The **Windows folder** typically resides in the **C:\ drive** and contains all core files for the **Windows operating system**.
    
- It *_doesn't have to be on C:*_ — it can be installed on a different drive or directory.
    
- You can refer to the Windows directory using the **system environment variable**:  
    👉 `%windir%`
    

---

### **🌐 Environment Variables**

- **Environment variables** store system info (e.g., OS path, processor count, temp folder locations).
    
- `%windir%` = dynamic reference to the Windows folder (e.g., `C:\Windows`)
    

---

### **🛠️ System32 Folder**

- Located at: `%windir%\System32` (typically `C:\Windows\System32`)
    
- Contains **critical system files and tools** required for Windows to function.
    
- Deleting or modifying anything in this folder can **break the OS** – **extreme caution is advised**.
    

> ⚠️ Many essential Windows tools used in diagnostics and administration are stored in this folder.

---

### 👥 **Types of User Accounts**

1. **Administrator**
    
    - Can make **system-wide changes** (install apps, manage users, change settings).
        
    - Has full control over the system.
        
2. **Standard User**
    
    - Can manage **own files/folders** only.
        
    - **Cannot** install apps or change system settings.
        

> ✅ You're currently logged in as an **Administrator**.

---

### 🔍 **How to View or Manage User Accounts**

- Open **Start Menu** → Type **Other User** → Click the result
    
- From there, you can:
    
    - **Add a user**
        
    - **Remove a user**
        
    - **Change account type**
        

> Only **Administrators** can see the “Add someone else to this PC” option.

---

### 📁 **User Profiles**

- Stored in: `C:\Users\<Username>`
    
- Created automatically **when the user logs in for the first time**
    
- Common folders in each profile:
    
    - Desktop
        
    - Documents
        
    - Downloads
        
    - Music
        
    - Pictures
        

---

### ⚙️ **Local Users and Groups Management**

- Open Run box: `Win + R` → Type `lusrmgr.msc`
    
- Two main sections:
    
    - **Users**: View and manage all local user accounts
        
    - **Groups**: View groups and their descriptions
        

> Users **inherit permissions** from the groups they’re assigned to.  
> A user can belong to **multiple groups**.

---

### 🧑‍💻 **Admin Accounts ≠ Always Elevated**

- Even if you're logged in as an **Administrator**, Windows does **not run everything with full privileges by default**.
    
- Instead, it only **elevates** (grants high privileges) **when needed**, to reduce risk.
    

---

### 🔐 **What is UAC (User Account Control)?**

- Introduced in **Windows Vista** to improve system security.
    
- **UAC prompts** the user before allowing **admin-level actions**, like:
    
    - Installing software
        
    - Changing system settings
        

> 🛡️ The shield icon on a program means **it requires elevation** and will trigger a UAC prompt.

---

### 🔑 **How UAC Works**

- Admin accounts **run with limited privileges by default**.
    
- When a system-level task is attempted, UAC:
    
    - Shows a **popup prompt**
        
    - Asks for the **admin password** or confirmation
        
- If denied or ignored, the action is **not performed**.
    

> 🔒 **Built-in Administrator account**: UAC is **disabled by default** for this account (no prompt appears).

---

### 👥 **Standard User vs Admin Behavior**

- **Standard users**:
    
    - Can't install software or perform system changes.
        
    - Will see a UAC prompt that **requires admin credentials**.
        
- **Admin users**:
    
    - See a UAC prompt that only needs **confirmation**, not a password.
        

---

### ⚠️ Why UAC Matters

- Many home users run as admins full-time.
    
- Without UAC, **malware** could easily make system changes.
    
- UAC adds a layer of **protection**, even for admin accounts.
    

---

### ⚙️ **Settings Menu**

- Introduced in **Windows 8** for touchscreen-friendly use.
    
- Now the **primary interface** for most system changes in **Windows 10/11**.
    
- Clean, modern look.
    
- Used for common tasks like:
    
    - Changing wallpaper
        
    - Managing accounts
        
    - Adjusting system preferences
        
    - Managing apps
        

🛠️ **Access**:

- Start Menu → Click gear icon
    
- Or search for specific settings (e.g., "Bluetooth", "Wallpaper")
    

---

### 🖥️ **Control Panel**

- Classic system management interface (since early versions of Windows).
    
- Still used for **more advanced or legacy settings**, like:
    
    - Device Manager
        
    - Administrative Tools
        
    - Network adapter settings
        
    - Programs & Features (to uninstall apps)
        

🛠️ **Access**:

- Start Menu → Type "Control Panel"
    
- Or reach it through links within Settings (e.g., **Change adapter options** in network settings).
    

---

### 🔄 **How They Work Together**

- Some settings **start in Settings** but **redirect to Control Panel** for advanced options.
    
- You can search for either in the **Start Menu** to find what you need quickly.
    

---

### 🧠 **What is Task Manager?**

- A **built-in Windows tool** that shows:
    
    - What programs and processes are running
        
    - How system resources like **CPU**, **Memory (RAM)**, **Disk**, and **Network** are being used
        

---

### 🛠️ **How to Open It**

- **Right-click** the Taskbar → Click **Task Manager**
    
- Or press `Ctrl + Shift + Esc`
    

---

### 🔍 **Simple View vs. Detailed View**

- **Simple View**:
    
    - Only shows open apps
        
    - Basic "End Task" button
        
- **Detailed View** (Click **"More details"**):
    
    - Shows multiple tabs:
        
        - **Processes**: List of running programs and background tasks
            
        - **Performance**: Real-time graphs of CPU, RAM, Disk, and Network usage
            
        - **App History**: Usage stats for apps (especially on tablets/laptops)
            
        - **Startup**: Apps that run at boot (can enable/disable them)
            
        - **Users**: Who is logged in and what they're using
            
        - **Details**: Technical view of each running process
            
        - **Services**: Running/stopped Windows services
            

---

### ✅ Why It's Useful

- Close frozen programs
    
- Monitor system performance
    
- Troubleshoot slowdowns
    
- Manage startup apps
    

---

### ⚙️ **What is MSConfig?**

- A **troubleshooting tool** used to diagnose **startup issues** on Windows.
    
- Helps control what loads when the system boots.
    

---

### 🛠️ **How to Open It**

- Search for **"msconfig"** from the **Start Menu** or **Run** dialog (`Win + R`)
    
- Requires **Administrator access**
    

---

### 🧩 **MSConfig Tabs Explained**

1. **General**
    
    - Choose startup mode:
        
        - **Normal** – Loads everything
            
        - **Diagnostic** – Basic boot (like Safe Mode)
            
        - **Selective** – Choose specific services/startups
            
2. **Boot**
    
    - Control boot options:
        
        - Safe Boot
            
        - No GUI boot
            
        - Boot log
            
        - Timeout settings
            
3. **Services**
    
    - View **all system services** (running or not)
        
    - Can disable/enable for troubleshooting
        
4. **Startup**
    
    - Just a shortcut to **Task Manager** to manage startup programs
        
    - (Note: May not work fully in certain VMs or older versions)
        
5. **Tools**
    
    - A list of **useful Windows utilities**
        
    - Includes description + command to launch each
        
    - You can launch them directly from here or copy the command
        

---

### ✅ Why Use It?

- **Speed up** boot time by disabling unnecessary services
    
- **Diagnose** apps/services causing boot failures
    
- **Launch useful tools** for further configuration
    

---

### 🔐 **What is UAC (User Account Control)?**

- UAC **prompts you** when a program tries to make **system-level changes.**
    
- Helps **prevent unauthorized changes** and **malware infections**.
    

---

### 🛠️ **How to Access UAC Settings**

- Go to **MSConfig → Tools**
    
- Look for a tool related to **"Change UAC Settings"** and click **Launch**
    

---

### 📊 **The UAC Slider Options (from most secure to least):**

1. 🔒 **Always notify**
    
    - You’re notified **every time** an app tries to make changes
        
    - Best for **maximum security**
        
2. ⚠️ **Notify me only when apps try to make changes (default)**
    
    - No prompt when _you_ make changes
        
    - Recommended for **standard users**
        
3. ⚠️ **Notify me only when apps try to make changes (no desktop dimming)**
    
    - Same as default, but **less secure**
        
    - The screen doesn’t dim—riskier if malware is involved
        
4. ❌ **Never notify**
    
    - **Turns off UAC**
        
    - Not recommended—**no warnings**, malware has a free pass
        
---
## 🧰 **Computer Management Utility Overview**

Computer Management consolidates several administrative tools under one roof. It's split into **three main sections**:

---

### 📁 **1. System Tools**

#### 📆 **Task Scheduler**

- Automate tasks like running scripts or programs.
    
- Trigger tasks based on events, logon, or schedules.
    
- Create a task via: `Create Basic Task` in the **Actions** pane.
    

#### 📋 **Event Viewer**

- Audits system activity and errors.
    
- Helpful for troubleshooting and investigations.
    
- Logs types:
    
    - **Information** – successful operations.
        
    - **Warning** – potential issues.
        
    - **Error** – failed operations.
        
    - **Critical** – serious failures.
        
    - **Verbose** – detailed info (mostly for debugging).
        
- Key logs under **Windows Logs**:
    
    - **Application** – app-related events.
        
    - **Security** – logins, access attempts.
        
    - **System** – Windows component events.
        
    - **Setup** – OS setup logs.
        
    - **Forwarded Events** – from remote computers.
        

#### 🔁 **Shared Folders**

- **Shares**: See what’s shared (like `C$`, `ADMIN$`).
    
- **Sessions**: View users connected to shares.
    
- **Open Files**: Files users are currently accessing.
    

#### 👥 **Local Users and Groups**

- Covered in Windows Fundamentals 1 (`lusrmgr.msc`).
    
- Create, manage users and group memberships.
    

#### 📈 **Performance Monitor (perfmon)**

- Monitor system resource usage live or from logs.
    
- Useful for diagnosing slowness or high resource usage.
    

#### 🖥️ **Device Manager**

- Manage hardware devices.
    
- Enable/disable drivers or check for issues.
    

---

### 💾 **2. Storage**

#### 🧱 **Disk Management**

- Perform disk-related tasks like:
    
    - Initialize or format drives.
        
    - Resize partitions (shrink/extend).
        
    - Assign/change drive letters.
        
- Crucial for managing system and external disks.
    

---

### ⚙️ **3. Services and Applications**

#### ⚙️ **Services**

- Manage background processes (start, stop, configure).
    
- Can set services to auto-start or manual.
    

#### 🧠 **WMI Control**

- Manages Windows Management Instrumentation (WMI).
    
- Useful for scripting and remote system management.
    
- Note: **WMIC is deprecated**; use **PowerShell** instead.
    

---

### 💡 Quick Tip:

You can launch **Computer Management** directly with:

```bash
compmgmt.msc
```

---

## 🧠 **What is `msinfo32` (System Information)?**

`msinfo32.exe` gives you **a complete overview of your system**—hardware, components, software environment—all in one place. It’s an essential utility for diagnosing issues, checking system specs, and viewing OS-level configurations.

### 🚀 How to Launch It:

You can launch it in a few ways:

- **Start Menu** > Type `msinfo32`
    
- **Run dialog (Win + R)** > Type `msinfo32` and press Enter
    
- **System Configuration > Tools tab** > Select “System Information” and click Launch
    

---

## 📋 **System Information Structure**

When you open it, you'll see three main sections on the left-hand side:

### 1. **System Summary**

- Basic details about the PC:
    
    - OS name and version
        
    - BIOS version/date
        
    - Processor info
        
    - Installed RAM
        
    - System Manufacturer and Model
        
    - Boot mode
        
    - Secure Boot status
        

### 2. **Hardware Resources**

- Low-level info mostly used by advanced users and IT pros:
    
    - Memory (RAM) allocation
        
    - IRQs (interrupt requests)
        
    - I/O ports
        
    - DMA channels
        

> ⚠️ Not beginner-friendly, but powerful for diagnostics.

### 3. **Components**

- Details about hardware attached to the system.
    
    - Examples:
        
        - **Display** – Graphics card
            
        - **Input** – Keyboards, mice
            
        - **Storage** – Drives and file systems
            
        - **Network** – Adapters, IP addresses
            

### 4. **Software Environment**

- Shows software-related details:
    
    - Drivers
        
    - Running tasks and services
        
    - Environment Variables
        
    - Network connections
        
    - Startup programs (this can also be seen in `taskmgr`)
        

---

## 🧭 **Environment Variables Recap**

From Windows Fundamentals 1:

- Stored data that programs and Windows itself use to function.
    
- Example: `%WINDIR%` points to the Windows directory (`C:\Windows` by default).
    
- Can be viewed in **msinfo32**, or:
    
    - **Control Panel > System > Advanced system settings**
        
    - **Settings > System > About > Advanced system settings**
        

---

## 🔍 **Using the Search Bar (Bottom of msinfo32)**

This is super helpful!

### ✅ Example: Search for IP Address

1. In the left-hand pane, click on **Components** (to scope your search).
    
2. Scroll to the very bottom of the window.
    
3. In the **Search bar**, type:
    
    ```
    IP address
    ```
    
4. Press Enter.
    

💡 The results should show:

- The **IP address** assigned to your system.
    
- Info related to the **Network Adapter**.
    

> 📌 This is useful if you want to quickly find where a setting or piece of data lives without browsing through every section.


---

## 🧰 **What is Resource Monitor?**

**`resmon`** is a **performance and diagnostic tool** that gives a real-time look at how system resources (CPU, memory, disk, and network) are being used by individual processes. It’s essentially a more advanced view of what you see in **Task Manager**, with **detailed filtering and monitoring options**.

### 📌 How to Launch It:

- **Start Menu** > Type `resmon`
    
- **Run dialog (Win + R)** > `resmon`
    
- **System Configuration > Tools tab** > Select **Resource Monitor**, click **Launch**
    
- **Task Manager > Performance tab > Open Resource Monitor** (link at the bottom)
    

---

## 🧭 Overview Tab: The All-in-One View

The **Overview** tab is your dashboard. It contains _four key sections_:

1. **CPU**
    
2. **Memory**
    
3. **Disk**
    
4. **Network**
    

Each section shows:

- Active processes using that resource
    
- Resource usage per process
    
- A **live graph** on the far right side for real-time monitoring
    

---

## 🔍 Tabs in Detail

### 🔹 **CPU Tab**

- Lists **all running processes**
    
- Shows CPU usage per process
    
- Also shows **services** associated with a process
    
- Tracks **threads**, **handles**, and even **CPU usage over time**
    
- Helps identify **hung or unresponsive processes**
    
- Can suspend/resume/terminate a process from here
    

### 🔹 **Memory Tab**

- Displays:
    
    - Total physical memory (RAM)
        
    - Used vs. available memory
        
    - Hard faults (how often memory is being read from disk instead of RAM—bad for performance)
        
- Shows **memory footprint per process**
    
- Helps spot **memory leaks**
    

### 🔹 **Disk Tab**

- Shows:
    
    - Disk activity by process
        
    - Files being accessed
        
    - Disk queue length (how busy the disk is)
        
- Great for troubleshooting **slow performance**, especially when an app is reading/writing a lot
    

### 🔹 **Network Tab**

- Tracks:
    
    - Network usage per process
        
    - Active TCP connections
        
    - Listening ports
        
- Helps spot suspicious activity (e.g., unknown programs making network connections)
    

---

## 📈 Real-Time Graphs

At the **far right**, each tab (and the Overview tab) has **real-time graphs** that update every second. This makes it easy to visually spot spikes or bottlenecks.

---

## ⚠️ Why Use Resource Monitor?

It’s useful when you need to:

- Figure out **which app is slowing down your system**
    
- Investigate **unusual CPU or disk spikes**
    
- Track **network usage** for suspicious connections
    
- Find out **why your system is lagging or freezing**
    
- Identify **file lock conflicts** (when two apps try to use the same file)
    
---

## 🖥️ What Is the Command Prompt?

The **Command Prompt** is a **text-based interface** that lets you interact directly with the Windows operating system using typed commands—no clicking required.

It's been around forever (since DOS days), and while the **GUI** is now dominant, the command line remains **essential for advanced users, troubleshooting, scripting, and automation.**

---

## ✅ Basic Commands to Know

Let’s start simple:

### 🔹 `hostname`

- **Use**: Shows the **computer’s name** on the network.
    
- **Example**:
    
    ```
    C:\> hostname
    MY-COMPUTER-NAME
    ```
    

### 🔹 `whoami`

- **Use**: Tells you **who is currently logged in** (username + domain if applicable).
    
- **Example**:
    
    ```
    C:\> whoami
    user1-pc\user1
    ```
    

---

## 🌐 Network Commands (Very Useful)

### 🔹 `ipconfig`

- **Use**: Displays **network configuration**—IP addresses, subnet mask, default gateway, etc.
    
- **Example**:
    
    ```
    C:\> ipconfig
    ```
    
- Want more info? Use:
    
    ```
    ipconfig /all
    ```
    

### 🔹 `ipconfig /?`

- **Use**: Shows the **help manual** for `ipconfig` (including all available options).
    
- Works for most commands—just add `/?`.
    

---

## 💡 Helpful Trick

### 🔹 `cls`

- Clears the command prompt screen. Super handy when things get cluttered.
    

---

## 🔎 More Advanced Tools

### 🔹 `netstat`

- **Use**: Displays current **network connections**, **ports**, **protocols**, and more.
    
- Run it plain:
    
    ```
    netstat
    ```
    
- Or add flags:
    
    - `-a` – Show all connections and listening ports
        
    - `-b` – Show which **executables** are making connections
        
    - `-n` – Show IPs **numerically** (instead of resolving DNS names)
        

### Check help:

```
netstat /?
```

---

## 🌐 Network Resource Management

### 🔹 `net`

- A command used to **manage network shares, users, sessions**, and more.
    
- Just type `net` to see available subcommands:
    
    ```
    net
    ```
    

Some common subcommands:

- `net user` – Manage users
    
- `net localgroup` – Manage local groups
    
- `net use` – Map network drives
    
- `net session` – View active sessions
    
- `net share` – Manage shared folders
    

#### Help for these:

- Use:
    
    ```
    net help user
    net help localgroup
    ```
    

⚠️ Unlike other commands, **`net` doesn’t use `/?’` for help**, it uses:

```
net help [subcommand]
```

---

## ✅ Summary

|Command|Description|
|---|---|
|`hostname`|Displays the computer’s name|
|`whoami`|Shows the current logged-in user|
|`ipconfig`|Shows network settings (IP address etc)|
|`cls`|Clears the command prompt screen|
|`netstat`|Shows network connections & ports|
|`net`|Manages users, shares, and sessions|

---

## 🧠 What Is the Windows Registry?

Per Microsoft:

> The **Windows Registry** is a **central hierarchical database** that stores **configuration settings** and **options** for the operating system, hardware, software, and users.

Basically, it's where Windows keeps track of almost _everything_ it needs to run and manage the system.

---

## 🧩 What Does the Registry Store?

Some examples of what’s stored in the registry:

- 👤 User profiles and settings
    
- 🖥️ Installed hardware (e.g., printers, graphics cards)
    
- 💾 Installed software and file associations
    
- 🗂️ Folder and desktop settings
    
- 📡 Network port usage
    
- 🧱 System-level configurations (startup behavior, services, policies)
    

---

## ⚠️ Be Careful!

> ❗ **Important:** Making incorrect changes in the registry can **break programs** or even make your system **unbootable**.

- Always **back up the registry** before making any changes.
    
- Only modify registry entries **if you know what you're doing** or you're following **trusted instructions**.
    

---

## 🛠️ How Do You Access the Registry?

Use the **Registry Editor**, called:

```
regedit
```

### How to open it:

- Press `Win + R` to open the Run dialog.
    
- Type `regedit` and hit **Enter**.
    
- Confirm the UAC prompt if it appears.
    

---

## 📂 Registry Structure (5 Main Root Keys)

The registry is structured like folders and files. The five root keys are:

|Root Key|Description|
|---|---|
|**HKEY_CLASSES_ROOT (HKCR)**|File associations and COM objects|
|**HKEY_CURRENT_USER (HKCU)**|Settings specific to the currently logged-in user|
|**HKEY_LOCAL_MACHINE (HKLM)**|System-wide settings, including drivers, services|
|**HKEY_USERS (HKU)**|All loaded user profiles on the machine|
|**HKEY_CURRENT_CONFIG (HKCC)**|Hardware profile currently in use|

---

## 🔄 What Is Windows Update?

**Windows Update** is Microsoft’s built-in service for:

- 🔐 **Security updates** – patch vulnerabilities in the OS and other Microsoft software (like Defender)
    
- 🛠️ **Bug fixes** – resolve system issues or glitches
    
- ✨ **Feature enhancements** – deliver new tools and improvements
    
- 🔄 **Driver updates** – update hardware drivers for better compatibility and stability
    

---

## 📅 When Are Updates Released?

### ⏰ **Patch Tuesday**

- Occurs on the **2nd Tuesday of each month**
    
- Primary day for releasing **scheduled updates**, especially security patches
    

### 🚨 Out-of-Band Updates

- If there’s a **critical issue or vulnerability**, Microsoft can push updates **immediately**, without waiting for Patch Tuesday
    

📘 Want to stay up to date? Check out the [Microsoft Security Update Guide](https://msrc.microsoft.com/update-guide).

---

## ⚙️ How to Access Windows Update

You can find Windows Update in:

- **Settings → Update & Security → Windows Update**
    
- Or, run this command from **Run dialog (Win + R)** or **Command Prompt**:
    
    ```
    control /name Microsoft.WindowsUpdate
    ```
    

---

## 🔒 Why Does It Say “Some settings are managed by your organization”?

- This appears if **group policies** or **IT-managed settings** are controlling update behavior
    
- Usually seen in **enterprise environments** or **virtual machines (VMs)** like the one attached in the TryHackMe lab
    

---

## 💻 No Updates Available?

That likely means:

- The VM is **offline** (no internet = no check-ins with Microsoft update servers)
    
- OR it’s up to date with the available **local update cache**
    

---

## 🔁 Updates Can’t Be Skipped Anymore

In older versions of Windows, users often delayed updates for **weeks or months**.

Starting with **Windows 10**, Microsoft changed this:

- Users can **pause** or **reschedule**, but **not skip** indefinitely
    
- Eventually, updates **install automatically**, and a **restart is required**
    

---

## 🕒 What Happens When a Restart Is Required?

When an update installs that needs a restart:

- You’ll see a **Restart required** message
    
- You can choose:
    
    - Restart **now**
        
    - Schedule a **time**
        
    - Be reminded **later**
        

Windows tries to reduce interruptions by learning your usage habits and choosing **non-active hours** for restarts.
protecting your device and data.

---

## 🛡️ What Is Windows Security?

Per Microsoft:

> _"Windows Security is your home to manage the tools that protect your device and your data."_

This app gives you quick access to essential protection features that monitor for viruses, firewall breaches, suspicious activity, and more.

You’ll find it here:

- **Settings → Update & Security → Windows Security**
    
- Or just search **"Windows Security"** in the Start menu
    

---

## 🔍 Protection Areas (Main Dashboard)

When you open Windows Security, you’ll see the **Protection areas** listed. Here’s what each one does:

1. **💥 Virus & threat protection**
    
    - Monitors threats from malware, ransomware, and other malicious software
        
    - Lets you run scans and review threat history
        
2. **🌐 Firewall & network protection**
    
    - Controls access to and from your network
        
    - Manages firewall settings for different network profiles (Domain, Private, Public)
        
3. **🛑 App & browser control**
    
    - Uses Microsoft Defender SmartScreen to protect against malicious apps, downloads, and websites
        
    - Also includes exploit protection settings
        
4. **🧱 Device security**
    
    - Reports on hardware-based security features (like Secure Boot and TPM)
        
    - Ensures system integrity from the ground up
        

---

## 🔔 What Do the Status Icons Mean?

Each protection area will show a **status icon**:

- ✅ **Green** – Everything is good; no action needed
    
- ⚠️ **Yellow** – Some recommendations or settings may need your attention
    
- ❌ **Red** – Something critical needs to be fixed right away
    

---

## 🛡️ Virus & Threat Protection Breakdown

This section is split into **two main parts**:

---

### 🔍 1. **Current Threats**

This area is all about what’s happening **right now** on your system.

#### ✅ **Scan Options**

You’ll see buttons here to launch different types of scans:

- **Quick Scan** – Scans folders where malware commonly hides (fastest).
    
- **Full Scan** – Checks _all_ files and running programs (can take an hour+).
    
- **Custom Scan** – You choose which folders/files to scan.
    

#### 🕒 **Threat History**

Shows you recent activity and scan results:

- **Last Scan** – When the last scan was run and what was scanned.
    
- **Quarantined Threats** – Detected malware that has been isolated.
    
- **Allowed Threats** – Potential threats that you've manually allowed.
    

> ⚠️ **Warning**: Only allow threats if you _absolutely know_ what you're doing. Letting malware run freely can seriously compromise your system.

---

### ⚙️ 2. **Virus & Threat Protection Settings**

Click **“Manage Settings”** to customize how Defender behaves.

#### Key Features:

- **🛑 Real-time Protection** – Scans for threats the moment they try to run.  
    (💡 Must be ON unless you’re using another antivirus.)
    
- **☁️ Cloud-delivered Protection** – Accesses Microsoft’s cloud intelligence for faster threat detection.
    
- **📤 Automatic Sample Submission** – Sends suspicious files to Microsoft for deeper analysis.
    
- **🛡️ Controlled Folder Access** – Protects sensitive areas (like Documents, Pictures) from unauthorized apps, especially ransomware.
    
- **🚫 Exclusions** – Lists files/folders that Defender won’t scan.
    
    > ⚠️ Dangerous if used incorrectly—skipped items won’t be protected.
    
- **🔔 Notifications** – Choose whether to be notified about scans, detections, and more.
    

---

### 🔄 **Virus & Threat Protection Updates**

Even without full Windows Updates, Microsoft Defender still updates its **definitions** (the list of known malware). You can check manually via:

```
Check for updates
```

---

### 🛡️ **Ransomware Protection**

Ransomware is a growing threat that locks your files unless you pay up.

This section includes:

- **Controlled Folder Access** (again, because it’s that important)
    
- 💡 _Note:_ Requires **Real-time Protection** to be ON.
    

---

## 🔥 What is a Firewall?

**Microsoft's definition:**

> “A firewall is what controls what is—and more importantly _isn't_—allowed to pass through network ports. Think of it like a security guard checking IDs at the door.”

### 🛡️ In other words:

A firewall watches everything trying to come into or go out of your computer over the internet (or local network), and either **allows** or **blocks** it based on rules.

---

## 🌐 Firewall Profiles Explained

Windows Firewall has **3 different profiles** depending on the type of network you’re connected to:

|Profile|Use Case|Example|
|---|---|---|
|**Domain**|When your computer is part of a corporate domain.|Office/work computers|
|**Private**|Home or trusted networks.|Home Wi-Fi|
|**Public**|Untrusted networks.|Coffee shop Wi-Fi, airports|

💡 **Public** is the most restrictive, **Private** is more relaxed, and **Domain** is typically managed by IT admins.

---

## ⚙️ Inside Each Firewall Profile

When you click on a profile (e.g., "Public network"), you’ll see options:

- **Turn Firewall On/Off**
    
    - 🔒 _Recommended: Keep ON unless you know exactly what you're doing._
        
- **Block all incoming connections**
    
    - Useful when you want maximum security—_even allowed apps_ will be blocked.
        

---

## ✅ Allowing Apps Through the Firewall

Sometimes, legitimate apps need access through the firewall (e.g., a game that connects to the internet, or a remote desktop tool).

- You can click **“Allow an app through firewall”**
    
- You’ll see a list of apps and checkboxes under **Private** and **Public**
    
- Use **Details** to learn more about a specific entry
    

> ⚠️ Be careful here. Don’t allow random apps through unless you trust them.

---

## 🧠 Advanced Settings (For Power Users)

If you really want to get into the weeds:

- You can launch **Advanced Settings** to:
    
    - Create _custom rules_ for inbound and outbound traffic
        
    - Manage rules per port, program, IP address, and protocol
        
    - Set _logging_ and _monitoring_
        

> 🔧 This is mostly for advanced users. If you’re curious, feel free to explore—but read Microsoft’s best practices first.

---

## 🏃‍♂️ Quick Command

You can open the Windows Firewall settings directly using:

```
WF.msc
```

Just type that into the **Run box (Windows + R)** or **Command Prompt**.

---

## 🧠 What is Microsoft Defender SmartScreen?

Per Microsoft:

> “**Microsoft Defender SmartScreen** protects against phishing or malware websites and applications, and the downloading of potentially malicious files.”

In simpler terms:  
SmartScreen checks websites, apps, and downloads to help protect you from known threats and suspicious behavior. It’s like a **bouncer** for your computer, stopping shady stuff before it gets through the door.

---

## 🔧 What You Can Control Here

### ✅ **Check apps and files**

- **Purpose:** Checks files and apps you open/download from the web.
    
- **Options:**
    
    - **Block** – Strictest; automatically blocks anything suspicious.
        
    - **Warn** – Shows a warning but lets you decide.
        
    - **Off** – Disables protection (_not recommended_ unless you have a really good reason).
        

### 🌐 **SmartScreen for Microsoft Edge**

- Works specifically with the **Microsoft Edge browser**
    
- Warns about:
    
    - Phishing sites
        
    - Malicious downloads
        
    - Websites known for scams or malware
        

### 💻 **SmartScreen for Microsoft Store apps**

- Protects you from shady apps in the **Microsoft Store**
    
- Checks reputation of apps before you install or run them
    

### 🛡️ **Exploit Protection**

- Built-in system-level protection against advanced malware attacks that try to exploit vulnerabilities in software
    
- You don’t need to install anything extra—Windows handles it
    
- Can be configured per-app for advanced users (via **Windows Security** or **Exploit Protection settings** under `ExploitProtectionSettings.exe`)
    

> ⚠️ **Warning:** Tweaking these settings incorrectly can reduce your system’s protection. Stick with the defaults unless you know what you're doing or are guided by a security professional.

---

## 🔐 **Device Security Overview**

This section gives you insight into hardware-based security features built into your system. These features make your computer more resilient to attacks at a deeper level (below the OS), especially against things like firmware attacks or tampering with boot processes.

---

## 🧱 **Core Isolation**

### ✅ **Memory Integrity**

- **What it does:** Keeps the memory space used by core system processes **isolated** from the rest of the system.
    
- **Why it's important:** Prevents malware from injecting code into high-privilege system processes.
    
- **Recommended Setting:** **On (enabled by default)**
    

> ⚠️ **Only disable this** if you know you’re troubleshooting something that absolutely requires it. Otherwise, leave it alone—it's there to protect you.

---

## 🛡️ **Security Processor (TPM)**

### 🔒 **What is the TPM?**

> “**Trusted Platform Module (TPM)** is a hardware chip designed to securely store cryptographic keys used for encryption and security features like BitLocker.”

- **Think of TPM as your device’s built-in vault**—it securely stores sensitive data like encryption keys, digital certificates, and even biometric data.
    
- **TPM helps enable:**
    
    - Secure boot
        
    - BitLocker encryption
        
    - Windows Hello
        
    - Credential Guard
        

### 🔧 **Security Processor Details**

- In Windows Security, you can view the version and status of your TPM under **Security processor > Security processor details**.
    
- If supported, there will also be options to **clear** or **troubleshoot** the TPM, though you should only do that under specific guidance (e.g., during a major system reset or domain re-enrollment).
    

---

## ✅ Best Practices:

- **Keep Memory Integrity ON** (under Core Isolation).
    
- **Leave TPM settings as-is.** No need to tinker unless you have a very specific reason.
    
- **If you don't see TPM info**, it could be:
    
    - The device doesn’t have a TPM chip (common in older hardware)
        
    - TPM is disabled in BIOS/UEFI (you can enable it manually if needed)
        

---

## 🔐 What is BitLocker?

**BitLocker Drive Encryption** is a **built-in security feature in Windows** that **encrypts your entire drive**, helping to prevent **unauthorized access** to your data—especially useful if a device is lost or stolen.

Imagine losing your laptop—if BitLocker is enabled, the data on your hard drive would be unreadable without the correct credentials (like a PIN, password, or recovery key). It’s like putting all your files in a digital safe.

---

## 🧩 How BitLocker Works:

- It encrypts the **entire drive**, not just individual files or folders.
    
- It runs **in the background**, and once it's turned on, you barely notice it.
    
- The encryption process uses **AES (Advanced Encryption Standard)**.
    
- BitLocker supports encryption with:
    
    - **TPM (Trusted Platform Module)** – best security.
        
    - **Password or USB Key** – if your device doesn’t have a TPM chip.
        

---

## 🛡️ Why is TPM Important?

When paired with a **TPM chip**, BitLocker can:

- **Verify system integrity** before Windows boots.
    
- Automatically **unlock the drive at boot** without needing a password.
    
- Protect encryption keys from tampering or unauthorized access.
    

Without TPM, you can still use BitLocker, but you'll need a **USB startup key** or a **password** every time you boot.

---

## 🧠 What is Volume Shadow Copy Service (VSS)?

**VSS**, also called **Shadow Copy**, is a **Windows service** that lets you create **point-in-time snapshots** of files or entire volumes—even while the system is running and in use. This is what makes **System Restore** and **File History** possible.

It’s like a **“backup time machine”** you can use to restore a previous state of the system or recover a deleted/changed file.

---

## 📂 Where are Shadow Copies Stored?

- Shadow copies are stored in the **System Volume Information** folder on the drive.
    
- This folder is **protected by the OS** and not accessible without elevated permissions.
    

---

## ⚙️ What Can You Do When VSS Is Enabled?

When **System Protection** is turned on (which enables VSS), you can:

- ✅ **Create a restore point** manually
    
- 🔄 **Restore system files/settings** to an earlier point via System Restore
    
- 🛠️ **Configure restore settings** (e.g., how much space is used)
    
- 🧹 **Delete all restore points** for a drive
    

> Access this via:  
> `Control Panel > System > System Protection`  
> or  
> Run: `SystemPropertiesProtection`

---

## ⚠️ Security Note: Malware & VSS

Cybercriminals **know** about VSS. Many ransomware strains **delete shadow copies** as soon as they infect a system to **prevent recovery**.

They often run:

```
vssadmin delete shadows /all /quiet
```

This is why having **offline or off-site backups** is so important.

---
