# Bandit Level 12

## Challenge

The password for the next level is stored in the file `data.txt`, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions.

---

## Initial Thoughts

The challenge requires decoding the contents of `data.txt`, which is encoded using ROT13 cipher. ROT13 is a simple substitution cipher that replaces each letter with the letter 13 positions later in the alphabet.

---

## Solution

### Step 1: Find the targeted file

```bash
pwd
ls -lah
```

`data.txt` is already stored in home directory.

### Step 2: Decode `data.txt`

```bash
cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

or

```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
```

The only thing left that I need to do is decode the ROT13 text of the file and get the password by using `tr` to rotate each letter.

## Key Takeaways

### tr

Replace, delete, or squeeze characters from text.

Syntax:

```bash
tr [options] '[SET1]' '[SET2]'
```

`[SET1]` — characters to be searched for.

`[SET2]` — characters to be replaced.

`[options]`

- `-d` — delete characters in given set, just need `[SET 1]`.
- `-s` — squeeze repeated characters in given set, just need `[SET 1]`.
- `-c` — take the complement of `[SET1]`.

**Important**: `tr` reads from `stdin` and writes to `stdout`. It does not read files directly.

Example: Replace one character

```bash
echo "banana" | tr 'a' 'x'
```

```text
bxnxnx
```

Example: Replace multiple characters

```bash
echo "hello world" | tr 'a-z' 'A-Z'
```

```text
HELLO WORLD
```

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
