# Bandit Level 0

## Challenge

The goal of this level is for you to log into the game using SSH. The host to which you need to connect is bandit.labs.overthewire.org, on port 2220. The username is bandit0 and the password is bandit0.

---

## Initial Thoughts

The challenge mentions the `ssh` command in the **"Commands you may need to solve this level"** section.

The challenge also provides four pieces of information:

- Host: `bandit.labs.overthewire.org`
- Port: `2220`
- Username: `bandit0`
- Password: `bandit0`

So I need to search for this command syntax.

---

## Solution

### Step 1: Connect using the default SSH command

```bash
ssh bandit.labs.overthewire.org
```

This returned the following message:

> You are trying to log into this SSH server on port 22, which is not intended.

From this, I learned that SSH connects to port **22** by default, while the required port is **2220**

### Step 2: Specify the port

```bash
ssh -p 2220 bandit.labs.overthewire.org
```

This time it requires a username, because I haven't specified one.

### Step 3: Specify the username

```bash
ssh -p 2220 -l bandit0 bandit.labs.overthewire.org
```

After I enter the password `bandit0`, I successfully logged in to the Bandit server.

---

## Key Takeaways

### ssh 

Securely connects to a remote machine.

Syntax:

```bash
ssh [options] [host_name]
```

Useful Options:

- `-p [port_number]` — specify the port number. The default port is 22.
- `-l [user_name]` — specify the login username. It is equivalent to writing the username before the host name using the `[user_name]@[host_name]` syntax.

Example:

```bash
ssh -p 2220 -l bandit0 bandit.labs.overthewire.org
```

Equivalent syntax:

```bash
ssh -p 2220 bandit0@bandit.labs.overthewire.org
```

---

## References

- `man ssh`
- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
