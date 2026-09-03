# Bandit Level 23

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

There were many files, but the most noticable one was `cronjob_bandit23`. Since the challenge asks us to find the cron jobs being executed, I decided to examine it. The file `cronjob_bandit22` was used in the previous level.

### Step 2: Examine `/etc/cron.d/cronjob_bandit23`

I used `file` to check its file type:

```bash
file /etc/cron.d/cronjob_bandit23
```

It is an ASCII text file, so I used `cat` to examine its contents.

```bash
cat /etc/cron.d/cronjob_bandit23
```

Output:

```text
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
```

This file defines a system-wide cron job that executes `/usr/bin/cronjob_bandit23.sh` as `bandit23`. The first entry runs the script when the system starts, while the second entry runs it every minute. The `&> /dev/null` redirects both `stdout` and `stderr` to `/dev/null`, meaning that the script's normal output and error messages are discarded.

Therefore, I decided to examine the script directly.

### Step 3: Examine `/usr/bin/cronjob_bandit23.sh`

I examined the script using `cat`:

```bash
cat /usr/bin/cronjob_bandit23.sh
```

Output:

```text
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
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

Now, I know that `mytarget='8ca319486bfbbc3663ea0fbe81326349'`.

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

### md5sum

**MD5** is a hash function that converts data of any size into a fixed-size 128-bit hash, usually displayed as 32 hexadecimal characters.

**MD5** is considered cryptographically broken and should not be used for security-sensitive purposes. However, it can still be encountered in older systems, integrity checks,...

In Linux, `md5sum` is a command used to calculate the MD5 hash of a file or standard input.

Syntax:

```bash
md5sum [files...]
```

Output:

```text
[md5sum_text] [source]
```

### cut

Extract specific parts of each line of text by character position, byte position, or fields separated by a delimiter.

Syntax:

```bash
cut [options] [file]
```

`[options]`

- `-c [n]-[m]` — extracts characters `n` through `m` from every line.
- `-d '[delimiter]' -f [field(s)]` — extracts fields using a delimiter.

`[field(s)]`

- `[n]` — field `n`
- `[n],[m]` — field `n` and `m`
- `[n]-[m]` — field `n` to `m`

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
