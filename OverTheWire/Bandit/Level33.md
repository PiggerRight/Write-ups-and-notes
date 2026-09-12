# Bandit Level 33

## Challenge

After all this git stuff, it’s time for another escape. Good luck!

---

## Initial Thoughts

In this level, the challenge doesn't give any hints. When I log in as `bandit32`, it invokes a shell with strange behavior. Therefore, I need to find a way to execute a normal Bash shell.

---

## Solution

### Step 1: Examine what is going on

At first, after logging in, I see that a strange program is executed instead of the normal `/bin/bash`. Therefore, I decided to investigate why.

I logged in as another user and looked up the login shell of `bandit32`:

```bash
grep "bandit32" /etc/passwd
```

Output:

```text
bandit32:x:11032:11032:bandit level 32:/home/bandit32:/home/bandit32/uppershell
```

The last field specifies the login shell of `bandit32`. Instead of `/bin/bash`, it is `/home/bandit32/uppershell`.

I then used `stat` to investigate this file:

```bash
stat /home/bandit32/uppershell
```

I found that this file has the SetUID permission and is owned by `bandit33`.

This gives us a better picture of what is happening:

Login as `bandit32`
       ↓
`/home/bandit32/uppershell`
       ↓
SetUID → effective UID = `bandit33`
       ↓
restricted uppercase shell with `bandit33`'s previlege

### Step 2: Escape the strange shell

At this point, I logged in as `bandit32` and tried some basic commands such as: `ls`, `whoami`,... The shell seems to convert commands to uppercase before executing them. Therefore, I need to find a way to execute a command that is not affected by this behavior.

I remembered the `$0` shell parameter. `$0` contains the command/name/path that was used to invoke the current shell or script. In this case, `$0` refers to the name by which the current Bash shell was invoked. If I enter:

```bash
$0
```

the shell first expands `$0` and then tries to execute the resulting command. Surprisingly, this causes another Bash shell to be executed, escaping the uppercase-command behavior.

Then, I use `whoami` to confirm current privilege and it is `bandit33`, since the new Bash shell is executed under `uppershell`. Therfore, it still has the effective privileges inherited from the SetUID `uppershell` process.

Finally, I retrieved the password:

```bash
cat /etc/bandit_pass/bandit33
```

## Key Takeaways

### Script Arguments

  - `$0` — contains the command/name/path that was used to invoke the current shell or script.

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
