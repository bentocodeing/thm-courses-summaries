# Hashing Basics - Revision Notes

## Fundamentals of Hash Functions

### What is a Hash Function?

A **hash function** takes input data of arbitrary size and produces a fixed-size output called a **hash value** or **digest**. Key characteristics:

* **One-way process** — impossible or computationally impractical to reverse (cannot recover input from output)
* **Deterministic** — same input always produces the same output
* **Avalanche effect** — even a single bit change in input causes significant changes in output
* **Fast to compute** — but prohibitively slow to reverse
* **Fixed output size** — regardless of input size
* **Output encoding** — typically in hexadecimal or base64 format

### Hash Functions vs. Encryption vs. Encoding

| Aspect | Hashing | Encryption | Encoding |
|--------|---------|-----------|----------|
| **Reversible** | No (one-way) | Yes (with key) | Yes (no key needed) |
| **Purpose** | Data integrity, authentication | Confidentiality | Data compatibility |
| **Key Required** | No | Yes | No |
| **Security** | Protects integrity | Protects confidentiality | No security |

---

## Hash Collisions

### What is a Hash Collision?

A **hash collision** occurs when two different inputs produce the same hash output.

**The Pigeonhole Principle:**

* Fixed number of possible outputs (pigeonholes)
* Unlimited possible inputs (pigeons)
* Collisions are mathematically inevitable
* Good hash functions minimize collision probability to negligible levels

### Compromised Algorithms

* **MD5** — cryptographically broken; collision attacks demonstrated
* **SHA-1** — deprecated; collision attacks published (Shattered attack)
* **Recommendation** — avoid both for password hashing or integrity verification; use SHA-256 or stronger alternatives

---

## Password Storage & Authentication

### Insecure Practices (Red Team Perspective)

#### 1. **Plaintext Storage**

* **Example:** RockYou breach — 14+ million plaintext passwords leaked
* **Impact:** Credentials reusable across multiple services
* **Wordlist:** `rockyou.txt` available in `/usr/share/wordlists`

#### 2. **Deprecated Encryption**

* **Example:** Adobe breach — used weak encryption instead of hashing
* **Risk:** Password hints stored in plaintext, sometimes containing the password itself

#### 3. **Insecure Hash Functions**

* **Example:** LinkedIn 2012 — used SHA-1 without salting
* **Vulnerability:** Enables rainbow table attacks

### Rainbow Tables

A **rainbow table** is a precomputed lookup table mapping hashes to plaintext passwords.

* **Trade-off:** Disk space for cracking speed
* **Vulnerability:** Only effective against unsalted hashes
* **Tools using them:** CrackStation, Hashes.com
* **Offensive use:** Fast password recovery without computational overhead

### Secure Password Storage

**Best practices:**

1. Use secure hashing algorithms: **Argon2, Scrypt, Bcrypt, or PBKDF2**
2. Generate unique **salt** per user (random value)
3. Concatenate password + salt before hashing
4. Store both hash and salt in database
5. Salts do NOT need to be kept secret

**Why not encrypt passwords?**

* Encryption key must be stored somewhere
* If key is compromised, all passwords are decrypted
* Hashing is one-way; even with database breach, passwords remain protected

---

## Hash Recognition & Identification (Red Team)

### Linux Password Hashes (`/etc/shadow`)

Format: `$prefix$options$salt$hash`

| Prefix | Algorithm | Strength | Notes |
|--------|-----------|----------|-------|
| `$y$` | yescrypt | ⭐⭐⭐⭐⭐ | Modern default, scalable |
| `$gy$` | gost-yescrypt | ⭐⭐⭐⭐⭐ | GOST R 34.11-2012 based |
| `$7$` | scrypt | ⭐⭐⭐⭐ | Key derivation function |
| `$2a$`, `$2b$`, `$2y$`, `$2x$` | bcrypt | ⭐⭐⭐⭐ | Blowfish-based |
| `$6$` | sha512crypt | ⭐⭐⭐ | SHA-512 based |
| `$md5` | SunMD5 | ⭐⭐ | Solaris systems |
| `$1$` | md5crypt | ⭐⭐ | Deprecated |

### Windows Password Hashes

