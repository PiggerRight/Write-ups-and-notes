# Bandit Level 5

## Challenge

The password for the next level is stored in the only human-readable file in the `inhere` directory

---

## Initial Thoughts

The challenge mentions about a only human-readable file in `inhere` directory, so i need to find that directory and search the file inside.

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
ls
```

Look up inside `inhere` directory and we can see it contains 10 different files.

```bash
cat ./*
```

I tried to read all of them to find out which file contains human readble content, but the output is ambiguous.

```bash
head ./*
```

I tried to read all of them again but with separation between each files. Then i find that only `-file07` is readble and the password is inside.

## Key Takeaways

### head

Displays the first part of file(s) with separation part.

Syntax:

```bash
head [options] [files...]
```

`[options]`

- `-n [n]` or `-[n]` — read from the first to n-th line.
- `-c [n]` — read the first n bytes.

---

## References

- `man pwd`
- `man ls`
- `help cd`
- `man cat`
- `man head`
- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
