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

## QKD (Quantum Key Distribution)
- Not really an "algorithm" — it's a physical method of exchanging a symmetric key using quantum properties of photons (e.g., BB84 protocol).
- The trick: quantum mechanics guarantees that if someone eavesdrops on the photon stream, the act of measuring disturbs the state, and both parties can detect the intrusion.
- Doesn't rely on math being "hard" (like factoring) — relies on physics itself. This is why it's considered "quantum-safe" even against future quantum computers.
- Limitation: needs specialized hardware (fiber optic/photon detectors), short range currently, expensive, still maturing.
## 5. Key Exchange / Key Sharing
This solves: "how do two people agree on a shared secret key over an insecure channel, without anyone else figuring it out?"
- Diffie-Hellman (DH): both sides pick a private random number, do some public math using a shared base/prime, exchange results publicly, then each independently computes the same shared secret — even though an eavesdropper sees the exchange, they can't compute the secret without solving the discrete log problem.
- ECDH: same idea but using elliptic curves — faster, smaller.
- RSA key exchange: sender encrypts a randomly generated symmetric key using the receiver's RSA public key.
- Hybrid systems (like TLS): use asymmetric crypto only to establish the session key, then switch to symmetric (AES) for actual data transfer, because symmetric is much faster for bulk traffic.
## 6. How Are Keys Actually Made? (Seeds & Randomness)
A cryptographic key is fundamentally just a very large random number. The quality of that randomness is everything — if an attacker can guess or predict your "random" number, the whole system collapses no matter how strong the algorithm is.

Keys are generated starting from a seed — an initial value fed into a random number generator, which then expands it into the actual key material.

There are three main types of random number generators:
### 1. TRNG (True Random Number Generator)
- Uses real, physical unpredictable phenomena: thermal noise, radioactive decay timing, atmospheric noise, mouse movement jitter, even camera sensor noise
- Genuinely non-deterministic — same conditions won't reproduce the same number.
- Slower and needs special hardware, but this is the gold standard for generating seeds.
### 2. PRNG (Pseudo-Random Number Generator)
- A deterministic algorithm that takes a seed and expands it into a long stream of numbers that look random statistically, but are 100% reproducible if you know the seed.
- Fast, software-based — used everywhere in day-to-day computing.
- Cryptographically secure versions are called CSPRNGs (e.g., used in OpenSSL) — designed so that even if you see part of the output, you can't predict the rest or work backward to the seed.
- Weakness: if the seed is weak/predictable, everything downstream is broken (this has caused real-world crypto failures).
### 3. QRNG (Quantum Random Number Generator)
- Uses quantum phenomena (like photon behavior at a beam splitter, or vacuum fluctuations) as the entropy source.
- Considered the most theoretically "true" form of randomness because quantum outcomes are fundamentally probabilistic, not just practically unpredictable.
- Increasingly used in high-security key generation, often combined with classical TRNG/PRNG systems.

Typical real-world flow: TRNG or QRNG generates high-quality entropy → this becomes (or seeds) a CSPRNG → the CSPRNG expands it efficiently into the actual key material your system uses.
## 7. PKI, CA, and Trust Models
### PKI (Public Key Infrastructure)
The whole ecosystem of hardware, software, policies, and procedures needed to create, manage, distribute, store, and revoke public key certificates. It's what makes "trusting a stranger's public key" possible at internet scale.
### CA (Certificate Authority)
A trusted third party that verifies someone's identity and then issues them a digital certificate binding their identity to their public key, signed with the CA's own private key. Your browser trusts a pre-installed list of "root CAs" — everything else builds trust from there in a chain.
### Let's Encrypt
A free, automated CA. It verifies domain ownership (not deep identity, just "you control this domain") and issues short-lived (90-day) TLS certificates automatically via the ACME protocol. This is why the whole web moved to HTTPS by default — cert issuance became free and scriptable instead of a costly manual process.

