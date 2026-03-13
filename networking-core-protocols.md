# Networking Core Protocols — Revision Notes

## DNS (Domain Name System)

- Maps domain names to IP addresses — operates at **Layer 7 (Application)**
- Default: **UDP port 53** (fallback: TCP port 53)
- Key record types:
  - **A** → IPv4 address
  - **AAAA** → IPv6 address
  - **MX** → Mail server
  - **CNAME** → Canonical name / alias
- Tools: `nslookup`, `whois`

---

## HTTP / HTTPS

- Defines browser ↔ web server communication, relies on **TCP**
- Key methods: **GET**, **POST**, **PUT**, **DELETE**
- Useful for troubleshooting: connect via `telnet <ip> 80` and send raw HTTP commands

---

## FTP (File Transfer Protocol)

- Designed for **file transfer** (more efficient than HTTP for this purpose)
- Control channel: **TCP port 21** | Data transfer uses a **separate connection**
- Key commands: `USER`, `PASS`, `LIST`, `RETR`, `QUIT`

---

## SMTP (Simple Mail Transfer Protocol)

- Handles **sending** email (client → server, server → server)
- Key commands: `HELO/EHLO`, `MAIL FROM`, `RCPT TO`, `DATA`, `.` *(signals end of message)*

---

## POP3 (Post Office Protocol v3)

- **Downloads** email to a single device, typically **deletes from server** after retrieval
- Key commands: `USER`, `PASS`, `STAT`, `LIST`, `RETR`, `DELE`, `QUIT`
- ⚠️ Credentials and content sent in **plaintext** — visible to packet capture

---

## IMAP (Internet Message Access Protocol)

- **Synchronizes** email across multiple devices — messages **stay on server**
- More complex command set than POP3
- Better suited for multi-device environments

---

## Default Port Summary

| Protocol | Default Port | Transport |
|----------|-------------|-----------|
| DNS | 53 | UDP (TCP fallback) |
| HTTP | 80 | TCP |
| HTTPS | 443 | TCP |
| FTP | 21 | TCP |
| SMTP | 25 | TCP |
| POP3 | 110 | TCP |
| IMAP | 143 | TCP |

---

> **SOC Tip:** All these protocols transmit data in **plaintext** by default — a key reason to know their secure equivalents (FTPS, SMTPS, IMAPS, etc.), which will be covered in *Networking Secure Protocols*.
