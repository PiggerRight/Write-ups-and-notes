# Bandit Level 13 (Scripting Solution)

## Challenge

The password for the next level is stored in the file `data.txt`, which is a hexdump of a file that has been repeatedly compressed.

---

## Initial Thoughts

After solving this level manually, I had to repeat a specific workflow many times so I think why not automate this workflow by using scripting.

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
file data.txt
head data.txt
```

This is a plain text file containing a hexdump. The first bytes sequence of the file is `1f8b`, which is the magic number of a `gzip` file.

### Step 3: Reconstruct the original binary file.

```bash
mkdir /tmp/PiggerRight
cd /tmp/PiggerRight
cp /home/bandit12/data.txt data.txt
```

I create a temporary space to work with and copy `data.txt` to this space.

```bash
xxd -r data.txt > data
```

I use `xxd` to turn hexdump into original binary file.

```bash
file data
```

Now, the binary file is compressed by `gzip`.

### Step 4: Decompress and Extract

We have this workflow to continuously decompress and extract the file till we get the original plain text file.

1. Use `file` to identify the file type.
2. If it is compressed, decompress it.
3. If it is archived, extract it.
4. Target newly produced file then repeat, stop when it is a plain text file.

So we can apply scripting. (We suppose that archived file only contains one file and there is only three file types are `gzip`,`bzip2`,and `tar`, which are true in this level)

```bash
#!/bin/bash

file_name="$1"
file_type=$(file -b "$file_name")

while true
do
    case "$file_type" in
        *gzip*)
            mv "$file_name" "$file_name.gz"
            gunzip "$file_name.gz"
            file_type=$(file -b "$file_name")
            ;;
        *bzip2*)
            mv "$file_name" "$file_name.bz2"
            bunzip2 "$file_name.bz2"
            file_type=$(file -b "$file_name")
            ;;
        *tar*)
            mv "$file_name" "$file_name.tar"
            new_file=$(tar -tf "$file_name.tar")
            tar -xf "$file_name.tar"
            file_name="$new_file"
            file_type=$(file -b "$file_name")
            ;;
        *ASCII*)
            echo "Successfully. The password in $file_name."
            break
            ;;
        *)
            echo "Unknown file type"
            break
            ;;
    esac
done
```

**Note**: 
- `file_name`: is the name of the file that we are working on.
- `file_type`: is the type of the file that we are working on.
- Then I use loop to continuously repeat the workflow and only stop when the file is a plain text file or has unknown file type.
- The reason why I suppose there only three file types is to save time due to numerous types.
- For the assumption that archived file only have one file inside because the variable `new_file` must take only one filename, if not `file_name` will store many filenames and lead to error. (This may be a flaw of the script but with this level it is enough)

```bash
echo '[copy the script here]' > script.sh
bash script.sh data
```

```text
Successfully. The password in data8.bin.
```

I copy the script to `script.sh` and run it with an argument `data` - the file that need to be decompressed.

```bash
cat data8.bin
```

Finally, I read the file and get the password.

```bash
rm -r /tmp/PiggerRight
```

Don't remember to delete temporary space.

## Key Takeaways

### Bash

**Terminal** is an application that provides an interface for interacting with a shell.

**Shell** is a program that accepts your commands, interprets them, and runs them on the operating system kernel. Many shells, including Bash, provide their own scripting language, allowing you to automate commands and write scripts.

**Bash** stands for **Bourne Again SHell**, which is a shell that was originally developed for Unix-like operating systems. Today, it can also run on Windows through environments such as WSL, Git Bash, or Cygwin.

```text
Command → Terminal → Shell (Bash,...) → Linux Kernel → Hardware
```

### Scripting

**Script** is a text file containing a sequence of commands that are executed automatically by an interpreter (such as **Bash**).

Instead of typing commands line by line, we can save it into a file and execute that file. This is useful when we have to solve repetitive tasks and we can re-use that script many times.

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
name="Alice"
echo "$name"
```

**Reading Input**

```bash
echo "Enter your name:"
read name
echo "Hello $name"
```

**Get command output**

```bash
current=$(pwd)
echo "$current"
```

**Conditional statements**

`if`-`else` statements — checks whether the command succeeds.

```bash
if grep "password" data.txt; then
    echo "Found"
else
    echo "Not found"
fi
```

`case` statements — checks different conditions.

```bash
type=$(file data)

case "$type" in
    *gzip*)
        echo "gzip"
        ;;
    *bzip2*)
        echo "bzip2"
        ;;
    *tar*)
        echo "tar"
        ;;
    *)
        echo "Unknown"
        ;;
esac
```

**Loops**

`for` loop

```bash
for file in *.txt
do
    echo "$file"
done
```

`while` loop

```bash
count=1

while [ $count -le 5 ]
do
    echo "$count"
    count=$((count + 1))
done
```

**Functions**

```bash
hello() {
    echo "Hello!"
}

hello
```

**Script Arguments**

- `$1` — first argument
- `$2` — second argument
- `$@` — all arguments
- `$#` — number of arguments

## References
- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
