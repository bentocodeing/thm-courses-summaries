### Cryptography Basics - Revision Notes

#### **What is Cryptography?**

Cryptography is the practice of secure communication in the presence of adversaries. Its main goals are to protect:

- **Confidentiality** — keeping data secret
- **Integrity** — ensuring data hasn't been altered
- **Authenticity** — verifying the source of data

You use cryptography every day without realizing it — encrypted websites, messaging apps, banking, etc.

---

#### **Real-World Applications**

Cryptography is essential for:

- **Payment Card Industry (PCI)** — credit card data must be encrypted at rest (stored) and in motion (transmitted)
- **Healthcare** — medical records must comply with regulations like HIPAA (USA), GDPR (EU), and DPA (UK)
- **General data protection** — any sensitive information needs encryption

---

#### **Key Cryptography Terms**

| Term | Definition |
|------|-----------|
| **Plaintext** | The original, readable message (before encryption) |
| **Ciphertext** | The encrypted, unreadable message |
| **Cipher** | An algorithm that converts plaintext to ciphertext and vice versa |
| **Encryption** | The process of converting plaintext to ciphertext |
| **Decryption** | The process of converting ciphertext back to plaintext |
| **Key** | A secret value used in encryption/decryption |

---

#### **Historical Example: Caesar Cipher**

The Caesar Cipher is one of the oldest and simplest ciphers (1st century BCE).

**How it works:**

- Shift each letter by a fixed number (e.g., shift by 3)
- Example: `TREASON` → `WUBKDFNPH` (shift right by 3)
- To decrypt: shift left by 3

**Why it's insecure:**

- Only 25 possible keys (26 letters in alphabet)
- Can be broken by trying all possible shifts
- By modern standards, completely unsafe

---

#### **Two Main Types of Encryption**

##### **1. Symmetric Encryption**

Uses the **same key** to encrypt and decrypt.

**Characteristics:**

- Also called "private key cryptography"
- Key must be kept secret
- Sharing the key securely is challenging
- Faster than asymmetric encryption

**Examples:**

- DES (Data Encryption Standard)
- 3DES (Triple DES)
- AES (Advanced Encryption Standard)

**Challenge:** How do you safely share the key with the other person?

---

##### **2. Asymmetric Encryption**

Uses a **pair of keys** — one public, one private.

**Characteristics:**

- Also called "public key cryptography"
- **Public key** — shared with everyone (used to encrypt)
- **Private key** — kept secret (used to decrypt)
- Data encrypted with public key can only be decrypted with private key
- Slower than symmetric encryption
- Uses larger key sizes

**Examples:**

- RSA (2048-bit, 3072-bit, or 4096-bit keys recommended)
- Diffie-Hellman (2048-bit minimum)
- Elliptic Curve Cryptography (256-bit key ≈ 3072-bit RSA security)

**Advantage:** No need to secretly share keys — public key is shared openly.

---

#### **Mathematical Operations in Cryptography**

##### **XOR (Exclusive OR) ⊕**

A binary operation that compares two bits:

- Returns **1** if bits are different
- Returns **0** if bits are the same

**Truth Table:**

| A | B | A ⊕ B |
|---|---|-------|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

**Key Properties:**

- `A ⊕ A = 0` (XOR with itself = 0)
- `A ⊕ 0 = A` (XOR with 0 = unchanged)
- `A ⊕ B = B ⊕ A` (commutative)
- `(A ⊕ B) ⊕ C = A ⊕ (B ⊕ C)` (associative)

**Simple XOR Encryption:**

- Encryption: `C = P ⊕ K` (plaintext XOR key = ciphertext)
- Decryption: `C ⊕ K = P` (ciphertext XOR same key = plaintext)
- **Limitation:** Key must be as long as the plaintext

---

##### **Modulo Operation %**

The **remainder** when one number is divided by another.

**Example:**

- `118613842 % 9091 = ?` (find the remainder)
- `60 % 12 = 0` (60 ÷ 12 = 5 with remainder 0)

**Important Properties:**

- **Not reversible** — if `x % 5 = 4`, infinite values of x satisfy this
- Result is always in range **0 to (divisor - 1)**
- Used heavily in cryptographic algorithms

---

#### **Key Takeaways**

✅ Cryptography protects confidentiality, integrity, and authenticity  
✅ **Symmetric encryption** uses one key (fast, but key sharing is hard)  
✅ **Asymmetric encryption** uses two keys (slower, but no key sharing problem)  
✅ **XOR** is a simple but weak encryption method (requires key as long as message)  
✅ **Modulo** is a mathematical operation crucial to modern cryptography  
✅ Caesar Cipher is historically interesting but completely insecure today  
