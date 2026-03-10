# Module 2 - Network Fundamentals

# What is Networking?

## What is networking?

- Networking = connecting computers so they can share data and resources (files, printers, services).​
- Networks allow devices to communicate using agreed rules called protocols.​
- Pen testers abuse misconfigurations in networks to gain unauthorized access.​


## What is the Internet?

- The Internet is a global network of smaller interconnected networks.​
- Data moves across the Internet using standard protocols (like IP) so different systems can talk.​
- As a pen tester, you often pivot between networks via Internet‑facing services (web apps, VPNs, SSH).​


## Identifying devices on a network

- Each device on a network is called a host.​
- Hosts have IP addresses so they can be uniquely identified and reached.​
- There are private IP ranges used inside local networks and public IPs used on the Internet.​
- Pen testers first discover live hosts, then map which services are running on them.​


## IP addresses (IPv4 and IPv6)

- An IP address is a number used to identify a device (host) on a network so other devices can send data to it.​
- IPv4 uses four sections (octets) of numbers, for example 192.168.1.77192.168.1.77, and supports about 4.29 billion unique addresses.​
- IPv4 addresses can be private (used only inside local networks) or public (used on the Internet and given by your ISP).​
- Two devices on the same local network talk to each other using private IPs but share one public IP when talking to the Internet.​
- IPv6 is a newer version of IP designed to fix the IPv4 address shortage.​
- IPv6 uses a much larger address space (up to 21282128 addresses), which is enough for huge numbers of devices.​
- IPv6 addresses look longer and use hexadecimal, for example 2001:0db8:85a3:0000:0000:8a2e:0370:73342001:0*db*8:85*a*3:0000:0000:8*a*2*e*:0370:7334.​


## MAC addresses

- Every network interface (NIC) on a device has a built‑in hardware identifier called a MAC address.​
- A MAC address is assigned in the factory and is meant to be globally unique for that network interface.​
- A MAC address is a 12‑character hexadecimal number split into pairs and separated by colons, for example **a4:c3:f0:85:ac:2d**.​
- The first six hexadecimal characters identify the manufacturer (vendor), and the last six identify the specific device.​
- Networks often use MAC addresses for low‑level control, such as access rules on Wi‑Fi hotspots or firewalls.​


## Pen‑testing relevance (identifying devices)

- As a penetration tester, you first enumerate hosts with IP addresses (IPv4 and sometimes IPv6) to build a map of potential targets.​
- Understanding public vs private IPs helps you see which systems are reachable directly and which may need pivoting or tunneling.​
- MAC spoofing lets you impersonate another device on the local network to bypass MAC‑based access control (for example, hotel or café Wi‑Fi).​
- Weak security designs that trust MAC addresses can be broken by spoofing the administrator’s MAC, bypassing firewall or network restrictions.


## Ping and ICMP

- Ping uses the ICMP protocol to test if a host is reachable on a network.​
- Ping sends an ICMP Echo Request, and a responsive host replies with an Echo Reply.​
- High latency or dropped ping replies can indicate network issues or filtering.​
- In offensive work, ping and ICMP sweeps help quickly find live hosts before deeper scanning.​


## Key tools and practical steps for pen testers

- Use ping (or similar tools like fping) to check if a target host or IP range is alive.​
- Follow up with port scanners (for example, nmap) to discover open ports and services on those hosts.​
- Map the network: list subnets, gateways, and important hosts (servers, routers, firewalls) for attack paths.​
- Always think in layers: host discovery, service discovery, then vulnerability discovery and exploitation.​

***

# 
Intro to LAN


## #1 - LAN TOPOLOGIES

### **What & Why**

- Topology = network design/layout; defines physical connections between devices
- Different topologies have trade-offs in cost, scalability, redundancy, and fault tolerance


### **Star Topology**

