# Bandit Level 21

## Challenge

There is a setuid binary in the `home` directory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (`bandit20`). If the password is correct, it will transmit the password for the next level (`bandit21`).

---

## Initial Thoughts

In this level, the challenge provides a setuid binary that connects to a specified port on localhost. It then reads a line of text from the connection and compares it with the password for `bandit20`.

Therefore, I need to create my own network listener on a available port on the Bandit server and use the provided setuid binary to connect to it.

---

## Solution

### Step 1: Establish a listener

First, I logged in to the Bandit server as `bandit20`.

To establish a listener, I use `nc` to listen for TCP connections on port `12345`:

```bash
nc -l localhost 12345
```

The port needs to be available and usable by the current user. Traditionally, ports below `1024` require elevated privileges, so I chose an unused port above `1024`.

After the listener was running, I entered the password for `bandit20`. This password would be sent through the connection when `suconnect` connected to the listener.

### Step 2: Connect to the listener using the provided setuid binary

Next, I opened another terminal session and logged in to the Bandit server as `bandit20` again.

I checked the contents of the current directory and examined the provided `suconnect` file.

```bash
pwd
ls -lah
file suconnect
```

`suconnect` is the provided setuid binary. Running it without arguments displays information about how to use it.

I then used `suconnect` to establish a TCP connection to port `12345` on localhost.

```bash
./suconnect 12345
```

The process works conceptually as follows:

```text
Terminal 1                         Terminal 2

nc listening on 12345              ./suconnect 12345
        │                                   │
        │ ◄──── TCP connection ──────────── │
        │                                   │
        │ ───── bandit20 password ────────► │
        │                                   │
        │ ◄─── bandit21 password ────────── │
```

Since `suconnect` is a setuid binary owned by `bandit21`, it reads the password for `bandit20` and compares it with the line received from the connection.

Because the password I sent was correct, `suconnect` transmitted the password for `bandit21` back through the connection. The `nc` listener then displayed the password for the next level.

## Key Takeaways

### Ports

Linux traditionally restricts normal users from binding to ports below `1024`:

- `0–1023`       → privileged ports
- `1024–65535`   → generally available to normal users

This restriction helps prevent unprivileged users from binding to ports traditionally associated with important system services.

Also a port can fail for other reasons. For example:

- `Permission denied`       → the current user does not have permission to bind to the port.
- `Address already in use`  → another process is already using the address and port combination.

Therefore, when creating a listener, the port should be both:

- Usable by the current user.
- Not already being used by another process.

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
