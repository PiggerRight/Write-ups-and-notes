# Bandit Level 8

## Challenge

The password for the next level is stored in the file `data.txt` next to the word `millionth`.

---

## Initial Thoughts

The challenge requires to find lines with given pattern inside `data.txt`.

---

## Solution

### Step 1: Find the targeted file

```bash
ls -lah
```

`data.txt` is already stored in home directory. The file has a large size with 4 MB, so we cannot read it line by line.

### Step 2: Find the lines with pattern inside the file

```bash
head data.txt
```

Output:

```bash
bracken's       w4m77B7X7GYsOmJz0t0F2GwggoBY9gnH
packing n5xbRJzpSDgdC6c9q967mzFhM2NrtsRc
weighty Yy2wte9aVxCwRaD7OE25OoSaXgCh6DEp
...
```

Due to the large size, I tried to take a glance at the file. Each line contains a word followed by a string.

```bash
grep "millionth" data.txt
```

I search the file for the pattern `millionth` and get only one result, after the pattern is the password.

## Key Takeaways

### grep

Searches for lines that match a given text patterns in files or command output.

Syntax:

```bash
grep [options] "[pattern]" [files...]
```

`[options]`

- `-i` — accept different in upper of lower case.
- `-n` — show line numbers in the origin file.
- `-v` — show lines that do not match.
- `-r` — search all files under given directory (replace `[files...]` by `[path]`).
- `-l` — only display filename of files that contain the pattern.
- `-c` — count the matching lines.
- `-w` — only match the whole word.

`[files...]`

- If `[files...]` is provided, `grep` reads directly from specified files.
- When `[files...]` is omitted, `grep` reads from `stdin` (data comes from keyboard by default or previous pipe command `stdout` if provided)

### Streams

If a command is designed to read input, it first checks whether you've explicitly given it input (such as a filename). If not, it reads from `stdin`.

---

## References

- `man head`
- `man ls`
- `man grep`
- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
