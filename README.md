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
