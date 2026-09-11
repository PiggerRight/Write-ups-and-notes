# Bandit Level 28

## Challenge

There is a Git repository at `ssh://bandit27-git@bandit.labs.overthewire.org/home/bandit27-git/repo` via the port `2220`. The password for the user `bandit27-git` is the same as for the user `bandit27`.

From your local machine (not the OverTheWire machine!), clone the repository and find the password for the next level. This needs git installed locally on your machine.

---

## Initial Thoughts

In this level, the challenge requires me to clone a Git repository to my local machine. Therefore, I decided to research the basics of Git and try to clone the given repository.

---

## Solution

### Step 1: Clone repository

I can use `git clone [git_url]` to clone a repository to my local machine.

The challenge requires us to access the server through port `2220`, so I need to specify the port in the given Git URL. The given URL uses the SSH URL format, so I can specify the port directly in the URL:

```text
From: ssh://bandit27-git@bandit.labs.overthewire.org/home/bandit27-git/repo
To:   ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo
```

Then, use `git clone` with the modified URL:

```bash
git clone ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo
```

Git asks for the password of `bandit27-git`, which is the same as the password of `bandit27`, according to the challenge. After entering the password, the repository is cloned to my local machine.

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

There is a directory named `.git`. This directory contains Git's metadata, including information about the repository's history and configuration.

There is also a file named `README`. I read its contents:

```bash
cat README
```

Output:

```text
The password to the next level is: [bandit28_password]
```

The password for the next level is displayed directly in the `README` file.

## Key Takeaways

### Git

Git is a **distributed source-control system**. It allows multiple people to work on the same project by maintaining versions of the project's files and their history.

A repository can be cloned to a local machine, modified, and then synchronized with a remote repository by pushing and pulling changes.

Git commands:

- `git clone [git_url]` — creates a local copy of a Git repository, including its tracked files and Git history.


## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