- Central hub/switch connects all devices; all traffic routes through center
- Most common today; highly reliable and scalable despite higher cost
- Device failure = isolated; central device failure = total network down (single point of failure)
- Easy to add devices; easier troubleshooting and maintenance
- Best for medium-large networks


### **Bus Topology**

- Single backbone cable; devices branch off (like tree leaves)
- Cheap and easy to set up; minimal cabling required
- Single backbone break = entire network fails; one point of failure
- All data on same cable = bottleneck; traffic collisions cause slowdowns; hard to troubleshoot
- Device identification difficult if issues arise


### **Ring Topology (Token Topology)**

- Devices form a loop; data flows one direction until reaching destination
- Lower cabling needs; less dependent on dedicated hardware than star
- Only one direction for traffic = easy to troubleshoot
- Unidirectional = inefficient (may visit multiple devices before destination)
- Cut cable or broken device = entire network breaks; no redundancy
- Less prone to bottlenecks vs. bus


### **Switches vs. Hubs**

- Switch: tracks device-port mapping; sends packets only to intended target; reduces network traffic
- Hub: broadcasts packet to every port (inefficient)
- Switches much more efficient; industry standard


### **Routers**

- Connect networks and pass data between them using routing (hence the name)
- Redundancy: multiple switches+routers = multiple data paths; if one fails, another exists
- Trade-off: longer travel time for packets but zero downtime


### **Key Terms**

- LAN = Local Area Network


## #2 - SUBNETTING

### **Core Concept**

- Splitting larger network into smaller miniature networks within it (like slicing a cake)
- Network administrators use subnetting to categorize and assign specific network parts


### **Why Subnetting**

- Efficiency: organize devices logically
- Security: separate departments/functions (e.g., accounting, HR, finance get own subnets)
- Full control: customize each subnet's rules and access


### **IP Address Structure**

