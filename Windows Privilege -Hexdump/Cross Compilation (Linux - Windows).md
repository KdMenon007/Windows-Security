
---
### **1. On Compilation**

- **Compilation** is the process of translating source code into a **binary executable** that can run on a specific system.
    
    | source code ---> compilation ---> binary
    
- The compilation process varies based on the environment, which includes:
    
    - **Hardware Architecture** (e.g., x86, ARM)
        
    - **Operating System** (e.g., Linux, Windows)
        
    - **User-space programs and configurations** (e.g., libraries, compilers)
        

---

### **2. On Cross Compilation**

- **Cross-compilation** is a method of compiling code for a **different environment** (target platform) than the one you're working on.
    
- In the example here, we're compiling code on a **Linux machine** for a **Windows machine**.
    
- **Linux uses ELF (Executable and Linkable Format)**, while **Windows uses PE (Portable Executable)** format, which means we need to cross-compile to produce a binary compatible with Windows.
    

---

### **3. A Practical Example**

Let’s walk through a practical example of compiling a simple C program for Windows while on a Linux system.

#### **Step 1: Compile for Linux (ELF - Executable and Linkable Format)**

If you write a simple C program, like the one below:

```c
#include <stdio.h>

int main(void) {
  printf("Hello World!\n");
  return 0;
}
```

Compiling it with the usual GCC on Linux:

```bash
gcc hello.c -o hello
```

This results in an ELF binary (`hello`), which is **not executable on Windows**. 
->Running `file hello` would show:

```bash
hello: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 4.4.0, not stripped
```

#### **Step 2: Cross-Compile for Windows (PE -Portable Executable)**

To compile for Windows, you need to use a cross-compiler, specifically **`mingw-w64`** for Windows. First, install the necessary compiler for cross-compilation:

```bash
sudo pacman -S mingw-w64-gcc
```

Now, you can compile your C program using the `x86_64-w64-mingw32-g++` cross-compiler for 64-bit Windows systems:

```bash
x86_64-w64-mingw32-g++ hello.c -static -o hello.exe
```

- **`x86_64-w64-mingw32-g++`** is for 64-bit architecture.
    
- **`i686-w64-mingw32-gcc`** is for 32-bit Windows executables.
    

After running the command, you’ll have a Windows-compatible executable `hello.exe`. Running `file hello.exe` will show:

```bash
hello.exe: PE32+ executable (console) x86-64, for MS Windows, 20 sections
```

#### **Step 3: Transfer the Executable to Windows**

To transfer the executable to a Windows machine, you can use Netcat (`nc`).

On your **Linux machine** (acting as a server), run:

```bash
nc -lvnp 4321 < hello.exe
```

On the **Windows machine**, execute:

```bash
.\nc64.exe 192.168.122.1 4321 > hello.exe
```

This command will download the `hello.exe` file from the Linux machine to the Windows machine. You can now run the file on the Windows system.

---
