# Bandit Level 11

## Challenge

The password for the next level is stored in the file `data.txt`, which contains base64 encoded data.

---

## Initial Thoughts

The challenge requires us to decode the content of the file `data.txt`, which encoded with base64.

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
base64 --decode data.txt
```

The only thing left that I need to do is decode the Base64 text of the file and get the password. 

## Key Takeaways

### base64

Base64 is a way to represent binary data as printable text, because many protocols and file formats expect printable text instead of arbitrary bytes. It is much more compact than writing the binary as a string of 0s and 1s, although it is still larger than the original binary data.

In Linux, `base64` is a command-line utility used to encode binary (or text) data into Base64 and decode Base64 back to its original form.

Syntax:

```bash
base64 [options] [file]
```

`[file]` — if `[file]` is omitted, `base64` reads from `stdin`.

`[options]`

- `-d` or `--decode` — decode Base64, the default is encoding.

### echo

Prints text or variable values to `stdout`.

Syntax:

```bash
echo [options] [string...]
```

`[string...]`

- The text or variables to print.
- To print special characters, put them in quotes.
- `echo` ignores `stdin`, it only reads `[string...]`.

`[options]`

- `-n` — do not print the trailing newline.
- `-e` — interpret escape sequences. (`\n`,`\t`,`\\`,`\"`)

## References

- `man echo`
- `man base64`
- `man ls`
- `man pwd`
- `man cat`
- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
