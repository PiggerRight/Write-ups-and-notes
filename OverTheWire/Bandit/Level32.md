# Bandit Level 32

## Challenge

There is a git repository at `ssh://bandit31-git@bandit.labs.overthewire.org/home/bandit31-git/repo` via the port `2220`. The password for the user `bandit31-git` is the same as for the user `bandit31`.

From your local machine (not the OverTheWire machine!), clone the repository and find the password for the next level. This needs git installed locally on your machine.

---

## Initial Thoughts

In this level, the challenge requires me to clone a Git repository to my local machine again. Therefore, I decided to clone the given repository and investigate it.

---

## Solution

### Step 1: Clone repository

I can use `git clone [git_url]` to clone the given repository to my local machine. Don't forget to access SSH server through port `2220`:

```bash
git clone ssh://bandit31-git@bandit.labs.overthewire.org:2220/home/bandit31-git/repo
```

Git asks for the password of `bandit31-git`, which is the same as the password of `bandit31`, according to the challenge. After entering the password, the repository is cloned to my local machine.

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
This time your task is to push a file to the remote repository.

Details:
    File name: key.txt
    Content: 'May I come in?'
    Branch: master
```

It is an instruction to create and push a commit containing `key.txt`. Therefore, I just follow the instructions.

### Step 3: Follow `README.md` instruction

We can check the current local branch using:

```bash
git branch
```

The current local branch is `master`.

Then, I create `key.txt` in the current branch with the exact content specified in `README.md`:

```bash
echo 'May I come in?' > key.txt
```

Now, I try to add and commit the file:

```bash
git add .
git commit -m "message"
```

At this point, don't forget that Git needs a `user.name` configured before creating a commit. If it has not been configured yet, we can set it using:

```bash
git config user.name [name]
```

However, we still cannot commit. Git reports that there are no changes to commit.

This is because of the `.gitignore` file. Let's examine it:

```bash
cat .gitignore
```

Output:

```text
*.txt
```

The pattern `*.txt` tells Git to ignore all `.txt` files. Therefore, `key.txt` is also ignored. Because of this, `git add .` does not stage `key.txt`, so there is nothing to commit.

Therefore, I remove the `.gitignore` file.

```bash
rm .gitignore
```

Now `key.txt` is no longer ignored, so we can add, commit, and push it normally:

```bash
git add .
git commit -m "message"
git push origin master
```

Finally, we submit the password for `bandit31-git`, and we get the password for the next level.

## Key Takeaways

### .gitignore

`.gitignore` is a special file that tells Git which files or directories it should ignore. This prevents sensitive files from accidentally entering Git, but it isn't a mechanism for securely storing secrets. 

Git commands:

- `git config --global user.name [name]` — Configuring user name globally
- `git config --global user.email [email]` — Configuring user email globally
- `git config user.name [name]` — Configuring user name for current repository
- `git config user.email [email]` — Configuring user email for current repository


## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
