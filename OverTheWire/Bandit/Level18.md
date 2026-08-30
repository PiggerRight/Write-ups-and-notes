# Bandit Level 18

## Challenge

There are 2 files in the `home` directory: `passwords.old` and `passwords.new`. The password for the next level is in `passwords.new` and is the only line that has been changed between `passwords.old` and `passwords.new`.

---

## Initial Thoughts

In this level, the challenge provides two files, and we need to find the only line that differs between them. Therefore, I decided to use diff to compare the files.

---

## Solution

### Step 1: Compare the files using `diff`

First, I checked my current directory and listed its contents.

```bash
pwd
ls -lah
```

The two files are already located in the `home` directory.

Next, I compared them:

```bash
diff passwords.new passwords.old
```

Output:

```text
[n]c[n]
< [different_line_in_passwords.new]
---
> [different_line_in_passwords.old]
```

The output means that the files have difference in the n-th line. Since the challenge told that the password is in `passwords.new`, the password is `[different_line_in_passwords.new]`.

## Key Takeaways

### diff

Compare the contents of two files line by line and show their differences.

Syntax:

```bash
diff [options] [file] [file]
```

`[options]`

- `-u` — unified format, easier for humans to read, and commonly used in patches
- `-q` — Only reports whether the files differ.
- `-s` — Reports when the files are identical.

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
