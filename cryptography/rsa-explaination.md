# RSA

## Backstory

  The first time I heard about **RSA** was when I watched a short video explaining how it works, and I completely misunderstood what they were talking about.

  When I reached Level 13 of OverTheWire's Bandit, I encountered this concept again. This time, I decided to dive into **RSA** and actually understand how it works.

  So, this write-up is about what I learned while diving into **RSA**.

---

## 1. What is Asymmetric cryptography

  In cryptography, there are several major areas:

  - Symmetric cryptography
  - Asymmetric cryptography
  - Hash functions
  - ...

  RSA is an asymmetric cryptographic algorithm. To explain what Asymmetric cryptography is, I will tell a "legend" that many people in cryptography have probably heard, instead of giving a boring definition.

  Suppose we have two students, Bob and Alice. Alice wants to send Bob her home password securely, but they are only able to communicate by letter. Alice cannot simply send the password to Bob because anyone could intercept and read it during transmission, so Alice decides to encrypt the password. With classical encryption (**Symmetric cryptography**), such as a simple substitution cipher, Alice and Bob need a secret key to encrypt and decrypt the password, but Bob doesn't have the key yet. If Alice sends the secret key together with the ciphertext, it doesn't make sense from a security perspective.

  So Alice comes up with another idea about creating something like a key and a lock, where only the person who owns the key can open the lock to see the password — this is the basic idea of asymmetric cryptography.

  In asymmetric cryptography, the lock is the **public key** and the key is the **private key**. Alice's password will be encrypted using the **public key**, and only the **private key** can decrypt it. So Bob creates a pair of keys, sends the **public key** to Alice, and keeps the **private key** safe. Alice encrypts the password using the **public key** and sends only the ciphertext to Bob. Finally, Bob can get the password by decrypting the ciphertext using the **private key**.

### Uses of symmetric cryptography

  - Encryption — confidentiality
  - Digital signatures — authentication and integrity

### Asymmetric cryptography in Encryption

  The problem here is quite similar to the story I just told — sending a small piece of data securely. 

  The process is:

  - Alice wants to send a message to Bob.
  - Bob generates a key pair and sends the public key to Alice.
  - Alice uses the public key to encrypt the message and sends the ciphertext to Bob.
  - Bob decrypts the ciphertext using the private key and gets the original message.

  **Confidentiality** is maintained because anyone who obtains the ciphertext cannot read it without the private key.

  The reason I said "small piece" is that symmetric cryptography is much more efficient for encrypting and decrypting large amount of data. However, as I have mentioned before, the problem occurs when Alice and Bob do not already share a secret key. Therefore, asymmetric cryptography can be used to establish or exchange a symmetric session key. After that, they can use symmetric cryptography to encrypt their messages.

### Asymmetric cryptography in Digital signatures

  The problem here is different. Now Alice wants to send a contract to Bob and allow Bob to verify that the contract hasn't been changed by anyone else.
  
  The process is:
  
  - Alice creates a digital signature for the original contract using her private key.
  - Alice sends to Bob the contract, the public key, and the digital signature.
  - Bob can use the public key to verify the digital signature against the contract.
  - If the signature is valid, Bob can confirm that the contract hasn't been changed.
  
  **Integrity** is maintained because if anyone changes the contract, the digital signature will no longer be valid for the modified contract. To create a new valid signature for the modified contract, attackers will need access to the private key.

  **Authentication** is established in practice because digital signature systems often use certificates or another trusted mechanism to bind a public key to an identity, so that Bob can trust that the public key belongs to Alice.

---

## 2. RSA — A Mathematical Mechanism for Asymmetric cryptography

  We know what Asymmetric cryptography is and what it is used for, but how to make the "lock and key" mechanism work on computers, how to generate a public key and a private key ?

  In general, key pairs are generated using mathematical formulas. These formulas are easy to compute in the intended direction but difficult in the inverse direction.

  **RSA**, named after its inventors Rivest, Shamir, and Adleman, is also an asymmetric cryptographic algorithm whose key generation and cryptographic operations are based on mathematical theory.

