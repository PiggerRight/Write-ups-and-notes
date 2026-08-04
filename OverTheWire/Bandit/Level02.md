# Bandit Level 2

## Challenge

The password for the next level is stored in a file called `-` located in the home directory.

---

## Initial Thoughts

It quite like level 1 but the name of the file is quite unusual, since many Linux commands use `-` to indicate command-line options.

---

## Solution

### Step 1: Find out where we are

```bash
pwd
```

Currently we are at home directory.

```bash
ls
```

Confirm that `-` is here.

### Step 3: Open the file and get the password.

```bash
cat ./-
```

I found out that with files begins with `-`, the prompt may treat them as `[option]` so we need to add prefix `./`.

---

## Key Takeaways

### cat

Display the contents of file(s).

Syntax:

```bash
cat [options] [files...]
```

With `[files...]` begin with `-`
- We need to add prefix `./`.
- Use absolute path.
- Use `--` to mark the end of options.

---

## References

- `man pwd`
- `man ls`
- `man cat`
- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
