# Bandit Level 14

## Challenge

The password for the next level is stored in `/etc/bandit_pass/bandit14` and can only be read by user `bandit14`. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Look at the commands that logged you into previous bandit levels, and find out how to use the key for this level.

If you need help with this level: a hint file can be found in the home directory.

Make sure to read the error messages as they are informative.

---

## Initial Thoughts

In this level, to get the password, instead of using a password as normal, we have to use a private SSH key to log in as `bandit14`. Level 13 provides us with two files: `sshkey.private` could be the mentioned key and `HINT`. So I looked up how this login method works before using the hint to log in as `bandit14`. After some research, I found that this is just another way to access Bandit through SSH instead of using a password as usual.

---

## Solution

### Step 1: Take a glance at given files

```bash
pwd
ls -lah
cat sshkey.private
file sshkey.private
```

```text
sshkey.private: OpenSSH private key
```

`sshkey.private` seems like the private SSH key used to log in as `bandit14`.

```bash
cat HINT
```

Bandit warns us that the Bandit server doesn't support connections to itself, so I need to copy the key to my own computer and use it to access as `bandit14` later.

### Step 2: Copy private SSH key

```bash
exit
```

I return to my local machine's CLI.

```bash
scp -P 2220 bandit13@bandit.labs.overthewire.org:~/sshkey.private [private_path]
```

I used `scp` to copy the `sshkey.private` file to my computer. I also provide the connection information, similar to when I use `ssh` — the port is `2220` and the user is `bandit13`.

### Step 3: Log in as `bandit14` using private SSH key

```bash
ssh -p 2220 -i "[private_path]\sshkey.private" bandit14@bandit.labs.overthewire.org
```

Then I successfully logged in as `bandit14`.

```bash
cat /etc/bandit_pass/bandit14
```

Finally, I obtain the password.

## Key Takeaways

### ssh

Sercurely connects to a remote server and supports several authentication methods, including password authentication and public-key authentication.

- **Password authentication:** The user proves their identity by providing a password.
- **Public-key authentication:** The user proves possession of a private key corresponding to a public key stored by the server.

Syntax:

```bash
ssh [options] [host_name]
```

`[options]`

- `-i [key_file]` — specifies the private key used for authentication.

**Notes**: `ssh` does not send the private key to the server. The private key is used locally to produce cryptographic proof of possession. The server verifies that proof using the corresponding public key.

### scp

`scp` stands for **Secure Copy Protocol**. It is a command-line tool for securely copying files between computers over an SSH connection.

Syntax:

```bash
scp [options] [source_path] [destination_path]
```

`[options]`

- `-P [port]` — specifies the SSH port to connect to.

`[source_path]`: is the path of the file or directory to copy.

- Remote server: `server:[path]`
- Normal machine: `[path]`

`[destination_path]`: is where the copied file or directory should be stored.

- Remote server: `server:[path]`
- Normal machine: `[path]`

**Notes**: `scp` uses SSH connection as its underlying transport for securely transferring files, so we need to provide `scp` with connection information similar to what we provide when using `ssh`.

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
