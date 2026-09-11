# Bandit Level 29

## Challenge

There is a git repository at `ssh://bandit28-git@bandit.labs.overthewire.org/home/bandit28-git/repo` via the port `2220`. The password for the user `bandit28-git` is the same as for the user `bandit28`.

From your local machine (not the OverTheWire machine!), clone the repository and find the password for the next level. This needs git installed locally on your machine.

---

## Initial Thoughts

In this level, the challenge requires me to clone a Git repository to my local machine again. Therefore, I decided to clone the given repository and investigate it.

---

## Solution

### Step 1: Clone repository

I can use `git clone [git_url]` to clone the given repository to my local machine. Don't forget to access SSH server through port `2220`:

```bash
git clone ssh://bandit28-git@bandit.labs.overthewire.org:2220/home/bandit28-git/repo
```

If you get the message:

```text
fatal: destination path 'repo' already exists and is not an empty directory.
```

It means the old repository still in the working directory, delete it by `rm -r` then clone the new one normally.

Git asks for the password of `bandit28-git`, which is the same as the password of `bandit28`, according to the challenge. After entering the password, the repository is cloned to my local machine.

I can check my current working directory:

```bash
ls -lah
```

The cloned repository should now appear as a `repo` directory.

### Step 2: Investigate the repository

I move into the repository and investigate its contents:

```bash
cd repo
ls -lah
```
There is a file named `README.md`. I read its contents:

```bash
cat README.md
```

Output:

```text
# Bandit Notes
Some notes for level29 of bandit.

## credentials

- username: bandit29
- password: xxxxxxxxxx
```

The file appears to contain credentials, but the password has been replaced with `xxxxxxxxxx`.

Git does not only clone the current version of the repository to our local machine, it also stores the repository's version history. Therfore, I look up for previous commits of this repository for more information.

```bash
git log
```

There are three commits:

```text
commit [commit_3] (HEAD -> master, origin/master, origin/HEAD)
Author: ...
Date:   ...

    fix info leak

commit [commit_2]
Author: ...
Date:   ...

    add missing data

commit [commit_1]
Author: ...
Date:   ...

    initial commit of README.md
```

According to the output, the oldest commit shown by `git log` is `[commit_1]` and it is the initial commit of `README.md`. So I examine this commit to see the first version of `README.md`:

```bash
git show [commit_1]
```

```text
+# Bandit Notes
+Some notes for level29 of bandit.
+
+## credentials
+
+- username: bandit29
+- password: <TBD>
```

In the inital version, the password field was still missing. So I move to the `[commit_2]`, which might update the password:

```bash
git show [commit_2]
```

```text

-- password: <TBD>
+- password: [password]
```

Now, we see that in this version, the password is updated and we retrieved it.

## Key Takeaways

### Git

Changing or removing information from the latest version of a file does not necessarily remove it from Git's history.

Git commands:

- `git log` — opens a screen showing commit history of your current branch, usually from newest to oldest. Navigation in pager:
  - `↑ / ↓` — move up/down
  - `Space / b` — next/previous page
  - `q` — quit
  - `/text` — search

- `git show [commit_code]` — displays information about a commit, including its metadata and the changes introduced by that commit.

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
