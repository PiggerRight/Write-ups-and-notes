# Bandit Level 15

## Challenge

The password for the next level can be retrieved by submitting the password of the current level to port `30000` on localhost.

---

## Initial Thoughts

In this level, we need to access port `30000` on the localhost of the Bandit server. At first, I thought I could access it from my own machine, but I couldn't (`bandit.labs.overthewire.org` - port `30000`), so I logged in as a Bandit user.

---

## Solution

### Step 1: Try accessing it from outside

```bash
nmap -p 30000 bandit.labs.overthewire.org
```

Now, I can confirm that port `30000` may not accessible from outside, because its state is `filtered`.

**Notes**: I do not use `localhost` here, because I am currently using my own computer's CLI.

### Step 2: Access the service through localhost

```bash
ssh -p 2220 bandit14@bandit.labs.overthewire.org
```

I log in as `bandit14` as usual.

```bash
nmap -p 30000 localhost
```

Now, the state changes to `open`, this means that port `30000` is now reachable.

**Notes**: At this point, using URL also returns the same output. However, URL is resolved to the server's IP address instead of loopback address.

```bash
nc localhost 30000
```

Actually, I don't know what service is running on port `30000`, so I use `nc` to connect to it. Finally, I paste the password of `bandit14` in and get the password for `bandit15`.

## Key Takeaways

### Localhost

**Localhost** is a hostname that represents the local host (the machine or environment on which the program is running). It allows a computer to make a connection to itself, which is known as loopback.

The standard loopback addresses for localhost are:

- IPv4 — `127.0.0.1`
- IPv6 — `::1`

On Linux, we can use the hostname `localhost` to refer to the loopback IP address. Depending on the system's configuration, `localhost` can resolve to `127.0.0.1`, `::1`, or both.

Commands to look up the localhost IP address and what `localhost` resolves to:

```bash
cat /etc/hosts
```

### Ports

A port is a numbered endpoint used by network services to receive network connections. A service can listen on a specific port, and clients can connect to that port to communicate with the service. Firewall rules can control which connections are allowed.

### nmap

Scans a target and reports reachable ports and their services.

Syntax:

```bash
nmap [options] [IP_address]
```

Example output:

```text
PORT     STATE     SERVICE
22/tcp   open      ssh
80/tcp   filtered  http
```

- `open` — Nmap determined that a service is listening and reachable on that port
- `closed` — Nmap reached the host, but no service is listening on that port
- `filtered` — Nmap cannot determine the state because filtering interferes on that port

`[options]`

- `-p [Ports...]` — Scan specific port(s)
- `-p-` — Scan all TCP ports (1–65535)
- `-sV` — Detect service/version

### nc

Creates a TCP (default) or UDP connection and sends/receives data.

Syntax:

```bash
nc [options] [IP_address] [Port]
```

`[options]`

- `-l` — listen for incoming connections
- `-u` — create UDP connection

**Notes**: Unlike `ssh`, which requires the connected port to use SSH service that has many application protocols, `nc` just requires connected port to establish a connection, and exchange data without any required application protocols. Therefore, `nc` is useful when investigating an unknown service.

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
