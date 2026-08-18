# RSA

  The first time I heard about **RSA** was when I watched a short video explaining it, and I completely misunderstood what they were talking about.

  When I had to deal with Level 13 of OverTheWire's Bandit, I encountered this concept again. This time, I decided to dive into **RSA** and actually understand how it works.

  So, this write-up is about what I learned while diving into **RSA**.

---

## 1. What is Asymmetric cryptography

  In cryptography, there are several major areas:

  - Symmetric cryptography
  - Asymmetric cryptography
  - Hash functions
  - ...

  And **RSA** belongs to **Asymmetric cryptography**. To explain what **Asymmetric cryptography** is, I will tell a "legend" that I guess almost every cryptographer has heard instead of giving a boring definition.

  Suppose we have two students — Bob and Alice. Alice wants to send Bob her home password securely, but they are only able to communicate by letter. Alice cannot simply send the password to Bob because anyone can see it without permission during the sending process, so Alice decides to encrypt the password. With classical encryption (**Symmetric cryptography**), such as a simple substitution cipher, Alice and Bob need a secret key to encrypt and decrypt the password, but Bob doesn't have the key yet. If Alice sends the secret key together with the ciphertext, it doesn't make sense from security perspective.

  So Alice continues with another encryption idea about creating something like a key and a lock, where only the person who owns the key can open the lock to see the password — this is the basic idea of **Asymmetric cryptography**.

  In **Asymmetric cryptography**, the lock is the **public key** and the key is the **private key**. Alice's password will be encrypted using the **public key** and only the **private key** is able to decrypt it. So Bob creates a pair of keys, sends the **public key** to Alice, and keeps the **private key** safe. Alice encrypts the password using the **public key** and sends only the ciphertext to Bob. Finally, Bob can get the password by decrypting the ciphertext using the **private key**.

### Uses of Asymmetric Cryptography

  - Encryption — confidentiality
  - Digital signatures — authentication & integrity

### Asymmetric cryptography in Encryption

  The problem here is quite similar to the story I just told — sending a small piece of data securely. 

  The way here is that:

  - Alice wants to send message to Bob
  - Bob generates a key pair and sends the public key to Alice
  - Alice uses the public key to encrypt the message and sends the ciphertext to Bob
  - Bob decrypts the ciphertext using the private key and gets the original message

  The **confidentiality** is maintained because anyone obtains the ciphertext cannot read it without private key.

  The reason why I said "small piece" is that Symmetric cryptography is much more efficient to encrypt and decrypt a huge amount of data. However, as I have mentioned before, the problem occurs when Alice and Bob have never communicated. Therefore Asymmetric cryptography can be used to establish or exchange a symmetric session key. After that, they can use Symmetric cryptography to encrypt the messages.

### Asymmetric cryptography in Digital signatures

  The problem here is different. Now Alice has sent a contract to Bob and she wants Bob to be able to confirm that the contract wasn't changed by anyone else.
  
  The way here is that:
  
  - Alice creates a digital signature from the original contract using her private key
  - Alice sends to Bob the contract, public key and the digital signature
  - Bob can use the public key to verify the digital signature against the given contract
  - If the signature is valid, Bob can confirm that the given contract hasn't been changed
  
  The **integrity** is maintained because if anyone has changed the contract, the digital signature will no longer be valid for the changed one. To create a new valid signature for the modified contract, they will need the private key.

  The **authentication** is maintained in reality because digital signature systems often use certificates or another trusted mechanism to bind a public key to an identity, so that Bob can trust that the public key belongs to Alice.

---

## 2. RSA — A Mathematical Mechanism for Asymmetric cryptography

  We have known what is Asymmetric cryptography and its usage, but how to make the "lock and key" mechanism works on computers, how to generate public key and private key ???

  In general, key pairs are generated using mathematical formulas. These formulas are easy to compute in the intended direction but difficult in the inverse direction.

  **RSA**, named after its inventors Rivest, Shamir, and Adleman, is also an asymmetric cryptographic algorithm whose key generation and cryptographic operations are based on mathematical theory.

