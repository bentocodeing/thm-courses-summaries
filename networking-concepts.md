## Networking Concepts — Revision Summary

### The OSI Model (7 Layers)

*Mnemonic (bottom to top): **"Please Do Not Throw Spinach Pizza Away"***

| Layer | Name | Key Function | Examples |
|-------|------|-------------|---------|
| 1 | Physical | Physical transmission of bits | Ethernet cable, WiFi radio bands |
| 2 | Data Link | Node-to-node transfer on same network segment | Ethernet (802.3), WiFi (802.11), MAC addresses |
| 3 | Network | Routing between different networks | IP, ICMP, IPSec VPN |
| 4 | Transport | End-to-end communication between applications | TCP, UDP |
| 5 | Session | Establishing & maintaining sessions | NFS, RPC |
| 6 | Presentation | Encoding, compression, encryption | ASCII, Unicode, MIME, JPEG |
| 7 | Application | Network services to end-user apps | HTTP, FTP, DNS, SMTP, IMAP |

---

### TCP/IP Model (4 Layers)

| TCP/IP Layer | Maps to OSI |
|--------------|-------------|
| Application | Layers 5, 6, 7 |
| Transport | Layer 4 |
| Internet | Layer 3 |
| Link | Layers 1 & 2 |

---

### IP Addressing (IPv4)

- **32-bit address** (4 octets, each 0–255), ~4 billion unique addresses
- **Private ranges** (memorise these!):
  - `10.0.0.0 – 10.255.255.255`
  - `172.16.0.0 – 172.31.255.255`
  - `192.168.0.0 – 192.168.255.255`
- Routers operate at **Layer 3** and forward packets between networks

---

### TCP vs UDP

| Feature | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented | Connectionless |
| Reliability | Guaranteed delivery | No guarantee |
| Handshake | Three-way (SYN → SYN-ACK → ACK) | None |
| Speed | Slower | Faster |

- Port numbers range from **1 to 65,535** (~65k ports)

---

### Encapsulation

Data gets wrapped with headers at each layer as it travels **down** the stack:

1. **Application data**
2. - TCP/UDP header → **Segment / Datagram**
3. - IP header → **IP Packet**
4. - Link header/trailer → **Ethernet/WiFi Frame**

The process is **reversed** on the receiving end.
