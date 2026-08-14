# Bandit Level 6

## Challenge

The password for the next level is stored in a file somewhere under the `inhere` directory and has all of the following properties:
- human-readable
- 1033 bytes in size
- not executable

---

## Initial Thoughts

The challenge mentions about a only human-readable file in `inhere` directory, so i need to find that directory and search the file inside.

---

## Solution

### Step 1: Find `inhere` directory

```bash
pwd
ls
```

Check current directory and look up inside home directory for `inhere` directory.

### Step 2: Find the file

```bash
cd inhere
ls
```

Look up inside `inhere` directory and we can see a lot of sub-directories, so i need a command that helps me find inside `inhere`.

```bash
find -size 1033c
```

I used the size property to find inside `inhere` and i got only one result is `./maybehere07/.file2`.

```bash
cat ./maybehere07/.file2
```

Read file and get password

## Key Takeaways

### find

Searches for files and directories based on conditions such as name, size, type, owner, or permissions

Syntax:

```bash
find [path] [options]
```

`[options]`

- `-name "[name]"` — search by exact name. (`[name]`: `*.txt` — search by file name extension | `.*` — search hidden files | `*abc*` - search file name containing "abc")
- `-iname "[name]"` — search by name, only accept different in upper of lower case.
- `-type [type]` — search by file type (`[type]`: `f` — regular files | `d` — directories | `l` — symbolic links).
- `-size [size]` — search by size (`[size]`: `100c` — exact 100 bytes | `+10k` — more than 1KB | `-1M` — less than 1MB | `1G` — exact 1GB).
- `-user [user_name]` - search by user name.
- `-group [group_name]` - search by group name.
- `-perm [permission_number]` - search by permission (`[permission_number]` contains 3 digits and each digit defines the permission - read, write, execute - of user, group and others).
- `-exec [excecuted_command]` - execute `[excecuted_command]` on each result.

`[excecuted_command]`

```bash
[command] \;
```

- `{}` — represent the each result.
- `\;` — end the command.

Example:

```bash
find -size 1033c -exec cat {} \;
```

---

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
