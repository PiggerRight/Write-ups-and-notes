# Bandit Level 4

## Challenge

The password for the next level is stored in a hidden file in the `inhere` directory.

---

## Initial Thoughts

The challenge mentions about a hidden file in `inhere` directory, so i need to find that directory and search the file inside.

---

## Solution

### Step 1: Find `inhere` directory

```bash
pwd
```

Currently we are at home directory.

```bash
ls
```

Look up inside home directory and we can directly see `inhere` directory.

### Step 2: Find the file

```bash
cd inhere
```

Move to `inhere` directory.

```bash
ls -a
```

Look up inside `inhere` directory and we can directly see a hidden file named `...Hiding-From-You`.

```bash
cat ...Hiding-From-You
```

Read that file and get the password.

---

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