### Mathematical foundations

  Let's start with some common properties:
  
  - `1` is coprime to every integer ⇔ For a positive integer `a`, `gcd(1,a) = 1`
  - `a.b ≡ (a mod n).(b mod n) (mod n)` → `a^b ≡ (a mod n)^b (mod n)`
  - If `gcd(m,n) = 1`, then `a ≡ b (mod n) ⇔ m.a ≡ m.b (mod n)`
  - Euler's totient function counts the number of positive integers that are less than `n` and coprime to `n`,: `φ(n) = (p - 1).(q - 1)` (`p` and `q` are distinct primes)
  - If `e.d ≡ 1 (mod φ(n))`, then `gcd(φ(n),e) = 1`
  - Euler's theorem: If `gcd(m,n) = 1`,  `m^φ(n) ≡ 1 (mod n)` 

  **Prove**: Euler's totient function

  - Let `p` and `q` be primes, and let `n = p.q`
  - There are `p.q - 1` positive integers less than `n`
  - Multiples of `p` are `(p,2p,3p,...,(q-1).p)` → `q - 1`
  - Multiples of `q` are `(q,2q,3q,...,(p-1).q)` → `p - 1`
  - Therefore, the number of positive integers that are coprime to `n` is `φ(n) = p.q - 1 - (q - 1) - (p - 1) = p.q - p - q + 1 = (p-1).(q-1)` (Proved)

  **Prove**: If `e.d ≡ 1 (mod φ(n))` → `gcd(φ(n),e) = 1`

  - We have: `e.d ≡ 1 (mod φ(n))` ⇔ `e.d = 1 + φ(n).k` where `k` is an integer
  - If `gcd(φ(n),e) ≠ 1` ⇔ `e = z.x` and `φ(n) = z.y` where `z`,`x`, and `y` are integers
  - So now: `e.d = 1 + φ(n).k` ⇔ `z.x.d = 1 + z.y.k`
  - Because `z.x.d ≡ 0 (mod z)` and `1 + z.y.k ≡ 1 (mod z)` ⇔ `z.x.d ≠ 1 + z.y.k` → `gcd(φ(n),e) ≠ 1`, which contradicts our assumption
  - So `e` must be coprime to `φ(n)` ⇔ `gcd(φ(n),e) = 1` (Proved)

  **Prove**: Euler's theorem: If `gcd(m,n) = 1`, `m^φ(n) ≡ 1 (mod n)`

  - There are `φ(n)` positive integers that are less than `n` and coprime to `n`, let them form the set `P`
  - Let `r_i` be an element of `P` → we have a sequence `r_1`,`r_2`,...,`r_φ`
  - When we multiply each `r_i` with `m` and take `mod n`, we can show that the resulting sequence is another permutation of the original sequence. This is because:
    - `s = m.r_i mod n` is also an element of `P`: If not, there exist `x` that is a common factor of `s` and `n` → `s = m.r_i + k.n` ⇔ `m.r_i = s - k.n` but when we take `mod x` of both sides, while `(s - k.n) mod x = 0`, `m.r_i mod x ≠ 0` because `m` and `r_i` are coprime with `n` so `x` cannot be the factor of them, which contradicts our assumption
    - There is no pair `r_i` and `r_j` (`i` ≠ `j`) that produces the same value after the transformation: If it exists, `m.r_i mod n = m.r_j mod n`. Because `gcd(m,n) = 1` → `r_i mod n = r_j mod n` ⇔ `r_i = r_j + k.n` with `k` is an integer → Because `r_i` and `r_j` are in the range `[1,n-1]`, so `r_i = r_j`, which contradicts our assumption
  - So `r_1.r_2...r_φ = (m.r_1 mod n).(m.r_2 mod n)...(m.r_φ mod n)`
  - Equivalently, `r_1.r_2...r_φ ≡ (m.r_1 mod n).(m.r_2 mod n)...(m.r_φ mod n) (mod n)`
  - Equivalently, `r_1.r_2...r_φ ≡ m.r_1.m.r_2...m.r_φ (mod n)`
  - Equivalently, `r_1.r_2...r_φ ≡ (m^φ).(r_1.r_2...r_φ) (mod n)`
  - Because `gcd(r_1.r_2...r_φ,n) = 1` → `1 ≡ m^φ (mod n)` (Proved)

### General idea of RSA

  First, we choose some **values**:
  
  - Choose two distinct prime numbers: `p` and `q`
  - `n = p.q`
  - Find `φ(n)`, the number of positive integers that are less than `n` and coprime to `n` → Using Euler totient function: `φ(n) = (p - 1).(q - 1)`
  - To choose `e` and `d` such that `e.d ≡ 1 (mod φ(n))`: Choose `e` such that it is coprime to `φ(n)`, then find a suitable `d`

  Then, we define **public key** and **private key**:
  
  - Public key: `(n,e)`
  - Private key: `(n,d)`

  This is where **encryption and decryption** come from:

  - We have a positive integer `m`, which is less than `n` and coprime to `n`, as a message
  - To encrypt with public key, we use the formula `m^e mod n = c` where `c` is the ciphertext
  - To decrypt with private key, we use the formula `c^d mod n = m` and we obtain the original message

### Small example

  Values:
  
  - `p = 5`, `q = 11`
  - `n = 55`
  - `φ(n) = 40`
  - `e = 3` → `d = 27`

  Let the message be `m = 7`

  - Encryption: `c = m^e mod n` → `c = 13`
  - Decryption: `m = c^d mod n` → `m = 7`

