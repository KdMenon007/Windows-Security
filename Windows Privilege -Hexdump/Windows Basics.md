# Windows vs Linux

## Open Source vs Proprietary

- **Linux**: Open-source OS developed by Linus Torvalds (1991). Maintained by the open-source community. Linux distributions include:
    
    - Arch Linux
        
    - Ubuntu
        
    - Debian
        
- **Windows**: Proprietary OS by Microsoft. The kernel and user-space programs are integrated, so there are no Windows distributions.
    

## What is a Kernel?

1. The **kernel** is the core component of an operating system that manages hardware and system resources.
    
2. It facilitates communication between hardware and software, handling tasks such as process management, memory allocation, and device control.
    

## History

### Microsoft DOS

Before Windows, Microsoft developed **MS-DOS** (Disk Operating System):

- **1981**: MS-DOS 1.0
    
- **1994**: MS-DOS 6.22 (final version)
    

### Microsoft Windows

- **1985**: Windows 1.0
    
- **1995**: Windows 95
    
- **2001**: Windows XP
    
- **2009**: Windows 7
    
- **2021**: Windows 11
    

## Windows Setup (Quickemu)

### Download Windows 11

```sh
quickget windows 11
```

### Start Virtual Machine

```sh
quickemu --vm windows-11.conf --display spice
```

Here’s a simplified version of the information you provided:

### Windows Networks

**1. Windows Domain:**

- A Domain is a network structure where all computers are connected to a central server called a **Domain Controller**.
    
- The Domain Controller manages user authentication and security settings (e.g., password length, account disabling).
    
- Once logged into the domain, users can access any machine in the network using their domain credentials.
    
- To set up a Domain, you need at least one Windows Server for the Domain Controller.
    

**2. Active Directory:**

- Active Directory (AD) is used to maintain a central database of users and configurations in Windows.
    
- It’s used with Domain networks to manage users and security policies.
    

**3. Domain Controller:**

- A computer that manages domain security, including user authentication.
    
- Typically, there are at least two Domain Controllers for redundancy.
    

**4. Workgroup:**

- A **Workgroup** is a simpler, peer-to-peer network structure.
    
- In a Workgroup, each computer manages its own security settings (there’s no central Domain Controller).
    
- Workgroups are usually used in smaller networks (less than a dozen machines).
    
- In a Workgroup, users can see and share files, but there is no centralized control.
    

**5. User Privileges:**

- **System:** A special security account that can access domain accounts.
    
- **Administrator:** The highest privilege level for a user, usually used for system management.
    
- **Normal User:** Has fewer privileges compared to an Administrator.
    

### Key Concepts

**Registry:**

- A database in Windows that stores settings for the operating system and applications.
    

**SAM (Security Account Manager):**

- Stores local account information on the system.
    

**IIS (Internet Information Services):**

- A web server included in most versions of Windows, excluding home editions.
    

**Important Files:**

- **BAT files:** Windows equivalent of shell scripts.
    
- **DLL (Dynamic Link Library):** Contains code shared between programs to promote code reuse.
    
- **LIB files:** Static libraries linked at compile-time, unlike DLLs, which are linked during runtime.
    

### Common Tools and Commands:

- **Net user:** Adds a new user.
    
- **Driverquery:** Lists installed drivers, useful for finding potential vulnerabilities in the system.
    

This is a more streamlined version that keeps the main points intact! Let me know if you'd like more details on any part.