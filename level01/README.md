# Snow Crash Level01 Walkthrough

## Steps

1. **Copy `/etc/passwd` from the VM to your local machine**

   On your local machine, run:
   ```bash
   scp level01@<VM_IP>:/etc/passwd .
   ```
   Replace `<VM_IP>` with your VM's IP address.

2. **Install John the Ripper**

   On your local machine:
   ```bash
   sudo apt update
   sudo apt install john
   ```

3. **Extract the hash for `flag01`**

   Find the line in `passwd` that looks like:
   ```
   flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash
   ```
   Save this line to a file called `pass`.

4. **Crack the password with John the Ripper**

   Run:
   ```bash
   john pass --show
   ```
   John will reveal the password, e.g.:
   ```
   flag01:abcdefg
   ```

5. **Switch to the `flag01` user on the VM**

   On the VM, run:
   ```bash
   su flag01
   ```
   Enter the password `abcdefg` when prompted.

6. **Get the flag for the next level**

   Run:
   ```bash
   getflag
   ```
   The output will be:
   ```
   Check flag.Here is your token : f2av5il02puano7naaf6adaaf
   ```

7. **Switch to the `level02` user**

   On the VM, run:
   ```bash
   su level02
   ```
   Use the password: `f2av5il02puano7naaf6adaaf`

---

**Summary:**  
You copied `/etc/passwd` to your local machine, used John the Ripper to crack the `flag01` password, switched users, retrieved the flag, and used it to access