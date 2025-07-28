# Level13 - SnowCrash

## Objective
Unlock the flag for level13 by bypassing a user ID check in the program `level13`.

---

## Overview

The program `level13` performs a check on the effective user ID (UID) before revealing the flag. If the UID does not match the expected value, the program exits and denies access.

---

## Approach

To obtain the flag, we used `gdb` (GNU Debugger) to manipulate the program's execution at runtime:

1. **Start GDB with the binary:**

   ```bash
   gdb ./level13
Disassemble the main function:

gdb
disassemble main
We observed that the program compares the UID against a specific value (0x1092 or decimal 4242) at instruction address 0x0804859a.

Set a breakpoint at the UID comparison:

gdb
break *0x0804859a
run
At the breakpoint, examine the current UID stored in register eax:

gdb
display/d $eax
It showed 2013 (our actual UID).

Modify the UID value to the expected one (4242 decimal):

gdb
set $eax=0x1092
Continue the program:

gdb
continue
The program then prints the token (flag):

your token is 2A31L79asukciNyi8uppkEuSx