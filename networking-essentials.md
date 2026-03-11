# Networking Essentials

### 🔧 DHCP (Dynamic Host Configuration Protocol)  

**Used For**: Automatically assigning IP addresses, gateways, and DNS to devices — so users don’t configure them manually. Critical for mobile devices and avoiding IP conflicts.  

- Uses **UDP ports 67 (server) / 68 (client)**  
- Follows **4 steps (DORA)**: Discover → Offer → Request → Acknowledge  
- Prevents IP conflicts and simplifies network setup

---

### 🔍 ARP (Address Resolution Protocol)  

**Used For**: Mapping IP addresses to MAC addresses on the same local network — needed for Ethernet/WiFi communication.  

- Sends **ARP Request** to broadcast MAC (`ff:ff:ff:ff:ff:ff`)  
- Target replies with **ARP Reply** containing its MAC  
- Operates at Layer 2, directly inside Ethernet frames

---

### 📡 ICMP (Internet Control Message Protocol)  

**Used For**: Network diagnostics and error reporting — powers `ping` and `traceroute` for testing connectivity and tracing routes.  

- `ping` = ICMP Echo Request (Type 8) → Reply (Type 0)  
- `traceroute` = uses **TTL field** → triggers ICMP Time Exceeded (Type 11) when TTL hits 0  
- Firewalls can block ICMP, affecting visibility

---

### 🗺️ Routing Protocols  

**Used For**: Helping routers choose the best path to forward packets across networks — essential for the Internet to function at scale.  

- **OSPF** – open, link-state (enterprise)  
- **BGP** – for inter-ISP routing (Internet backbone)  
- **EIGRP** – **Cisco proprietary**  
- **RIP** – older, distance-vector

---

### 🌐 NAT (Network Address Translation)  

**Used For**: Letting many private devices share one public IP to access the internet — conserves IPv4 and hides internal network structure.  

- Router maintains a **translation table** (internal IP:port ↔ public IP:port)  
- Web servers see the **router’s public IP**, not your device’s private one  
- Enables home networks, offices, and IoT devices to connect without unique public IPs

---

This version keeps it clean, scannable, and perfect for quick revision. Want it as flashcards or printable format next?
