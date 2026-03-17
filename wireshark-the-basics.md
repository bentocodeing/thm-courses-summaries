### Wireshark: The Basics

#### Overview

- Open-source packet analyzer for inspecting live traffic or PCAP files
- Reads packets without modifying them; analysis depends on user skill
- Not an IDS — discovers anomalies but doesn't detect automatically

#### Interface

- **5 main sections**: packet list, details pane, bytes pane, status bar, filter bar
- Click packet to view details; double-click opens in new window
- Packet numbers enable quick navigation through large captures

#### File Operations

- Load files via drag/drop, double-click, or File > Open
- Merge files: File > Merge (save merged file manually)
- View details: Statistics > Capture File Properties

#### Packet Dissection (OSI Layers)

1. **Frame** — Physical layer details
2. **Source [MAC]** — Data Link layer (MAC addresses)
3. **Source [IP]** — Network layer (IPv4 addresses)
4. **Protocol** — Transport layer (TCP/UDP, ports)
5. **Protocol Errors** — TCP reassembly segments
6. **Application Protocol** — Application layer (HTTP, DNS, etc.)
7. **Application Data** — Application-specific payload

#### Coloring & Marking

- Packets color-coded by protocol or custom rules (View > Coloring Rules)
- **Temporary rules** — session only; **Permanent rules** — saved in preferences
- Mark packets (right-click or Edit) for reference — lost on file close
- Add persistent comments for team notes

#### Searching & Filtering

- Find packets: Edit > Find Packet (Display Filter, Hex, String, Regex)
- **Apply as Filter** — filter single value (right-click or Analyze > Apply as Filter)
- **Conversation Filter** — view related packets by IP/port
- **Colourise Conversation** — highlight linked packets without filtering

#### Display Filter Queries

- By protocol: `http`, `arp`, `dhcp`, `ftp`, `smtp`
- By port: `tcp.port == 80`
- By IP: `ip.addr == 192.168.1.2`

#### Advanced Features

- **Follow Stream** — reconstruct application-level data (HTTP, TCP, UDP)
- **Export Objects** — extract transferred files (DICOM, HTTP, IMF, TFTP)
- **Expert Info** — flags protocol anomalies (Analyze > Expert Information)
- **Time Display Format** — change from "Seconds Since Beginning" to readable format
- **Apply as Column** — add specific fields to packet list pane
