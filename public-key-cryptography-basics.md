# Public Key Cryptography Basics — Complete Revision Notes

## Foundational Security Principles

In secure communication, four key properties must be established:

- **Authentication:** Confirming the identity of who you're communicating with
- **Authenticity:** Verifying that a message genuinely comes from a specific sender
- **Integrity:** Ensuring data has not been altered or tampered with
- **Confidentiality:** Preventing unauthorized parties from accessing data

**Symmetric encryption** (private key cryptography) primarily protects confidentiality, while **asymmetric encryption** (public key cryptography) plays a significant role in authentication, authenticity, and integrity.

---

## Core Cryptographic Concepts

### Cryptography vs. Cryptanalysis vs. Attacks

- **Cryptography:** Science of securing communication and data using codes and ciphers
- **Cryptanalysis:** Study of methods to break or bypass cryptographic security systems without knowing the key
- **Brute-Force Attack:** Trying every possible key or password to decrypt a message
- **Dictionary Attack:** Trying dictionary words or combinations instead of all possibilities

---

## Asymmetric Encryption: Key Exchange

### The Problem

How do two parties agree on a symmetric encryption key over an insecure channel without transmitting it directly?

### The Analogy

- **Secret Code** = Symmetric encryption cipher and key
- **Lock** = Public key
- **Lock's Key** = Private key

