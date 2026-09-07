# Bandit Level 27

## Challenge

Good job getting a shell! Now hurry and grab the password for `bandit27`!

---

## Initial Thoughts

In this level, the challenge provided `bandit27-do` file is a setuid binary owned by `bandit27` and allows `bandit26` to execute it. Therefore, this challenge requires us to use this setuid binary to get `bandit27`'s password.

---

## Solution

### Step 1: Execute `bandit27-do` without arguments

First, I checked my current directory and listed its contents.

```bash
pwd
ls -lah
```

I found a file named `bandit27-do` and checked its file type.

```bash
file bandit27-do
```

It is the setuid binary that the challenge informed. Then, I execute `bandit27-do` without arguments to find out how to use it.

```bash
./bandit27-do
```

Output:

```text
Run a command as another user.
  Example: ./bandit27-do id
```

Now I know that `./bandit27-do` accepts a command as an argument and executes that command with its owner's privileges.

### Step 2: Use `./bandit27-do` to read the password

Next, I execute `./bandit27-do` with a command that reads the password file for `bandit27`.

```bash
./bandit27-do cat /etc/bandit_pass/bandit27
```

Instead of receiving a `Permission denied` error, I can read the file since `./bandit27-do` is a setuid binary owned by `bandit27`.

Therefore, the command `cat /etc/bandit_pass/bandit27` is executed with `bandit27`'s effective privileges, allowing the program to access the password file.

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
