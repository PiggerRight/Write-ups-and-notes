# Bandit Level 9

## Challenge

The password for the next level is stored in the file `data.txt` and is the only line of text that occurs only once.

---

## Initial Thoughts

The challenge requires to find the only line inside `data.txt` that is unique.

---

## Solution

### Step 1: Find the targeted file

```bash
pwd
ls -lah
```

`data.txt` is already stored in home directory. The file has a large size with 33 KB, so we cannot read it line by line.

### Step 2: Find the unique line inside `data.txt`

```bash
sort data.txt | uniq -u
```

I used `sort` to sort the lines and used `uniq` to find out the only line that is not duplicated.

## Key Takeaways

### sort

Sorts lines of text alphabetically, numerically, or according to other criteria. Note that `sort` does not change the input files.

Syntax:

```bash
sort [options] [files...]
```

`[files...]` — if `[files...]` is omitted, `sort` reads from `stdin`.

`[options]`

- `-r` — sort in reverse order.
- `-n` — numeric sort.
- `-u` — removes duplicate lines while sorting.
- `-k[n]` —  sort by a field, the n-th column.
- `-o [output_file]` — store output on `[output_file]` rather than print on Terminal.

### uniq

Removes or reports consecutive duplicate lines. Since `uniq` only compares adjacent (consecutive) lines, it is often used with `sort`.

Syntax:

```bash
uniq [options] [input_file] [output_file]
```

`input_file` — if no file provided, `uniq` reads from `stdin`.

`output_file` — if no file provided, `stdout` print on Terminal be default.

`[options]`

- `-c` — count consecutive duplicates.
- `-d` — show duplicated lines only.
- `-u` — show unique lines only.
- `-i` — accept case differences.

## References

- `man pwd`
- `man ls`
- `man sort`
- `man uniq`
- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
