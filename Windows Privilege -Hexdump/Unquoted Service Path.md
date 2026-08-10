
---
## **Understanding the Unquoted Service Path Vulnerability**

When Windows runs a service, it looks for the program (binary) it should execute. If the **==service path contains spaces and is not enclosed in quotes==**, Windows may mistakenly try to execute the wrong file if an attacker places a malicious file in certain locations.

### **Example Service Without Quotes**

Let's say we have a service called `SimpleService` that is configured like this:

```
C:\Users\Quickemu\Downloads\simpleService.exe
```

If we change the service path to include folders with spaces **but don’t use quotes**, Windows might not correctly understand which file to execute.

---

## **Step 1: Creating a Vulnerable Service Path**

To demonstrate the issue, we move the service binary into a new folder that has spaces in its name:

```powershell
mkdir "Example Directory"
cd "Example Directory"
mkdir "Another Directory"
cd "Another Directory"
move ..\..\simpleService.exe .
or 
move "C:\Users\kiran\simpleService.exe" "C:\Users\kiran\Downloads\Example Directory\Another Directory\"
```

Now, we update the service to use this new path:

```powershell
sc.exe config SimpleService binpath="C:\Users\Quickemu\Downloads\Example Directory\Another Directory\simpleService.exe"
```

---

## **Step 2: What Happens When the Service Starts?**

When the service starts, Windows **does not** immediately run:

```
C:\Users\Quickemu\Downloads\Example Directory\Another Directory\simpleService.exe
```

Instead, because of the missing quotes, Windows will check for the following files **in order**:

1. `C:\Users\Quickemu\Downloads\Example.exe`
    
2. `C:\Users\Quickemu\Downloads\Example Directory\Another.exe`
    
3. `C:\Users\Quickemu\Downloads\Example Directory\Another Directory\simpleService.exe` (finally the correct file)
    

If an attacker can **create a fake file** (like `Example.exe`) in a folder they can write to, Windows might execute it instead of the real service binary.

---

## **Step 3: Exploiting the Vulnerability**

If the attacker can place a malicious file **where Windows looks first**, they can execute their code with the service’s privileges.

For example, if the service runs as `LocalSystem`, the attacker's file will run **with system-level privileges**.

### **Example Attack**

An attacker drops a fake program (`malicious.exe`) in one of the searched locations:

```powershell
move malicious.exe "Example Directory\Example.exe"
```

Now, when the service starts, **Windows runs `Example.exe` instead of the real service**!

---

## **Step 4: How to Find This Vulnerability**

We can use tools to find unquoted service paths. One popular tool is **winPEAS**:

```powershell
.\winPEAS.exe quiet servicesinfo
```

Look for messages like:

```
No quotes and Space detected
```

Another way is using the command line:

```powershell
wmic service get name,displayname,pathname,startmode | findstr /i "C:\Program"
```

This shows paths without quotes.

---

## **Step 5: How to Fix It**

### **Option 1: Fix Using RegEdit**

1. Open `RegEdit` (Windows Registry Editor).
    
2. Go to:
    
    ```
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\SimpleService
    ```
    
3. Find `ImagePath`.
    
4. **Add quotes** around the path:
    
    ```
    "C:\Users\Quickemu\Downloads\Example Directory\Another\simpleService.exe"
    ```
    

### **Option 2: Fix Using Command Line**

Instead of editing the registry, use `sc.exe` to fix the path:

```powershell
sc.exe config SimpleService binpath="\"C:\Users\Quickemu\Downloads\Example Directory\Another Directory\simpleService.exe\""
```

---

## **Final Thoughts**

- **If a service path has spaces and no quotes, it can be hijacked** by placing a malicious executable in an earlier searched location.
    
- **Always ensure service paths are quoted** to prevent unintended execution.
    
- **Regularly check your system** for unquoted service paths using tools like `winPEAS` or `WMIC`.
    
