# Level00

## Objective
Find the password to access the `flag00` user account by identifying files that only the `flag00` user has access to.

## Steps Taken

1. **Find files owned by flag00:**
   ```bash
   find / -user flag00 2>/dev/null

This command lists files owned by flag00. It returned:

/usr/sbin/john
/rofs/usr/sbin/john

2. Inspect file content:

cat /usr/sbin/john

Output:

cdiiddwpgswtgt

3. Decrypt the string:

The string appears to be encrypted using a Caesar cipher (ROT13-like). After testing with Caesar cipher online tools like cryptii or dcode, the ROT15 shift reveals:

nottoohardhere

4. Switch to flag00 user:

su flag00
Password: nottoohardhere

5. Get the flag (password for level01):

getflag

Output:

Check flag.Here is your token : x24ti5gi3x0ol2eh4esiuxias