* **Algorithm:** NTLM (variant of MD4)
* **Storage:** SAM (Security Accounts Manager)
* **Hash types:** NT hashes and LM hashes
* **Extraction tool:** mimikatz (bypasses Windows security)
* **Identification challenge:** Visually identical to MD5/MD4 hashes — use context

### Hash Identification Strategy

* **Automated tools:** hashID (unreliable for many formats)
* **Reliable indicators:** Prefixes ($2a$, $6$, etc.) provide clear identification
* **Context clues:** Web app database = likely MD5; Windows system = likely NTLM
* **Manual analysis:** Check hash length, encoding, and application context

---

## Password Cracking Techniques

### Methodology

1. Identify hash type (algorithm, salt presence)
2. Select appropriate tool (Hashcat, John the Ripper)
3. Choose wordlist (rockyou.txt covers common passwords)
4. If salt exists, include it in the cracking process
5. Compare generated hashes against target until match found

### Cracking Tools

#### **Hashcat**

* GPU-accelerated
* Syntax: `hashcat -m <hash_type> -a <attack_mode> hashfile wordlist`
* `-m`: Hash type code (e.g., 1000 = NTLM, 3200 = Bcrypt)
* `-a`: Attack mode (0 = dictionary/wordlist attack)
* **Best for:** GPU-capable systems, high-speed cracking

#### **John the Ripper**

* CPU-based by default
* Works in VMs without GPU passthrough
* Slower than Hashcat but more accessible
* Supports incremental mode and rule-based attacks

### GPU vs. CPU Cracking

**GPU Advantages:**

* Thousands of cores optimized for mathematical operations
* Dramatically faster hash computation
* Ideal for algorithms without GPU resistance

**GPU Limitations:**

* VMs typically cannot access host GPU
* Some algorithms (Bcrypt, Scrypt) designed to resist GPU acceleration
* Performance degradation in virtualized environments

**Recommendation:** Run Hashcat on host OS for maximum performance; use John the Ripper in VMs

---

## File Integrity Verification

### Use Cases

* **Download verification** — confirm file hasn't been corrupted or modified
* **Supply chain validation** — verify files from USB drives or untrusted sources
* **Duplicate detection** — identify identical files by hash comparison

### Implementation

* Compute SHA-256 hash of downloaded file
* Compare against official hash from trusted source
* Match = file integrity confirmed with high certainty
* Mismatch = file modified, corrupted, or compromised

**Example:** Fedora ISO verification using published SHA-256 checksums

---

## HMACs (Keyed-Hash Message Authentication Codes)

### Purpose

Combines cryptographic hashing with a secret key to verify both:

* **Authenticity** — proves sender identity
* **Integrity** — proves message hasn't been modified

### How HMACs Work

1. Pad secret key to hash function block size
2. XOR padded key with outer padding constant (opad)
3. Hash message using inner XORed key (ipad)
4. Hash the result again with outer XORed key
5. Final output = HMAC value

**Formula:** `HMAC(K,M) = H((K⊕opad)||H((K⊕ipad)||M))`

### Advantages Over Plain Hashing

* Requires secret key — attacker cannot forge valid HMACs
* Protects against tampering by authenticated parties
* Commonly used in APIs and message authentication

---

## Red Team / Penetration Testing Implications

### Password Cracking Workflow

1. **Dump hashes** from target system (`/etc/shadow`, SAM, database)
2. **Identify hash type** using prefixes, length, context
3. **Select wordlist** (rockyou.txt, custom lists, hybrid attacks)
4. **Crack offline** using Hashcat or John the Ripper
5. **Gain access** with recovered credentials

### Common Vulnerabilities to Exploit

* Unsalted hashes → rainbow table attacks
* Weak algorithms (MD5, SHA-1) → collision attacks
* Plaintext storage → direct credential theft
* Deprecated encryption → decryption possible
* Default/weak passwords → dictionary attacks succeed quickly

### Defense Evasion

* Bcrypt/Scrypt resist GPU acceleration (slower cracking)
* Unique salts per user prevent rainbow tables
* Strong password policies reduce wordlist effectiveness
* Rate limiting on authentication attempts slows brute force