- 4 octets (sections), each 0-255 range
- Example: 192.168.1.1 (octet1.octet2.octet3.octet4)
- Subnet mask: also 4 octets (32 bits total), ranges 0-255 per octet
- Network address = start of network (used to identify network's existence)
- Host address = individual device within subnet
- Default gateway = device sending off-network traffic; usually first (.1) or last (.254) address


### **Three IP Address Uses in Subnets**

1. Network Address: identifies network start (e.g., 192.168.1.0)
2. Host Address: identifies specific device (e.g., 192.168.1.100)
3. Default Gateway: sends traffic to another network (e.g., 192.168.1.254)


### **Subnet Mask: 32 bits**

- Each octet = 0-255
- Example: 255.255.255.0 common for small networks (\~254 devices)


### **Practical Limits**

- Small home networks: unlikely to exceed 254 devices at once
- Businesses: thousands of devices = subnetting essential


## #3 - ARP

### **Definition & Purpose**

- ARP = Address Resolution Protocol
- Maps MAC addresses to IP addresses
- Allows devices to identify themselves and locate others on a network
- Two identifiers per device: MAC (physical) + IP (logical)


### **How ARP Works**

- Each device maintains ARP cache (ledger of MAC-IP mappings from other devices)
- When device wants to communicate, sends broadcast: "What is the MAC of IP X.X.X.X?"
- Only device with that IP responds with own MAC; others stay silent
- Requesting device stores this MAC-IP mapping in cache for future use


### **ARP Messages**

1. ARP Request: broadcast asking "Who has IP X?"
2. ARP Reply: targeted response with "I have IP X, my MAC is Y"


### **Practical Flow**

- Device A requests: "Who has IP 192.168.1.10?" (broadcast)
- Device B replies: "I have IP 192.168.1.10, my MAC is 01:00:AB:78:99:33" (directed)
- Device A remembers this mapping for next time


### **Key Identifiers**

- MAC Address: physical identifier (48-bit, format: XX:XX:XX:XX:XX:XX)
- IP Address: logical identifier (32-bit, format: X.X.X.X)


## #4 - DHCP

### **Definition & Automation**

- DHCP = Dynamic Host Configuration Protocol
- Automatic IP assignment (vs. manual entry)
- Server assigns IPs to devices; much more practical at scale


### **DHCP Process: 4-Step Handshake**

1. **DHCP Discover**: New device broadcasts "Anyone here? Can I get an IP?"
2. **DHCP Offer**: Server replies "Yes, you can have IP 192.168.1.10"
3. **DHCP Request**: Device confirms "I want to use 192.168.1.10"
4. **DHCP ACK**: Server confirms "Okay, use that IP for the next 24 hours"


### **Key Detail**

- IP assigned for limited time (lease), typically 24 hours
- Device must renew or IP reassigned to another device


### **Message Types**

- Discover = retrieve IP
- Offer = server proposes IP
- Request = device confirms it wants that IP
- ACK = server finalizes lease


### **Contrast with Manual**

- Manual = physical entry into each device (impractical, error-prone)
- DHCP = automatic, scalable, standard method


## RED TEAM / PENTEST FOCUS

### **Network Enumeration Tools & Concepts**

- Topology mapping = identify single points of failure for disruption
- Subnetting = understand network segmentation (where to focus lateral movement)
- ARP = ARP spoofing/poisoning vector; cache manipulation; man-in-the-middle potential
- DHCP = DHCP starvation attacks; rogue DHCP servers; IP exhaustion

### **Reconnaissance Priorities**

- Map topology (find central switches/routers)
- Identify subnets and their boundaries
- Enumerate ARP cache entries (who talks to whom)
- Discover DHCP server location and configuration

***

# OSI Model



## What is the OSI Model?

The OSI (Open Systems Interconnection) model is a fundamental networking framework that explains how all networked devices send, receive, and interpret data. The main benefit of using this model is that devices with completely different designs and functions can communicate with each other, as long as they follow the same standards. The model has seven layers, numbered from 7 at the top down to 1 at the bottom. As data moves through each layer, specific information is added to it—a process called encapsulation.

## Layer 1: Physical

This is the easiest layer to understand and the lowest one in the model. It deals with the actual physical hardware components used in networking. Devices use electrical signals to transfer data in a binary numbering system (ones and zeros). The most common example is ethernet cables that connect devices together. At this layer, you're dealing with the real, tangible equipment that makes networks work.

## Layer 2: Data Link

This layer focuses on the physical addressing of data being sent. It works with MAC (Media Access Control) addresses, which are unique identifiers that come built into every Network Interface Card (NIC). MAC addresses are set by the manufacturer and are burned into the card permanently—they can't be changed, though they can be spoofed. The job of this layer is to add the physical MAC address so the network knows exactly where to send information on the local network. It also makes sure data is presented in a format suitable for transmission.

## Layer 3: Network

This is where routing happens. The network layer takes small chunks of data and decides the best path for them to travel across the network. It considers factors like which path has the fewest devices, which path is most reliable, and which has the fastest physical connection. Everything at this layer uses IP addresses (like 192.168.1.100). Routers are the key devices here—they can deliver packets using IP addresses, which is why they're called Layer 3 devices. Two important routing protocols to know are OSPF (Open Shortest Path First) and RIP (Routing Information Protocol).

## Layer 4: Transport

This layer handles how data actually gets transmitted across the network. There are two main protocols you need to know: TCP and UDP, and they work very differently.

**TCP (Transmission Control Protocol)** is reliable and connection-oriented. It's slower than UDP, but it guarantees that your data arrives accurately. TCP includes error checking, keeps a constant connection open between devices, synchronizes them to prevent flooding, and does a lot of extra work to ensure reliability. Use TCP when you need accuracy—for things like file sharing, email, or web browsing where losing data would be a problem.

**UDP (User Datagram Protocol)** is much faster but doesn't care if your data gets lost. It doesn't reserve a constant connection, leaves control to the application layer, and sends data hoping for the best. UDP works well for things like video streaming or discovering devices (ARP and DHCP) where losing a few packets is acceptable and speed matters more than perfection.

## Layer 5: Session

This layer creates, maintains, and closes the connection between two computers. When you establish a connection, a session is created. As long as that connection is active, the session exists. If the connection isn't used for a while or gets lost, this layer closes it. The session layer also sets "checkpoints" in your data so that if something goes wrong, only the newest pieces of data need to be sent again instead of everything—this saves bandwidth. One important thing to remember is that sessions are unique to each connection—data can't jump between different sessions.

## Layer 6: Presentation

This layer is all about standardization and making data presentable. It acts as a translator between different systems. When you send an email using one email client, the receiving person can see it properly in their different email client—that's this layer doing its job. It translates data so that different programs can understand it even if they normally work with different formats. Security features like HTTPS encryption happen at this layer too. The main function is to ensure that data from different systems can be understood by each other.

## Layer 7: Application

This is the layer that users interact with directly. It's where protocols and rules are put in place to determine how users should interact with data being sent or received. Applications like email clients, web browsers, and file servers all operate here. The layer provides a Graphical User Interface (GUI) so people can easily interact with the network. Important protocols at this layer include DNS (Domain Name System), which translates website addresses into IP addresses so your browser can find them. This is the most familiar layer because it's what you actually see and use every day.

## Key Facts to Remember

- Encapsulation is the process of adding information to data as it passes through each layer
- MAC addresses handle local network addressing (Layer 2)
- IP addresses handle network-wide routing (Layer 3)
- TCP guarantees your data arrives; UDP doesn't care but it's fast
- Routers work at Layer 3; switches work at Layer 2
- Encryption happens at Layer 6
- Your applications and browsers operate at Layer 7
- The model goes from Layer 7 (Application) down to Layer 1 (Physical)

## For Penetration Testing

Understanding the OSI model reveals where you can attack a network. At Layer 2, you can spoof MAC addresses. At Layer 3, you can manipulate routing or spoof IP addresses. At Layer 4, you can scan ports and analyze protocols. At Layer 7, you can intercept applications or poison DNS. The OSI model is essentially a map of all the places where security weaknesses can exist.

***

# Packets & Frames

## Overview: Understanding Network Communication

Data travels across networks in small, manageable pieces rather than as whole files or messages. A packet is a piece of data from Layer 3 (Network Layer) containing IP addressing information and payload, while a frame operates at Layer 2 (Data Link Layer) and wraps the packet with additional information like MAC addresses. Think of it like mailing a letter: the envelope (frame) protects the letter (packet) during transit, and the recipient opens the envelope to access the contents. This layering is called encapsulation, which allows networks to function with standardized protocols and rules.

## Packets and Their Structure

Packets contain various headers that provide routing and integrity information. The Source Address header identifies where the packet originates so data knows where to return. The Destination Address tells routers where the packet is headed. Time to Live (TTL) acts as an expiry timer preventing packets from circulating endlessly if they fail to reach their destination. Checksum provides integrity checking for TCP/IP—if data corruption occurs during transmission, the receiving device detects it through mathematical calculation mismatch. The Data header is where the actual file bytes are stored. These headers are essential because billions of devices on the internet require standardization to prevent breakdown.

## TCP/IP: Connection-Based Communication

TCP (Transmission Control Protocol) is a connection-based protocol that must establish a connection between a client and server before any data transmission. This reliability comes at a cost—TCP processes require more computing power and result in slower speeds than alternatives. The TCP/IP model consists of four layers: Application (user software), Transport (TCP/UDP), Internet (IP routing), and Network Interface (physical connectivity). Like the OSI model, information is added to each layer as packets traverse down, creating encapsulation.

## TCP Headers

Source Port is randomly chosen from ports 0-65535 by the sender. Destination Port is predetermined (port 80 for web servers). Source IP identifies the sending device. Destination IP specifies where the packet travels. Sequence Number is a random starting value ensuring data arrives in correct order—incremented by 1 for each subsequent piece. Acknowledgement Number confirms the server has received the client's initial sequence. Checksum provides the mathematical integrity verification. Flag determines how packets should be handled during the handshake process.

## The Three-Way Handshake: Establishing TCP Connections

TCP connections require three distinct message exchanges. In Step 1, the client sends a SYN packet with its Initial Sequence Number (ISN) synchronized to 0. In Step 2, the server responds with SYN/ACK, providing its own ISN and acknowledging the client's initial number. In Step 3, the client sends ACK, acknowledging the server's ISN (incremented by 1). Both devices must agree on starting sequence numbers so data arrives in proper order. After establishment, data flows via the DATA message. Closing requires a FIN packet to gracefully terminate the connection, with the receiving device ACKnowledging receipt. If something goes wrong, RST abruptly ends communication as a last resort.

## TCP Closing a Connection

TCP reserves system resources, so connections should be closed promptly. When a device initiates closure, it sends a FIN packet. The receiving device acknowledges with ACK and sends its own FIN to indicate mutual agreement. The initiator confirms with a final ACK. This four-step process ensures both sides agree the connection is terminated. If resources aren't released, network congestion can occur.

## UDP/IP: Speed Over Reliability

UDP (User Datagram Protocol) is a stateless protocol—it sends data without establishing any connection or ensuring delivery. Unlike TCP's handshake, UDP simply sends information. Applications tolerating data loss (video streaming, voice chat) use UDP for its speed advantage. UDP packets contain fewer headers: Time to Live, Source Address, Destination Address, Source Port (randomly chosen), Destination Port (predetermined), and Data. UDP doesn't care whether packets arrive, nor does it check integrity like TCP does. This flexibility appeals to software developers but creates terrible user experiences when connections are unstable.

## Comparing TCP and UDP

TCP guarantees data integrity and maintains synchronized order but requires significant processing. It prevents device flooding and synchronizes devices to avoid data arrival in wrong order. However, TCP is substantially slower and requires constant connection reservation. UDP is much faster because it demands no connection establishment or acknowledgement. Applications control how quickly packets send. UDP leaves error-handling to software developers, making it unsuitable for reliable file transfers. TCP works for file transfer, UDP for video calls.

## Ports: Directing Network Traffic

Ports are numerical values between 0 and 65535 (1024 is the maximum for common ports) that enforce strict rules about which applications handle what communication. Like a physical harbour requiring ships to dock at compatible ports, network ports ensure data goes to appropriate services. Port 80 is the standard for HTTP (web browsing), port 443 for HTTPS (secure web), port 21 for FTP (file transfer), port 22 for SSH (secure terminal access), port 445 for SMB (file/printer sharing), and port 3389 for RDP (remote desktop). Applications expect standardized port usage, but administrators can technically run services on non-standard ports if they specify the colon notation (e.g., 8080 instead of 80).

## Common Protocols and Their Ports

File Transfer Protocol (FTP) operates on port 21 for client-server file-sharing. Secure Shell (SSH) uses port 22 for secure terminal-based system management. HyperText Transfer Protocol (HTTP) uses port 80 for web browsing. HTTPS uses port 443 for encrypted web traffic. Server Message Block (SMB) on port 445 enables file and printer sharing like FTP but supports device sharing. Remote Desktop Protocol (RDP) on port 3389 provides secure visual desktop access, avoiding SSH's text-only limitations.

## Red Team/Penetration Testing Relevance

Understanding packets and frames is fundamental for network reconnaissance. Port scanning reveals which services run on a target. TCP/UDP protocol differences inform attack vector selection—unreliable UDP connections might allow packet injection, while TCP's rigid handshake can be exploited through SYN flood attacks (sending numerous SYN packets without completing handshakes, exhausting server resources). Monitoring sequence numbers and flags reveals connection establishment patterns useful for man-in-the-middle attacks. Knowing that data lacks encryption at lower layers (frames contain MAC addresses in plaintext) shows why network sniffing is effective. Common ports are first reconnaissance targets.

## Key Facts to Remember

Packets carry Layer 3 data with IP addresses; frames carry Layer 2 data with MAC addresses. TCP requires three-way handshake before data transmission; UDP sends immediately. Sequence numbers ensure TCP data arrives in order; acknowledgement numbers confirm receipt. TTL prevents infinite packet circulation. Checksum detects corruption. TCP is reliable but slow; UDP is fast but unreliable. Ports 0-1024 are common/reserved; applications running on different ports must specify colon notation. FTP, SSH, HTTP, HTTPS, SMB, and RDP are the most critical protocols for both administration and penetration testing.

***

# Extending Your Network: Revision Notes

## Overview

This module covers technologies that extend networks beyond their local boundaries and connect them to the Internet. The three core concepts are port forwarding (making internal services accessible publicly), firewalls (controlling traffic between networks), and VPNs (creating secure encrypted tunnels between separate networks). Understanding these technologies is essential for network administration and critical for penetration testing, as attackers exploit misconfigured forwarding rules, firewall bypasses, and unencrypted network segments.

## Port Forwarding: Opening Internal Services to the Internet

**What it does:** Port forwarding maps external ports on a router's public IP to internal ports on specific devices within the local network. Without it, applications and services running on internal machines (like web servers on 192.168.1.10 port 80) are only accessible to devices on the same network (intranet).

**How it works:** An administrator configures the router to accept traffic on a specific external port and forward it to an internal IP address and port. For example, traffic coming to 82.62.51.70 port 80 gets forwarded to 192.168.1.10 port 80, making the internal web server publicly accessible while maintaining the intranet for direct device-to-device access.

**Key distinction:** Port forwarding only opens specific ports. Firewalls determine whether traffic can actually travel across those ports, even if they're opened by forwarding rules. They serve different functions—forwarding creates the pathway, firewalls decide what's allowed through.

**Configured at:** The router level, using its administrative interface (usually a website or command console).

**Red team relevance:** Identifying port forwarding misconfigurations can expose internal services to the Internet. Attackers scan for these exposed services to find entry points or information about internal infrastructure. Looking for common patterns like exposed database ports, management interfaces, or debug services is a valuable reconnaissance technique.

## Firewalls: Border Security for Networks

**Definition:** A firewall is a network security device responsible for determining what traffic is permitted to enter and exit a network. Think of it as border control—it examines incoming and outgoing traffic based on rules and makes allow/deny decisions.

**Decision factors:** Firewalls examine multiple attributes when deciding whether to permit or deny traffic:

- Source network (where is the traffic coming from?)
- Destination network (where is the traffic going?)
- Port number (specific service ports like 80 for HTTP, 443 for HTTPS)
- Protocol type (TCP, UDP, or both?)


**Two firewall categories:**

    1. **Stateful Firewalls** – Track entire connections rather than individual packets. They maintain context about ongoing connections and can make dynamic decisions. A stateful firewall might allow the opening handshake of a TCP connection but block later packets if the connection becomes suspicious. They consume more resources but offer sophisticated protection. If one host is flagged as malicious, the entire connection from that device can be blocked. These are common in enterprise environments.
    2. **Stateless Firewalls** – Apply static rules to individual packets independently, without connection context. They evaluate each packet against predefined rules and don't track whether packets belong to an established connection. They use fewer resources and are useful for handling massive traffic volumes (like DDoS attacks), but they're dumber—they can't detect sophisticated attacks that span multiple packets or connections. If a single bad packet passes the rules, it won't block related traffic from that source.


**Operating layers:** Firewalls work at layers 3 and 4 of the OSI model—the network layer (IP addresses) and transport layer (ports and protocols). Some advanced firewalls also inspect application-layer data (layer 7), but the core functionality operates at these lower layers.


**Red team relevance:** Understanding firewall rulesets is crucial. Attackers look for overly permissive rules, default deny/allow configurations, or protocol-specific bypasses. Identifying what ports are open and what protocols they accept reveals potential attack vectors. Many firewalls can be circumvented through protocol manipulation or by finding unexpected open ports. During reconnaissance, scanning for filtered vs. closed ports tells you where a firewall is operating.

## VPN Technology: Creating Secure Private Networks

**Core concept:** A Virtual Private Network (VPN) allows devices on physically separate networks to communicate securely by creating an encrypted tunnel over the Internet. All traffic within this tunnel is private—devices connected via a VPN form their own isolated network even though they may be in different cities or countries.


**Practical application:** Two office networks can be connected via VPN, allowing employees to access servers and resources from either location as if they were on the same physical network. The VPN creates a virtual tunnel (hence the name) where all data between the offices is encrypted and protected from eavesdropping.


**Benefits:**

- **Geographic connectivity:** Businesses with multiple offices can connect remote locations securely, accessing centralized resources without exposing them to the public Internet.
- **Privacy:** VPN encryption ensures data can only be understood by the sending and receiving devices. Even on public WiFi networks where no encryption exists, a VPN protects your traffic from being sniffed by other users.
- **Anonymity:** VPNs can hide your true IP address, making your traffic appear to come from the VPN server's location instead. Journalists and activists use VPNs to safely report from countries with restricted freedom of speech. However, anonymity depends on VPN provider trustworthiness—a VPN that logs all traffic provides zero anonymity.


**TryHackMe's use of VPNs:** The platform uses VPNs to securely connect students to vulnerable machines without making those machines directly accessible on the Internet. This provides security to learners and prevents ISPs from flagging penetration testing activities as attacks on Internet-facing machines.

## VPN Technologies: Three Main Protocols

**PPP (Point-to-Point Protocol)** – The foundation technology for VPN data transmission. PPP handles authentication and encryption using private keys and public certificates (similar to SSH). It works by matching a private key with its corresponding certificate for secure connection establishment. However, PPP alone is non-routable—it cannot leave a network by itself. It's a low-level protocol that handles the encryption layer but needs additional technology to actually transmit the encrypted data across networks.


**PPTP (Point-to-Point Tunneling Protocol)** – Builds on PPP by adding the ability to tunnel encrypted PPP data across networks over the Internet. PPTP is easy to set up and widely supported across devices, making it popular for simple VPN deployments. The tradeoff is that PPTP offers weak encryption compared to alternatives. It's suitable for basic privacy scenarios but inadequate for security-critical applications. In penetration testing, PPTP implementations are often vulnerable to known attacks due to weak encryption.


**IPSec (Internet Protocol Security)** – Encrypts data using the existing Internet Protocol (IP) framework itself rather than wrapping protocols. IPSec is much more complex to set up and configure correctly, but when properly implemented, it provides strong encryption. It's supported on many devices and considered enterprise-grade. IPSec is difficult to crack due to its robust implementation, making it the preferred choice for security-critical connections despite higher setup complexity.


**Key questions to remember:**

- PPP only encrypts and authenticates data (doesn't leave networks)
- PPTP allows PPP data to travel across networks
- IPSec uses the IP framework for strong encryption

## LAN Networking Devices: Routers and Switches

**Routers: The Connectors**

A router is a dedicated device responsible for connecting different networks and passing data between them using routing logic. Routers work at Layer 3 (Network layer) of the OSI model. They feature administrative interfaces (web interfaces or CLI consoles) where administrators configure rules like port forwarding and firewalling.

Routing is the process of creating optimal paths across multiple networks so data successfully reaches its destination. When multiple paths exist between networks, different protocols determine which to use based on factors like path length, reliability, and link speed. Routers do this automatically using routing protocols. They are dedicated devices—distinct from switches—and don't perform the same functions as switches.

Red team application: Routers are primary targets in network attacks. Compromising a router gives access to all traffic passing through it. Port forwarding rules often expose valuable services. Default credentials on routers are frequently exploited. Understanding router-level filtering is essential for bypassing network segmentation.


**Switches: The Connectors (Within Networks)**

A switch is a dedicated networking device that connects multiple devices within a local network using Ethernet cables. Switches can facilitate between 3 and 63 devices depending on port count. They forward frames (Layer 2 data packets) based on MAC addresses to the correct physical ports. Switches are purely responsible for connecting devices locally—they don't route traffic between networks.


**Layer 2 Switches** – Operate only at the Data Link layer. They learn MAC addresses from incoming frames and build a forwarding table, then forward subsequent frames to the correct port based on the destination MAC address. They cannot perform any network-level (Layer 3) functions.

**Layer 3 Switches** – More sophisticated devices that can perform some router responsibilities. They handle both frame forwarding (Layer 2) and IP-based routing (Layer 3), allowing them to route packets to other networks using IP addresses. Layer 3 switches are essentially routers embedded in switches.


**VLAN (Virtual Local Area Network)** – A technology that virtually segments a physical switch so specific devices communicate only with each other, even though they're connected to the same switch. VLANs provide security through logical isolation. For example, a Sales Department (VLAN 1) and Accounting Department (VLAN 2) could be connected to the same physical switch but unable to directly communicate due to VLAN separation. Communication between VLANs requires a router, which can enforce additional security policies. Layer 3 switches with VLAN support handle this routing transparently.


**Red team relevance:** Network segmentation via VLANs is a critical defensive control. Attacking one VLAN-separated department doesn't give immediate access to others. However, finding VLAN configuration issues or ways to trick the switch into bridging VLANs is a valuable attack technique. MAC address spoofing, VLAN hopping, and switch exploitation are common attack vectors.

## **Common misunderstandings to avoid:**

- Firewalls don't create pathways—port forwarding does. Firewalls only permit or deny traffic on existing pathways.
- VPNs don't make you untraceable to the VPN provider itself—only to outside observers. Choose trustworthy VPN providers.
- Layer 2 switches forward based on MAC addresses, not IP addresses. Only routers and Layer 3 switches use IP routing.
- A single VLAN separation is only as strong as the router enforcing it—router misconfiguration breaks VLAN security.

## Key Facts for Quick Review

**Port Forwarding:** Maps external ports to internal services. Configured at routers. Exposes internal machines to the Internet without being a firewall.

**Firewalls - Stateful:** Track connections, make dynamic decisions, use more resources, suitable for standard networks.

**Firewalls - Stateless:** Judge individual packets, use fewer resources, excellent for high-volume attacks, unsuitable for sophisticated threats.

**VPN:** Encrypts traffic between networks. Three main technologies: PPP (encryption only), PPTP (easy setup, weak encryption), IPSec (complex setup, strong encryption).

**Routers:** Connect networks at Layer 3, use IP addresses, implement routing logic. Pentesting targets due to access to all network traffic.

**Switches:** Connect devices within networks at Layer 2, use MAC addresses. Layer 3 switches add routing capabilities.

**VLANs:** Virtual network segmentation on physical switches. Provides logical isolation but only as strong as router enforcement.

## Penetration Testing Angles

- Identify exposed port forwarding rules by scanning external IPs for unexpected open ports
- Test firewall rules for overly permissive configurations or protocol-specific bypasses
- Exploit weak VPN implementations (particularly PPTP) for credential harvesting
- Compromise routers to intercept all network traffic or modify routing rules
- Exploit VLAN misconfigurations to move laterally between network segments
- Social engineer network administrators to reveal firewall and VPN configurations
- Perform man-in-the-middle attacks on poorly configured VPN tunnels