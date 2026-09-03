# Bandit Level 24

## Challenge

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in `/etc/cron.d/` for the configuration and see what command is being executed.

---

## Initial Thoughts

In this level, the challenge again hints that we should look in `/etc/cron.d/` to find the cron jobs being executed. Therefore, I will start by examining `/etc/cron.d/`.

---

## Solution

### Step 1: Examine `/etc/cron.d/`

`/etc/cron.d/` is commonly used to store system-wide cron job definitions.

First, I checked its contents:

```bash
ls -lah /etc/cron.d/
```

There were many files, but the most noticable one was `cronjob_bandit24`. Since the challenge asks us to find the cron jobs being executed, I decided to examine it. The file `cronjob_bandit23` was used in the previous level.

### Step 2: Examine `/etc/cron.d/cronjob_bandit24`

I used `file` to check its file type:

```bash
file /etc/cron.d/cronjob_bandit24
```

It is an ASCII text file, so I used `cat` to examine its contents.

```bash
cat /etc/cron.d/cronjob_bandit24
```

Output:

```text
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
```

This file defines a system-wide cron job that executes `/usr/bin/cronjob_bandit24.sh` as `bandit24`. This cron job executes the script after reboot and every minute. The `&> /dev/null` redirects both `stdout` and `stderr` to `/dev/null`, meaning that the script's normal output and error messages are discarded.

Therefore, I decided to examine the script directly.

### Step 3: Examine `/usr/bin/cronjob_bandit24.sh`

I examined the script using `cat`:

```bash
cat /usr/bin/cronjob_bandit24.sh
```

Output:

```bash
#!/bin/bash

shopt -s nullglob

myname=$(whoami)

cd /var/spool/"$myname"/foo || exit
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." ] && [ "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" "./$i")"
        if [ "${owner}" = "bandit23" ] && [ -f "$i" ]; then
            timeout -s 9 60 "./$i"
        fi
        rm -rf "./$i"
    fi
done
```

The first command uses `shopt` to enable `nullglob` option of the Bash shell.

The next command assigns the output of `whoami` to the variable `myname`:

```bash
myname=$(whoami)
```

Since the cron job executes the script as `bandit24`, the value of `myname` is `bandit24`.

Next, the script changes the working directory:

```bash
cd /var/spool/"$myname"/foo || exit
```

The script will be terminated if `cd` fails. Because the value of `myname` is `bandit24`, the working directory now is `/var/spool/bandit24/foo`.

Then, the script prints the purpose of the next loop statement, which is executing and deleting all scripts in `/var/spool/$myname/foo`.

The loop statement iterates through all files and directories (hidden ones are included) in the working directory. The `if` statement sercurely checks to make sure that no `i` matches `.` or `..`.

```bash
for i in * .*;
do
    if [ "$i" != "." ] && [ "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" "./$i")"
        if [ "${owner}" = "bandit23" ] && [ -f "$i" ]; then
            timeout -s 9 60 "./$i"
        fi
        rm -rf "./$i"
    fi
done
```

In short, with each `i`, the script:

- inspects the owner of `i` using `stat --format "%U" "./$i"` and assigns the owner username to `owner`
- uses `timeout -s 9 60 "./$i"` to execute `i` that is a regular file owned by `bandit23`
- forcely and recursively removes `i`

I realized that this script is executed with `bandit24`s' privilege and able to execute `bandit23`'s script located in `/var/spool/bandit24/foo`. Therefore, I though I can try to inject to `/var/spool/bandit24/foo` a script that secretly read `bandit24`'s password.

To make sure this decision is possible, I checked the permission of `/var/spool/bandit24/foo`:

```bash
stat /var/spool/bandit24/foo
```

Its permission is `drwxrwx-wx`, which is a directory allowing others to create and execute scripts.

### Step 4: Inject script to `/var/spool/bandit24/foo`

I created a temporary directory `/tmp/Piggeright`. Remember to allow others can modify the directory because the script will run with privilege of `bandit24`.

```bash
mkdir /tmp/Piggeright
chmod 777 /tmp/Piggeright
```

I decided to use this directory to store the stolen password.

Then I write the script:

```bash
#!/bin/bash
cat "/etc/bandit_pass/bandit24" > "/tmp/Piggeright/password.txt"
```



Then, I waited for 1 minute to let cron do its job. 

# Remember to check preset perm when created of file and dir and how to check the setting rather than mannually trying
# Vim in linux

## Key Takeaways

### shopt

`shopt` is a Bash builtin command used to view and change Bash shell options. It isn't a standalone program like `ls` or `cat`.

1. Display the current state of many Bash options

```bash
shopt
```

```text
autocd          off
cdspell         off
checkjobs       off
nullglob        off
...
```

2. Enable/Disable an option

```bash
shopt -s nullglob
```

```bash
shopt -u nullglob
```

- `-s` → set/enable
- `-u` → unset/disable
- `nullglob` → the option being enabled/disabled

3. Check one option

```bash
shopt nullglob
```

4. Options

- `nullglob` — causes unmatched wildcard patterns to expand to nothing instead of remaining as literal text.

### `&&` and `||` in control flow

- `[command_1] && [command_2]` — if `[command_1]` succeeds, `[command_2]` executes.
- `[command_1] || [command_2]` — if `[command_1]` fails, `[command_2]` executes.
- `[command_1] && [command_2] || [command_3]` — `[command_2]` executes if `[command_1]` succeeds; `[command_3]` executes if either `[command_1]` or `[command_2]` fails.

### stat

Display detailed information about a file or directory.

Syntax:

```bash
stat [options] [file]
```

Output:
```text
  File: file.txt
  Size: 123        Blocks: 8          IO Block: 4096   regular file
Device: ...        Inode: 123456      Links: 1
Access: (0644/-rw-r--r--)  Uid: (1000/user)   Gid: (1000/user)
Access: 2026-08-29 10:00:00
Modify: 2026-08-29 09:30:00
Change: 2026-08-29 09:30:00
```

`[options]`

- `--format "[info]"` — get a specific information.

`[info]`

- `%U` — file owner
- `%u` — numeric UID
- `%A` — permission
- `%a` — permission number

### ls

`[options]`

- `-d` — treat the directory itself as the item to display, rather than listing what's inside it.

### File test operator

- `[ -f file.txt ]` — check whether the path exists and is a regular file.
- `[ -d mydir ]` — check whether the path exists and it is a directory.

### `timeout`

Execute a command or script and automatically terminates it if it runs longer than a specified time limit.

Syntax:

```bash
timeout [options] [duration] [command/script]
```

`[duration]`

- `s` — seconds (default)
- `m` — minutes
- `h` — hours
- `d` — days
- Example: `10` or `10s` (10 seconds), `5m` (5 minutes)

`[options]`

- `-k [duration]` — sends `SIGKILL` after extra delay if `SIGTERM` fails
- `-s [signal]` — specifies initial signal

`[signal]`

- `SIGTERM` or `15` — (default signal) requests a graceful termination
- `SIGINT` or `2` — simulates pressing `Ctrl`+`C` in the terminal
- `SIGHUP` or `1` — simulates a lost terminal session or disconnected modem
- `SIGKILL` or `9` — immediately terminates the process at the OS level

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
