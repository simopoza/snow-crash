# Level09 - Snow Crash Exploit

## Description

Upon logging in as `level09`, there are two files in the home directory:

```
-rwsr-sr-x 1 flag09 level09 7640 Mar  5  2016 level09
----r--r-- 1 flag09 level09   26 Mar  5  2016 token
```

- `level09`: SUID executable owned by `flag09`
- `token`: file readable only by `flag09`, but world-readable

Running the binary without arguments:

```bash
./level09
# Output: You need to provied only one arg.
```

Trying to read the `token` file:

```bash
./level09 token
# Output: tpmhr
cat token
# Output: f4kmm6p|=��p�n��DB�Du{���
```

The `token` file contains non-ASCII characters.

## Analysis

Testing with different arguments shows a pattern:

```bash
./level09 "0123456789"
# Output: 02468:<>@B

./level09 "abcdefghij"
# Output: acegikmoqs
```

Using `ltrace` reveals:

```
"You should not reverse this"
```

This suggests the output is obfuscated by adding the index position to each character in the input.

## Exploitation

To recover the original token, subtract the index from each character in the file. The following Python script does this:

```python
#!/usr/bin/python
import sys
i = -1
content = open("/home/user/level09/token").readlines()[0]
for c in content:
    i += 1
    try:
        sys.stdout.write(chr(ord(c) - i))
    except:
        pass
print "\n",
```

Save this script as `/tmp/reverse.py`, make it executable, and run it:

```bash
chmod +x /tmp/reverse.py
/tmp/reverse.py
# Output: f3iji1ju5yuevaus41q1afiuq
```

Use this password to switch to the `flag09` user and retrieve the flag:

```bash
su flag09
Password: f3iji1ju5yuevaus41q1afiuq
getflag
# Output: Check flag.Here is your token : s5cAJpM8ev6XHw998pRWG728z
```

## Mitigation

- Avoid using weak or reversible obfuscation for sensitive data.
- Use proper encryption and access controls.

## Flag