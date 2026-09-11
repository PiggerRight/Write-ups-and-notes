# Bandit Level 31

## Challenge

There is a git repository at `ssh://bandit30-git@bandit.labs.overthewire.org/home/bandit30-git/repo` via the port `2220`. The password for the user `bandit30-git` is the same as for the user `bandit30`.

From your local machine (not the OverTheWire machine!), clone the repository and find the password for the next level. This needs git installed locally on your machine.

---

## Initial Thoughts

In this level, the challenge requires me to clone a Git repository to my local machine again. Therefore, I decided to clone the given repository and investigate it.

---

## Solution

### Step 1: Clone repository

I can use `git clone [git_url]` to clone the given repository to my local machine. Don't forget to access SSH server through port `2220`:

```bash
git clone ssh://bandit30-git@bandit.labs.overthewire.org:2220/home/bandit30-git/repo
```

Git asks for the password of `bandit30-git`, which is the same as the password of `bandit30`, according to the challenge. After entering the password, the repository is cloned to my local machine.

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

However, the file is empty. Then I check the current commit history:

```bash
git log
```

There is only one commit, which creates `README.md`. I also check the remote-tracking branches, but there is only the current branch.

So, I continue investigating other Git features. One of them is tags, which are names used to reference specific Git objects, most commonly commits.

```bash
git tag
```

I find a tag named `secret`, so I try to examine what it points to:

```bash
git show secret
```

The output contains the password for the next level, so I have successfully retrieved the key.

## Key Takeaways

### Git tag

Git tag is a name used to **mark a specific Git object**, most commonly a commit. It provides a convenient and meaningful way to refer to an important point in a repository's history without needing to remember the commit hash. Unlike a branch, which normally moves forward as new commits are added, a tag is generally fixed to the object it references. Tags are commonly used to mark software releases, versions, or important milestones.

Git commands:

- `git tag` — lists the tags in the repository.

- `git show [tag]` — displays information about the Git object referenced by the tag.

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
