# Bandit Level 13

## Challenge

The password for the next level is stored in the file `data.txt`, which is a hexdump of a file that has been repeatedly compressed.

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

### mkdir

Create directories.

Syntax:

```bash
mkdir [options] [directories_name...]
```

Examples:

```bash
mkdir projects
```

```bash
mkdir dir1 dir2 dir3
```

```bash
mkdir -p notes/linux/bandit
```

`[options]`

- `-p` — create parent directories if they don't exist.

### rmdir

Remove directory. This only works if the directory is empty.

Syntax:

```bash
rmdir [directory]
```

### rm

Remove file.

Syntax:

```bash
rm [options] [file]
```

`[options]`

- `-r` — remove directory (can be non-empty), replace `[file]` by `[directory]`.
- `-f` — force to remove anyway.
- `-i` — ask for confirmation.
- `-I` — asf for confirmation each file and directory.

### mv

Rename file or directory.

Syntax:

```bash
mv [old_name] [new_name]
```

Move files.

Syntax:

```bash
mv [file] [path (directory)]
```

### cp

Copy file or directory.

```bash
cp [file] [file]
```

```bash
cp -r [directory] [directory]
```

### touch

Creates an empty file. If the file already exists, `touch` updates its modification timestamp.

Syntax:

```bash
touch [file]
```

**Important**: When a file used after `>`, if the file already exists, it is truncated (its contents are erased); if the file doesn't exist, it is created.



## References

- `man tr`
- `man cat`
- `man ls`
- `man pwd`
- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
