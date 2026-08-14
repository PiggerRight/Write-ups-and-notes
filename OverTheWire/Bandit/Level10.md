# Bandit Level 10

## Challenge

The password for the next level is stored in the file `data.txt` in one of the few human-readable strings, preceded by several `=` characters.

---

## Initial Thoughts

The challenge may require us to filter out human-readable lines from `data.txt` which seems like a binary file.

---

## Solution

### Step 1: Find the targeted file

```bash
pwd
ls -lah
```

`data.txt` is already stored in home directory.

### Step 2: Extract readable texts from `data.txt`

```bash
strings -n 10 data.txt
```

I used `strings` to extract readable texts. Most password for bandit is longer than 10 so I thought the text must longer than that, so i used `-n` option.

## Key Takeaways

### strings

Extracts printable text strings from binary files or non-text file, which may contain readable texts.

Syntax:

```bash
strings [options] [files,...]
```

`[options]`

- `-n [n]` — only display strings with at least `[n]` printable characters, default is 4.
- `-a` — scan the entire file, mostly default on many Linux system.

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
