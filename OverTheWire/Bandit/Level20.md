# Bandit Level 20

## Challenge

To gain access to the next level, you should use the setuid binary in the `home` directory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (`/etc/bandit_pass`), after you have used the setuid binary.

---

## Initial Thoughts

In this level, the challenge introduces setuid binary, which allows a program to run with the effective user ID (EUID) of the file owner instead of the user who executed it.

I found that the provided `bandit20-do` file is a setuid binary owned by `bandit20`. Therefore, when I execute it, the program runs with the effective privileges of `bandit20`, allowing it to perform actions that `bandit20` has permission to perform.

This means I can use `bandit20-do` to read `/etc/bandit_pass/bandit20`.

---

## Solution

### Step 1: Execute `bandit20-do` without arguments

First, I checked my current directory and listed its contents.

```bash
pwd
ls -lah
```

I found a file named `bandit20-do` and checked its file type.

```bash
file bandit20-do
```

It is the setuid binary that the challenge informed. Then, I execute `bandit20-do` without arguments to find out how to use it.

```bash
./bandit20-do
```

Output:

```text
Run a command as another user.
  Example: ./bandit20-do whoami
```

Now I know that `bandit20-do` accepts a command as an argument and executes that command with its owner's privileges.

### Step 2: Use `bandit20-do` to read the password

Next, I execute `bandit20-do` with a command that reads the password file for `bandit20`.

```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```

Instead of receiving a `Permission denied` error, I can read the file since `bandit20-do` is a setuid binary owned by `bandit20`.

Therefore, the command `cat /etc/bandit_pass/bandit20` is executed with `bandit20`'s effective privileges, allowing the program to access the password file.

## Key Takeaways

### Commands

**Commands** are instructions given by the user to the shell to perform specific actions. When a command refers to an external program, the shell finds and executes that program.

Example:

```bash
cat file1.txt file2.txt file3.txt
```

The shell interprets the command as:

```text
cat file1.txt file2.txt file3.txt
│   │         │         │
│   └─────────┴─────────┴── arguments
└────────────────────────── program/command
```

The shell searches for the `cat` program in the directories listed in `$PATH` and executes it. The files `file1.txt`, `file2.txt`, and `file3.txt` are passed to `cat` as arguments.

If the command does not contain a path, the shell searches for the program in `$PATH`:

```bash
cat file.txt
```

We can also specify the program's path explicitly:

```bash
./program
```

Here, `./` specifies that `program` is located in the current directory.

Or:

```bash
/usr/bin/cat file.txt
```

Here, `/usr/bin/cat` is an absolute path to the program.

Therefore, when executing a program, we can use a relative path, an absolute path, or no path at all if the program can be found through `$PATH`.

### Setuid

**Setuid** (Set User ID) is a special permission bit for executable files in Linux. When a setuid executable is run, the program runs with the **effective user ID (EUID) of the file owner** instead of the user who executed it.

For example:

```text
-rwsr-xr-x 1 alice alice program
```

The `s` in the owner's execute permission indicates that the **setuid bit** is enabled.

If `bob` executes the program:

```bash
./program
```

the process has:

```text
Real user ID (UID):      bob
Effective user ID (EUID): alice
```

Now, the program can perform actions with `alice`'s effective privileges.

Setuid itself does **not** define what actions the user can perform. It only causes the executable to run with the file owner's effective privileges. The **program's implementation** determines what can actually be done with those privileges.

For example, a setuid program can:

* Perform a fixed privileged action.
* Accept specific arguments and perform corresponding actions.
* Accept a command from the user and execute it with the owner's effective privileges.

Therefore, a setuid program that allows arbitrary command execution can be dangerous if the file owner has higher privileges.

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