**Process:** You send instructions in a locked box (encrypted with the server's public key). Only the server (with the private key) can unlock it. After key exchange, both parties communicate using symmetric encryption for speed.

---

## RSA (Rivest-Shamir-Adleman)

### Security Foundation

RSA is based on the **difficulty of factoring large numbers**:

- Multiplying two large primes is easy
- Factoring their product is computationally hard (especially with 600+ digit numbers)

### Key Generation

1. Choose two large prime numbers: **p** and **q**
2. Calculate **n = p × q**
3. Calculate **ϕ(n) = (p - 1) × (q - 1)** (Euler's totient function)
   - Alternative form: ϕ(n) = n − p − q + 1
4. Choose **e** (public exponent) such that e is coprime to ϕ(n)
5. Calculate **d** (private exponent) where **e × d ≡ 1 (mod ϕ(n))**

### RSA Variables in CTFs

| Variable | Meaning |
|----------|---------|
| **p, q** | Large prime numbers |
| **n** | Product of p and q (modulus) |
| **e** | Public exponent (part of public key) |
| **d** | Private exponent (part of private key) |
| **m** | Original message (plaintext) |
| **c** | Encrypted message (ciphertext) |

### Encryption & Decryption

- **Encryption:** `c = m^e mod n` (Alice encrypts with Bob's public key)
- **Decryption:** `m = c^d mod n` (Bob decrypts with his private key)

### RSA in CTF Challenges

- Tools: **RsaCtfTool**, **rsatool**
- Challenges often provide some RSA variables; you must calculate missing ones or decrypt messages

---

## Diffie-Hellman Key Exchange

### Purpose

Establish a **shared secret** between two parties over an insecure channel without pre-existing shared secrets, preventing eavesdroppers from obtaining the key.

### Process

1. **Public Agreement:** Alice and Bob agree on:
   - Large prime number **p**
   - Generator **g** (where 0 < g < p)
   - These values are disclosed publicly

2. **Private Selection:** Each party independently chooses a private integer:
   - Alice chooses **a** (private key)
   - Bob chooses **b** (private key)

3. **Public Key Calculation:**
   - Alice calculates: **A = g^a mod p** (public key)
   - Bob calculates: **B = g^b mod p** (public key)

4. **Key Exchange:** Alice and Bob send their public keys to each other

5. **Shared Secret Calculation:**
   - Alice calculates: **B^a mod p = g^(ab) mod p**
   - Bob calculates: **A^b mod p = g^(ab) mod p**
   - Both arrive at the same shared secret: **g^(ab) mod p**

### Integration with RSA

- **Diffie-Hellman:** Used for key agreement
- **RSA:** Used for digital signatures, key transport, and authentication
- Together they prevent man-in-the-middle attacks by proving identity

---

## SSH (Secure Shell)

### Server Authentication

**The Fingerprint Check:**
When connecting to an SSH server for the first time, the client displays the server's **public key fingerprint** (hash of the public key).

```
The authenticity of host 'example.com' can't be established.
ED25519 key fingerprint is SHA256:xxxxx...
Are you sure you want to continue connecting (yes/no)?
```

**Security Verification:**

- Obtain the server's official fingerprint through a **trusted channel** (phone, in-person, official website)
- **Compare** the displayed fingerprint with the official one
- If they match → type `yes` | If they don't match → **do not connect**
- Protects against **man-in-the-middle (MITM) attacks**

**Storage:**

- Once verified, the fingerprint is stored in `~/.ssh/known_hosts`
- Future connections proceed silently unless the fingerprint changes
- A changed fingerprint indicates either server reinstallation (usually harmless) or a potential attack (very bad)

### Client Authentication

SSH supports multiple key algorithms for client authentication:

| Algorithm | Type | Characteristics |
|-----------|------|-----------------|
| **RSA** | Traditional | Default; longer keys |
| **DSA** | Digital Signature Algorithm | Legacy; not recommended |
| **ECDSA** | Elliptic Curve DSA | Smaller keys, equivalent security |
| **ECDSA-SK** | ECDSA with Security Key | Hardware-based private key protection |
| **Ed25519** | EdDSA (Edwards-curve) | Modern, recommended; compact keys |
| **Ed25519-SK** | Ed25519 with Security Key | Hardware-based protection |

### SSH Key Generation

```bash
ssh-keygen -t ed25519
```

**Output:**

- **Public key:** `id_ed25519.pub` (shared with servers)
- **Private key:** `id_ed25519` (kept secret)
- **Optional passphrase:** Encrypts the private key file (never transmitted)

**Key Fingerprint:**

```bash
ssh-keygen -lf ~/.ssh/id_ed25519.pub
```

### SSH Private Keys — Critical Security

- **Never share** your private key — treat it like a password
- Passphrase protects the key file only, not authentication to the server
- Passphrase is never transmitted and never leaves your system
- Tools like **John the Ripper** can crack weak passphrases
- Generate keys on your machine, copy public key to server via `ssh-copy-id`
- Private key permissions must be `600` (owner read/write only)

**Usage:**

```bash
ssh -i privateKeyFileName user@host
```

### authorized_keys File

- Located in `~/.ssh/authorized_keys` on the remote host
- Contains public keys allowed to authenticate
- By default, key authentication is enabled on most Linux distributions
- For root SSH access, **only key authentication should be accepted**

### SSH Keys in Penetration Testing

During CTFs, penetration testing, and red teaming:

- SSH keys provide a **stable shell** (no Control-C issues, tab completion works)
- Adding your public key to `authorized_keys` creates a persistent backdoor
- Preferable to unstable reverse shells
- Note: `www-data` usually doesn't allow SSH login; regular users and root work

---

## Digital Signatures

### Purpose

Verify **authenticity** (who created/modified a document) and **integrity** (document hasn't been tampered with).

### How It Works

1. **Signing:** Creator encrypts document hash with their **private key**
2. **Verification:** Recipient decrypts with creator's **public key**
3. **Comparison:** Recipient compares decrypted hash with hash of received document
4. **Proof:** Only the creator has the private key, proving they signed it

**Important:** Pasting an image of a signature does NOT prove integrity — it can be copied by anyone.

### Legal Status

In many modern countries, digital signatures have the same legal value as physical signatures.

---

## Certificates & Chain of Trust

### Purpose

Prove the identity of a web server (e.g., HTTPS connections).

### How It Works

1. Web server presents a **TLS certificate** (digitally signed by a Certificate Authority)
2. Browser checks if the **Certificate Authority (CA)** is trusted
3. **Chain of Trust:** Certificate → Signed by CA → CA trusted by browser → Browser trusts certificate
4. Protects against **man-in-the-middle attacks** by verifying server identity

### Root Certificate Authorities

- Pre-installed on devices, operating systems, and browsers
- Browsers trust certificates signed by these CAs
- Examples: Mozilla Firefox, Google Chrome (each maintains their own list)

### Obtaining TLS Certificates

| Method | Cost | Use Case |
|--------|------|----------|
| **Certificate Authority** | Annual fee | Commercial websites |
| **Let's Encrypt** | Free | Any domain you own |

**Recommendation:** All modern websites should use HTTPS with valid certificates.

---

## PGP/GPG (Pretty Good Privacy / GNU Privacy Guard)

### Overview

- **PGP:** Software implementing encryption, digital signing, and secure communication
- **GPG:** Open-source implementation of the OpenPGP standard

### Common Uses

- Secure email communication
- Digital signing of messages
- File encryption

### Key Generation

```bash
gpg --full-gen-key
```

**Configuration Options:**

- Key type (RSA, DSA, ECC, etc.)
- Elliptic curve (if applicable)
- Expiry date
- User identity (name, email, comment)

### Key Protection

- Private keys can be passphrase-protected (similar to SSH)
- Passphrase protects the key file; never transmitted
- Crack attempts use `gpg2john` + **John the Ripper**

### Common Operations

```bash
# Import key from backup
gpg --import backup.key

# Decrypt message
gpg --decrypt confidential_message.gpg

# List keys
gpg --list-keys
```

### Best Practices

- Keep backup copies of GPG keys in secure locations
- Use strong passphrases
- Treat private keys with extreme care
- Share only public keys

---

## Penetration Testing & Attack Vectors

### Man-in-the-Middle (MITM) Prevention

- **SSH fingerprint verification** before first connection
- **Certificate chain of trust** for HTTPS
- **Digital signatures** to prove authenticity

### Cracking Encrypted Keys

- **SSH keys:** Use `ssh2john` + John the Ripper
- **GPG keys:** Use `gpg2john` + John the Ripper
- Importance of **strong passphrases**

### CTF Challenges

- RSA factorization and key recovery
- Diffie-Hellman parameter extraction
- SSH key analysis
- Certificate inspection
- GPG decryption

### Tools for Attackers

- **RsaCtfTool:** Breaking RSA challenges
- **rsatool:** RSA calculations
- **John the Ripper:** Passphrase cracking
- **ssh-keyscan:** Gathering SSH public keys

---

## Security Recommendations Summary

### ✅ Best Practices

- Verify SSH/server fingerprints before first connection
- Use strong, complex passphrases for private keys
- Store private keys securely (never share)
- Use modern algorithms (Ed25519, ECC)
- Keep backups of critical keys in secure locations
- Enable key authentication over passwords for SSH
- Use HTTPS with valid certificates
- Implement proper file permissions (600 for private keys)

### ❌ Critical Mistakes

- Sharing private keys
- Using weak or no passphrases
- Skipping fingerprint verification
- Ignoring certificate warnings
- Storing private keys on untrusted systems
- Allowing password-only SSH authentication for critical accounts
- Using outdated algorithms (DSA, weak RSA)
