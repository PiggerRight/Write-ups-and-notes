# Bandit Level 3

## Challenge

The password for the next level is stored in a file called `--spaces in this filename--` located in the home directory

---

## Initial Thoughts

The challenge is similar to level 1, but there may have different modification in reading file command. This time the file name has spaces.

---

## Solution

### Step 1: Find out where we are

```bash
pwd
```

Currently we are at home directory.

```bash
ls
```

Confirm that `--spaces in this filename--` is here.

### Step 2: Open the file and get the password

```bash
cat ./--spaces\ in\ this\ filename--
```

 Since the command use spaces to separate command arguments, before the space we need to add `\` in order to treat them as a part of the name rather than a argument of the command.

---

## Key Takeaways

### cat

```bash
cat [options] [files...]
```

With `[files...]` have spaces
- Before the space we need to add `\`.
- put them inside single quotes or double quotes. Eg: `'./--spaces in this filename--'`

---

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
