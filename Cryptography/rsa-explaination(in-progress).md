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

  In common, the pairs of keys are generated based on several mathematical formulars. These formulars are easy to be computed in the intended direction but seem impossible in the inverse direction.

  **RSA**, stands for "Rivest-Shamir-Adleman" — the inventors of **RSA**, is also a mathematical formular for generating keys.

### General idea

  At first we have some **values**:
  
  - Choose two prime numbers: `p` and `q`
  - `n = p.q`
  - Choose `φ` is number of integers that less than and coprime with `n` → Using Euler totient function: `φ(n) = (p - 1).(q - 1)`
  - Choose `e` and `d` that `e.d ≡ 1 (mod φ)` → so `e` is coprime with `φ`

  **Prove**: Euler's totient function

  - With `p`,`q` are primes and `n = p.q`
  - There are `p.q - 1` integers less than `n`
  - The number of integers that are prime with `n` by `p` is `q - 1`: `(p,2p,3p,...,(q-1).p)`
  - The number of integers that are prime with `n` by `q` is `p - 1`: `(q,2q,3q,...,(p-1).q)`
  - So the number of integers that are coprime with `n` is `φ(n) = p.q - 1 - (q - 1) - (p - 1) = p.q - p - q + 1 = (p-1).(q-1)`

  **Prove**: why `e` is coprime with `φ`

  - We have: `e.d ≡ 1 (mod φ)` ⇔ `e.d = 1 + φ.k` with `k` is an integer
  - If `e` isn't prime to `φ` ⇔ `e = z.x` and `φ = z.y` with `z`,`x`,`y` are integers
  - So now: `e.d = 1 + φ.k` ⇔ `z.x.d = 1 + z.y.k`
  - Because `z.x.d ≡ 0 (mod z)` and `1 + z.y.k ≡ 1 (mod z)` → `z.x.d = 1 + z.y.k` false so `e.d ≡ 1 (mod φ)` will not happen
  - So `e` must be prime to `φ` (Proved)

  Then, we can define **public key** and **private key**:
  
  - Public key: `(n,e)`
  - Private key: `(n,d)`

  This is where **encryption and decryption** come from:

  - We have an integer `m`, which is coprime with and less than `n`, as a message
  - To encrypt with private key, we use the formular `m^d ≡ c (mod n)` with `c` is the ciphertext
  - To decrypt with public key, we use the formular `c^e ≡ m (mod n)` and we can obtain the original message

### Why these two formulars have this *undo* properties ???

  Let start with a common property of mod operator: `a.b mod n = (a mod n).(b mod n)` → `a^b mod n = (a mod n)^b`

  So to prove that:
  
  - `m^d ≡ c (mod n)`
  - `c^e ≡ m (mod n)`

  ⇔ Prove that: `m^(e.d) ≡ m (mod n)`

  Because `e.q ≡ 1 (mode φ)` → `e.q = k.φ + 1` with k is an integer

  ⇔ Prove that: `m^(k.φ + 1) ≡ m (mod n)` ⇔ `m.m^(k.φ) ≡ m (mod n)` ⇔ `m^(k.φ) ≡ 1 (mod n)`

  Apply Euler's theorem, with `m` is coprime with `n` →  `m^φ ≡ 1 (mod n)`

  **Prove**: Euler's theorem

  - There are `φ(n)` integers that are less than and coprime with `n`, let them belong to set `P` and we get a sequence `r_1`,`r_2`,...,`r_φ`
  - With `r_i` is an element of `P`
  - Because `m` also belongs to `p` → `m.p` is coprime to `n` → `m.p mod n` belongs to `P`
  - When we multiply each `r_i` with `m` and take `mod n`, we can assume that the new sequence is just another arrangement of the original sequence. This is because:
    - `s = m.r_i mod n` is still belongs to `P`: If not, with there will exist `x` that is the factor of `s` and `n` → `s = m.r_i + k.n` → `m.r_i = s - k.n` but when we take `mod x` of both side, while `(s - k.n) mod x = 0`, `m.r_i mod x != 0` because `m` and `r_i` are coprime with `n` so `x` cannot be the factor of them → Proved.
    - 

### How RSA acquires the property of *easy to be computed in the intended direction but seem impossible in the inverse direction*
