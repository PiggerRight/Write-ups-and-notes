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

  In common, the pairs of keys are generated based on several mathematical formulars. These formulars are easy to be computed in the intended direction but it seems impossible in the inverse direction.

  **RSA**, stands for "Rivest-Shamir-Adleman" — the inventors of **RSA**, is also a mathematical formular for generating keys.

### General idea

  At first we have some values
  
  - Choose two prime numbers: `p` and `q`
  - `n = p * q`
  - Choose `φ` that coprime with `n`: `φ(n) = (p-1)*(q-1)`
  - Choose `e` and `d` that `ed ≡ 1 (mod φ)` → so `e` is coprime with `φ`

  **Prove**:

  - We have: `ed ≡ 1 (mod φ)` ⇔ `ed = 1 + φk` with `k` is an integer
  - If `e` isn't prime to `φ` ⇔ `e = nx` and `φ = n.y` with `n`,`x`,`y` are integers
  - So now: `ed = 1 + φk` ⇔ `nxd = 1 + nyk`
  - Because `nxd ≡ 0 (mod n)` and `1 + nyk ≡ 1 (mod n)` → `nxd = 1 + nyk` false so `ed ≡ 1 (mod φ)` will not happen
  - So `e` must be prime to `φ` (Proved)


  Then, we can define public key and private key:
  
  - Public key: `(n,e)`
  - Private key: `(n,d)`

  This is where *encryption and decryption* come from:

  - We have a number as a message: 
