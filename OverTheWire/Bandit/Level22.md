# Bandit Level 22

## Challenge

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in `/etc/cron.d/` for the configuration and see what command is being executed.

---

## Initial Thoughts

In this level, the challenge mentions cron, a time-based job scheduler, and hints us to look in `/etc/cron.d/`. Therefore, I will start by examining `/etc/cron.d/`.

---

## Solution

### Step 1: Examine `/etc/cron.d/`

First, I checked the given path:

```bash
file /etc/cron.d/
```

The path points to a directory. `/etc/cron.d/` is commonly used to store system-wide cron job definitions. Then, I checked its content:

```bash
ls -lah /etc/cron.d/
```

There were many files, but the most noticable one was `cronjob_bandit22`. Since the challenge asks usto find the command being executed, and this is the cron job associated with the current level, I decided to examine it.

### Step 2: Examine `/etc/cron.d/cronjob_bandit22`

I investigated the file using `file`.

```bash
file /etc/cron.d/cronjob_bandit22
```

It is an ASCII text file, so I used `cat` to read its contents.

```bash
cat /etc/cron.d/cronjob_bandit22
```

Output:

```text
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

I can see that this cron job executes `/usr/bin/cronjob_bandit22.sh` as the user `bandit22`. The `&> /dev/null` redirects both stdout and stderr to `/dev/null`, meaning that both normal output and error messages are discarded.

The first entry runs the script when the system reboots, while the second entry runs it every minute.

### Step 3: Examine `/usr/bin/cronjob_bandit22.sh`

Since the script's output is discarded by the cron job, I wanted to examine what `/usr/bin/cronjob_bandit22.sh` actually does.

```bash
cat /usr/bin/cronjob_bandit22.sh
```

Output:

```text
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

This script first changes the permission of `/tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv` to `644`, which allows `others` to read it.

The script then reads `/etc/bandit_pass/bandit22` and redirects its contents to `/tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv`. Since the cron job executes the script as `bandit22`, the script has sufficient privileges to read `/etc/bandit_pass/bandit22`.

Therefore, I can simply read the file:

```bash
cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

This reveals the password for the next level.

## Key Takeaways

### Shell Redirection

- `&>` — redirect both `stdout` and `stderr` to a file, overwriting its previous contents. If the file doesn't exist, it is created.

### Cron

**Cron** is a daemon (background service) that automatically executes scheduled tasks.

Depending on the Linux distribution, the cron daemon can be started manually by:

```bash
cron
```

or:

```bash
crond
```

On most Linux systems, cron is already managed by the system's service manager, often `systemd`. If the daemon is already running, manually running another cron process may fail or behave differently depending on the system configuration.

**Crontab** (cron table) is a configuration file containing cron jobs.

**Cron job** defines:

- When a task should run
- Who the task runs as
- What command should be executed

Cron jobs are not only stored in users' personal crontabs. Their locations can vary depending on the Linux distribution.

```text
Cron
│
├── Personal crontabs
│
├── /etc/crontab
│
├── /etc/cron.d/
│
├── /etc/cron.hourly/
├── /etc/cron.daily/
├── /etc/cron.weekly/
└── /etc/cron.monthly/
```

**Personal crontab format**

A personal user's crontab does not include a `username` field. The task runs as the user who owns the crontab.

```text
┌──────── Minute (0–59)
│ ┌────── Hour (0–23)
│ │ ┌──── Day of month (1–31)
│ │ │ ┌── Month (1–12)
│ │ │ │ ┌ Day of week (0–7)
│ │ │ │ │
* * * * * command
```

**System-wide crontab format**

System-wide crontab includes a `username` field. The `username` specifies which user executes the command.

```text
┌──────── Minute (0–59)
│ ┌────── Hour (0–23)
│ │ ┌──── Day of month (1–31)
│ │ │ ┌── Month (1–12)
│ │ │ │ ┌ Day of week (0–7)
│ │ │ │ │
* * * * * username command
```

Examples:

```text
30 2 * * * command
```

> Run every day at 2:30 AM as the owner of the personal crontab.

```text
*/5 * * * * command
```

> Run every 5 minutes as the owner of the personal crontab.

```text
30 2 * * * username command
```

> Run the command every day at 2:30 AM as `username`.

Commands:

```text
crontab [options]
```

`[options]`

- `-l` — view the current user's personal cron jobs.
- `-e` — edit the current user's personal cron jobs.
- `-r` — remove the current user's personal cron jobs.

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
