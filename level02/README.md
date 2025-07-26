# Snow Crash Level02 Walkthrough

## Steps

1. **Copy the `level02.pcap` file from the VM to your local machine**

   On your local machine, run:
   ```bash
   scp -P 4242 level02@192.168.11.101:/home/user/level02/level02.pcap .
   ```
   Replace the IP address and port if needed.

2. **Install Wireshark**

   On your local machine:
   ```bash
   sudo apt update
   sudo apt install wireshark
   ```

3. **Analyze the capture file**

   - Open `level02.pcap` with Wireshark.
   - Look for packets containing the word **Password**.
   - Right-click the relevant packet and select **Follow TCP Stream**.

4. **Extract the password**

   - At first glance, the password appears as:  
     ```
     ft_wandr...NDRel.L0L
     ```
   - Switch the view mode to **Hex Dump** in the TCP Stream window.
   - Notice that some characters are followed by a dot, indicating non-printable characters (specifically, the `0x7f` DEL character).
   - Remove all characters followed by a dot to get the actual password:  
     ```
     ft_waNDReL0L
     ```

5. **Switch to the `flag02` user on the VM**

   On the VM, run:
   ```bash
   su flag02
   ```
   Enter the password: `ft_waNDReL0L`

6. **Get the flag for the next level**

   Run:
   ```bash
   getflag
   ```
   The output will be:
   ```
   Check flag.Here is your token : kooda2puivaav1idi4f57q8iq
   ```

---

**Summary:**  
You copied the packet capture file to your local machine, used Wireshark to analyze it, extracted the correct password by removing control characters, switched users, and retrieved the flag for