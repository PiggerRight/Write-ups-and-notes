# Bandit Level 26

## Challenge

Logging in to `bandit26` from `bandit25` should be fairly easy… The shell for user `bandit26` is not `/bin/bash`, but something else. Find out what it is, how it works and how to break out of it.

---

## Initial Thoughts

In this level, the challenge already provided the credential for `bandit26` in `bandit25`, but there is a problem with `bandit26`'s shell configuration. Therefore, I decided to start by investigating the shell configuration of `bandit26`.

---

## Solution

### Step 1: Try to log in `bandit26` as usual

First, I took a look at the current home directory of `bandit25`:

```bash
pwd
ls -lah
```

I saw a file named `bandit26.sshkey`. I then used `file` and `cat` to examine its contents:

```bash
file bandit26.sshkey
```

Output:

```text
bandit26.sshkey: OpenSSH private key
```

```bash
cat bandit26.sshkey
```

Output:

```text
-----BEGIN OPENSSH PRIVATE KEY-----
...
-----END OPENSSH PRIVATE KEY-----
```

This is a private key that can be used to log in as `bandit26`.

I then saved the private key on my local machine as `private.key` and tried to log in:

```bash
ssh -p 2220 -i private.key bandit26@bandit.labs.overthewire.org
```

However, the connection was closed immediately. Since the challenge mentioned that there was a problem with `bandit26`'s shell configuration, I decided to investigate it.

### Step 2: Investigate `bandit26`'s shell configuration

I logged in as `bandit25` again and investigated `/etc/passwd` for `bandit26`'s information:

```bash
grep bandit26 /etc/passwd
```

Output:

```text
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```

The last field of `/etc/passwd` specifies the user's login shell. Instead of the usual `/bin/bash`, bandit26 uses `/usr/bin/showtext`.

I then examined this file:

```bash
file /usr/bin/showtext
cat /usr/bin/showtext
```

This is a shell script:

```bash
#!/bin/sh

export TERM=linux

exec more ~/text.txt
exit 0
```

The important line is `exec more ~/text.txt`. `exec` replaces the current shell process with the `more` process. Therefore, after logging in, instead of receiving a normal shell, `bandit26` runs more to display `~/text.txt`.

Because `more` eventually finishes, the login session also ends.

I noticed that I can use `more` to interact with the system rather than being logged out immediately.

### Step 3: Use `more` to interact with the system

I used the private key again to log in as `bandit26`, but first reduced the size of my terminal so that `more` would become interactive.

At first, I tried using `![command]` to see whether I could execute commands through `more`:

```bash
!pwd
```

However, instead of giving me the output of `pwd`, it resulted in another more session.

This happens because `more` uses a shell to execute commands entered through `![command]`. In this environment, the shell available to the process is `/usr/bin/showtext`, which itself runs `more` again, but `more` does not support any methods to change the shell executable.

Therefore, I looked for another way to interact with the system through `more`.

I used:

```bash
v
```

This opened the displayed text in Vim.

### Step 3: Use Vim to interact with the system

Now, Vim is different from `more`. Vim provides a configurable `shell` option that determines which shell it uses for external shell operations. I first checked the current setting of Vim:

```bash
:set shell?
```

Output:

```bash
shell=/usr/bin/showtext
```

This is why if I use `:shell` to open a shell in vim, it will lead back to `showtext` and then to `more`.

I changed Vim's `shell` option to `/bin/bash`:

```bash
:set shell=/bin/bash
```

Then I started an interactive shell:

```bash
:shell
```

Since Vim's `shell` option is now `/bin/bash`, this started an interactive Bash shell.

The Bash shell still runs with the privileges of `bandit26`, because child processes normally inherit the user identity and privileges of their parent process (vim and `more`).

Finally, I retrived the password:

```bash
cat /etc/bandit_pass/bandit26
```

## Key Takeaways

### Must-know Files

  - `/etc/passwd` — stores user account information, including which login shell executable is assigned to each user.
  - `/bin/bash` — the Bash executable. It is not a configuration file; it is the program that implements the Bash shell.
  - `/etc/profile` — system-wide configuration file commonly read by **login shells**.
  - `/etc/bash.bashrc` — system-wide configuration file commonly read by **interactive Bash shells** on Debian/Ubuntu-based systems.
  - `~/.profile` — user-specific startup file commonly read by a **login shell**. Often used to set up the user's environment.
  - `~/.bashrc` — user-specific startup file read by an **interactive Bash shell**. Commonly used to configure the interactive Bash environment.
  - `~/.bash_logout` — user-specific file executed when an **interactive login Bash shell** exits.

### more

Is a pager, displays long text one screen at a time.

```bash
more [file]
```

**How it works**:

If the text fits on the screen:

```text
file.txt → more → prints everything → exits
```

But if the text is longer than the terminal:

```text
                    ┌─────────────────┐
                    │ line 1          │
                    │ line 2          │
 file.txt → more →  │ line 3          │ → waits for command
                    │ ...             │
                    │ --More--        │
                    └─────────────────┘
```

Now `more` becomes interactive.

**Commands**:

- `Space` — next screen
- `Enter` — next line
- `q` — quit
- `v` — open the current file in an editor (Vim/when supported)
- `!command` — execute a command using executable from `$SHELL` (when supported)

### vim

**Modes**

1. Normal mode:
  - Vim's Normal mode can be used to run shell commands using Vim's configured shell, but you enter them through Vim's Ex command line:
    - `:![command]` → Vim asks the OS to run `[command]` through Vim's configured shell, with the same privileges as Vim.
    - `:shell` → starts an interactive shell using Vim's configured shell, with the same privileges as Vim. Vim's shell option is initially set based on `$SHELL` and can be changed internally with `:set shell=[path]`. To check current `shell` option, use `:set shell=?`.

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