### General idea

  At first we have some **values**:
  
  - Choose two prime numbers: `p` and `q`
  - `n = p.q`
  - Choose `φ(n)` is number of positive integers that less than and coprime with `n` → Using Euler totient function: `φ(n) = (p - 1).(q - 1)`
  - Choose `e` and `d` that `e.d ≡ 1 (mod φ(n))` → so `e` is coprime with `φ(n)` → choose `e` first then find feasible `d`

  **Prove**: Euler's totient function

  - With `p`,`q` are primes and `n = p.q`
  - There are `p.q - 1` positive integers less than `n`
  - Multiples of `p` is `(p,2p,3p,...,(q-1).p)` → `q - 1`
  - Multiples of `q` is `(q,2q,3q,...,(p-1).q)` → `p - 1`
  - So the number of positive integers that are coprime with `n` is `φ(n) = p.q - 1 - (q - 1) - (p - 1) = p.q - p - q + 1 = (p-1).(q-1)` (Proved)

  **Prove**: why `e` is coprime with `φ(n)`

  - We have: `e.d ≡ 1 (mod φ(n))` ⇔ `e.d = 1 + φ(n).k` with `k` is an integer
  - If `e` isn't coprime with `φ(n)` ⇔ `e = z.x` and `φ(n) = z.y` with `z`,`x`,`y` are integers
  - So now: `e.d = 1 + φ(n).k` ⇔ `z.x.d = 1 + z.y.k`
  - Because `z.x.d ≡ 0 (mod z)` and `1 + z.y.k ≡ 1 (mod z)` → `z.x.d = 1 + z.y.k` false so `e.d ≡ 1 (mod φ(n))` will not happen
  - So `e` must be coprime to `φ(n)` (Proved)

  Then, we can define **public key** and **private key**:
  
  - Public key: `(n,e)`
  - Private key: `(n,d)`

  This is where **encryption and decryption** come from:

  - We have an positive integer `m`, which is coprime with and less than `n`, as a message
  - To encrypt with public key, we use the formula `m^e ≡ c (mod n)` with `c` is the ciphertext
  - To decrypt with private key, we use the formula `c^d ≡ m (mod n)` and we can obtain the original message

### Why these two formulas have this *undo* properties ???

  Let start with a common property of `mod` operator: `a.b mod n = [(a mod n).(b mod n)] mod n` → `a^b mod n = [(a mod n)^b] mod n`

  So to prove that:
  
  - `m^e ≡ c (mod n)`
  - `c^d ≡ m (mod n)`

  ⇔ Prove that: `m^(e.d) ≡ m (mod n)`

  Because `e.d ≡ 1 (mod φ(n))` → `e.d = k.φ(n) + 1` with k is an integer

  ⇔ Prove that: `m^(k.φ(n) + 1) ≡ m (mod n)` ⇔ `m.m^(k.φ(n)) ≡ m (mod n)` ⇔ `m^(k.φ(n)) ≡ 1 (mod n)`

  Apply Euler's theorem, with `m` is coprime with `n` →  `m^φ(n) ≡ 1 (mod n)` ⇔ `(m^φ(n))^k ≡ 1 (mod n)` (Proved)

  **Prove**: Euler's theorem

  - There are `φ(n)` positive integers that are less than and coprime with `n`, let them belong to set `P` and we get a sequence `r_1`,`r_2`,...,`r_φ`
  - With `r_i` is an element of `P`
  - When we multiply each `r_i` with `m` and take `mod n`, we can assume that the new sequence is just another arrangement of the original sequence. This is because:
    - `s = m.r_i mod n` is still belongs to `P`: If not, with there will exist `x` that is the factor of `s` and `n` → `s = m.r_i + k.n` → `m.r_i = s - k.n` but when we take `mod x` of both side, while `(s - k.n) mod x = 0`, `m.r_i mod x != 0` because `m` and `r_i` are coprime with `n` so `x` cannot be the factor of them → Proved.
    - Does not exist a pair `r_i` and `r_j` that holds the same value after transformation: Suppose it exists → `m.r_i mod n = m.r_j mod n` ⇔ `r_i mod n = r_j mod n` ⇔ `r_i = r_j + k.n` → with `k` is an integer, if `k = 0`, `r_i = r_j`, so what we supposed is wrong.

### How RSA acquires the property of *easy to be computed in the intended direction but seem impossible in the inverse direction*

  This property will be met by choosing sufficiently large `p` and `q` so that factoring `n` is computationally infeasible with known classical algorithms. Everyone knows the public key - `(n,e)`, and the ciphertext but to obtain the private key - (n,d), or decrypt ciphertext by using only the public key are difficult:
  
  - By decrypting ciphertext using only public key: The formula `m^e ≡ c (mod n)` is impossible to reverse because we know `c` but there are many `m` that match the formula excluding the real `m`
  - By trying to obtain private key: with huge `n`, it seems impossible for normal computers to compute the exact pair of `p` and `q` → unable to find `d`

### Small example

  Values:
  
  - `p = 5`, `q = 11`
  - `n = 55`
  - `φ(n) = 40`
  - `e = 3` → `d = 27`

  With message is `m = 7`

  - Encryption: `c = m^e mod n` → `c = 13`
  - Decryption: `m = c^d mod n` → `m = 7`

---

## 3. RSA and Nowadays problems