### Why do these two formulas have *undo* property?

  To prove this, we need to show that:
  
  - `m^e mod n = c`
  - `c^d mod n = m`

  ⇔ Prove that: `m^(e.d) mod n = m`

  Because `e.d ≡ 1 (mod φ(n))` → `e.d = k.φ(n) + 1` where k is an integer

  ⇔ Prove that: `m^(k.φ(n) + 1) mod n = m` ⇔ `m.m^(k.φ(n)) mod n = m` ⇔ Prove that: `m^(k.φ(n)) mod n = 1`

  Applying Euler's theorem, since `gcd(m,n) = 1` → `m^φ(n) ≡ 1 (mod n)` ⇔ `(m^φ(n))^k ≡ 1 (mod n)` (Proved)

### How does RSA achieve the property of *easy to computed in the intended direction but difficult to reverse*

This property is achieved by choosing sufficiently large `p` and `q` so that factoring `n` is computationally infeasible with known classical algorithms. The public key — `(n,e)` is known to everyone, so I think there are several possible approaches to breaking RSA:
  
**By directly decrypting ciphertext using public key**

  - With the formula `m^e ≡ c (mod n)`, attackers can try all value of `m` that seem feasible but this only works with small `m`.
  - If we transform the formula a bit `m = (k.n + c)^(1/e)`, the attackers don't know `k`. However, this also works with small `m`, which leads to a small `k`.
    
  As we can see, this approach mainly relies on brute-force techniques but only works with small `m`. For this reason, real RSA encryption does not encrypt raw messages directly, instead we use secure padding schemes such as OAEP to mitigate this problem.
    
**By trying to obtain private key**

  Now, in order to obtain `d`, attackers need to find `p` and `q` first by factoring `n = p.q`. When `n` is sufficiently large, it takes an infeasible amount of time for normal computers to factor `n` → making it challenging to obtain `d`.

**By trying to create new private key**

  Now, we need to find another `d` that match the property `e.d ≡ 1 (mod φ(n))`, but you need to find `φ(n)` first. This also lead to the problem of finding `p` and `q`.

On the other hand, with huge `n`,`e`, and `d`, does it possible to compute `m^e mod n = c` and `c^d mod n = m` in an appropriate time? The answer is possible, we have two mathematics tools to solve this problem.

- **Modular reduction**: `a.b ≡ (a mod n).(b mod n) (mod n)` → This lets us keep intermediate numbers small.
- **Binary exponentiation**: Instead of calculating `m^e` directly, we break the exponent into powers of 2. → So even though `m^e` would be astronomically large, computation is practical.

Therefore, RSA encryption relies on the computational difficulty of certain mathematical problems. Even when the original message is small, secure padding schemes such as OAEP can be used to prevent attacks based on small messages. This algorithm is designed to maintain security against attackers using currently available classical computing resources, because the computational resources required to break RSA are infeasible with current technology. But what happens if attackers have access to more powerful computing resources?

---

## 3. RSA and the Quantum Computing Problem

  As I mentioned, RSA relies on computational difficulty to maintain security against attackers using current technology. As classical computing continues to improve, we can increase the size of numbers used in RSA, making the computations more difficult. However, there is a fundamental problem: increasing the problem size can only deal with improvements in classical computing for so long.

  Everything changes with the development of quantum computing. Quantum computers can solve certain mathematical problems much more efficiently than classical computers. In particular, Shor's algorithm can efficiently solve the integer factorization problem underlying RSA on a sufficiently powerful quantum computer. Since RSA's security heavily depends on the computational difficulty of the factoring problem, quantum computing could allow attackers to obtain private key.

  Cryptography needs to develop new techniques that are believed to remain secure even against quantum computers. This leads us to the field of **post-quantum cryptography**.

---

## Last words

  This write-up is a small introduction to RSA — an asymmetric cryptography algorithm. I hope that anyone who reads this write-up also learns something about RSA!

## References

- [OverTheWire — Bandit](https://overthewire.org/wargames/bandit)
- [RSA — Wikipedia](https://vi.wikipedia.org/wiki/RSA_(m%C3%A3_h%C3%B3a))
- [Euler's theorem — Wikipedia](https://en.wikipedia.org/wiki/Euler%27s_theorem)
- [Euler's totient function — Wikipedia](https://en.wikipedia.org/wiki/Euler%27s_totient_function)
- [Shor's algorithm — Wikipedia](https://en.wikipedia.org/wiki/Shor%27s_algorithm)
- [The RSA Encryption Algorithm - How Does It Actually Work + Step-by-Step Example. - BrainWave on Youtube](https://www.youtube.com/watch?v=hm8s6FAc4pg)
