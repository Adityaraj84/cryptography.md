# cryptography
## 1. The Three Core Building Blocks
## Symmetric key cryptography
One single key is used to both lock (encrypt) and unlock (decrypt) data. Fast, efficient, great for bulk data. Problem: both sides need the same key, so how do you get that key to the other person safely? That's the "key distribution problem."

Examples: AES, DES (old, broken), ChaCha20
## Asymmetric key cryptography (public key crypto)
Two mathematically linked keys — a public key (share with everyone) and a private key (keep secret, never share). Whatever one key encrypts, only the other can decrypt.

If you encrypt with someone's public key, only their private key can open it → used for confidentiality.
If you encrypt (sign) with your private key, anyone with your public key can verify it came from you → used for authentication/digital signatures.
Slower than symmetric, so usually used just to exchange a symmetric key, then symmetric crypto takes over for the actual bulk data. This hybrid approach is literally what TLS/HTTPS does.

Examples: RSA, ECC (Elliptic Curve Cryptography), Diffie-Hellman
## Hashing
Not encryption at all — it's a one-way fingerprint function. You put data in, you get a fixed-length string out, and you can never reverse it back to the original. Same input always gives same output; even a 1-bit change gives a totally different hash (avalanche effect).

Used for: integrity checking, password storage, digital signatures (you hash the message, then sign the hash — signing the whole message directly would be slow).
Examples: SHA-256, SHA-3, (MD5/SHA-1 are broken, avoid them)
## 2. Digital Signatures — How Public/Private Keys Actually Work Together
Think of it like a wax seal that only you can produce, but anyone can check.

1. Sender takes the message, runs it through a hash function → gets a hash.
2. Sender encrypts that hash with their private key → this encrypted hash is the "digital signature."
3. Sender sends: message + signature.
4. Receiver hashes the message themselves, then decrypts the signature using the sender's public key, and compares the two hashes.
5. If they match: message wasn't tampered with, AND it really came from the person who owns that private key (because only their private key could have produced a signature that opens correctly with their public key).

This gives you two things at once: integrity (hash) + authentication/non-repudiation (private key signing).

Note the reversal compared to confidentiality:

**Confidentiality**: encrypt with receiver's public key, decrypt with receiver's private key.
**Authentication/signing**: "encrypt" (sign) with sender's private key, verify with sender's public key.
## 3. Where Cryptography in the OSI Model
<img width="745" height="433" alt="Screenshot 2026-08-24 151512" src="https://github.com/user-attachments/assets/cb759591-9ec5-4770-9237-7b92b912f4bd" />

## 4. Algorithms
*RSA (Rivest–Shamir–Adleman)*

Asymmetric algorithm based on the difficulty of factoring the product of two very large prime numbers.

Key sizes: 2048-bit or 4096-bit today.<br>
Used for: key exchange, digital signatures, certificates.<br>
Downside: slow, large keys, and vulnerable to quantum computers (Shor's algorithm can break it).
## EC / ECC (Elliptic Curve Cryptography)
Asymmetric algorithm based on the difficulty of the "elliptic curve discrete logarithm problem."<br>
Gives the same security as RSA but with much smaller keys (a 256-bit EC key ≈ security of a 3072-bit RSA key). Faster, less power/bandwidth — great for mobile and IoT.<br>
Used in: ECDSA (signatures), ECDH (key exchange). Bitcoin, TLS 1.3, WhatsApp all use it.<br>
Still vulnerable to quantum attacks, just like RSA.<br>
