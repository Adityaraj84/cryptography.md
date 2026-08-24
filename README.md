# cryptography
## 1. The Three Core Building Blocks
## Symmetric key cryptography
# One single key is used to both lock (encrypt) and unlock (decrypt) data. Fast, efficient, great for bulk data. Problem: both sides need the same key, so how do you get that key to the other person safely? That's the "key distribution problem."

Examples: AES, DES (old, broken), ChaCha20
## Asymmetric key cryptography (public key crypto)
Two mathematically linked keys — a public key (share with everyone) and a private key (keep secret, never share). Whatever one key encrypts, only the other can decrypt.

If you encrypt with someone's public key, only their private key can open it → used for confidentiality.
If you encrypt (sign) with your private key, anyone with your public key can verify it came from you → used for authentication/digital signatures.
Slower than symmetric, so usually used just to exchange a symmetric key, then symmetric crypto takes over for the actual bulk data. This hybrid approach is literally what TLS/HTTPS does.

Examples: RSA, ECC (Elliptic Curve Cryptography), Diffie-Hellman
