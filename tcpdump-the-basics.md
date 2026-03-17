### Tcpdump: The Basics

#### Overview

Tcpdump is a command-line tool for capturing, filtering, and displaying network packets. Built with C/C++ and based on the libpcap library, it's stable and optimal for performance.

#### Basic Command Structure

##### Specify Network Interface

- `-i INTERFACE` — listen to a specific interface
- `-i any` — listen to all available interfaces
- Use `ip address show` to list available interfaces

##### Save and Read Packets

- `-w FILE` — save captured packets to a file (typically `.pcap` extension)
- `-r FILE` — read packets from a file

##### Limit Packet Capture

- `-c COUNT` — capture a specific number of packets
- Without `-c`, capture continues until interrupted (CTRL-C)

##### DNS and Port Resolution

- `-n` — avoid DNS lookups for IP addresses
- `-nn` — avoid both DNS and port number resolution

##### Verbose Output

- `-v` — slightly more verbose output
- `-vv` — more verbose
- `-vvv` — even more verbose

#### Filtering Expressions

##### By Host

- `host IP` or `host HOSTNAME` — packets exchanged with a specific host
- `src host IP` — packets from a source
- `dst host IP` — packets to a destination

##### By Port

- `port PORT_NUMBER` — packets on a specific port
- `src port PORT_NUMBER` — from a source port
- `dst port PORT_NUMBER` — to a destination port

##### By Protocol

- Filter by: `ip`, `ip6`, `udp`, `tcp`, `icmp`

##### Logical Operators

- `&` (And), `|` (Or), `!` (Not)

#### Output Formatting Options

| Option | Purpose |
|--------|---------|
| `-q` | Quick/brief output |
| `-e` | Display MAC addresses (link-level header) |
| `-A` | Display packets as ASCII |
| `-xx` | Display packets in hexadecimal format |
| `-X` | Display packets in both hexadecimal and ASCII |

#### Advanced Filtering

- `proto[expr:size]` — filter based on header byte contents
- `tcp[tcpflags]` — filter TCP packets based on TCP flags
