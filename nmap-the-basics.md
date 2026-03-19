# Nmap: The Basics — Revision Notes

## What is Nmap?

An open-source network scanner (published 1997) used to **discover live hosts**, **find open ports**, and **detect services/versions** on a network. Always run with `sudo` for full functionality.

---

## Host Discovery

| Scenario | Method Used by Nmap |
|---|---|
| Local network (same subnet) | ARP requests |
| Remote network (through routers) | ICMP, TCP SYN/ACK, UDP |

### Target Specification Options

- Single IP: `192.168.1.1`
- Range: `192.168.1.1-254`
- Subnet: `192.168.1.0/24`
- Hostname: `example.com`

---

## Key Commands

### Host Discovery

| Command | Description |
|---|---|
| `nmap -sn <target>` | Ping scan — discover live hosts only, no port scan |
| `nmap -sL <target>` | List scan — lists targets without scanning them |
| `nmap -Pn <target>` | Treat all hosts as online (skip host discovery) |

### Port Scanning

| Command | Description |
|---|---|
| `nmap -sT <target>` | **Connect scan** — full TCP three-way handshake (default without sudo) |
| `nmap -sS <target>` | **SYN scan** (stealth) — sends SYN only, never completes handshake (default with sudo) |
| `nmap -sU <target>` | **UDP scan** |

### Port Selection

| Command | Description |
|---|---|
| `nmap -p 80,443 <target>` | Scan specific ports |
| `nmap -p 1-1000 <target>` | Scan a port range |
| `nmap -p- <target>` | Scan all 65,535 ports |
| `nmap -F <target>` | Fast scan — top 100 most common ports |

### Service & OS Detection

| Command | Description |
|---|---|
| `nmap -sV <target>` | Service/version detection |
| `nmap -O <target>` | OS detection |
| `nmap -A <target>` | **Aggressive** — enables OS detection, version scanning, traceroute, and more |

---

## Timing & Performance

Six timing templates (slowest → fastest):

| Flag | Name |
|---|---|
| `-T0` | Paranoid |
| `-T1` | Sneaky |
| `-T2` | Polite |
| `-T3` | Normal (default) |
| `-T4` | **Aggressive** |
| `-T5` | Insane |

### Additional Performance Options

| Option | Description |
|---|---|
| `--min-parallelism <n>` | Minimum parallel probes |
| `--max-parallelism <n>` | Maximum parallel probes |
| `--min-rate <n>` | Minimum packets per second |
| `--max-rate <n>` | Maximum packets per second |
| `--host-timeout <time>` | Max wait time per host |

---

## Output & Verbosity

### Verbosity / Debugging

| Flag | Description |
|---|---|
| `-v` | Verbose output |
| `-vv` / `-v4` | Increased verbosity |
| `-d` | Debugging output |
| `-d9` | Maximum debugging level |

### Saving Results

| Flag | Description |
|---|---|
| `-oN <file>` | Normal (human-readable) output |
| `-oX <file>` | XML output |
| `-oG <file>` | Grepable output |
| `-oA <file>` | **All three formats** at once |

---

## Important Notes

- **With `sudo`** → defaults to SYN scan (`-sS`)
- **Without `sudo`** → defaults to Connect scan (`-sT`), many features unavailable
- TCP and UDP each have **65,535 ports**
- Nmap scans the **top 1,000 most common ports** by default
