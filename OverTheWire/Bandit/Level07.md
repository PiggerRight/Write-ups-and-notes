# Bandit Level 7

## Challenge

The password for the next level is stored somewhere on the server and has all of the following properties:

- owned by user bandit7
- owned by group bandit6
- 33 bytes in size

---

## Initial Thoughts

The challenge gives three properties of the targeted file and it stays somewhere on the server, so i need to search on the whole server.

---

## Solution

### Step 1: Find the targeted file directly

```bash
find / -size 33c -user bandit7 -group bandit6 -exec cat {} \;
```

I've tried to search from the root directory with all given properties and read the result, but the outut looks quite ambiguous, a lot of them have `Permission denied` status.

### Step 2: Filter the result

```bash
find / -size 33c -user bandit7 -group bandit6 -exec cat {} \; 2>/dev/null
```

I wanted to hide the error messages while keeping the successful search results. To filter `Permission denied` results, these result come from `stderr` stream represented by number `2`. As usual, both `stderr` and `stdout` are directed to terminal, so i just redirect `stderr` to `/dev/null` the file that discard everything written to it.

Finally, only `stdout` prints its result on the terminal, so I got the password.

## Key Takeaways

### Streams

Every Linux program has three standard streams:

- `0` — `stdin`  : input (keyboard by default)
- `1` — `stdout` : normal output (terminal by default)
- `2` — `stderr` : error messages (terminal by default) 

### Shell Redirection

We can rediect these streams as we want. For example, instead of print normal output on the terminal, we can redirect it to a file.

- `<`  — redirect content of a file to `stdin`.
- `>`  — redirect `stdout` to a file, overwriting its previous contents.
- `>>` — redirect `stdout` to a file, append `stdout` to the end of the file.
- `2>` — redirect `stderr` to a file, overwriting its previous contents.
- `/dev/null` — is a special file that anything written to it disappears forever, `stderr` are often redirected to here to filter error messages.

Example:

```bash
find / > output.txt 2> errors.txt
```

```text
stdout → output.txt
stderr → errors.txt
```

- `|` — this is called pipe and it sends the `stdout` of one command (not `stderr`) into the `stdin` of another.

```bash
[command_1] | [command_2]
```

The normal output of `[command_1]` becomes the input of `[command_2]`

---

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
