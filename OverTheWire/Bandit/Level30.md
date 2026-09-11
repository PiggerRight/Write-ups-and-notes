# Bandit Level 30

## Challenge

There is a git repository at `ssh://bandit29-git@bandit.labs.overthewire.org/home/bandit29-git/repo` via the port `2220`. The password for the user `bandit29-git` is the same as for the user `bandit29`.

From your local machine (not the OverTheWire machine!), clone the repository and find the password for the next level. This needs git installed locally on your machine.

---

## Initial Thoughts

In this level, the challenge requires me to clone a Git repository to my local machine again. Therefore, I decided to clone the given repository and investigate it.

---

## Solution

### Step 1: Clone repository

I can use `git clone [git_url]` to clone the given repository to my local machine. Don't forget to access SSH server through port `2220`:

```bash
git clone ssh://bandit29-git@bandit.labs.overthewire.org:2220/home/bandit29-git/repo
```

Git asks for the password of `bandit29-git`, which is the same as the password of `bandit29`, according to the challenge. After entering the password, the repository is cloned to my local machine.

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

The file does not contain the password for the next level, so I check the commit history of the current branch:

```bash
git log
```

The commits in this branch also do not contain any information about the password.

Therefore, I decide to investigate the other branches.

```bash
git branch -a
```

Output:

```text
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/sploits-dev
```

This shows that, besides `master`, there are other remote-tracking branches, including `origin/dev` and `origin/sploits-dev`.

I can examine the commit history of another branch using:

```bash
git log remotes/origin/dev
```

I can then inspect a specific commit using `git show [commit_code]`

After investigating the other branches, I find a commit in `remotes/origin/dev` containing the information needed to retrieve the password for `bandit30`. The commit message is `add data needed for development`.

I then use `git show [commit_code]` to inspect that commit and retrieve the password.

## Key Takeaways

### Git

Git supports multiple branches. When investigating a Git repository, we should not only examine the current branch because other branches may contain commits or information that are not present in the current branch.

Git commands:

- `git log [branch]` — opens a screen showing commit history of `[branch]`, usually from newest to oldest. If `[branch]` is omitted, it displays the commit history of the current branch.

- `git branch -a` — displays local branches and remote-tracking branches.

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
