### PowerShell Overview

PowerShell is a command-line shell and scripting language built on the .NET framework, introduced with Windows 7 but also available for earlier versions. It's often considered the Windows equivalent of Bash (the default shell in Linux).

### Key Points:

- **Cmdlets:** PowerShell commands are called cmdlets. They follow the pattern `Verb-Noun` (e.g., `Get-Help`, `Set-ExecutionPolicy`).
    
- **Get Help:** To get help on using any cmdlet, use `Get-Help` followed by the cmdlet name or topic.
    
    - Example:
        
        - `Get-Help echo` – Shows help for the `echo` cmdlet.
            
        - `Get-Help get-command` – Shows help for the `get-command` cmdlet, which lists available cmdlets.
            

### Common Cmdlets:

- **Get-Help:** Provides detailed documentation for a cmdlet.
    
- **Get-Command:** Lists all available cmdlets.
    
- **Get-Process:** Displays running processes.
    
- **Set-ExecutionPolicy:** Controls the script execution policy (important for running scripts).
    

PowerShell is very powerful and can be used for everything from administrative tasks to advanced penetration testing, as it allows almost full access to Windows systems and settings.

### PowerShell Variables & Script Execution Policies

#### **Variables in PowerShell**

In PowerShell, you can declare a variable by prefixing it with a dollar sign (`$`), followed by the variable name. Here's an example:

```powershell
$test = "something"
```

This assigns the string "something" to the `$test` variable. You can use this variable later in your script or commands.

#### **PowerShell Script Execution Policies**

By default, PowerShell restricts the execution of scripts for security reasons. The **execution policy** defines which scripts can be run on your system. Here are the available policies:

1. **Restricted**:
    
    - Default policy.
        
    - Does not allow any scripts to run.
        
2. **AllSigned**:
    
    - Only runs scripts that are signed with a trusted digital signature.
        
    - If you attempt to run a script from an unknown publisher, PowerShell will ask if you trust the publisher.
        
3. **RemoteSigned**:
    
    - Does not run scripts downloaded from the Internet unless they are signed.
        
    - Local scripts can be executed without any restrictions.
        
4. **Unrestricted**:
    
    - Ignores digital signature checks but prompts the user before running scripts downloaded from the Internet.
        

#### **Checking the Current Execution Policy**

To check the current execution policy on your system, you can use the following command:

```powershell
Get-ExecutionPolicy
```

#### **Changing the Execution Policy**

To change the execution policy and allow scripts to run, you can use the following command:

```powershell
Set-ExecutionPolicy Unrestricted
```

- To allow scripts to run without being prompted, you might choose `Unrestricted` or `RemoteSigned`, depending on your needs.
    
- Remember that you may need to run PowerShell as **Administrator** to change the execution policy.
    

#### **Running a Script**

Once the execution policy is set correctly, you can run a PowerShell script like `myscript.ps1` by navigating to its directory and executing:

```powershell
.\myscript.ps1
```

### References

- **Nishang (PowerShell scripts for penetration testing)**: [Nishang GitHub](https://github.com/samratashok/nishang)
    
- **Video on PowerShell execution policy**: [YouTube](https://www.youtube.com/watch?v=czJrXiLs0wM)
    

# Command Line

The **command line** is a text-based way to control your computer. Instead of clicking buttons and using a mouse, you type commands to do tasks like opening files, managing systems, or running programs.

### Difference Between CMD and PowerShell

**1. CMD (Command Prompt)**

- **Older Tool**: CMD is a simple, old-school command-line tool in Windows.
    
- **Basic Commands**: Used for simple tasks like navigating files and running basic commands.
    
- **Text-Based Output**: It shows results as text.
    

**Example CMD Command**:

```cmd
dir
```

**2. PowerShell**

- **Newer Tool**: PowerShell is a more powerful, modern command-line tool.
    
- **Advanced Scripting**: It’s designed for automating tasks, running scripts, and managing systems more easily.
    
- **Object-Based Output**: PowerShell works with objects (not just text), making it more flexible.
    

**Example PowerShell Command**:

```powershell
Get-Process
```

### **Key Differences**:

|**Feature**|**CMD**|**PowerShell**|
|---|---|---|
|**Purpose**|Simple tasks|Advanced scripting and automation|
|**Commands**|Basic commands|Complex commands (cmdlets)|
|**Output**|Text|Objects (easier to work with)|
|**Use**|Simple tasks like file management|Automating, managing systems|

### **When to Use Each**:

- **Use CMD** for basic tasks like file management.
    
- **Use PowerShell** for advanced scripting, automation, and system management.
    

In short, **CMD** is for quick, basic tasks, while **PowerShell** is for more powerful and automated tasks.