# Bandit Level 1

## Challenge

The password for the next level is stored in a file called readme located in the home directory. Use this password to log into bandit1 using SSH.

---

## Initial Thoughts

The challenge mentions that the file `readme` is located at the home directory, so I just need to navigate to the home directory and open that file.

---

## Solution

### Step 1: Find out where we are

```bash
pwd
```

Currently we are at `/home/bandit0`.

### Step 2: Find `readme` file

```bash
cd
```

This command is to navigate the home directory.

```bash
ls
```

Confirm that `readme` is here.

### Step 3: Open the file and get the password

```bash
cat readme
```

---

## Key Takeaways

### pwd

Displays the absolute path of the current working directory. Useful for confirming where you are before navigating or accessing files.

Syntax:

```bash
pwd
```

### ls

List files and direcctories in `[path]` directory.

Syntax:

```bash
ls [options] [path]
```

`[path]`

- `[path]` can be absolute or relative path.
- If `[path]` is omitted or `[path] == .`, `[path]` equals to the current directory.
- If `[path] == ..`, `[path]` equals to the parent directory.
- If `[path] == ~`, `[path]` equals to the home directory.
- If `[path] == ../..`, `[path]` equals to the grandparent directory.
- If `[path] == /`, `[path]` equals to the root directory.

`[options]`

- `-l` — show detailed information (File permissions | Number of links | Owner | Group | File size | Last modified date | File name).
- `-a` — show hidden files, files beginning with `.` are hidden files.
- `-h` — human readable format for file size.
- `-R` — explore the whole directory tree rooted from `[path]`.
- `-t` — sort by last modified time.
- `-S` — sort by size, largest first.
- It is able to apply more than one option. Eg: `-la`

### cd

It changes your current directory to another directory.

Syntax:

```bash
cd [directory]
```

`[directory]`

- `[directory] == [path]` — move to `[path]` directory, `[path]` must be absolute or relative path.
- `[directory]` is omited or `[directory] == ~` — move to home directory.
- `[directory] == ..` — move to parent directory.
- `[directory] == ../..` — move up two levels and so on.
- `[directory] == /` — move to root directory.
- `[directory] == -` — move to previous directory.

### cat

Display the contents of file(s).

Syntax:

```bash
cat [options] [files...]
```

`[files...]`

- Can display multiple files.
- Use `*` to read all files in current directory.

`[options]`

- `-n` — add line numbers.
- `-b` — add line numbers except blank lines.
- `-E` — show end of each line, the `$` marks the end of each line.
- `-T` — Tabs are displayed as `^I`, making them visible.
- `-A` — This combines several display options, making tabs, line endings, and other non-printing characters visible.

---

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
