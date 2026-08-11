# Bandit Level 13 (Scripting Solution)

## Challenge

The password for the next level is stored in the file `data.txt`, which is a hexdump of a file that has been repeatedly compressed.

---

## Initial Thoughts

After solving this level manually, I need to repeat a specific workflow many times so I think why not automate this workflow by using scripting.

---

## Solution

### Step 1: Find the targeted file

```bash
pwd
ls -lah
```

`data.txt` is already stored in home directory.

### Step 2: Examine `data.txt`

```bash
mkdir /tmp/PiggerRight
cd /tmp/PiggerRight
cp /home/bandit12/data.txt data.txt
```

I maka a temporary space to work with and copy `data.txt` to this space.

```bash
file data.txt
head data.txt
```

This is a plain text file containing a hexdump. The first bytes sequence of the file is `1f8b`, which is the magic number of a `gzip` file.

### Step 3: Recontruct the original binary file.

```bash
xxd -r data.txt > data
```

I use `xxd` to turn hexdump into original binary file.

```bash
file data
```

Now, the binary file is compressed by `gzip`.

### Step 4: Decompress and Extract

```bash
mv data data.gz
gunzip data.gz
```

I change the file extention to `.gz` because `gunzip` accepts `.gz` files by default and decompress it.

Then I repeat the workflow until get a plain text file:

1. Use `file` to identify the file type.
2. If it is compressed, decompress it.
3. If it is archived, extract it.
4. Target newly produced file then repeat.

```bash
file data
mv data data.bz2
bunzip2 data.bz2

file data
mv data data.gz
gunzip data.gz

file data
mv data data.tar
tar -xf data.tar
ls -lah
```

At this time, `data5.bin` was found so I continue with this file.

```bash
file data5.bin
mv data5.bin data5.tar
tar -xf data5.tar
ls -lah
```

At this time, `data6.bin` was found so I continue with this file.

```bash
file data6.bin
mv data6.bin  data6.bz2
bunzip2 data6.bz2

file data6
mv data6 data6.tar
tar -xf data6.tar
ls -lah
```

At this time, `data8.bin` was found so I continue with this file.

```bash
file data8.bin
mv data8.bin data8.gz
gunzip data8.gz

file data8
cat data8
```

At this time, the `data8` file is a plain text file so I tried to read it and got the password.

```bash
rm -r /tmp/PiggerRight
```

Don't remember to delete temporary space.

**Important**: `gunzip`, `bunzip2` are decompressing commands for `gzip`,`bzip2` file, and `tar -xf` is extracting command for `tar` file. There are many file types so I will not dive in these commands. Remember that these command only accept the correct extention, so I need to rename the file before use them.

## Key Takeaways

### Hexdump

Hexdump is a way to display the raw bytes of a file in hexadecimal (base 16) so humans can inspect binary data.

Example:

```text
00000000: 1f8b 0808 a6f0 3b6a 0203 6461 7461 322e  ......;j..data2.
00000010: 6269 6e00 0144 02bb fd42 5a68 3931 4159  bin..D...BZh91AY
00000020: 2653 5904 ab91 e100 001c 7fff fffb bebf  &SY.............
...
```
**Important**: Unprintable characters are presented by `.`.

Magic number (file signature) is a sequence of bytes at the beginning of the hexdump, it helps to examine the file type.

Plain text files usually don't have a magic number; while the others have. (Eg: `.png`, `.gz`,...)

### file

Determine the type of a file by examining its contents, not its filename or extension. This command rely on magic number or the signatures of the file content to examine the file type.

Syntax:

```bash
file [options] [files...]
```

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

### xxd

Create a hexdump of a binary file. The hexdump is stored in a plain text file.

Syntax:

```bash
xxd [options] [file]
```

`[file]` — If `[file]` is omitted, `xxd` reads from `stdin`.

`[options]`
`-r` — recontruct original binary file of a hexdump.

## References

- `man ls`
- `man pwd`
- `man mkdir`
- `help cd`
- `man cp`
- `man rmdir`
- `man rm`
- `man cp`
- `man mv`
- `man touch`
- `man file`
- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
