
---
## **1. What are Scheduled Tasks?**

Scheduled Tasks are Windows programs or scripts that execute automatically based on specific **triggers**. Managed by the **Task Scheduler**, they are commonly used for:

- **Backups**
    
- **System maintenance**
    
- **Running scripts and automation**
    

### **⚠️ Why are they important for security?**

Since Scheduled Tasks execute arbitrary code, misconfigurations or improper permissions can make them a **privilege escalation vector**.

---

## **1.1 Understanding Scheduled Task Data**

Each task contains multiple attributes:

### **📌 General Information**

- **Name**
    
- **Path** (Task Scheduler Library folder)
    
- **Description**
    
- **Enabled/Disabled status**
    
- **Author**
    

**⚠️ Important:** Task names must be unique _within the same folder_, but can exist in different folders.

### **📌 Triggers (When the Task Runs)**

- **Time-based Triggers**: Runs at a set time (daily, weekly, monthly, etc.).
    
- **Event-based Triggers**: Runs on logon, system startup, or a specific event.
    
- **Custom Triggers**: Based on idle time, network connectivity, etc.
    

### **📌 Actions (What the Task Does)**

- **Executable Path / Command**
    
- **Arguments** (optional)
    
- **Working Directory**
    

### **📌 Conditions**

- **Idle Time** (Run only if the system is idle for X minutes).
    
- **Power Conditions** (Run only on AC power).
    
- **Network Conditions** (Run only when connected to a network).
    

### **📌 Settings**

- **Allow Run on Demand**
    
- **Restart on Failure**
    
- **Stop Task If Running Too Long**
    

### **📌 Security Settings**

- **Run as User** (Specifies the user account under which the task runs).
    
- **Run with Highest Privileges** (Executes with admin rights).
    
- **Group Access Permissions** (Defines which users/groups can modify or run the task).
    

### **📌 Execution Information**

- **Last Run Time** (When the task last executed).
    
- **Last Run Status** (Success or failure).
    
- **Next Run Time** (When the task is scheduled to run again).
    

---

## **2. Enumeration (Finding Scheduled Tasks)**

### **🔍 List all tasks**

```powershell
Get-ScheduledTask
schtasks /query
```

### **🔍 List tasks in a specific folder**

```powershell
Get-ScheduledTask | Where-Object {$_.TaskPath -eq "\Microsoft\Windows\Shell\"}
```

### **🔍 Get detailed task info**

```powershell
Get-ScheduledTask -TaskName "MyTask" | Get-ScheduledTaskInfo
schtasks /query /FO LIST /V
```

### **🔍 Extract binary path and arguments**

```powershell
(Get-ScheduledTask -TaskName "XblGameSaveTask").Actions
Get-ScheduledTask | ForEach-Object { $_.Actions }
```

### **🔍 Export a task’s XML configuration**

```powershell
Export-ScheduledTask -TaskName "XblGameSaveTask" -TaskPath "\Microsoft\XblGameSave\"
```

---

## **3. Creating and Deleting Tasks**

### **✅ Create a simple task**

This creates a task that runs **Notepad** when the user logs in:

```powershell
$action = New-ScheduledTaskAction -Execute "notepad.exe"
$trigger = New-ScheduledTaskTrigger -AtLogOn
Register-ScheduledTask -TaskName "MyTask" -Action $action -Trigger $trigger -User "computername\username"
```

### **❌ Delete a task**

```powershell
Unregister-ScheduledTask -TaskName "SimpleTask" -Confirm:$false
```

---

## **4. Exploitation (Privilege Escalation)**

### **🛠 Define an Exploitable Task**

This task executes a **malicious PowerShell script** (`test1.ps1`) **every minute for an entire year**, running as `SYSTEM`:

```powershell
$Action = New-ScheduledTaskAction -Execute "powershell.exe" -Argument "-File C:\Users\Quickemu\tasks\test1.ps1"
$Trigger = New-ScheduledTaskTrigger -Once -At (Get-Date) -RepetitionInterval (New-TimeSpan -Minutes 1) -RepetitionDuration (New-TimeSpan -Days 365)
$Principal = New-ScheduledTaskPrincipal -UserId "SYSTEM" -LogonType ServiceAccount -RunLevel Highest
```

### **🛠 Register the task**

```powershell
Register-ScheduledTask -TaskName "SimpleTask" -Action $Action -Trigger $Trigger -Principal $Principal
```

🚀 **This allows us to execute arbitrary code with SYSTEM privileges!**

### **❌ Clean up**

```powershell
Unregister-ScheduledTask -TaskName "SimpleTask" -Confirm:$false
```

---

## **5. References**

- 📖 [Task Scheduler Documentation](https://learn.microsoft.com/en-us/windows/win32/taskschd/task-scheduler-start-page)
    

---
