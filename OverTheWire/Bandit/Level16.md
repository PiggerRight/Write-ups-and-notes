# Bandit Level 16

## Challenge

The password for the next level can be retrieved by submitting the password of the current level to port `30001` on localhost using SSL/TLS encryption.

---

## Initial Thoughts

In this level, we need to access port 30001 on the localhost of the Bandit server again. I already know how to connect to the localhost of the Bandit server, but this port requires an SSL/TLS connection. Therefore, I use openssl s_client to establish a TLS connection.

---

## Solution

### Step 1: Check the state of port `30001` on localhost

```bash
nmap -p 30001 localhost
```

The state is open, so a service is listening on this port and is reachable from localhost.

```bash
openssl s_client -connect localhost:30001
```

Then, I paste the password for bandit15 into the connection and receive the password for the next level.

## Key Takeaways

### SSL/TLS

**SSL/TLS** are protocols used to securely communicate over a network.

- **SSL** (Secure Sockets Layer) → older protocol, now obsolete/insecure.
- **TLS** (Transport Layer Security) → the modern protocol used today, the successor to **SSL**.

**TLS** provides:

- Encryption: Data is encrypted so someone intercepting the traffic cannot easily read it.
- Authentication: TLS can help verify that your browser is actually communicating with the intended server by using TLS certificates.
- Integrity: TLS helps detect whether data was modified while traveling across the network.

**TLS** usually runs on top of TCP and is commonly associated with the Presentation and Session layers in the OSI model. After 3-way handshake, TCP connection is established on Transport layer. Then, TLS relies on the TCP connection to establish a TLS connection, and TLS also perform a "handshake" first.

**TLS handshake** is performed to exchange required information that allows both sides to communicate sercurely. The process is:

1. `Client request`:
  - Supported TLS versions
  - Supported cipher suites
  - Client ECDHE public key

2. `Server response`:
  - Selected TLS version
  - Selected cipher suite
  - Sever ECDHE public key

3. Private calculation of Server and Client to get symmetric traffic key set:
  - Server: Server ECDHE private key + Client ECDHE public key ── ECDHE algorithm + Key derivation ──► Traffic key set
  - Client: Server ECDHE public key + Client ECDHE private key ── ECDHE algorithm + Key derivation ──► The same traffic key set
  - The traffic key set is not sent across the network, Client and Server calculate the key set by themself.
  - The key set have four main keys, each of them is used as a symmetric key for encryption of messages. After this step, hanshake traffic keys are used to encrypt **TLS handshake** messages.
    - Server handshake key (Derived at this step)
    - Client handshake key (Derived at this step)
    - Server application key (Derived during **TLS handshake**)
    - Client application key (Derived during **TLS handshake**)
  - An attacker who only observes the two ECDHE public keys cannot feasibly derive the key set without breaking the underlying cryptographic assumption.

4. Server send `Certificate`:
  - `Certificate` includes:
    - Domain: `example.com`
    - Certificate public key
    - CA's digital signature
  - The `Certificate` binds a domain name to a public key, called certificate public key. A trusted third party, called a Certificate Authority (CA), digitally signs the `Certificate` to attest to this binding. The Client verifies the CA's signature to make sure the binding. Now the Client just need to confirm does the communicating server posses the certificate private key or not, because IP address is not enough for authentication.

5. Server send `CertificateVerify`:
  - `CertificateVerify` is a digital signature created by using certificate private key to sign data derived from the TLS handshake transcript.
  - The Client uses certificate public key to verify the `CertificateVerify` against data derived from its TLS handshake transcript. Therefore, the Client can verify that the communicating server posses the certificate private key or not.
  - The TLS handshake transcript contains connection-specific values that depend on different TLS connections, so attacker cannot use `CertificateVerify` of a TLS connection for another TLS connection. This prevent the attackers to copy the `Certificate` or `CertificateVerify` to mimic the Server.

**Notes**: There are three relevant key pairs in this simplified explanation:

  - Client's ephemeral ECDHE key pair → key exchange
  - Server's ephemeral ECDHE key pair → key exchange
  - Server's certificate key pair → **authentication**

6. The Server and the Client respectively send `Finished message` to verify the integrity of the **TLS handshake**,...

After the **TLS handshake**, the Server and Client use their derived application traffic keys to protect application data. TLS uses AEAD algorithms to provide both **encryption** and **integrity**/**authentication** of each TLS record.

### openssl

A command-line toolkit and software library that implements cryptographic protocols and algorithms, especially SSL/TLS.

Syntax:

```bash
openssl [subcommand] [options]
```

**`[subcommand]`**

1. `s_client` — Connect to a SSL/TLS server

`[options]`

- `-connect [IP_address/domain]:[port]` — specify the server and port to connect to.
- `-state` — show detailed TLS handshake information
- `-showcerts` — show certificates
- `-servername [SNI]` — Specify the server name for Server Name Indication (SNI)

**Notes**: When using `openssl s_client` to connect to a TLS server over TCP, a TCP connection is established first. Then, the TLS handshake occurs over that TCP connection.

2. `s_server` — Create a simple TLS server

3. `version` — Check the OpenSSL version and build information

## References

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
