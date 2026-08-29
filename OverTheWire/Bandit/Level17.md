# Bandit Level 17

## Challenge

The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range `31000` to `32000`. First find out which of these ports have a server listening on them. Then find out which of those speak SSL/TLS and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.

---

## Initial Thoughts

In this level, we need to investigate the ports on localhost in the range `31000` to `32000` to find out which is reachable, uses SSL/TLS and returns the credentials for the next level.

---

## Solution

### Step 1: Investigate the ports on localhost

```bash
nmap -sV -p 31000-32000 localhost
```

Output:

```bash
PORT      STATE SERVICE     VERSION
31046/tcp open  echo
31518/tcp open  ssl/echo
31691/tcp open  echo
31790/tcp open  ssl/unknown
31960/tcp open  echo
```

I used `-sV` to perform service and version detection, which can help identify whether a service uses SSL/TLS.

As we can see, only five ports are open. Two of them appear to use SSL/TLS:

```text
PORT      STATE SERVICE     VERSION
31518/tcp open  ssl/echo
31790/tcp open  ssl/unknown
```

According to the challenge, only one server returns the credentials, while the others simply echo back whatever we send. Therefore, the `ssl/unknown` service on port `31790` is the likely target.

### Step 2: Retrieve credentials

At first, I tried to connect using openssl s_client, as in the previous level. 

```bash
openssl s_client -connect localhost:31790
```

However, when I submitted the password, it returned `KEYUPDATE`. The helpful note in the challenge suggested reading the `CONNECTED COMMANDS` section of the `openssl s_client` manual page.

When `s_client` is used interactively, some input can be interpreted as special commands that control the connection instead of being sent as application data. The password for the current level begins with the letter `k`, which can be interpreted as the `s_client` command for requesting a TLS KeyUpdate.

To prevent `s_client` from interpreting the input as an interactive command, I used:

```bash
openssl s_client -connect localhost:31790 -quiet
```

Alternatively, the `-nocommands` option can disable interactive command processing:

```bash
openssl s_client -connect localhost:31790 -nocommands
```

After submitting the password correctly, the server returned the credentials for the next level: an OpenSSH private key.

### Step 3: Sign in as `bandit17` using credentials

I copied the private key into a file on my local machine. The file must contain only the complete key:

```text
-----BEGIN OPENSSH PRIVATE KEY-----
...
-----END OPENSSH PRIVATE KEY-----
```

```bash
echo "[key_content]" > private.key
```

Then I used the private key to log in as `bandit17`:

```bash
ssh -i private.key -p 2220 bandit17@bandit.labs.overthewire.org
```

However, SSH rejected the key because its permissions were open with others:

```text
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0644 for 'key.key' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "key.key": bad permissions
```

Therefore, I restricted its permissions:

```bash
chmod 600 private.key
```

This gives the owner read and write permissions while giving the group and others no permissions.

Now, I could use the private key to log in as `bandit17`:

```bash
ssh -i private.key -p 2220 bandit17@bandit.labs.overthewire.org
```

**Notes**: When copying the private key between Windows and Linux, Windows-style CRLF use `\r\n` for line endings instead of `\n` as Linux. This causes problems with some Linux tools. We can use `cat -A` to reveal invisible characters. If necessary, the carriage return characters can be removed with `sed -i 's/\r$//' private.key`. Alternatively, the key can be copied directly into a new file in a Linux environment to avoid the line-ending conversion issue.

Finally, after successfully logging in:

```bash
cat /etc/bandit_pass/bandit17
```

I retrieved the password for the next level.

## Key Takeaways

### chmod

Change the permissions of files and directories.

Syntax:
```bash
chmod [permission_number] [files...]
```

`[permission_number]` — a 3-digits integer and each digit defines the permission (read, write and execute) of user, group and others.

- Permission value:
  - Read    = 4
  - Write   = 2
  - Execute = 1
- Permission number: (Eg: 600)
  - 6 = Read (4) + Write (2)  = rw-  → Owner:  read and write
  - 0 = No permissions        = ---  → Group:  no permissions
  - 0 = No permissions        = ---  → Others: no permissions
  - Permission: -rw------- (we can see this in the `ls -l` permission collumn)

### sed

A stream editor to read and transform text.

Syntax:

```bash
sed [options] '[sed_command]' [files...]
```

`[options] '[sed_command]'`

- `-n 'np'` — print line `n` (`$` represent last line)
- `-n 'n,mp'` — print lines `n-m`
- `'nd'` — delete line `n` from the output
- `'nd;md'` — delete line `n` and `m` from the output
- `'s/old/new/'` — replace the first occurence of `old` with `new` on each line
- `'s/old/new/g'` — replace all occurences of `old` with `new` on each line

`[options]`

- `-i` — directly modify the file(s)

**Notes**:

- Line-oriented processing: `\n` is treated as a separator between lines and is often removed from the line being processed.
- Byte-oriented processing: `\n` remains an actual byte in the input.

### openssl s_client

`[options]`

- `-quiet` — changes `s_client` interactive behavior and output by suppressing most connection information and disabling interactive command processing, allowing user input to be sent as application data over the TLS connection.
- `-nocommands` — disables interactive command processing, allowing user input to be sent as application data over the TLS connection.

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
