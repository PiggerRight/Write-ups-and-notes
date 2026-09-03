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
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
```

This file defines a system-wide cron job that executes `/usr/bin/cronjob_bandit24.sh` as `bandit24` every minute. The `&> /dev/null` redirects both `stdout` and `stderr` to `/dev/null`, meaning that the script's normal output and error messages are discarded.

Therefore, I decided to examine the script directly.

### Step 3: Examine `/usr/bin/cronjob_bandit24.sh`

I examined the script using `cat`:

```bash
cat /usr/bin/cronjob_bandit23.sh
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

The first command assigns the output of `whoami` to the variable `myname`:

```bash
myname=$(whoami)
```

Since the cron job executes the script as `bandit23`, the value of `myname` is `bandit23`.

Next, the script creates the variable `mytarget` using a pipeline:

```bash
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)
```

The `echo` command produces: `I am user bandit23`

This output is passed to `md5sum`, which calculates its MD5 hash. The output of `md5sum` has the format: `[md5sum_text] -`

The cut command extracts the first space-separated field, which is `[md5sum_text]`.

Therefore, I replaced `$myname` with `bandit23` and ran the same pipeline:

```bash
echo I am user bandit23 | md5sum | cut -d ' ' -f 1
```

Output:

```text
8ca319486bfbbc3663ea0fbe81326349
```

Now, I know that `mytarget="8ca319486bfbbc3663ea0fbe81326349"`.

Then, the script print something out. The final command in the script is: 

```bash
cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

After substituting the variables, it becomes:

```bash
cat /etc/bandit_pass/bandit23 > /tmp/8ca319486bfbbc3663ea0fbe81326349
```

Since the script runs as `bandit23`, it can read `/etc/bandit_pass/bandit23` and write its contents to the file in `/tmp/`.

Therefore, I tried to read the generated file:

```bash
cat /tmp/8ca319486bfbbc3663ea0fbe81326349
```

Finally, I retrieved the password for the next level.

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



## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
