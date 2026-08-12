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

### Bash

**Terminal** is an application that provides an interface for interacting with a shell.

**Shell** is a program that accepts your commands, interprets them, and runs them on the operating system kernel. **Shell** is also a programming language, this allows **Scripting**.

**Bash** stands for **Bourne Again SHell**, which is a **Shell** that was originally developed for Unix/Linux systems, but it can also run on Windows.

```text
Command → Terminal → Shell (Bash,...) → Linux Kernel → Hardware
```

### Scripting

**Script** is a text file containing a sequence of commands that are executed automatically by an interpreter (such as **Bash**).

Instead of typing commands line by line, we can save it into a file and execute that file. This is usefull when we have to solve repetitive tasks and we can re-use that script many times.

### Scripting Basics

**Script structure**

```bash
#!/bin/bash

echo "Hello World"
```

- `#!/bin/bash` tells Linux to execute the script using Bash.
- Everything below is ordinary Bash commands.

**Run script**

```bash
bash [file]
```

`[file]` — have `.sh` extension.

**Comments**

```bash
# This is a comment

echo "Hello"
```

Comments are ignored by Bash.

**Variables**

```bash
# This is a comment

echo "Hello"
```

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
