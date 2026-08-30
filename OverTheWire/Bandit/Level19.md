# Bandit Level 19

## Challenge

The password for the next level is stored in a file `readme` in the `home` directory. Unfortunately, someone has modified `.bashrc` to log you out when you log in with SSH.

---

## Initial Thoughts

In this level, the password is stored in the `readme` file, but the modified `.bashrc` automatically logs me out. After researches, I know that `.bashrc` is a startup file commonly read when an interactive Bash shell starts. Therefore, I need to read `readme` without starting an interactive Bash shell.

---

## Solution

### Step 1: Connect to Bandit server as `bandit18` normally

At first, I tried to log in as `bandit18` normally.

```bash
ssh -p 2220 bandit18@bandit.labs.overthewire.org
```

However, I was immediately logged out. Because SSH normally starts an interactive login shell for the remote user, `.bashrc` is read, and the modified `.bashrc` logs me out.

### Step 2: Connect to Bandit server as `bandit18` without establishing an interactive Bash shell

Instead of starting an interactive shell, I can provide a command for SSH to execute remotely:

```bash
ssh -p 2220 bandit18@bandit.labs.overthewire.org "cat readme"
```

SSH still authenticates me as `bandit18`, but instead of starting an interactive shell, it requests the remote system to execute the provided command directly. Therefore, the command is executed as `bandit18`, and its output is returned to my terminal, which is the password for next level.

## Key Takeaways

### Must-know Files

- `.profile` — startup file commonly read when a **login shell** starts, set up the environment for that user's login shell.
- `.bashrc` — startup file commonly read when an **interactive Bash shell** starts, set up the interactive Bash environment.
- `.bash_logout` — a file commonly executed when an **interactive login Bash shell** exits.

### ssh

**Notes**: If no command is provided, after the SSH authentication process, the remote machine normally starts an **interactive login shell**, allowing us to enter commands through the remote terminal (`ssh user@host` → **interactive login shell** → you type commands). We can also provide a command to SSH. In this case, SSH executes the command on the remote machine without starting an **interactive login shell** (ssh user@host "command" → execute command remotely → return output → exit).

Syntax:

```bash
ssh [options] [host_name] "[commands]"
```

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
