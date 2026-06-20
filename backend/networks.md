---
name: Networks — Backend Engineering
---

# What are the seven layers of the OSI model and what does each do?

The OSI (Open Systems Interconnection) model is a conceptual framework for understanding how network protocols interact.

| Layer | Name             | Responsibility                                    | Example protocols                 |
| ----- | ---------------- | ------------------------------------------------- | --------------------------------- |
| 7     | **Application**  | User-facing network services                      | HTTP, DNS, FTP, SMTP              |
| 6     | **Presentation** | Data encoding, encryption, compression            | TLS, MIME, JPEG                   |
| 5     | **Session**      | Establishing, managing, terminating sessions      | TLS session resumption, RPC       |
| 4     | **Transport**    | End-to-end delivery, reliability, multiplexing    | TCP, UDP                          |
| 3     | **Network**      | Logical addressing, routing between networks      | IP, ICMP, OSPF, BGP               |
| 2     | **Data Link**    | Node-to-node delivery on the same network segment | Ethernet, Wi-Fi (802.11), ARP     |
| 1     | **Physical**     | Raw bit transmission over physical media          | Cables, radio waves, fibre optics |

In practice, TLS spans layers 4–6 and is often described as sitting between layers 4 and 7.

**Source:** <https://www.cloudflare.com/learning/ddos/glossary/open-systems-interconnection-model-osi/>

<AnkiTags OSI model layers fundamentals junior/>

# What is the TCP/IP model and how does it map to OSI?

The TCP/IP model (also called the Internet model) is the practical four-layer framework that the modern Internet is built on. It consolidates OSI's seven layers.

| TCP/IP Layer              | Equivalent OSI Layers | Protocols                      |
| ------------------------- | --------------------- | ------------------------------ |
| **Application**           | 5, 6, 7               | HTTP, DNS, SMTP, FTP, SSH, TLS |
| **Transport**             | 4                     | TCP, UDP, QUIC                 |
| **Internet**              | 3                     | IPv4, IPv6, ICMP, OSPF, BGP    |
| **Link** (Network Access) | 1, 2                  | Ethernet, Wi-Fi, ARP           |

The OSI model is a teaching/reference model; the TCP/IP model describes how the Internet actually works. Engineers reference OSI layers by number ("L3 routing", "L7 load balancer") even when using TCP/IP.

**Source:** <https://www.cloudflare.com/learning/network-layer/what-is-a-protocol/>

<AnkiTags TCP-IP OSI model fundamentals junior/>

# What is encapsulation in networking?

Encapsulation is the process by which each OSI layer **wraps the payload from the layer above with its own header** (and sometimes trailer) before passing it down. Decapsulation is the reverse at the receiver.

```
Application data:          [ HTTP request body         ]
Transport (TCP) adds:      [ TCP header | HTTP data    ]  → "segment"
Network (IP) adds:         [ IP header  | TCP segment  ]  → "packet"
Data Link adds:            [ ETH header | IP packet | ETH trailer ] → "frame"
Physical:                    raw bits transmitted on wire
```

Each layer generally only reads its own header — TCP does not parse the IP header's routing fields, and the switch does not read TCP. This separation is what allows protocols to be swapped independently (e.g. replacing IPv4 with IPv6 without changing TCP or HTTP). There are a few narrow exceptions where layers do share signals — e.g. TCP reacts to ECN bits carried in the IP header, and Path MTU Discovery feeds IP-layer fragmentation information back into TCP's segment sizing — but as a model, "each layer minds its own header" holds for the vast majority of cases.

**Source:** <https://www.cloudflare.com/learning/network-layer/what-is-a-protocol/>

<AnkiTags encapsulation OSI layers fundamentals junior/>

# What is the PDU (Protocol Data Unit) at each OSI layer?

Each layer has a specific name for its unit of data:

| Layer           | PDU name                               |
| --------------- | -------------------------------------- |
| 7 – Application | **Message** (or data)                  |
| 4 – Transport   | **Segment** (TCP) / **Datagram** (UDP) |
| 3 – Network     | **Packet**                             |
| 2 – Data Link   | **Frame**                              |
| 1 – Physical    | **Bit**                                |

Knowing the correct names is important for precision in conversations about networking — a router forwards _packets_, a switch forwards _frames_, and TCP deals in _segments_.

**Source:** <https://www.cloudflare.com/learning/network-layer/what-is-a-protocol/>

<AnkiTags PDU OSI layers fundamentals junior/>

# What are the main types of network transmission media?

**Copper (electrical):**

- **Twisted pair (UTP/STP):** Cat5e (1 Gbps), Cat6 (10 Gbps), Cat6a (10 Gbps at 100m). Susceptible to EMI.
- **Coaxial cable:** Used in cable TV and older 10BASE2 Ethernet.

**Fibre optic (light):**

- **Single-mode fibre (SMF):** Long distance (40+ km), narrow core, uses laser. Used in WAN/backbone.
- **Multi-mode fibre (MMF):** Shorter distance (< 500m), wider core, uses LED/VCSEL. Used in datacentres.

**Wireless (electromagnetic):**

- **Wi-Fi (2.4/5/6 GHz):** 802.11 standards, shared medium.
- **Cellular:** LTE, 5G — used for mobile and IoT backhaul.
- **Satellite:** Latency varies sharply by orbit — LEO (e.g. Starlink) ~20–60ms RTT; GEO ~500–700ms RTT.

Fibre dominates modern datacentre interconnects due to its bandwidth (400 Gbps+) and low loss.

**Source:** <https://standards.ieee.org/ieee/802.3/7071/>
<https://www.cloudflare.com/learning/network-layer/what-is-a-network/>

<AnkiTags physical-layer media cables fibre junior intermediate/>

# What is the difference between bandwidth, throughput, and latency?

**Bandwidth:** The maximum data rate a link can carry — a property of the physical medium. Measured in bits/second (Mbps, Gbps). Analogous to a pipe's diameter.

**Throughput:** The actual data rate achieved end-to-end, accounting for protocol overhead, congestion, and loss. Always ≤ bandwidth.

**Latency:** The time for one bit to travel from source to destination. Components:

- **Propagation delay:** distance ÷ speed of light in medium (~2×10⁸ m/s in copper).
- **Transmission delay:** packet size ÷ bandwidth.
- **Processing delay:** time in routers/switches.
- **Queuing delay:** time waiting in buffers.

```
RTT (Round-Trip Time) = 2 × one-way latency (approximately)
Bandwidth × RTT = Bandwidth-Delay Product (BDP)
```

For most backend services: latency limits interactive performance; bandwidth limits bulk transfers.

**Source:** <https://www.cloudflare.com/learning/performance/glossary/network-latency/>

<AnkiTags performance bandwidth throughput latency fundamentals junior intermediate/>

# What is a MAC address and how is it used?

A MAC (Media Access Control) address is a **48-bit hardware identifier**, traditionally assigned to a network interface card (NIC) at manufacture. It is used for communication within a local network segment (Layer 2). Modern operating systems, virtualized NICs, and Wi-Fi privacy features routinely override the manufacturer-assigned address with a software-defined one, so "burned-in" is no longer a safe assumption.

Format: 6 bytes written as hex pairs — `AA:BB:CC:DD:EE:FF`

- First 3 bytes: **OUI** (Organizationally Unique Identifier) — identifies the manufacturer.
- Last 3 bytes: **NIC-specific** identifier assigned by the manufacturer.

Key properties:

- Used by Ethernet and Wi-Fi frames as source/destination addressing.
- Only meaningful within a single network segment — routers do not forward L2 frames.
- Can be spoofed in software (`ip link set eth0 address AA:BB:CC:DD:EE:FF`).
- `FF:FF:FF:FF:FF:FF` is the **broadcast address** — sent to all devices on the segment.

**Source:** <https://standards.ieee.org/ieee/802/711/>
<https://www.cloudflare.com/learning/network-layer/what-is-a-mac-address/>

<AnkiTags MAC data-link Ethernet layer2 junior/>

# What is the structure of an Ethernet frame?

An Ethernet II frame (the standard used in modern networks) has the following structure:

```
┌─────────────────────────────────────────────────────────────────┐
│ Preamble (7B) │ SFD (1B) │ Dst MAC (6B) │ Src MAC (6B) │       │
│ EtherType (2B) │ Payload (46–1500B) │ FCS (4B CRC)           │
└─────────────────────────────────────────────────────────────────┘
```

| Field           | Size          | Purpose                                                            |
| --------------- | ------------- | ------------------------------------------------------------------ |
| Preamble + SFD  | 8 bytes       | Synchronisation; marks start of frame                              |
| Destination MAC | 6 bytes       | Recipient's MAC address                                            |
| Source MAC      | 6 bytes       | Sender's MAC address                                               |
| EtherType       | 2 bytes       | Payload protocol: `0x0800` = IPv4, `0x0806` = ARP, `0x86DD` = IPv6 |
| Payload         | 46–1500 bytes | Data (MTU = 1500 bytes by default)                                 |
| FCS (CRC)       | 4 bytes       | Error detection                                                    |

The maximum frame size of 1500 bytes payload is the standard **MTU (Maximum Transmission Unit)** for Ethernet. Jumbo frames extend this to 9000 bytes in datacentres.

**Source:** <https://standards.ieee.org/ieee/802.3/7071/>

<AnkiTags Ethernet frame data-link layer2 MTU intermediate/>

# What is ARP (Address Resolution Protocol)?

ARP resolves a known **IP address to its corresponding MAC address** on a local network segment. It is used every time a host wants to send a packet to an IP address on the same subnet.

**ARP process:**

```
1. Host A wants to send to 192.168.1.50 but doesn't know its MAC.
2. A broadcasts:  "Who has 192.168.1.50? Tell 192.168.1.10 (AA:BB:CC:…)"
   Destination MAC: FF:FF:FF:FF:FF:FF (broadcast)
3. Host B (192.168.1.50) unicasts back: "192.168.1.50 is at DD:EE:FF:…"
4. Host A caches this mapping in its ARP table for future use.
```

ARP is **stateless and unauthenticated** — any host can send a gratuitous ARP reply, poisoning the ARP cache of others. This is the basis of ARP spoofing/man-in-the-middle attacks on local networks.

```bash
# View ARP cache on Linux
arp -n
ip neigh show
```

**Source:** <https://www.rfc-editor.org/rfc/rfc826>
<https://www.cloudflare.com/learning/network-layer/what-is-arp/>

<AnkiTags ARP data-link layer2 MAC intermediate/>

# How does a network switch work?

A **switch** is a Layer 2 device that forwards Ethernet frames between devices on the same LAN based on MAC addresses.

**MAC address table (CAM table):**

- The switch learns which MAC addresses are reachable on which port by inspecting the **source MAC** of incoming frames.
- When a frame arrives for a known destination MAC, the switch forwards it only to the correct port (**unicast forwarding**).
- For unknown destinations, it **floods** the frame to all ports except the ingress port.
- For broadcast frames (`FF:FF:FF:FF:FF:FF`), it always floods.

```
Port 1: AA:BB:CC:11:22:33
Port 2: DD:EE:FF:44:55:66
Port 3: unknown → flood

Advantages over a hub:
- No collisions (each port is a separate collision domain)
- Bandwidth not shared (full-duplex per port)
```

**Layer 3 switches** can also route IP packets — they combine a switch with a router.

**Source:** <https://www.cloudflare.com/learning/network-layer/what-is-a-network-switch/>

<AnkiTags switch data-link layer2 MAC CAM-table junior intermediate/>

# What is a VLAN (Virtual LAN)?

A VLAN logically partitions a single physical switch into multiple **isolated broadcast domains**, as if they were separate switches. Devices in different VLANs cannot communicate at Layer 2 — they need a router or L3 switch.

**802.1Q VLAN tagging:** Frames between switches carry a 4-byte tag inserted after the source MAC:

```
[ Dst MAC | Src MAC | 802.1Q tag (VLAN ID 1–4094) | EtherType | Payload | FCS ]
```

**Port types:**

- **Access port:** Carries traffic for one VLAN, used for end devices. Tag stripped on egress.
- **Trunk port:** Carries traffic for multiple VLANs between switches, with tags preserved.

**Why VLANs matter for backend engineers:**

- Separating management, application, and storage traffic.
- Security isolation between tenants or environments (dev/staging/prod).
- Reducing broadcast storm blast radius.

**Source:** <https://standards.ieee.org/ieee/802.1Q/10323/>

<AnkiTags VLAN data-link layer2 802.1Q intermediate/>

# What is the difference between a hub, switch, and router?

| Device     | Layer        | Forwarding basis               | Scope                              |
| ---------- | ------------ | ------------------------------ | ---------------------------------- |
| **Hub**    | L1 Physical  | Repeats all bits to every port | Single collision domain            |
| **Switch** | L2 Data Link | MAC address table              | Single broadcast domain (per VLAN) |
| **Router** | L3 Network   | IP routing table               | Between different networks/subnets |

**Hub:** Dumb repeater. All connected devices share one collision domain and one broadcast domain. Half-duplex only. Obsolete.

**Switch:** Learns MAC addresses and forwards frames only to the correct port. Each port is its own collision domain. Can segment broadcast domains with VLANs.

**Router:** Connects different IP networks. Makes forwarding decisions based on IP destination and routing tables. Decrements TTL, rewrites L2 headers at each hop.

**Source:** <https://www.cloudflare.com/learning/network-layer/what-is-a-router/>

<AnkiTags router switch hub network-devices junior/>

# What is an IPv4 address and how is it structured?

An IPv4 address is a **32-bit number** written as four decimal octets separated by dots (dotted-decimal notation): `192.168.1.100`

Each octet is 8 bits → range 0–255 per octet → ~4.3 billion total addresses.

**Structure:** An IP address has two parts, determined by a subnet mask:

- **Network portion:** Identifies the network.
- **Host portion:** Identifies the device within that network.

```
IP:      192.168.  1.100
Binary:  11000000.10101000.00000001.01100100
Mask:    255.255.255.0  = /24
Network: 192.168.1.0/24
Host:    .100
```

**Special addresses:**

- `0.0.0.0` — unspecified / any address
- `127.0.0.1` — loopback (localhost)
- `255.255.255.255` — limited broadcast
- `169.254.0.0/16` — link-local (APIPA — auto-assigned when DHCP fails)

**Source:** <https://www.rfc-editor.org/rfc/rfc791>
<https://www.cloudflare.com/learning/network-layer/what-is-an-ip-address/>

<AnkiTags IPv4 addressing network-layer layer3 junior/>

# What are the private IPv4 address ranges?

RFC 1918 defines three ranges of private IP addresses — not routed on the public Internet:

| Range                             | CIDR             | Size               | Common use                    |
| --------------------------------- | ---------------- | ------------------ | ----------------------------- |
| `10.0.0.0` – `10.255.255.255`     | `10.0.0.0/8`     | 16.7 million hosts | Large enterprises, cloud VPCs |
| `172.16.0.0` – `172.31.255.255`   | `172.16.0.0/12`  | 1 million hosts    | Docker default bridge network |
| `192.168.0.0` – `192.168.255.255` | `192.168.0.0/16` | 65 536 hosts       | Home/office routers           |

Other special ranges:

- `127.0.0.0/8` — loopback
- `169.254.0.0/16` — link-local (APIPA)
- `100.64.0.0/10` — shared address space (RFC 6598, carrier-grade NAT)

Private addresses require **NAT** to communicate with the public Internet.

**Source:** <https://www.rfc-editor.org/rfc/rfc1918>

<AnkiTags IPv4 private-addresses RFC1918 NAT junior intermediate/>

# What is CIDR notation and how do you calculate a subnet?

CIDR (Classless Inter-Domain Routing) notation expresses an IP address and its prefix length as `IP/prefix`. The prefix length indicates how many leading bits are the **network portion**.

```
192.168.1.0/24
            └─ 24 bits = network; remaining 8 bits = hosts

Host count   = 2^(32-prefix) - 2  (subtract network & broadcast)
/24 → 2^8  - 2 = 254 usable hosts
/25 → 2^7  - 2 = 126 usable hosts
/30 → 2^2  - 2 = 2 usable hosts (point-to-point links)
/32 → single host address

Subnet mask for /24: 255.255.255.0
Subnet mask for /20: 255.255.240.0  (12 host bits → 4094 usable hosts)

Network address:   192.168.1.0    (host bits all 0)
Broadcast address: 192.168.1.255  (host bits all 1)
Usable range:      192.168.1.1 – 192.168.1.254
```

CIDR replaced the old class-based system (Class A /8, B /16, C /24) enabling more flexible address allocation.

**Source:** <https://www.rfc-editor.org/rfc/rfc4632>

<AnkiTags CIDR subnetting IPv4 addressing intermediate/>

# What is IPv6 and what are its key differences from IPv4?

IPv6 addresses the IPv4 exhaustion problem. Key differences:

| Feature       | IPv4                   | IPv6                                      |
| ------------- | ---------------------- | ----------------------------------------- |
| Address size  | 32 bits                | **128 bits**                              |
| Address space | ~4.3 billion           | ~3.4 × 10³⁸                               |
| Notation      | Dotted decimal         | Colon-hex: `2001:db8::1`                  |
| Header size   | 20–60 bytes (variable) | **40 bytes fixed**                        |
| Fragmentation | Routers can fragment   | **Only the source** fragments             |
| Broadcast     | Yes                    | **No** (replaced by multicast)            |
| ARP           | Yes                    | **NDP** (Neighbour Discovery Protocol)    |
| Auto-config   | DHCP                   | **SLAAC** (stateless address auto-config) |
| NAT required? | Often                  | Generally not needed                      |
| Loopback      | `127.0.0.1`            | `::1`                                     |

IPv6 address shorthand: consecutive groups of all zeros can be replaced by `::` once per address. `2001:0db8:0000:0000:0000:0000:0000:0001` → `2001:db8::1`

**Source:** <https://www.rfc-editor.org/rfc/rfc8200>

<AnkiTags IPv6 IPv4 addressing layer3 intermediate/>

# What is ICMP and how do ping and traceroute use it?

ICMP (Internet Control Message Protocol) is a Layer 3 helper protocol used to send **error messages and operational information** about IP packet processing. It does not carry application data.

**ping** uses ICMP Echo Request / Echo Reply:

```bash
ping 8.8.8.8
# Sends ICMP type 8 (Echo Request)
# Receives ICMP type 0 (Echo Reply)
# Measures RTT, reports packet loss
```

**traceroute** exploits the ICMP Time Exceeded message:

```bash
traceroute 8.8.8.8
# Sends packets with TTL=1, 2, 3, ...
# Each router that drops a packet (TTL=0) sends back ICMP type 11 (Time Exceeded)
# Traceroute identifies each hop's IP and RTT
```

Common ICMP message types: `0` Echo Reply, `3` Destination Unreachable, `8` Echo Request, `11` Time Exceeded, `12` Parameter Problem.

**Source:** <https://www.rfc-editor.org/rfc/rfc792>
<https://www.rfc-editor.org/rfc/rfc4443>

<AnkiTags ICMP ping traceroute layer3 network-layer junior intermediate/>

# What is a routing table and how does routing work?

A routing table is a data structure in a router (or host OS) that maps **destination IP prefixes to next-hop information**. When a packet arrives, the router performs **longest prefix match** (LPM) to find the most specific matching route.

```bash
# Linux routing table
ip route show
# default via 10.0.0.1 dev eth0      ← default route (0.0.0.0/0)
# 10.0.0.0/24 dev eth0 proto kernel   ← directly connected
# 172.16.0.0/12 via 10.0.0.5 dev eth0 ← static/dynamic route
```

**Longest prefix match:** `10.0.1.5` matches both `10.0.0.0/8` and `10.0.1.0/24` — the router chooses `/24` (more specific).

**Route types:**

- **Connected routes:** Automatically added for directly attached subnets.
- **Static routes:** Manually configured.
- **Dynamic routes:** Learned from routing protocols (OSPF, BGP).

The router rewrites L2 (Ethernet) headers at each hop but the IP addresses remain unchanged end-to-end (except through NAT).

**Source:** <https://www.rfc-editor.org/rfc/rfc1812>

<AnkiTags routing routing-table longest-prefix-match layer3 intermediate/>

# What is OSPF (Open Shortest Path First)?

OSPF is an **interior gateway protocol (IGP)** — a link-state routing protocol used within a single autonomous system (AS). It is the most common IGP in enterprise and ISP networks.

**How it works:**

1. Routers discover neighbours using OSPF Hello packets.
2. Each router floods **LSAs (Link State Advertisements)** describing its directly connected links.
3. Every router builds an identical **LSDB (Link State Database)**.
4. Each router independently runs **Dijkstra's SPF algorithm** to compute the shortest path to every destination.
5. Best paths are installed in the routing table.

**Key features:**

- Converges faster than distance-vector protocols (no counting to infinity).
- Supports **areas** to limit LSA flooding (Area 0 = backbone area).
- Uses IP protocol 89 (not TCP or UDP).
- Supports ECMP (Equal-Cost Multi-Path) natively.
- OSPFv3 supports IPv6.

**Source:** <https://www.rfc-editor.org/rfc/rfc2328>

<AnkiTags OSPF routing IGP link-state dynamic-routing intermediate advanced/>

# What is BGP (Border Gateway Protocol)?

BGP is the **exterior gateway protocol** that routes traffic between different autonomous systems (ASes) on the Internet. It is the protocol that makes the Internet work at a global scale — every ISP, cloud provider, and large enterprise uses BGP.

**Key concepts:**

- **AS (Autonomous System):** A network under a single administrative domain with a unique ASN (e.g., Google = AS15169, Cloudflare = AS13335).
- **eBGP:** BGP between different ASes — used at Internet exchange points and peering links.
- **iBGP:** BGP within the same AS — distributes externally learned routes internally.
- **BGP attributes:** Path selection uses attributes: AS_PATH, LOCAL_PREF, MED, NEXT_HOP. AS_PATH prevents routing loops.
- **Route advertisement:** Peers explicitly advertise their prefixes; BGP is **path-vector** (not distance-vector or link-state).

**Why backend engineers care:**

- BGP hijacking can redirect your traffic (e.g., the 2010 China Telecom BGP hijack).
- Anycast (used by CDNs and DNS) relies on multiple ASes advertising the same prefix.
- Cloud VPN/Direct Connect attachments use BGP to exchange routes.

**Source:** <https://www.rfc-editor.org/rfc/rfc4271>
<https://www.cloudflare.com/learning/security/glossary/what-is-bgp/>

<AnkiTags BGP routing EGP autonomous-system internet intermediate advanced/>

# What is the difference between IGP and EGP? What are common examples of each?

**Interior Gateway Protocols (IGP)** route traffic **within** a single autonomous system:

- **OSPF** — link-state, fast convergence, widely used in enterprise/ISP cores.
- **IS-IS** — link-state, used heavily by ISPs and large cloud providers.
- **EIGRP** — Cisco proprietary, distance-vector/hybrid.
- **RIP** — distance-vector, max 15 hops, largely obsolete.

**Exterior Gateway Protocols (EGP)** route traffic **between** autonomous systems:

- **BGP-4** — the only EGP in use today; runs the global Internet.

The distinction matters because IGPs optimise for fast convergence and shortest path inside a controlled network, while EGP (BGP) optimises for policy, business relationships, and scale across tens of thousands of independent organisations.

**Source:** <https://www.rfc-editor.org/rfc/rfc4271>
<https://www.rfc-editor.org/rfc/rfc2328>

<AnkiTags routing IGP EGP BGP OSPF dynamic-routing intermediate/>

# What is DHCP and how does the DORA process work?

DHCP (Dynamic Host Configuration Protocol) automatically assigns IP addresses and network configuration to hosts.

**DORA — the four-step process:**

```
Client                          DHCP Server
  │──── DISCOVER ─────────────────►│  (broadcast: "I need an IP")
  │◄─── OFFER ──────────────────────│  (unicast/broadcast: "Take 192.168.1.50")
  │──── REQUEST ────────────────────►│  (broadcast: "I'll take 192.168.1.50")
  │◄─── ACK ────────────────────────│  (unicast: "It's yours for 24h")
```

**DHCP lease:** The IP is assigned for a **lease time** (e.g., 24 hours). The client renews at 50% of lease time (T1), then again at 87.5% (T2). If not renewed, the IP is returned to the pool.

**DHCP provides:** IP address, subnet mask, default gateway, DNS servers, NTP servers, domain name.

**DHCP relay agent:** On networks with multiple subnets, a relay agent (often the router) forwards DHCP broadcasts to a central DHCP server since broadcasts don't cross router boundaries.

**Source:** <https://www.rfc-editor.org/rfc/rfc2131>

<AnkiTags DHCP DORA IP-assignment layer7 junior intermediate/>

# What is a default gateway and when is it used?

A **default gateway** is the router that a host sends packets to when the destination IP address is **not in any of the host's directly connected subnets**. It is the "door out" to other networks.

```
Host:            192.168.1.100/24
Default gateway: 192.168.1.1

Packet to 192.168.1.200 → same subnet → send directly (ARP for MAC)
Packet to 8.8.8.8       → different subnet → send to 192.168.1.1

# Set default gateway on Linux
ip route add default via 192.168.1.1
```

The default gateway is learned via DHCP, manual configuration, or routing protocols. Without a default gateway, a host can only communicate with devices on its directly connected network.

**Source:** <https://www.rfc-editor.org/rfc/rfc1122>

<AnkiTags routing gateway IPv4 networking junior/>

# What does TTL (Time to Live) mean at the IP layer?

At the IP layer, **TTL is an 8-bit field** in the IP header that is decremented by 1 at every router hop. When it reaches 0, the router drops the packet and sends an **ICMP Time Exceeded** message back to the sender.

Purpose: prevent packets from looping forever in case of routing loops.

```
Common initial TTL values:
Linux:   64
Windows: 128
Cisco:   255

traceroute output — TTL exploitation:
hop 1: TTL=1 → first router decrements to 0, drops, sends ICMP back
hop 2: TTL=2 → survives first router, dropped at second
...
```

**TTL in DNS** has a completely different meaning — the number of seconds a resolver should cache a DNS record. This is a different concept with the same acronym.

**Source:** <https://www.rfc-editor.org/rfc/rfc791>

<AnkiTags TTL IP layer3 ICMP traceroute junior intermediate/>

# What is TCP and what guarantees does it provide?

TCP (Transmission Control Protocol) is a **connection-oriented, reliable, ordered, full-duplex byte-stream protocol** at Layer 4.

Guarantees:

1. **Reliability:** Lost segments are retransmitted. The sender maintains unacknowledged segments in a retransmission buffer.
2. **Ordering:** Segments are reassembled in order using sequence numbers, even if they arrive out of order.
3. **Error detection:** Each segment has a checksum.
4. **Flow control:** The receiver advertises a receive window (`rwnd`) to prevent the sender from overwhelming it.
5. **Congestion control:** The sender reduces its rate when the network is congested.
6. **Full-duplex:** Data can flow simultaneously in both directions on a single connection.

These guarantees make TCP suitable for: HTTP, databases, SSH, email — any application where data integrity matters.

**Source:** <https://www.rfc-editor.org/rfc/rfc9293>

<AnkiTags TCP transport-layer layer4 reliability fundamentals junior/>

# What is the TCP 3-way handshake?

The 3-way handshake establishes a TCP connection and synchronises **sequence numbers** between client and server before any data is sent.

```
Client                          Server
  │──── SYN (seq=x) ──────────────►│   Client picks ISN x; sends SYN
  │◄─── SYN-ACK (seq=y, ack=x+1) ──│   Server picks ISN y; acks client's SYN
  │──── ACK (ack=y+1) ─────────────►│   Client acks server's SYN
  │                                 │
  │═══════════ DATA ════════════════│   Connection established
```

- **SYN:** Synchronise — initiates connection, sends Initial Sequence Number (ISN).
- **SYN-ACK:** Synchronise-Acknowledge — server acknowledges the client's ISN and sends its own.
- **ACK:** Acknowledge — client acknowledges the server's ISN. Can carry the first data segment (TCP Fast Open).

**Cost:** One round trip (RTT) before any data can be sent. This is why connection reuse (keep-alive, connection pools) matters.

**Source:** <https://www.rfc-editor.org/rfc/rfc9293>

<AnkiTags TCP handshake SYN ACK connection layer4 junior intermediate/>

# What is the TCP 4-way connection teardown?

TCP connections are closed using a 4-way exchange because **each direction is closed independently** — one side can stop sending while still receiving (half-close).

```
Client                           Server
  │──── FIN (seq=m) ──────────────►│   Client done sending
  │◄─── ACK (ack=m+1) ─────────────│   Server acks; may still send data
  │                 ...            │   (server continues sending if needed)
  │◄─── FIN (seq=n) ───────────────│   Server done sending
  │──── ACK (ack=n+1) ─────────────►│   Client acks
```

After sending the final ACK, the active closer enters **TIME_WAIT** for 2 × MSL (Maximum Segment Lifetime, typically 60s) to ensure the final ACK was received and to prevent stale segments from interfering with a new connection on the same 4-tuple.

**RST (Reset):** An abrupt close — no 4-way handshake, connection is immediately terminated. Sent on error, or by a firewall that drops a packet.

**Source:** <https://www.rfc-editor.org/rfc/rfc9293>

<AnkiTags TCP teardown FIN ACK RST TIME-WAIT layer4 intermediate/>

# What are the TCP connection states?

A TCP connection transitions through a defined set of states:

| State          | Description                                 |
| -------------- | ------------------------------------------- |
| `CLOSED`       | No connection                               |
| `LISTEN`       | Server waiting for incoming connections     |
| `SYN_SENT`     | Client sent SYN, waiting for SYN-ACK        |
| `SYN_RECEIVED` | Server received SYN, sent SYN-ACK           |
| `ESTABLISHED`  | Connection open, data transfer active       |
| `FIN_WAIT_1`   | Active closer sent FIN                      |
| `FIN_WAIT_2`   | Active closer received ACK of its FIN       |
| `CLOSE_WAIT`   | Passive closer received FIN, app must close |
| `LAST_ACK`     | Passive closer sent FIN, waiting for ACK    |
| `TIME_WAIT`    | Active closer waiting 2×MSL before `CLOSED` |
| `CLOSING`      | Both sides sent FIN simultaneously          |

```bash
ss -tan   # view TCP states on Linux
netstat -an | grep ESTABLISHED
```

**Common issues:**

- Many `TIME_WAIT` → high connection rate; tune `net.ipv4.tcp_tw_reuse`.
- Many `CLOSE_WAIT` → app not calling `close()` — typically a bug.

**Source:** <https://www.rfc-editor.org/rfc/rfc9293>

<AnkiTags TCP states TIME-WAIT CLOSE-WAIT ESTABLISHED layer4 intermediate advanced/>

# What is TCP flow control and the sliding window?

Flow control prevents the **sender from overwhelming the receiver's buffer**. The receiver advertises its available buffer space as the **receive window** (`rwnd`) in every ACK. The sender cannot have more unacknowledged data in flight than `rwnd`.

```
Sender window = min(cwnd, rwnd)   (congestion window and receive window)

Sequence: 1000 bytes in flight, rwnd = 4096
Sender can send up to 4096 unacked bytes at once.

Receiver buffer fills up → rwnd → 0 → sender stops (Zero Window)
Receiver drains buffer → sends Window Update → sender resumes
```

**Sliding window:** As ACKs arrive, the window "slides" forward — the sender can send new data equal to the number of bytes newly acknowledged.

A small `rwnd` (e.g. default 64 KB) can bottleneck high-latency links. **TCP window scaling** (RFC 7323) allows windows up to 1 GB.

**Source:** <https://www.rfc-editor.org/rfc/rfc9293>
<https://www.rfc-editor.org/rfc/rfc7323>

<AnkiTags TCP flow-control sliding-window rwnd layer4 intermediate advanced/>

# What is TCP congestion control?

TCP congestion control prevents the **sender from overwhelming the network** (as opposed to flow control, which protects the receiver). The sender maintains a **congestion window (cwnd)** in bytes.

**Four phases:**

1. **Slow Start:** cwnd begins at ~1–10 MSS (Max Segment Size) and **doubles per RTT** (exponential growth) until it hits `ssthresh` or detects loss.

2. **Congestion Avoidance:** Above `ssthresh`, cwnd grows by ~1 MSS per RTT (linear growth).

3. **Fast Retransmit:** On 3 duplicate ACKs (a segment is likely lost), retransmit without waiting for a timeout. `ssthresh = cwnd / 2`.

4. **Fast Recovery:** After fast retransmit, continue in congestion avoidance (don't reset to slow start). On timeout, reset `cwnd = 1` and restart slow start.

**Algorithms:** TCP Reno (classic), TCP Cubic (Linux default), TCP BBR (Google, bandwidth-delay product based).

```bash
sysctl net.ipv4.tcp_congestion_control   # e.g. "cubic" or "bbr"
```

**Source:** <https://www.rfc-editor.org/rfc/rfc5681>

<AnkiTags TCP congestion-control slow-start cwnd ssthresh layer4 advanced/>

# What is Nagle's algorithm and when should you disable it?

Nagle's algorithm (RFC 896) reduces the number of small TCP packets by **buffering small writes until either the previous send is ACKed or enough data accumulates to fill a full-sized segment**.

```
Without Nagle: 10 × 40-byte writes → 10 separate packets (lots of overhead)
With Nagle:    10 × 40-byte writes → fewer, fuller packets (less overhead)
```

**When to disable (`TCP_NODELAY`):**

- **Interactive applications** (SSH, telnet, database clients): Nagle adds up to 200ms latency for small sends because the kernel waits for an ACK before sending more data.
- **Request-response protocols** where client and server alternate: the server may wait for data that's been held by Nagle, causing head-of-line blocking.
- Most modern HTTP/gRPC clients set `TCP_NODELAY` by default.

```python
import socket
sock = socket.socket()
sock.setsockopt(socket.IPPROTO_TCP, socket.TCP_NODELAY, 1)
```

**Source:** <https://www.rfc-editor.org/rfc/rfc896>

<AnkiTags TCP Nagle TCP-NODELAY socket-options performance intermediate advanced/>

# What is the TIME_WAIT state and why does it matter for backend services?

`TIME_WAIT` is the state entered by the **active closer** (the side that sends the first FIN) after the connection is completely shut down. It lasts **2 × MSL (Maximum Segment Lifetime)**, typically 60–120 seconds.

**Why it exists:**

1. Ensures the final ACK reaches the passive closer (if it's lost, the passive closer re-sends the FIN and the active closer can re-ACK).
2. Prevents **stale packets** from a previous connection being misinterpreted by a new connection on the same 4-tuple (src IP, src port, dst IP, dst port).

**Why it matters for high-throughput services:**

- A server making many short-lived outbound connections (to databases, APIs) can exhaust the ~28 000 ephemeral ports because ports in `TIME_WAIT` cannot be reused immediately.
- **Mitigations, roughly in the order an interviewer wants to hear them:**
  - Connection pooling to avoid frequent teardown in the first place — fixes the root cause rather than the symptom.
  - Increase ephemeral port range: `net.ipv4.ip_local_port_range = 1024 65535`.
  - `net.ipv4.tcp_tw_reuse = 1` — reuse `TIME_WAIT` sockets for new outbound connections (safe with timestamps). This is kernel-level tuning; measure actual `TIME_WAIT` exhaustion (e.g. via `ss -tan | grep TIME-WAIT | wc -l`) before reaching for it — modern kernels already handle `TIME_WAIT` reasonably well, and reducing connection churn is usually the higher-leverage fix.

**Source:** <https://www.rfc-editor.org/rfc/rfc9293>

<AnkiTags TCP TIME-WAIT socket-options performance intermediate advanced/>

# What is UDP and how does it differ from TCP?

UDP (User Datagram Protocol) is a **connectionless, unreliable, unordered** Layer 4 protocol. It sends datagrams with no handshake, no acknowledgement, and no guarantee of delivery or ordering.

| Feature            | TCP                        | UDP                                |
| ------------------ | -------------------------- | ---------------------------------- |
| Connection         | Required (3-way handshake) | None                               |
| Reliability        | Yes (retransmission)       | No                                 |
| Ordering           | Yes (sequence numbers)     | No                                 |
| Flow control       | Yes (rwnd)                 | No                                 |
| Congestion control | Yes (cwnd)                 | No                                 |
| Header size        | 20 bytes minimum           | **8 bytes**                        |
| Latency            | Higher (RTT for handshake) | Lower                              |
| Use cases          | HTTP, DB, SSH, email       | DNS, VoIP, gaming, QUIC, streaming |

UDP header: src port (2B), dst port (2B), length (2B), checksum (2B), data.

**Source:** <https://www.rfc-editor.org/rfc/rfc768>

<AnkiTags UDP TCP transport-layer layer4 junior/>

# When should you use UDP over TCP?

Use UDP when:

1. **Low latency matters more than reliability:** VoIP, video conferencing, online gaming — a retransmitted packet arrives too late to be useful; dropping is better than waiting.

2. **The application implements its own reliability:** QUIC (the transport for HTTP/3) runs over UDP and implements its own selective ACKs, encryption, and multiplexing.

3. **Simple request-response fits in one datagram:** DNS queries — a single small packet; TCP's handshake overhead is disproportionate.

4. **Multicast or broadcast:** UDP supports one-to-many delivery; TCP is point-to-point.

5. **Streaming media:** A dropped video frame is less harmful than waiting for retransmission and stalling playback.

6. **High-frequency telemetry:** IoT sensor data, metrics — occasional loss is acceptable; freshness matters.

The trade-off: you take on responsibility for ordering, reliability, and congestion control if needed.

**Source:** <https://www.rfc-editor.org/rfc/rfc768>

<AnkiTags UDP use-cases transport-layer layer4 junior intermediate/>

# What are common TCP socket options important for backend engineers?

```python
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# SO_REUSEADDR — allow reuse of a port in TIME_WAIT
# Essential for servers: lets you restart immediately without "Address already in use"
s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)

# SO_REUSEPORT — allow multiple sockets to bind the same port
# Useful for multi-process servers (each worker has its own socket)
s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEPORT, 1)

# TCP_NODELAY — disable Nagle's algorithm (low-latency applications)
s.setsockopt(socket.IPPROTO_TCP, socket.TCP_NODELAY, 1)

# SO_KEEPALIVE — enable TCP keep-alive probes
s.setsockopt(socket.SOL_SOCKET, socket.SO_KEEPALIVE, 1)

# TCP_KEEPIDLE  — idle time before first probe (seconds)
# TCP_KEEPINTVL — interval between probes
# TCP_KEEPCNT   — number of probes before dropping connection
s.setsockopt(socket.IPPROTO_TCP, socket.TCP_KEEPIDLE, 60)
s.setsockopt(socket.IPPROTO_TCP, socket.TCP_KEEPINTVL, 10)
s.setsockopt(socket.IPPROTO_TCP, socket.TCP_KEEPCNT, 5)

# SO_LINGER — control behaviour on close() when data is unsent
# l_onoff=1, l_linger=0 → RST instead of FIN (immediate close)
import struct
s.setsockopt(socket.SOL_SOCKET, socket.SO_LINGER, struct.pack('ii', 1, 0))
```

**Source:** <https://man7.org/linux/man-pages/man7/tcp.7.html>
<https://man7.org/linux/man-pages/man7/socket.7.html>

<AnkiTags TCP socket-options SO-REUSEADDR TCP-NODELAY SO-KEEPALIVE intermediate advanced/>

# What is the TCP listen backlog?

The **listen backlog** is the maximum number of pending connections that the OS will queue for a server socket that has not yet been `accept()`-ed by the application.

There are actually two queues in Linux:

- **SYN queue (incomplete):** Connections that have completed only the first step (SYN received, SYN-ACK sent). Size: `net.ipv4.tcp_max_syn_backlog`.
- **Accept queue (complete):** Connections that have completed the full 3-way handshake, waiting for `accept()`. Size: the `backlog` argument to `listen()`, capped at `net.core.somaxconn`.

```python
server = socket.socket()
server.bind(('0.0.0.0', 8080))
server.listen(128)   # backlog = 128
```

**Under high load:**

- If the accept queue is full, the OS drops the final ACK from the client (or sends RST depending on configuration).
- If the application is slow to `accept()`, new connections are refused.

Tune `net.core.somaxconn` and `net.ipv4.tcp_max_syn_backlog` for high-throughput servers.

**Source:** <https://man7.org/linux/man-pages/man2/listen.2.html>

<AnkiTags TCP listen-backlog socket accept-queue SYN-queue intermediate advanced/>

# What is connection pooling and why is it important?

Connection pooling maintains a **cache of established TCP connections** that can be reused across multiple requests, avoiding the overhead of the 3-way handshake, TLS handshake, and OS resource allocation for each request.

**Without pooling (new connection per request):**

```
Each request: SYN → SYN-ACK → ACK → TLS → Query → FIN (1–2 RTTs minimum overhead)
1000 req/s × 2ms handshake = 2s of overhead per second
```

**With pooling:**

```
Pool opens N connections at startup or on demand
Requests borrow a connection, use it, return it
No handshake overhead per request
```

**Critical parameters for a connection pool:**

- `min_connections` — pre-warm connections at startup
- `max_connections` — prevent overwhelming the server
- `idle_timeout` — close unused connections (avoid stale/broken connections)
- `max_lifetime` — rotate connections to avoid server-side state issues
- `checkout_timeout` — fail fast when the pool is exhausted

**Source:** <https://www.cloudflare.com/learning/performance/connection-pool/>
<https://man7.org/linux/man-pages/man7/tcp.7.html>

<AnkiTags connection-pooling TCP performance backend intermediate advanced/>

# What is the difference between TCP keep-alive and application-level heartbeats?

**TCP keep-alive** (`SO_KEEPALIVE`) is a kernel-level mechanism that sends empty **ACK probes** on an idle connection after a configurable idle period. It detects dead peers (crashed hosts, NAT timeout-dropped connections) and closes them.

```bash
# Typical defaults (Linux)
net.ipv4.tcp_keepalive_time    = 7200   # 2 hours idle before first probe
net.ipv4.tcp_keepalive_intvl   = 75     # 75 seconds between probes
net.ipv4.tcp_keepalive_probes  = 9      # 9 probes before declaring dead
```

Default `tcp_keepalive_time` of 2 hours is too long for most backend services — connections can silently die (e.g., due to NAT timeout at ~5 minutes in cloud environments) without TCP detecting it.

**Application-level heartbeats** (e.g., HTTP/2 PING frames, WebSocket ping/pong, gRPC keepalive) operate at the protocol layer and are:

- Faster to detect failures (configurable at seconds, not hours).
- Aware of protocol state (they also keep NAT/firewall entries alive).
- Necessary when the connection passes through middleboxes that don't respect TCP keep-alive.

**Best practice:** Use both — TCP keep-alive as a last resort, application heartbeats for fast failure detection.

**Source:** <https://man7.org/linux/man-pages/man7/tcp.7.html>
<https://www.rfc-editor.org/rfc/rfc9293>

<AnkiTags TCP keepalive heartbeat connection-management intermediate advanced/>

# How does DNS resolution work end-to-end?

When a client resolves `www.example.com` for the first time:

```
1. Browser cache check (TTL-based)
2. OS stub resolver checks /etc/hosts, then queries...
3. Recursive resolver (e.g. 8.8.8.8 — "knows everything by asking")
   ├─► Root nameserver (.) — "I don't know, ask .com TLD"
   ├─► .com TLD nameserver — "I don't know, ask example.com's NS"
   └─► Authoritative nameserver for example.com — "www → 93.184.216.34"
4. Recursive resolver caches the answer (up to TTL seconds)
5. OS caches the answer
6. Browser uses the IP
```

**Key terms:**

- **Stub resolver:** The client-side library (`/etc/resolv.conf` points to it).
- **Recursive resolver (full-service resolver):** Does the full lookup on behalf of the client.
- **Authoritative nameserver:** The definitive source for a zone's records.
- **Root nameservers:** 13 logical root servers (A–M), operated by ICANN/IANA.

**Source:** <https://www.rfc-editor.org/rfc/rfc1035>
<https://www.cloudflare.com/learning/dns/what-is-dns/>

<AnkiTags DNS resolution recursive authoritative junior intermediate/>

# What are the main DNS record types?

| Type    | Full name           | Purpose                                                   | Example                                  |
| ------- | ------------------- | --------------------------------------------------------- | ---------------------------------------- |
| `A`     | Address             | IPv4 address for a hostname                               | `api.example.com → 1.2.3.4`              |
| `AAAA`  | IPv6 Address        | IPv6 address for a hostname                               | `api.example.com → 2001:db8::1`          |
| `CNAME` | Canonical Name      | Alias to another hostname                                 | `www → example.com`                      |
| `MX`    | Mail Exchanger      | Mail server for a domain (has priority)                   | `example.com → mail.example.com (10)`    |
| `NS`    | Name Server         | Authoritative nameservers for a zone                      | `example.com → ns1.example.com`          |
| `TXT`   | Text                | Arbitrary text; used for SPF, DKIM, domain verification   | `"v=spf1 include:…"`                     |
| `PTR`   | Pointer             | Reverse DNS (IP → hostname)                               | `4.3.2.1.in-addr.arpa → api.example.com` |
| `SOA`   | Start of Authority  | Zone metadata: primary NS, serial, refresh, retry, expire | One per zone                             |
| `SRV`   | Service             | Service location (host, port, priority, weight)           | `_http._tcp.example.com`                 |
| `CAA`   | Cert Authority Auth | Which CAs may issue TLS certs for a domain                | `"0 issue letsencrypt.org"`              |

A `CNAME` cannot be used at a zone apex (`example.com` itself) — only on subdomains. Many DNS providers support an `ALIAS`/`ANAME` record as a workaround.

**Source:** <https://www.rfc-editor.org/rfc/rfc1035>
<https://www.rfc-editor.org/rfc/rfc2782>

<AnkiTags DNS records A CNAME MX TXT PTR NS junior intermediate/>

# What is DNS TTL and how does caching work in the DNS hierarchy?

**TTL (Time to Live)** on a DNS record tells resolvers and clients how long (in seconds) they may cache the answer before querying again.

```
api.example.com.  300  IN  A  1.2.3.4
                  ^^^
                  TTL = 300 seconds (5 minutes)
```

**Caching layers:**

1. **Browser DNS cache** — fast, per-process. Chrome's TTL cap: 1 minute.
2. **OS DNS cache** (nscd, systemd-resolved) — shared by all processes.
3. **Recursive resolver cache** (ISP's or 8.8.8.8) — shared by many clients.

**Implications for backend engineers:**

- **Before changing IPs:** Lower TTL to 60s a day in advance, make the change, then raise TTL back.
- **Negative caching (RFC 2308):** `NXDOMAIN` responses are also cached (for the SOA's minimum TTL, typically 5 minutes). Failed lookups for mistyped names persist.
- **TTL ≠ reliability:** Even if TTL = 0, some resolvers will cache briefly.

**Source:** <https://www.rfc-editor.org/rfc/rfc1035>
<https://www.rfc-editor.org/rfc/rfc2308>

<AnkiTags DNS TTL caching negative-caching intermediate/>

# What is the difference between a recursive resolver and an authoritative nameserver?

**Recursive resolver (full-service resolver):**

- Acts on behalf of the client.
- Walks the DNS hierarchy (root → TLD → authoritative) to find the answer.
- Caches responses.
- Examples: 8.8.8.8 (Google), 1.1.1.1 (Cloudflare), your ISP's resolver.
- Does not hold the definitive records — it relays and caches.

**Authoritative nameserver:**

- Holds the definitive DNS records for a zone.
- Answers queries about its zone without recursing.
- Does not cache — it is the source of truth.
- Examples: NS records for `example.com` point to its authoritative servers.

**Forwarding resolver:** A hybrid — forwards queries to another resolver rather than recursing itself. Used in corporate environments to route queries through a specific DNS server.

```
Client → Recursive Resolver → (cache miss) → Root → TLD → Authoritative → Answer
Client → Recursive Resolver → (cache hit)  → Answer immediately
```

**Source:** <https://www.rfc-editor.org/rfc/rfc1035>
<https://www.cloudflare.com/learning/dns/dns-server-types/>

<AnkiTags DNS recursive authoritative resolver intermediate/>

# What is split-horizon (split-brain) DNS?

Split-horizon DNS returns **different answers to the same DNS query** depending on the source of the query — typically different answers for internal vs. external clients.

```
Query: api.example.com

External client (Internet) → gets: 203.0.113.10  (public IP / load balancer)
Internal client (VPN/office) → gets: 10.0.1.50   (private IP / internal endpoint)
```

**Why use it:**

- Avoid internal traffic leaving the network and re-entering via a public load balancer.
- Expose different services internally vs. externally.
- Security: internal services not visible/reachable from the Internet.

**Implementation:** Run separate authoritative zones (same domain, different records) for internal and external resolvers, or configure resolvers with ACL-based view policies (e.g., BIND's `view` directive).

**Source:** <https://www.cloudflare.com/learning/dns/glossary/split-horizon-dns/>

<AnkiTags DNS split-horizon split-brain internal external intermediate advanced/>

# What is DNSSEC?

DNSSEC (DNS Security Extensions) adds **cryptographic signatures** to DNS records, allowing resolvers to verify that the answer was not tampered with in transit. It addresses DNS cache poisoning attacks (e.g., Kaminsky attack).

**How it works:**

- Zone owners sign their records with a private key.
- Public keys are published as `DNSKEY` records.
- Each record set has a corresponding `RRSIG` (Resource Record Signature).
- A chain of trust is established from the root (`.`) down through TLDs to the zone.

**What DNSSEC does NOT do:**

- Does not encrypt DNS queries (use DNS-over-HTTPS or DNS-over-TLS for privacy).
- Does not prevent DDoS against nameservers.
- Does not protect against a compromised authoritative server.

**Deployment complexity:** Key rotation, signing pipeline, and resolver support make DNSSEC operationally challenging. Many large sites (e.g., major cloud providers) sign their zones.

**Source:** <https://www.rfc-editor.org/rfc/rfc4033>

<AnkiTags DNS DNSSEC security cryptography intermediate advanced/>

# What is DNS over HTTPS (DoH) and DNS over TLS (DoT)?

Both encrypt DNS queries to prevent eavesdropping and tampering by network intermediaries (ISP, coffee shop Wi-Fi, etc.).

**DNS over TLS (DoT) — RFC 7858:**

- DNS transported over a dedicated TLS connection on port **853**.
- Easy to identify and block (dedicated port).
- Supported by `systemd-resolved`, Android Private DNS.

**DNS over HTTPS (DoH) — RFC 8484:**

- DNS queries encoded as HTTPS requests on port **443**.
- Indistinguishable from regular HTTPS traffic — hard to block without blocking all HTTPS.
- Supported by major browsers (Firefox, Chrome) — can bypass system resolver entirely.

```
Traditional: Client → Resolver (UDP port 53, plaintext) → answer visible to ISP
DoH:         Client → Resolver (HTTPS port 443, encrypted)
```

**Implications for backend engineers:** Browsers using DoH may bypass corporate DNS servers and split-horizon configurations. Awareness is important for network security and debugging.

**Source:** <https://www.rfc-editor.org/rfc/rfc7858>
<https://www.rfc-editor.org/rfc/rfc8484>

<AnkiTags DNS DoH DoT security privacy intermediate advanced/>

# What are the HTTP methods and their semantics?

| Method    | Safe? | Idempotent? | Purpose                                            |
| --------- | ----- | ----------- | -------------------------------------------------- |
| `GET`     | ✅    | ✅          | Retrieve a resource                                |
| `HEAD`    | ✅    | ✅          | Like GET but no body — check existence/headers     |
| `OPTIONS` | ✅    | ✅          | Describe communication options (CORS preflight)    |
| `PUT`     | ❌    | ✅          | Replace a resource entirely                        |
| `DELETE`  | ❌    | ✅          | Remove a resource                                  |
| `POST`    | ❌    | ❌          | Create a resource or trigger an action             |
| `PATCH`   | ❌    | ❌\*        | Partial update (\*idempotent if designed that way) |
| `CONNECT` | ❌    | ❌          | Establish a tunnel (HTTPS through proxy)           |
| `TRACE`   | ✅    | ✅          | Echo request — diagnostic                          |

**Safe:** The method has no observable side effects on the server.
**Idempotent:** Repeating the request N times has the same effect as one request.

**Source:** <https://www.rfc-editor.org/rfc/rfc9110>

<AnkiTags HTTP methods idempotent safe REST junior/>

# What does idempotency mean in HTTP and why does it matter?

An HTTP method is **idempotent** if making the same request multiple times produces the same server-side state as making it once. Repeating the request is safe.

```
GET  /users/1         → always returns same user (safe + idempotent)
PUT  /users/1 {name}  → sets name to value each time (idempotent)
DELETE /users/1       → first deletes, subsequent calls get 404 (idempotent)
POST /orders          → creates a new order each time (NOT idempotent)
```

**Why it matters for backends:**

- **Retries:** Network failures or timeouts may cause clients to retry. Idempotent operations can be retried safely. Non-idempotent operations (POST) risk duplicates.
- **Idempotency keys:** `POST` can be made effectively idempotent using an `Idempotency-Key` header — the server stores the result keyed by the client-generated UUID and de-duplicates repeated requests (used by Stripe, Twilio, etc.).
- **Exactly-once delivery** in distributed systems leverages idempotency.

**Source:** <https://www.rfc-editor.org/rfc/rfc9110>

<AnkiTags HTTP idempotency REST retry intermediate/>

# What do HTTP status code ranges mean? Give key examples

| Range | Meaning       | Key codes                                                                                                                                                                          |
| ----- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `1xx` | Informational | `100 Continue`, `101 Switching Protocols`                                                                                                                                          |
| `2xx` | Success       | `200 OK`, `201 Created`, `204 No Content`, `206 Partial Content`                                                                                                                   |
| `3xx` | Redirection   | `301 Moved Permanently`, `302 Found`, `304 Not Modified`, `307 Temp Redirect`, `308 Perm Redirect`                                                                                 |
| `4xx` | Client error  | `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`, `405 Method Not Allowed`, `409 Conflict`, `410 Gone`, `422 Unprocessable Entity`, `429 Too Many Requests` |
| `5xx` | Server error  | `500 Internal Server Error`, `502 Bad Gateway`, `503 Service Unavailable`, `504 Gateway Timeout`                                                                                   |

**Key distinctions:**

- `301` vs `308`: Both permanent; `301` may change method to `GET`, `308` preserves the method.
- `401` vs `403`: `401` = not authenticated, `403` = authenticated but not authorised.
- `502` vs `504`: `502` = upstream returned an invalid response, `504` = upstream timed out.

**Source:** <https://www.rfc-editor.org/rfc/rfc9110>

<AnkiTags HTTP status-codes 2xx 3xx 4xx 5xx junior intermediate/>

# What are the most important HTTP request headers for backend engineers?

| Header                              | Purpose                                                                   |
| ----------------------------------- | ------------------------------------------------------------------------- |
| `Host`                              | Target hostname (required in HTTP/1.1; enables virtual hosting)           |
| `Authorization`                     | Credentials: `Bearer <token>`, `Basic <base64>`                           |
| `Content-Type`                      | Media type of the request body: `application/json`, `multipart/form-data` |
| `Content-Length`                    | Body size in bytes                                                        |
| `Accept`                            | Acceptable response types: `application/json`                             |
| `Accept-Encoding`                   | Acceptable compression: `gzip, br, deflate`                               |
| `User-Agent`                        | Client identification string                                              |
| `Cookie`                            | Session cookies sent to server                                            |
| `X-Request-ID` / `X-Correlation-ID` | Distributed tracing identifier                                            |
| `X-Forwarded-For`                   | Original client IP (set by proxy/load balancer)                           |
| `X-Forwarded-Proto`                 | Original protocol (http/https) before TLS termination                     |
| `If-None-Match`                     | Conditional GET with ETag: only send body if changed                      |
| `If-Modified-Since`                 | Conditional GET with date                                                 |
| `Range`                             | Partial content request: `bytes=0-1023`                                   |

**Source:** <https://www.rfc-editor.org/rfc/rfc9110>
<https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers>

<AnkiTags HTTP headers request Authorization Content-Type junior intermediate/>

# What are the most important HTTP response headers for backend engineers?

| Header                            | Purpose                                                   |
| --------------------------------- | --------------------------------------------------------- |
| `Content-Type`                    | Media type of response: `application/json; charset=utf-8` |
| `Content-Length`                  | Body size in bytes                                        |
| `Content-Encoding`                | Applied compression: `gzip`, `br`                         |
| `Cache-Control`                   | Caching instructions: `max-age=3600, public`              |
| `ETag`                            | Entity tag for conditional requests                       |
| `Last-Modified`                   | Timestamp of last change                                  |
| `Location`                        | URL for redirect (3xx) or newly created resource (201)    |
| `Set-Cookie`                      | Set a cookie with attributes                              |
| `WWW-Authenticate`                | Challenge for `401` responses                             |
| `Retry-After`                     | When to retry after `429` or `503`                        |
| `Strict-Transport-Security`       | HSTS: force HTTPS for future requests                     |
| `X-Content-Type-Options: nosniff` | Prevent MIME type sniffing                                |
| `X-Frame-Options`                 | Clickjacking protection: `DENY`, `SAMEORIGIN`             |
| `Access-Control-Allow-Origin`     | CORS response header                                      |
| `Vary`                            | Which request headers affect caching                      |

**Source:** <https://www.rfc-editor.org/rfc/rfc9110>
<https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers>

<AnkiTags HTTP headers response Cache-Control ETag Set-Cookie junior intermediate/>

# What is HTTP persistent connection (keep-alive)?

HTTP/1.0 opened a new TCP connection for every request. HTTP/1.1 introduced **persistent connections** — the TCP connection is kept open after a response and reused for subsequent requests.

```
HTTP/1.0 (per-request connection):
TCP connect → GET / → 200 → TCP close
TCP connect → GET /style.css → 200 → TCP close

HTTP/1.1 (keep-alive):
TCP connect → GET / → 200 → GET /style.css → 200 → ... → TCP close (after timeout)
```

```
# Request header to close connection after this response:
Connection: close

# Default in HTTP/1.1 (persistent):
Connection: keep-alive

# Server-side keep-alive timeout (e.g. nginx default: 75s)
keepalive_timeout 75;
```

**Limits:** A single HTTP/1.1 connection is still sequential — the next request must wait for the previous response (no multiplexing). Browsers open 6 connections per origin to work around this. HTTP/2 solves this with multiplexing.

**Source:** <https://www.rfc-editor.org/rfc/rfc9112>
<https://developer.mozilla.org/en-US/docs/Web/HTTP/Connection_management_in_HTTP_1.x>

<AnkiTags HTTP keep-alive persistent-connections HTTP1-1 performance junior intermediate/>

# What is chunked transfer encoding?

Chunked transfer encoding (HTTP/1.1) allows a server to send a response in **pieces without knowing the total content length in advance**. Each chunk is preceded by its size in hexadecimal.

```http
HTTP/1.1 200 OK
Transfer-Encoding: chunked
Content-Type: text/plain

7\r\n
Hello, \r\n
6\r\n
World!\r\n
0\r\n
\r\n
```

**Use cases:**

- Streaming large responses (logs, exports, server-sent data) where the total size is unknown.
- Dynamic content generated as it's produced.
- In HTTP/2 and HTTP/3, chunked encoding is not used — framing handles this at the transport layer.

When `Transfer-Encoding: chunked` is present, `Content-Length` must not be set.

**Source:** <https://www.rfc-editor.org/rfc/rfc9112>

<AnkiTags HTTP chunked transfer-encoding streaming intermediate/>

# What were the problems with HTTP/1.1 pipelining?

HTTP/1.1 pipelining allowed sending **multiple requests without waiting for each response**, but the responses had to be returned in **strict order** — causing **head-of-line (HOL) blocking**.

```
Without pipelining:    Request 1 → Response 1 → Request 2 → Response 2
With pipelining:       Request 1, Request 2, Request 3 sent together
                       But: server must reply in order: R1, R2, R3
If R1 is slow:         R2 and R3 wait, even if they're ready
```

Practical problems:

- **HOL blocking:** A slow response blocks all subsequent responses on that connection.
- **Proxy support:** Many HTTP proxies didn't implement pipelining correctly.
- As a result, **no major browser enabled pipelining by default**.

HTTP/2 solved this with **multiplexed streams** on a single connection, where each stream is independent.

**Source:** <https://www.rfc-editor.org/rfc/rfc9112>
<https://developer.mozilla.org/en-US/docs/Web/HTTP/Connection_management_in_HTTP_1.x>

<AnkiTags HTTP pipelining HOL-blocking HTTP1-1 intermediate/>

# What are the key improvements in HTTP/2 over HTTP/1.1?

| Feature                | HTTP/1.1                        | HTTP/2                                                       |
| ---------------------- | ------------------------------- | ------------------------------------------------------------ |
| **Framing**            | Text-based                      | **Binary frames**                                            |
| **Multiplexing**       | Sequential (1 req/connection)   | **Multiple concurrent streams on one connection**            |
| **Header compression** | None (repeated headers in full) | **HPACK** (header compression, ~30% savings)                 |
| **Server push**        | Not supported                   | **Server can push resources proactively**                    |
| **Connection**         | 6+ connections per origin       | **1 connection per origin**                                  |
| **HOL blocking**       | At application layer            | Eliminated at application layer                              |
| **TLS**                | Optional                        | Effectively required (browsers only support HTTP/2 over TLS) |
| **Priority**           | None                            | Streams have priority weights                                |

HTTP/2 still suffers from **TCP HOL blocking** — a single lost packet stalls all streams sharing the TCP connection. HTTP/3 (QUIC) fixes this.

**Source:** <https://www.rfc-editor.org/rfc/rfc7540>

<AnkiTags HTTP HTTP2 binary-framing multiplexing HPACK intermediate/>

# What is HTTP/2 multiplexing and how does it work?

HTTP/2 allows multiple **logical streams** to be interleaved on a single TCP connection simultaneously. Each stream is identified by a stream ID (odd for client-initiated, even for server-initiated).

```
Single TCP connection:
┌──────────────────────────────────────────────────────────┐
│ Stream 1: [HEADERS frame] [DATA frame 1] [DATA frame 2]  │
│ Stream 3: [HEADERS frame] [DATA frame 1]                 │
│ Stream 5: [HEADERS frame] [DATA frame 1] [DATA frame 2]  │
└──────────────────────────────────────────────────────────┘
Frames from different streams are interleaved freely.
```

**Benefits:**

- No more blocking on a slow response — streams are independent.
- One TCP connection eliminates the overhead of 6× parallel connections used in HTTP/1.1.
- Stream priority and dependency allow the client to signal which resources are more urgent (e.g., CSS before images).

**Remaining limitation:** TCP packet loss causes all streams to stall — QUIC (HTTP/3) gives each stream independent loss recovery.

**Source:** <https://www.rfc-editor.org/rfc/rfc7540>

<AnkiTags HTTP HTTP2 multiplexing streams intermediate advanced/>

# What is HPACK header compression in HTTP/2?

HPACK compresses HTTP/2 headers to reduce the overhead of repeating identical headers (like `Authorization`, `User-Agent`, `Accept`) on every request.

**Two mechanisms:**

1. **Static table:** 61 pre-defined commonly used headers (e.g., `:method: GET`, `:status: 200`, `content-type: text/html`). A header matching an entry is sent as a single index byte.

2. **Dynamic table:** Both sides maintain a table of previously seen header name/value pairs. Headers seen before are referenced by index rather than sent in full.

**Huffman encoding:** Individual header string values are also Huffman-coded.

```
First request:  Authorization: Bearer eyJ... (sent in full, added to dynamic table)
Second request: [index 62]                   (one or two bytes instead of 200+ bytes)
```

HPACK requires **ordered delivery** — dynamic table updates must arrive in order. HTTP/3 uses **QPACK** instead, which works without strict ordering.

**Source:** <https://www.rfc-editor.org/rfc/rfc7541>

<AnkiTags HTTP HTTP2 HPACK header-compression intermediate advanced/>

# What is HTTP/2 Server Push?

HTTP/2 Server Push allows a server to **proactively send resources to the client before the client requests them**, anticipating what the client will need next.

```
Client: GET /index.html
Server: 200 /index.html (HTML body)
Server: PUSH_PROMISE /style.css   ← pushes before client asks
Server: PUSH_PROMISE /app.js      ← pushes before client asks
```

**In practice:** Server Push had poor real-world adoption because:

- The server can't know whether the client already has the resource cached.
- Pushed resources consumed bandwidth even when cached.
- Browsers often deprioritised or cancelled pushes.

HTTP/3 (RFC 9114) retains the mechanism but it is rarely used. The `103 Early Hints` status code is a more effective modern alternative — it hints at resources before the final response, letting the browser start fetching while the server prepares the response.

**Source:** <https://www.rfc-editor.org/rfc/rfc7540>
<https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/103>

<AnkiTags HTTP HTTP2 server-push performance intermediate advanced/>

# What is QUIC and why was it created?

QUIC is a **general-purpose transport protocol** built on top of UDP, originally developed by Google and standardised by the IETF (RFC 9000). It is the transport layer for HTTP/3.

**Problems QUIC solves:**

1. **TCP HOL blocking:** In HTTP/2, a single lost TCP packet stalls all streams. QUIC gives each stream **independent loss recovery** — a lost packet only blocks the stream it belongs to.

2. **Connection establishment latency:** TCP + TLS 1.2 requires 2–3 RTTs before data. QUIC combines the transport and TLS 1.3 handshake into **1 RTT** (and **0-RTT** for resumed connections).

3. **Connection migration:** TCP connections are identified by 4-tuple (src IP, src port, dst IP, dst port). QUIC uses **connection IDs** — connections survive IP changes (e.g. switching from Wi-Fi to cellular without reconnecting).

4. **Middlebox ossification:** TCP is hard to evolve because middleboxes inspect it. QUIC is encrypted end-to-end — only endpoints see the transport state.

**Source:** <https://www.rfc-editor.org/rfc/rfc9000>
<https://www.rfc-editor.org/rfc/rfc9114>

<AnkiTags QUIC HTTP3 UDP transport HOL-blocking intermediate advanced/>

# What is 0-RTT connection establishment in QUIC/TLS 1.3?

**0-RTT (Zero Round Trip Time) resumption** allows a client that has previously connected to a server to send **application data on the very first packet** of a new connection, before the handshake completes.

```
Standard TLS 1.3 (1-RTT):
Client → ClientHello (with key material)
Server → ServerHello + Certificate + Finished
Client → Finished + HTTP request    ← data sent after 1 RTT
Server → HTTP response

0-RTT resumption (TLS 1.3 / QUIC):
Client → ClientHello + Early Data (HTTP request)   ← data sent immediately!
Server → ServerHello + HTTP response
```

**How:** The server issues a **session ticket** (PSK — Pre-Shared Key) on first connection. The client uses it on reconnection without waiting for the handshake.

**Security caveat:** 0-RTT data is vulnerable to **replay attacks** — an attacker could capture and re-send the first packet. Therefore 0-RTT should only be used for **safe, idempotent requests** (e.g. `GET`).

**Source:** <https://www.rfc-editor.org/rfc/rfc8446>
<https://www.rfc-editor.org/rfc/rfc9000>

<AnkiTags QUIC TLS 0-RTT session-resumption security advanced/>

# What does TLS provide and what are its three security properties?

TLS (Transport Layer Security) runs on top of TCP and provides:

1. **Confidentiality:** Data is encrypted — an eavesdropper cannot read the content.
2. **Integrity:** A message authentication code (MAC/AEAD) detects any tampering in transit.
3. **Authentication:** The server (and optionally the client) is verified using X.509 certificates and a chain of trust to a trusted CA.

TLS does **not** provide:

- Anonymity — the server IP and domain (via SNI) are visible.
- Availability — TLS-encrypted DDoS traffic is still disruptive.
- Application-level security — SQL injection still works over HTTPS.

**Current versions:**

- **TLS 1.3 (RFC 8446):** Current standard. Removes weak algorithms, 1-RTT handshake.
- **TLS 1.2:** Widely deployed, still acceptable with correct cipher configuration.
- **TLS 1.0/1.1:** Deprecated (RFC 8996) — should be disabled.
- **SSL:** Completely broken — do not use.

**Source:** <https://www.rfc-editor.org/rfc/rfc8446>
<https://www.rfc-editor.org/rfc/rfc8996>

<AnkiTags TLS HTTPS security authentication encryption junior intermediate/>

# What happens during a TLS 1.3 handshake?

TLS 1.3 completes in **1 RTT** (down from 2 RTTs in TLS 1.2):

```
Client                                    Server
  │──── ClientHello ───────────────────────►│
  │     (supported cipher suites,            │
  │      key_share: client DH public key)    │
  │                                          │
  │◄─── ServerHello ────────────────────────│
  │     (chosen cipher suite,               │
  │      key_share: server DH public key,   │
  │      Certificate, CertificateVerify,    │
  │      Finished)                          │
  │                                         │
  │──── Finished ──────────────────────────►│
  │──── HTTP request (application data) ───►│  ← data on same flight
```

**Key improvements over TLS 1.2:**

- Handshake is encrypted earlier (no plaintext certificates).
- Only strong cipher suites (no RSA key exchange — forward secrecy required).
- DH key agreement (ECDHE) is always used → **forward secrecy** by default.
- Server's certificate is encrypted (in the second flight).

**Forward secrecy:** Each session uses ephemeral DH keys — compromising the server's private key later does not decrypt past sessions.

**Source:** <https://www.rfc-editor.org/rfc/rfc8446>

<AnkiTags TLS TLS1-3 handshake forward-secrecy HTTPS intermediate advanced/>

# What is an X.509 certificate and the certificate chain?

An **X.509 certificate** is a digitally signed document that binds a **public key** to an identity (hostname, organisation). It is issued by a Certificate Authority (CA).

**Certificate chain (chain of trust):**

```
Browser trust store
└── Root CA certificate (self-signed; pre-installed in OS/browser)
    └── Intermediate CA certificate (signed by Root CA)
        └── Server/leaf certificate (signed by Intermediate CA)
                Contains: subject (CN=api.example.com),
                          public key, validity period (NotBefore, NotAfter),
                          Subject Alternative Names (SANs),
                          issuer, signature
```

**Why intermediates?** Root CA private keys are kept offline. Intermediates are online and can be revoked without invalidating the root.

**Validation steps (browser):**

1. Verify each certificate in the chain is signed by the one above it.
2. Verify the root is in the trust store.
3. Verify the leaf's SAN matches the hostname being connected to.
4. Verify the certificate is not expired.
5. Check revocation (CRL or OCSP).

**Source:** <https://www.rfc-editor.org/rfc/rfc5280>
<https://www.cloudflare.com/learning/ssl/what-is-an-ssl-certificate/>

<AnkiTags TLS certificate X509 PKI chain-of-trust intermediate advanced/>

# What is SNI (Server Name Indication)?

SNI is a TLS extension that allows a client to specify **which hostname it wants to connect to at the start of the TLS handshake**, before the server certificate is sent.

**Problem it solves:** A server can host multiple TLS domains on the same IP address. Without SNI, the server doesn't know which certificate to present during the handshake (the `Host` header in HTTP isn't visible until after TLS is established).

```
TLS ClientHello includes:
  server_name: "api.example.com"    ← SNI extension

Server selects the correct certificate for api.example.com and presents it.
```

**Privacy implication:** SNI is sent in plaintext — an eavesdropper can see which hostname you're connecting to even though the content is encrypted. **Encrypted Client Hello (ECH)**, the successor to ESNI, aims to solve this by encrypting the SNI in the ClientHello.

**Source:** <https://www.rfc-editor.org/rfc/rfc6066>
<https://www.cloudflare.com/learning/ssl/what-is-sni/>

<AnkiTags TLS SNI virtual-hosting HTTPS intermediate advanced/>

# What is HSTS (HTTP Strict Transport Security)?

HSTS tells browsers that a website should **only ever be accessed over HTTPS**. Once received, the browser automatically upgrades HTTP requests to HTTPS and refuses to connect if the certificate is invalid — even if the user clicks through a warning.

```http
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
```

- **`max-age`:** How long (seconds) the policy is remembered. `31536000` = 1 year.
- **`includeSubDomains`:** Applies to all subdomains.
- **`preload`:** Opt-in to browser preload lists (domain hardcoded in browsers, no first HTTP request needed).

**Without HSTS:**

1. User types `example.com` → browser makes HTTP request.
2. Server redirects to HTTPS.
3. Attacker on the network can intercept step 1–2 (SSL stripping attack).

**With HSTS:** The browser skips the HTTP step entirely after the first secure visit.

**Source:** <https://www.rfc-editor.org/rfc/rfc6797>
<https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security>

<AnkiTags TLS HSTS HTTPS security intermediate/>

# What is mutual TLS (mTLS)?

In standard TLS, only the **server** presents a certificate. In **mutual TLS (mTLS)**, both the **client and server** authenticate each other with certificates.

```
Standard TLS:  Client verifies server's certificate
mTLS:          Client verifies server AND server verifies client
```

**Use cases in backend engineering:**

- **Service-to-service authentication** in microservices (a service mesh like Istio automates this).
- **Zero-trust networking** — replace VPN with per-service identity.
- **API authentication** — clients present a certificate instead of an API key or Bearer token.

```nginx
# NGINX: require client certificate
ssl_client_certificate /etc/ssl/client-ca.crt;
ssl_verify_client on;
ssl_verify_depth 2;
```

**Challenges:** Certificate distribution, rotation, and revocation for many services. Service meshes (Istio, Linkerd) automate mTLS certificate lifecycle.

**Source:** <https://www.rfc-editor.org/rfc/rfc8446>
<https://www.cloudflare.com/learning/access-management/what-is-mutual-tls/>

<AnkiTags TLS mTLS client-authentication service-mesh security advanced/>

# What is OCSP stapling?

OCSP (Online Certificate Status Protocol) checks whether a TLS certificate has been **revoked** by the CA. In the naive implementation, the browser queries the CA's OCSP responder on every TLS connection — this is slow and leaks which websites you visit.

**OCSP stapling:** The server periodically fetches a **timestamped, CA-signed OCSP response** and includes ("staples") it in the TLS handshake. The client receives the revocation status without a separate request to the CA.

```
Without stapling: Browser → TLS handshake with server
                  Browser → OCSP request to CA (extra RTT, privacy leak)

With stapling:    Server → fetches OCSP response from CA (caches for hours)
                  Browser → TLS handshake with server (OCSP response included)
```

**OCSP Must-Staple** (X.509 extension): If present, a browser must reject the certificate if no valid OCSP staple is provided — prevents downgrade attacks where an attacker blocks the OCSP request to force acceptance of a revoked cert.

**Source:** <https://www.rfc-editor.org/rfc/rfc6961>
<https://developer.mozilla.org/en-US/docs/Web/Security/Certificate_Transparency>

<AnkiTags TLS OCSP certificate-revocation security advanced/>

# What is the Cache-Control header and its key directives?

`Cache-Control` is the primary HTTP caching mechanism. It can appear in both requests and responses.

**Response directives (server → cache/browser):**

```
Cache-Control: max-age=3600           # Cache for 3600 seconds
Cache-Control: no-cache               # Must revalidate with server before using cached copy
Cache-Control: no-store               # Don't cache at all (sensitive data)
Cache-Control: public                 # Any cache (CDN, proxy) may store it
Cache-Control: private                # Only browser cache (not CDNs) — user-specific
Cache-Control: immutable              # Never revalidate (for versioned assets: /style.abc123.css)
Cache-Control: s-maxage=3600          # TTL for shared caches (CDN); overrides max-age
Cache-Control: stale-while-revalidate=60  # Serve stale for 60s while refreshing in background
Cache-Control: stale-if-error=86400   # Serve stale for 24h if origin is down
```

**Request directives (browser → cache):**

```
Cache-Control: no-cache               # Force revalidation (browser reload)
Cache-Control: max-stale=60           # Accept content up to 60s past expiry
```

**Source:** <https://www.rfc-editor.org/rfc/rfc9111>
<https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control>

<AnkiTags HTTP caching Cache-Control CDN intermediate/>

# What is the difference between ETags and Last-Modified for HTTP caching?

Both are **cache validators** — they let caches check whether a cached response is still fresh without downloading the full body.

**`Last-Modified` / `If-Modified-Since`:**

- Server responds with `Last-Modified: Wed, 15 May 2024 10:00:00 GMT`.
- On next request, client sends `If-Modified-Since: Wed, 15 May 2024 10:00:00 GMT`.
- If unchanged, server replies `304 Not Modified` (no body). Otherwise `200` with new body.
- Limitation: 1-second granularity; file system timestamps can be unreliable.

**`ETag` / `If-None-Match`:**

- Server responds with `ETag: "abc123"` (an opaque string, typically a hash of content).
- Client sends `If-None-Match: "abc123"`.
- If the ETag still matches → `304 Not Modified`. If changed → `200` with new ETag.
- More precise than `Last-Modified`; works for dynamically generated content.
- **Weak ETag** (`W/"abc123"`) — semantically equivalent content (e.g., different whitespace).
- **Strong ETag** (`"abc123"`) — byte-for-byte identical.

**Source:** <https://www.rfc-editor.org/rfc/rfc9110>
<https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching>

<AnkiTags HTTP caching ETag Last-Modified conditional-requests intermediate/>

# What is the Vary header and why does it matter for CDN caching?

`Vary` tells caches that the **response may differ based on specified request headers** — the cache must store a separate copy for each unique combination of those header values.

```http
Vary: Accept-Encoding
```

This means: "My response body depends on what encoding the client accepts. Cache separately for `gzip` vs `br` vs `identity`."

```http
Vary: Accept-Language     # Different language versions
Vary: Accept              # JSON vs XML versions
Vary: Origin              # CORS responses
Vary: Cookie              # DANGEROUS — personalised content bypasses CDN
```

**CDN implications:**

- `Vary: Accept-Encoding` is safe and common.
- `Vary: Cookie` or `Vary: Authorization` essentially disables CDN caching — every request is unique.
- Some CDNs (Fastly, Cloudflare) normalise `Accept-Encoding` to prevent unnecessary cache splits.

**Source:** <https://www.rfc-editor.org/rfc/rfc9110>
<https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Vary>

<AnkiTags HTTP caching Vary CDN headers intermediate advanced/>

# What is stale-while-revalidate and why is it useful?

`stale-while-revalidate=N` (RFC 5861) allows a cache to **serve a stale (expired) response immediately** while **asynchronously fetching a fresh copy** in the background.

```
Cache-Control: max-age=60, stale-while-revalidate=300

t=0:   Response cached (max-age=60)
t=60:  Cache expires (stale)
t=61:  Request arrives → serve stale response immediately (no wait)
       → simultaneously fetch fresh copy from origin
t=62:  Fresh response stored in cache
t=63:  Next request gets fresh response

Without stale-while-revalidate:
t=61:  Request arrives → cache revalidates (blocking) → user waits
```

**Why it matters:**

- Near-zero added latency at cache expiry.
- Protects origin from thundering-herd (many simultaneous cache misses at expiry).
- Works at CDN and browser level.
- The classic trade-off: users may see data up to `stale-while-revalidate` seconds old.

**Source:** <https://www.rfc-editor.org/rfc/rfc5861>
<https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control>

<AnkiTags HTTP caching stale-while-revalidate CDN performance intermediate advanced/>

# What is the Same-Origin Policy (SOP)?

The Same-Origin Policy is a browser security model that restricts **how documents and scripts loaded from one origin can interact with resources from a different origin**.

Two URLs have the **same origin** only if they share all three: **scheme + hostname + port**.

```
https://api.example.com:443   ← origin

https://api.example.com       → same  (port 443 is default for https)
https://api.example.com:8080  → different (different port)
http://api.example.com        → different (different scheme)
https://www.example.com       → different (different host)
https://other.com             → different (different host)
```

SOP prevents a malicious page at `evil.com` from reading the cookies or response body from `bank.com`. JavaScript can _send_ cross-origin requests (with CORS) but cannot _read_ the response without explicit permission.

**Source:** <https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy>

<AnkiTags CORS SOP security browser junior intermediate/>

# What is CORS and how does a preflight request work?

CORS (Cross-Origin Resource Sharing) is the mechanism by which a server tells browsers that **cross-origin requests from specific origins are allowed**.

**Simple request** (no preflight): `GET`/`POST`/`HEAD` with safe headers — the browser sends the request directly with an `Origin` header. The server responds with `Access-Control-Allow-Origin`.

**Preflight request:** Before sending a "non-simple" request (any method other than GET/HEAD/POST, or custom headers like `Authorization`), the browser sends an `OPTIONS` request to ask for permission:

```
Browser → OPTIONS /api/data
          Origin: https://frontend.example.com
          Access-Control-Request-Method: DELETE
          Access-Control-Request-Headers: Authorization

Server  ← 200 OK
          Access-Control-Allow-Origin: https://frontend.example.com
          Access-Control-Allow-Methods: GET, POST, DELETE
          Access-Control-Allow-Headers: Authorization
          Access-Control-Max-Age: 86400     ← cache this preflight for 24h

Browser → DELETE /api/data (actual request, now permitted)
```

**Key headers:** `Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, `Access-Control-Allow-Headers`, `Access-Control-Allow-Credentials`, `Access-Control-Expose-Headers`.

**Source:** <https://www.rfc-editor.org/rfc/rfc6454>
<https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS>

<AnkiTags CORS preflight SOP security headers junior intermediate/>

# What is the WebSocket protocol?

WebSocket (RFC 6455) provides **full-duplex communication** over a single TCP connection. Either side can send messages at any time without the request/response pattern of HTTP.

**Upgrade handshake:**

```http
Client → GET /chat HTTP/1.1
         Connection: Upgrade
         Upgrade: websocket
         Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
         Sec-WebSocket-Version: 13

Server ← HTTP/1.1 101 Switching Protocols
         Connection: Upgrade
         Upgrade: websocket
         Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
```

After the `101` response, the connection is no longer HTTP — both sides send **WebSocket frames** directly.

**Message framing:** Frames have a 2–10 byte header with opcode (text/binary/ping/pong/close), masking flag, and payload length. Client frames are always masked.

**Source:** <https://www.rfc-editor.org/rfc/rfc6455>

<AnkiTags WebSocket real-time full-duplex protocol intermediate/>

# What is Server-Sent Events (SSE)?

Server-Sent Events is an HTTP-based protocol for **one-way push from server to browser**. The browser makes a single HTTP GET request and the server streams events indefinitely.

```http
GET /events HTTP/1.1
Accept: text/event-stream

HTTP/1.1 200 OK
Content-Type: text/event-stream
Cache-Control: no-cache

data: {"temp": 23.5}\n\n
data: {"temp": 23.8}\n\n
event: alert\ndata: {"msg": "High temp!"}\n\n
```

**SSE features:**

- Automatic reconnection with `Last-Event-ID` header.
- Named events (`event:` field).
- Works over HTTP/2 (multiple SSE streams can share one connection).
- Built-in browser `EventSource` API.

**SSE vs WebSocket:**

- SSE: server → client only, HTTP/2 compatible, simpler, automatic reconnect.
- WebSocket: bidirectional, requires protocol upgrade, more complex.

**Source:** <https://html.spec.whatwg.org/multipage/server-sent-events.html>
<https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events>

<AnkiTags SSE server-sent-events real-time push HTTP intermediate/>

# What is long-polling and how does it compare to WebSockets and SSE?

**Long-polling** simulates server push over plain HTTP/1.1:

```
Client → GET /events (request held open by server)
Server → (waits until there's data, then responds)
         200 OK { "event": "..." }
Client → immediately opens another GET /events
         (repeat)
```

**Comparison:**

| Technique    | Direction       | Protocol   | Latency         | Complexity | Overhead                  |
| ------------ | --------------- | ---------- | --------------- | ---------- | ------------------------- |
| Polling      | Client → Server | HTTP       | High (interval) | Low        | High (many requests)      |
| Long-polling | Server → Client | HTTP       | Low             | Medium     | Medium (many connections) |
| SSE          | Server → Client | HTTP/HTTP2 | Very low        | Low        | Low                       |
| WebSocket    | Bidirectional   | WS (TCP)   | Very low        | Medium     | Very low                  |

**When to use each:**

- Long-polling: broad HTTP/1.1 compatibility, simple infrastructure, tolerable latency.
- SSE: server-push only, works with standard HTTP infrastructure, auto-reconnect.
- WebSocket: bidirectional real-time, chat, gaming, collaborative editing.

**Source:** <https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events>
<https://www.rfc-editor.org/rfc/rfc6455>

<AnkiTags WebSocket SSE long-polling real-time comparison intermediate/>

# What is the difference between L4 and L7 load balancing?

**L4 Load Balancer (Transport Layer):**

- Routes based on IP address and TCP/UDP port only.
- Does not inspect the payload — no HTTP awareness.
- Very fast, low latency, minimal CPU overhead.
- Cannot route based on URL path, headers, cookies, or content.
- Example tools: Linux IPVS, AWS NLB, HAProxy in TCP mode.

**L7 Load Balancer (Application Layer):**

- Inspects and routes based on HTTP headers, URL path, cookies, hostnames.
- Can terminate TLS, add headers, rewrite URLs, enforce rate limits, sticky sessions.
- Higher CPU/latency overhead than L4.
- Example tools: NGINX, HAProxy (HTTP mode), AWS ALB, Envoy.

```
L4 example: all TCP connections to port 443 → round-robin across backends
L7 example: /api/* → api servers; /static/* → storage servers; sticky by cookie
```

Backend engineers typically interact with L7 load balancers — L4 is usually transparent infrastructure.

**Source:** <https://www.nginx.com/resources/glossary/layer-4-load-balancing/>
<https://www.nginx.com/resources/glossary/layer-7-load-balancing/>

<AnkiTags load-balancing L4 L7 infrastructure intermediate/>

# What are the main load balancing algorithms?

| Algorithm                | How it works                                               | Best for                           |
| ------------------------ | ---------------------------------------------------------- | ---------------------------------- |
| **Round Robin**          | Requests distributed sequentially, wrapping around         | Identical backend capacities       |
| **Weighted Round Robin** | Round robin with proportional weight per backend           | Mixed-capacity backends            |
| **Least Connections**    | New request goes to backend with fewest active connections | Long-lived, variable-cost requests |
| **Least Response Time**  | Routes to backend with lowest latency + fewest connections | Latency-sensitive workloads        |
| **IP Hash**              | `hash(client_ip) % n` → always same backend per client IP  | Session affinity without cookies   |
| **Random**               | Pick a random backend                                      | Simple, low overhead               |
| **Consistent Hashing**   | Hash ring — minimal reshuffling when backends change       | Cache affinity, stateful services  |

**Least Connections** outperforms Round Robin when request processing time varies significantly (e.g., mix of fast queries and slow queries).

**Source:** <https://www.nginx.com/blog/choosing-nginx-plus-load-balancing-technique/>
<https://www.cloudflare.com/learning/performance/what-is-load-balancing/>

<AnkiTags load-balancing algorithms round-robin least-connections consistent-hashing intermediate advanced/>

# What is consistent hashing and why is it used in load balancing and caching?

Consistent hashing maps both keys and nodes to a **circular hash ring (modulo 2^32)**. Each request is routed to the closest node clockwise on the ring.

```
Hash ring (0 to 2^32 - 1):
          0
         /|\
     n3 / | \ n1
       /  |  \
      n2──┘

Key k1 → hash(k1) = 105 → nearest node clockwise = n1
Key k2 → hash(k2) = 220 → nearest node clockwise = n3
```

**Key property — minimal disruption on change:**

- Add a node: only the keys between the new node and its predecessor shift to the new node. O(K/N) keys remapped.
- Remove a node: only its keys shift to the next node.
- Traditional modulo hashing (`key % N`): adding/removing a node remaps ~all keys.

**Why it matters:**

- **Distributed caches** (Memcached, Redis Cluster): cache affinity — same key always hits the same node, minimising cache misses.
- **Load balancing stateful services** — requests for the same user/session consistently hit the same backend.
- **Virtual nodes:** Each physical node has V virtual positions on the ring for better balance.

**Source:** <https://www.cloudflare.com/learning/cdn/glossary/consistent-hashing/>

<AnkiTags consistent-hashing load-balancing caching distributed intermediate advanced/>

# What are sticky sessions and what are their tradeoffs?

**Sticky sessions** (session affinity) ensure that requests from the **same client always go to the same backend server**. Necessary when server-side session state is stored locally (not in a shared store).

**Implementation methods:**

- **Cookie-based:** The load balancer inserts a cookie (e.g., `SERVERID=backend-3`). Subsequent requests with that cookie are routed to the same backend.
- **IP-based:** Route by client IP hash. Problem: NAT hides many users behind one IP.

**Tradeoffs:**

| Pros                                   | Cons                                           |
| -------------------------------------- | ---------------------------------------------- |
| Works with local session state         | Uneven distribution if sessions are long-lived |
| Reduces cache misses per backend       | Backend failure loses all its sessions         |
| Lower latency (warm in-process caches) | Hard to scale out new backends smoothly        |

**Best practice:** Prefer stateless services + shared session store (Redis) over sticky sessions — enables true horizontal scaling. Sticky sessions are a workaround, not an architectural goal.

**Source:** <https://www.nginx.com/resources/wiki/modules/sticky/>

<AnkiTags load-balancing sticky-sessions session-affinity intermediate/>

# What is a health check in load balancing?

A health check is a periodic probe from the load balancer to each backend to determine whether it should receive traffic.

**Types:**

- **TCP health check:** Opens a TCP connection to the backend port. Passes if the connection succeeds. Minimal — doesn't verify the application is working.
- **HTTP health check:** Makes an HTTP request (e.g., `GET /health`) and expects a `200 OK`. Can also check response body.
- **gRPC health check:** Uses the gRPC Health Checking Protocol — calls the `Check` RPC.

**Parameters:**

```yaml
healthcheck:
  path: /healthz
  interval: 10s # how often to probe
  timeout: 2s # max time to wait for response
  healthy_threshold: 2 # consecutive successes before marking UP
  unhealthy_threshold: 3 # consecutive failures before marking DOWN
```

**Backend health endpoint should check:** database connectivity, dependency availability, memory pressure — not just "is the process alive".

**Source:** <https://www.nginx.com/resources/wiki/load_balancer_nginx_round_robin_health_check/>
<https://www.cloudflare.com/learning/performance/what-is-load-balancing/>

<AnkiTags load-balancing health-checks availability intermediate/>

# What is a reverse proxy and what does it do?

A **reverse proxy** sits in front of backend servers and intercepts requests from clients on behalf of those servers. The client communicates with the proxy; the proxy forwards to backends.

```
Client → Reverse Proxy (NGINX/Envoy) → Backend Server(s)
```

**What a reverse proxy provides:**

- **Load balancing:** Distributes requests across multiple backends.
- **TLS termination:** Handles HTTPS; backend receives plain HTTP (simpler certificates, less CPU on backends).
- **Caching:** Stores responses and serves them without hitting the backend.
- **Compression:** Applies gzip/br before sending to clients.
- **Request/response rewriting:** Adds headers (`X-Real-IP`), modifies paths.
- **Rate limiting:** Throttles clients.
- **Authentication offloading:** Validates JWTs/API keys before forwarding.
- **Circuit breaking:** Stops sending to unhealthy backends.

**Source:** <https://www.nginx.com/resources/glossary/reverse-proxy-server/>
<https://www.cloudflare.com/learning/cdn/glossary/reverse-proxy/>

<AnkiTags reverse-proxy load-balancing TLS-termination infrastructure junior intermediate/>

# What is a forward proxy and how is it different from a reverse proxy?

A **forward proxy** sits between clients and the Internet, acting on behalf of clients. The server sees the proxy's IP, not the client's.

```
Client → Forward Proxy → Internet (server)   [client knows about the proxy]
Client → Reverse Proxy → Backend             [client thinks it's talking to the backend]
```

**Forward proxy uses:**

- **Anonymity / IP masking:** Hide client IPs (VPN-like behaviour).
- **Content filtering:** Block certain URLs in a corporate network.
- **Access control:** Only allow clients through the proxy.
- **Caching:** Cache frequently requested resources for multiple clients.
- **`CONNECT` method:** HTTP CONNECT tunnels TCP through a proxy (used for HTTPS through an HTTP proxy).

**Key difference:**

- Forward proxy: configured on the **client** side, serves the client.
- Reverse proxy: configured on the **server** side, serves the server.

**Source:** <https://www.cloudflare.com/learning/cdn/glossary/reverse-proxy/>

<AnkiTags proxy forward-proxy reverse-proxy networking intermediate/>

# What is the X-Forwarded-For header and what are its pitfalls?

`X-Forwarded-For` (XFF) carries the **original client IP** when a request passes through proxies or load balancers. Each proxy appends the IP of the previous sender.

```
X-Forwarded-For: client-ip, proxy1-ip, proxy2-ip
```

**How to get the real client IP:**

- If you control all proxies and trust the entire chain: use the **leftmost IP** that wasn't added by a trusted proxy.
- If using a CDN (Cloudflare, AWS ALB): use the CDN-specific header (`CF-Connecting-IP`, `X-Amz-Cf-Connecting-IP`) which is harder to spoof.

**Security pitfall — IP spoofing:**

```
Attacker sets:  X-Forwarded-For: 1.2.3.4
Your server reads: 1.2.3.4  ← completely attacker-controlled!
```

Never trust the raw `X-Forwarded-For` header for security decisions (rate limiting, geo-blocking, allow-listing) unless you know the leftmost untrusted IP the first proxy appended. Use `REMOTE_ADDR` (the TCP peer IP of the last proxy) as the trust anchor.

**Source:** <https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Forwarded-For>

<AnkiTags proxy X-Forwarded-For headers security intermediate advanced/>

# How does a CDN work?

A CDN (Content Delivery Network) is a geographically distributed network of **edge servers** (Points of Presence / PoPs) that cache content close to users.

```
Without CDN:  User (Tokyo) → Request → Origin server (New York) [~150ms RTT]
With CDN:     User (Tokyo) → Request → CDN edge (Tokyo PoP) [~5ms RTT]
              CDN edge (cache hit): serves content directly
              CDN edge (cache miss): fetches from origin, caches, serves user
```

**Components:**

- **Origin server:** Your actual backend. CDN pulls from it on cache miss.
- **Edge node / PoP:** Caches content close to users. There are typically hundreds worldwide.
- **Anycast routing:** CDN's IP address is announced from multiple locations — BGP automatically routes users to the nearest PoP.

**What CDNs accelerate:**

- Static assets (images, JS, CSS) — long TTL, high cache hit rates.
- API responses — shorter TTL, selective caching.
- TLS termination at the edge — handshake latency reduced.
- DDoS mitigation — absorbs traffic at the edge.

**Source:** <https://www.cloudflare.com/learning/cdn/what-is-a-cdn/>

<AnkiTags CDN edge caching performance infrastructure intermediate/>

# What is anycast and how do CDNs and DNS use it?

**Anycast** is a routing method where **multiple servers share the same IP address** and BGP advertises that IP from multiple locations. Routers automatically deliver packets to the **topologically nearest** instance.

```
IP: 1.1.1.1 is announced from:
  - San Francisco PoP (AS13335)
  - London PoP (AS13335)
  - Singapore PoP (AS13335)
  - Tokyo PoP (AS13335)

User in Tokyo: packets flow to Tokyo PoP automatically
User in London: packets flow to London PoP automatically
```

**Uses:**

- **CDN edge routing** — Cloudflare, Fastly, Akamai all use anycast.
- **DNS** — 8.8.8.8 (Google), 1.1.1.1 (Cloudflare), and all 13 DNS root server addresses are anycast — there are thousands of physical servers behind them.
- **DDoS mitigation** — attacks are absorbed/spread across all PoPs instead of concentrating on one target.

Anycast is connectionless for UDP (DNS). For TCP (HTTPS), anycast requires that all sessions from the same user go to the same PoP — BGP routing is stable enough for this in practice.

**Source:** <https://www.cloudflare.com/learning/cdn/glossary/anycast-network/>

<AnkiTags CDN anycast BGP routing DNS infrastructure intermediate advanced/>

# What is NAT (Network Address Translation)?

NAT rewrites **IP addresses (and often ports)** in packet headers as they pass through a router, allowing many private IP addresses to share one or a few public IP addresses.

**Types:**

- **SNAT (Source NAT):** Rewrites the source IP — used when private clients reach the Internet. The router maintains a connection table to match return packets.
- **DNAT (Destination NAT):** Rewrites the destination IP — used for **port forwarding** (incoming traffic to a public IP:port is forwarded to a private server).
- **PAT (Port Address Translation / NAPT):** Most common form — maps `private_ip:ephemeral_port` → `public_ip:translated_port`. This is what home routers and cloud NAT gateways do.

```
Private client: 10.0.0.5:32100 → 8.8.8.8:53
After SNAT:     203.0.113.1:41000 → 8.8.8.8:53  (public IP:translated port)
Return packet:  8.8.8.8:53 → 203.0.113.1:41000 → de-NATted → 10.0.0.5:32100
```

**Impact on backend services:** NAT breaks TCP connection symmetry, causes `TIME_WAIT` issues with many outbound connections, and requires a connection tracking table that can be exhausted.

**Source:** <https://www.rfc-editor.org/rfc/rfc3022>

<AnkiTags NAT SNAT DNAT networking infrastructure intermediate/>

# What is a stateful firewall and how does it differ from a packet filter?

**Packet filter (stateless):** Inspects each packet in isolation against a set of rules (source IP, destination IP, port, protocol). Does not understand connection state.

**Stateful firewall:** Tracks the **state of TCP/UDP connections** in a connection table. Only allows response packets that belong to an established connection.

```
Stateless rule: ALLOW TCP dst_port=80 (allows any packet to port 80)
Stateful rule:  ALLOW ESTABLISHED,RELATED (only allows return packets for
                connections initiated from the trusted side)

Stateful firewall connection table:
src: 10.0.0.5:42000 → dst: 93.184.216.34:80  state: ESTABLISHED
→ return packet 93.184.216.34:80 → 10.0.0.5:42000 allowed automatically
```

**Security groups (AWS, GCP):** Virtual stateful firewalls at the VM level — you only define inbound/outbound rules for desired traffic; return traffic is automatically allowed.

**NACLs (Network ACLs in AWS):** Stateless — you must explicitly allow both inbound and outbound traffic for each direction of a connection.

**Source:** <https://www.cloudflare.com/learning/network-layer/what-is-a-firewall/>

<AnkiTags firewall stateful NAT security-groups networking intermediate/>

# What are the different types of network timeouts for backend services?

```python
import requests
import httpx

# Requests library (widely used)
response = requests.get(
    "https://api.example.com/data",
    timeout=(3.05, 30)  # (connect_timeout, read_timeout) in seconds
)

# HTTPX (modern async)
async with httpx.AsyncClient(
    timeout=httpx.Timeout(
        connect=3.0,    # TCP + TLS handshake
        read=30.0,      # Time to receive response body
        write=10.0,     # Time to send request body
        pool=5.0        # Time to get connection from pool
    )
) as client:
    response = await client.get("https://api.example.com/data")
```

**Timeout types:**

- **Connect timeout:** Time to establish the TCP connection (+ TLS handshake). Should be short (1–5s) — if a server doesn't respond to a SYN quickly, it's likely unreachable.
- **Read timeout:** Time between bytes of the response. Not the total response time — it resets on each received byte. Long enough for slow queries but not infinite.
- **Write timeout:** Time to upload the request body (relevant for file uploads).
- **Total timeout:** Absolute deadline for the entire request.

**Always set timeouts.** Default is no timeout in most libraries — a hung server will block your thread/goroutine forever.

**Source:** <https://docs.python-requests.org/en/latest/user/advanced/#timeouts>
<https://www.rfc-editor.org/rfc/rfc9293>

<AnkiTags timeouts networking backend performance intermediate/>

# What is gRPC and what are its main advantages over REST?

gRPC is an open-source RPC framework developed by Google that uses **Protocol Buffers** for serialisation and **HTTP/2** as transport.

**Advantages over REST/JSON:**

| Feature             | gRPC                                   | REST/JSON                 |
| ------------------- | -------------------------------------- | ------------------------- |
| **Serialisation**   | Protocol Buffers (binary, compact)     | JSON (text, verbose)      |
| **Transport**       | HTTP/2 (multiplexed, binary frames)    | HTTP/1.1 or HTTP/2        |
| **Streaming**       | Native (4 modes)                       | Requires SSE or WebSocket |
| **Code generation** | Strong typed client/server stubs       | Manual or OpenAPI-based   |
| **Performance**     | 5–10× smaller payload, faster encoding | Larger, slower            |
| **Browser support** | Requires gRPC-Web proxy                | Native                    |
| **Observability**   | Rich metadata, interceptors            | Custom                    |

**Best for:**

- Internal service-to-service communication (microservices).
- High-throughput, low-latency APIs.
- Polyglot systems needing strong typed contracts.

**Less suited for:** Browser-facing APIs (gRPC-Web is a workaround), ad-hoc querying, human-readable APIs.

**Source:** <https://grpc.io/docs/what-is-grpc/overview/>
<https://developers.google.com/protocol-buffers/docs/overview>

<AnkiTags gRPC REST protobuf HTTP2 intermediate advanced/>

# What are Protocol Buffers (protobuf)?

Protocol Buffers (protobuf) is Google's **language-neutral, binary serialisation format** used as the default IDL and wire format for gRPC.

```protobuf
// user.proto
syntax = "proto3";

message User {
    int32  id         = 1;
    string name       = 2;
    string email      = 3;
    bool   is_active  = 4;
}

service UserService {
    rpc GetUser (GetUserRequest) returns (User);
    rpc ListUsers (ListUsersRequest) returns (stream User);
}
```

**How it works:**

- Each field has a **field number** (not its name) encoded in the wire format — schema changes (adding optional fields) are backward compatible.
- Encoding: field number + wire type in one byte, then the value. Much more compact than JSON.

**Compared to JSON:**

```
JSON:  {"id":1,"name":"Alice","email":"alice@example.com","is_active":true}
       → 65 bytes text

Protobuf: ~18 bytes binary
```

The `protoc` compiler generates type-safe client and server code in Go, Python, Java, C++, and many other languages.

**Source:** <https://protobuf.dev/programming-guides/proto3/>

<AnkiTags gRPC protobuf serialisation intermediate advanced/>

# What are the four streaming modes in gRPC?

gRPC supports four communication patterns, all built on HTTP/2 streams:

**1. Unary RPC** — single request, single response (like an HTTP request):

```protobuf
rpc GetUser (GetUserRequest) returns (User);
```

**2. Server streaming** — single request, stream of responses:

```protobuf
rpc ListEvents (ListEventsRequest) returns (stream Event);
```

**3. Client streaming** — stream of requests, single response:

```protobuf
rpc UploadChunks (stream Chunk) returns (UploadResponse);
```

**4. Bidirectional streaming** — stream of requests and stream of responses simultaneously:

```protobuf
rpc Chat (stream ChatMessage) returns (stream ChatMessage);
```

Bidirectional streaming is particularly powerful — both sides send messages independently and simultaneously without request/response coupling.

**Source:** <https://grpc.io/docs/what-is-grpc/core-concepts/>

<AnkiTags gRPC streaming unary bidirectional intermediate advanced/>

# What is a service mesh and what problem does it solve?

A **service mesh** is a dedicated infrastructure layer for managing **service-to-service communication** in a microservices architecture. It handles: mTLS, load balancing, observability, retries, circuit breaking, and traffic shaping — transparently, without changes to application code.

**Architecture:** Each service instance gets a **sidecar proxy** (typically Envoy) injected alongside it. All network traffic flows through the sidecar.

```
Service A → Sidecar A → (mTLS) → Sidecar B → Service B
```

**What the mesh provides:**

- **mTLS everywhere:** Automatic cert rotation, zero-trust between services.
- **Observability:** Distributed tracing, metrics, and logs from the network layer.
- **Traffic management:** Canary deployments, A/B testing, fault injection.
- **Resilience:** Timeouts, retries, circuit breaking.

**Common implementations:** Istio (uses Envoy), Linkerd (uses Rust proxy), Consul Connect, AWS App Mesh.

**Tradeoff:** Significant operational complexity and added latency (sidecar hops). Evaluate whether the benefits justify the overhead.

**Source:** <https://www.cloudflare.com/learning/performance/what-is-a-service-mesh/>

<AnkiTags service-mesh mTLS microservices Envoy Istio advanced/>

# What is a DDoS attack and what are its three main categories?

DDoS (Distributed Denial of Service) overwhelms a target with traffic from many sources, exhausting some resource so legitimate users cannot be served.

**1. Volumetric attacks** — exhaust bandwidth:

```
UDP flood, ICMP flood, DNS amplification
Example: DNS amplification — attacker spoofs victim's IP,
sends small DNS queries to open resolvers requesting large records,
resolvers send large responses to the victim (10-70x amplification)
```

**2. Protocol attacks** — exhaust server/network device state:

```
SYN flood — exhausts the SYN backlog queue
Ping of Death, Smurf attack — exploit protocol implementation flaws
```

**3. Application-layer attacks** — exhaust application resources:

```
HTTP flood — many legitimate-looking GET/POST requests
Slowloris — opens many connections, sends data extremely slowly,
            holding connections open and exhausting the server's
            connection pool
```

**Mitigation layers:** Anycast/CDN absorption (volumetric), SYN cookies (protocol), rate limiting + WAF (application).

**Source:** <https://www.cloudflare.com/learning/ddos/what-is-a-ddos-attack/>

<AnkiTags DDoS security volumetric protocol-attack application-layer intermediate advanced/>

# What is a SYN flood attack and how do SYN cookies defend against it?

A **SYN flood** exploits the TCP 3-way handshake: the attacker sends many SYN packets (often with spoofed source IPs) but never completes the handshake with the final ACK.

```
Attacker → SYN (spoofed src IP #1) → Server allocates state, sends SYN-ACK
Attacker → SYN (spoofed src IP #2) → Server allocates state, sends SYN-ACK
... (repeated thousands of times)

Normally: server stores connection state in the SYN queue for each
half-open connection, waiting for the final ACK that never comes.
SYN queue fills up → legitimate SYNs are dropped → DoS.
```

**SYN cookies (defense):** Instead of storing connection state for each SYN, the server **encodes the necessary state into the SYN-ACK's sequence number** itself (a cryptographic hash of source/dest IP, port, and a secret).

```
Server receives SYN → does NOT allocate a queue entry
Server computes seq = hash(src_ip, src_port, dst_ip, dst_port, secret, timestamp)
Server sends SYN-ACK with that seq, no state stored

If the final ACK arrives: server recomputes the hash and verifies it
matches → reconstructs connection state on the fly, no memory was held
hostage during the attack window.
```

```bash
# Enable on Linux
sysctl net.ipv4.tcp_syncookies = 1
```

**Source:** <https://www.rfc-editor.org/rfc/rfc4987>
<https://www.cloudflare.com/learning/ddos/syn-flood-ddos-attack/>

<AnkiTags SYN-flood TCP security DDoS SYN-cookies advanced/>

# What is DNS amplification and why is it effective?

DNS amplification exploits the fact that a **small DNS query can trigger a much larger DNS response**, and combines this with **IP spoofing** to redirect that large response at a victim.

```
1. Attacker spoofs the victim's IP as the source of a DNS query
2. Attacker sends the query to an open DNS resolver, requesting a
   large record type (e.g., ANY, TXT, DNSSEC records)
3. Resolver sends the large response to the SPOOFED source — the victim
4. Victim receives traffic it never asked for, at a much higher volume
   than the attacker had to send (amplification factor: 10x-100x)

Attacker (10 Mbps) → many open resolvers → Victim receives 1+ Gbps
```

**Mitigations:**

- **BCP 38 / ingress filtering:** ISPs reject packets with spoofed source IPs that don't belong to their network — prevents spoofing at the source.
- **Closing open resolvers:** Only allow recursive DNS queries from trusted clients.
- **Response Rate Limiting (RRL)** on authoritative servers.
- **Anycast + scrubbing centers** to absorb volume.

**Source:** <https://www.rfc-editor.org/rfc/rfc2827>
<https://www.cloudflare.com/learning/ddos/dns-amplification-ddos-attack/>

<AnkiTags DNS-amplification DDoS spoofing security advanced/>

# What is the Slowloris attack?

Slowloris is an **application-layer DoS attack** that exploits the way some web servers handle concurrent connections — it ties up connection slots by sending HTTP requests **extremely slowly**, never completing them.

```
Attacker opens many connections to the server.
For each connection, attacker sends a partial HTTP request:

GET / HTTP/1.1\r\n
Host: target.com\r\n
X-a: b\r\n          ← sent, then attacker waits...
                       (sends another junk header every ~10 seconds,
                        just often enough to avoid the server's timeout)

Server keeps each connection open, waiting for the request to complete.
Eventually all of the server's worker threads/connection slots are
consumed by incomplete requests → legitimate clients can't connect.
```

**Why it's efficient:** Requires very little bandwidth from the attacker (unlike volumetric attacks) — just enough to keep connections alive.

**Mitigations:**

- Set aggressive **request header timeouts** (e.g., NGINX `client_header_timeout`).
- Use event-driven servers (NGINX, not thread-per-connection like older Apache).
- Limit concurrent connections per IP.
- Put a reverse proxy/CDN in front that buffers slow clients.

**Source:** <https://www.cloudflare.com/learning/ddos/ddos-attack-tools/slowloris/>

<AnkiTags slowloris application-layer DDoS security advanced/>

# What is rate limiting and what are the common algorithms?

Rate limiting restricts how many requests a client can make in a given time window, protecting backend services from overload (intentional or accidental).

**1. Fixed Window Counter:**

```
Window: 60s, limit: 100 requests
Count resets every 60s boundary.
Problem: burst at window boundary can allow 2x the limit
(100 requests at 0:59, another 100 at 1:00 = 200 in 2 seconds)
```

**2. Sliding Window Log:**

```
Store timestamp of every request; count requests in the last 60s
from "now", not from a fixed boundary. Accurate but memory-heavy
(stores every timestamp).
```

**3. Token Bucket:** (most common in practice)

```
Bucket holds up to N tokens, refills at rate R tokens/sec.
Each request consumes 1 token. No tokens → request rejected/queued.
Allows bursts up to bucket size, then throttles to refill rate.
```

**4. Leaky Bucket:**

```
Requests enter a queue (the "bucket") and are processed at a fixed
rate, regardless of burst size. Smooths bursts into steady output.
```

```
Response when limited:
HTTP/1.1 429 Too Many Requests
Retry-After: 30
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1715800000
```

**Source:** <https://www.rfc-editor.org/rfc/rfc6585>
<https://www.cloudflare.com/learning/bots/what-is-rate-limiting/>

<AnkiTags rate-limiting token-bucket sliding-window security intermediate advanced/>

# What is the difference between CSRF and CORS, and how does CSRF work?

**CSRF (Cross-Site Request Forgery)** tricks a victim's browser into making an unwanted **state-changing request** to a site where they're already authenticated — exploiting the fact that browsers automatically attach cookies to requests regardless of origin.

```html
<!-- Malicious page at evil.com -->
<form action="https://bank.com/transfer" method="POST">
  <input type="hidden" name="to" value="attacker-account" />
  <input type="hidden" name="amount" value="10000" />
</form>
<script>
  document.forms[0].submit();
</script>

<!-- Victim's browser automatically sends bank.com's session cookie
     along with this cross-origin POST request -->
```

**Why this isn't blocked by CORS:** CORS controls whether **JavaScript can read the response** of a cross-origin request — it does **not** prevent the request from being **sent**. The form above is a simple cross-origin POST; CORS preflight isn't even triggered for simple form submissions.

**CSRF defenses:**

- **CSRF tokens:** Server embeds a unique, unpredictable token in forms; rejects requests without a matching token.
- **SameSite cookies:** `Set-Cookie: session=abc; SameSite=Strict` — browser won't send the cookie on cross-site requests.
- **Custom headers:** Requiring a custom header (e.g., `X-Requested-With`) that simple form submissions can't set — this does trigger CORS preflight.

**Source:** <https://owasp.org/www-community/attacks/csrf>
<https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#samesitesamesite-value>

<AnkiTags CSRF CORS security cookies SameSite intermediate advanced/>

# What is SameSite cookie attribute and its three values?

`SameSite` controls whether a cookie is sent with **cross-site requests**, providing CSRF protection at the browser level.

```http
Set-Cookie: session=abc123; SameSite=Strict; Secure; HttpOnly
```

| Value    | Behaviour                                                                                                                                      |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `Strict` | Cookie **never** sent on cross-site requests, even when following a link from another site                                                     |
| `Lax`    | Cookie sent on **top-level navigation** (clicking a link) but not on cross-site `POST`, `fetch`, `<img>`, etc. **Default in modern browsers.** |
| `None`   | Cookie sent on all cross-site requests — **requires `Secure` attribute** (HTTPS only)                                                          |

```
Strict example: bank.com session cookie won't be sent even if the user
clicks a link from gmail.com to bank.com — user appears logged out
until they navigate within bank.com again.

Lax example: clicking a link works (GET request), but a cross-site
form POST or fetch() does not send the cookie.

None example: needed for legitimate cross-site use cases like embedded
widgets, third-party analytics, or SSO — but offers no CSRF protection
on its own (use CSRF tokens too).
```

**Source:** <https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#samesitesamesite-value>
<https://www.rfc-editor.org/rfc/rfc6265bis>

<AnkiTags cookies SameSite CSRF security intermediate/>

# What is certificate pinning and why is it used?

Certificate pinning hardcodes (pins) the **expected certificate or public key** for a server within the client application, rather than trusting any certificate signed by any CA in the system trust store.

```
Normal TLS: trust any cert signed by any of ~100+ trusted root CAs
Pinned TLS: trust ONLY this specific certificate/public key for this host,
            even if another (technically valid) cert is presented
```

**Why:** Protects against:

- A compromised or malicious CA issuing a fraudulent certificate for your domain.
- Man-in-the-middle attacks via corporate/government TLS interception proxies.
- Compromised intermediate CAs (has happened historically — e.g., DigiNotar 2011).

**Implementation approaches:**

- **Pin the leaf certificate:** Most restrictive — breaks immediately on cert renewal unless the app is updated first.
- **Pin the public key (SPKI):** Survives certificate renewal as long as the key pair is reused.
- **Pin the intermediate/CA:** Less restrictive, more operationally forgiving.

**Tradeoff:** Pinning makes certificate rotation operationally risky — if you rotate to a cert with a different key without updating pinned clients first, **you lock out your own legitimate clients**. Mobile apps (especially banking apps) commonly use this; general web browsers have moved away from it (HPKP was deprecated).

**Source:** <https://owasp.org/www-community/controls/Certificate_and_Public_Key_Pinning>

<AnkiTags certificate-pinning TLS security mobile advanced/>

# What is mutual TLS vs API keys vs OAuth for service authentication — when to use each?

| Method                 | How it works                                              | Best for                                      |
| ---------------------- | --------------------------------------------------------- | --------------------------------------------- |
| **API Key**            | Static secret sent in header (`X-API-Key`)                | Simple, low-security third-party integrations |
| **Bearer Token (JWT)** | Signed token with claims, sent in `Authorization: Bearer` | User-facing APIs, stateless auth              |
| **OAuth 2.0**          | Delegated authorization via token exchange flows          | Third-party access on behalf of a user        |
| **mTLS**               | Both sides present X.509 certificates                     | Service-to-service in zero-trust networks     |

**API keys:** Easy to implement but easy to leak (often committed to repos, logged accidentally). No built-in expiry or scoping unless built by hand.

**JWT Bearer tokens:** Self-contained (claims encoded in the token), can be verified without a database lookup (using a public key), but revocation before expiry is hard.

**mTLS:** Strongest mutual authentication — both parties cryptographically verify identity. Used heavily inside service meshes (Istio/Linkerd) for zone-internal east-west traffic. Overhead: certificate lifecycle management.

**Backend engineers typically combine these:** mTLS for service mesh internal traffic + JWT for end-user-facing API authentication + API keys for external partner integrations.

**Source:** <https://www.rfc-editor.org/rfc/rfc6749>
<https://www.rfc-editor.org/rfc/rfc7519>
<https://www.cloudflare.com/learning/access-management/what-is-mutual-tls/>

<AnkiTags authentication mTLS JWT OAuth API-keys security intermediate advanced/>

# What is the Bandwidth-Delay Product (BDP) and why does it matter for TCP performance?

The **Bandwidth-Delay Product** is the amount of data that can be "in flight" on a network path at any given moment — it determines the TCP window size needed to fully utilise the link.

```
BDP = Bandwidth × RTT

Example: 1 Gbps link, 100ms RTT
BDP = 1,000,000,000 bits/sec × 0.1 sec = 100,000,000 bits = 12.5 MB

If TCP's window (rwnd/cwnd) is smaller than the BDP, the connection
cannot fully utilise the available bandwidth — the sender runs out
of window and must wait for ACKs before sending more, leaving the
"pipe" partially empty.
```

**Long Fat Networks (LFNs):** High-bandwidth, high-latency links (e.g., trans-continental fibre) have a large BDP. Without TCP window scaling, the default 64KB window severely limits throughput:

```
64KB window / 100ms RTT = 5.24 Mbps max throughput
regardless of how fast the underlying link actually is!
```

**Fix:** TCP Window Scaling (RFC 7323) extends the window field to support up to ~1GB, allowing full utilisation of high-BDP paths.

**Source:** <https://www.rfc-editor.org/rfc/rfc1323>
<https://www.rfc-editor.org/rfc/rfc7323>

<AnkiTags performance BDP TCP window-scaling latency advanced/>

# What is head-of-line (HOL) blocking and how does it differ across HTTP/1.1, HTTP/2, and HTTP/3?

HOL blocking occurs when **one slow or lost item blocks the processing of items behind it**, even though those items are independently ready.

**HTTP/1.1 (application-layer HOL blocking):**

```
One TCP connection, one request at a time (without pipelining).
A slow response blocks everything queued behind it on that connection.
Mitigated by opening 6 parallel TCP connections per origin (not fixed,
just worked around with more connections).
```

**HTTP/2 (transport-layer HOL blocking):**

```
Multiple streams multiplexed on ONE TCP connection — solves the
application-layer problem above.
BUT: TCP guarantees in-order delivery at the transport layer.
If one packet is lost, ALL streams stall until that packet is
retransmitted and TCP can deliver bytes in order again — even though
the lost packet may belong to only one of the streams.
```

**HTTP/3 / QUIC (HOL blocking eliminated):**

```
QUIC implements its own stream multiplexing directly over UDP.
Each stream has independent loss detection and recovery.
A lost packet for stream A does NOT block stream B's already-received
data from being delivered to the application.
```

**Source:** <https://www.rfc-editor.org/rfc/rfc9000>
<https://www.rfc-editor.org/rfc/rfc7540>

<AnkiTags HOL-blocking HTTP1-1 HTTP2 HTTP3 QUIC performance advanced/>

# What is the RTT budget concept and why does it matter for API latency?

The **RTT budget** is the total number of round trips a request needs before useful data is exchanged — each RTT adds latency proportional to the distance between client and server.

```
Cold connection to an HTTPS API, no caching, no session reuse:

1. DNS resolution:        ~1 RTT  (if not cached)
2. TCP handshake:         1 RTT   (SYN, SYN-ACK, ACK)
3. TLS 1.3 handshake:     1 RTT   (ClientHello → ServerHello+Finished)
4. HTTP request/response: 1 RTT

Total: ~4 RTTs before the first byte of useful data.
At 150ms RTT (e.g., cross-continental): 600ms of pure overhead!
```

**Reducing the RTT budget:**

- **DNS caching / low TTL tradeoffs.**
- **TCP Fast Open:** Send data in the SYN packet for known servers.
- **TLS session resumption / 0-RTT:** Skip the full handshake on reconnect.
- **HTTP/2 or HTTP/3 connection reuse:** Amortise the handshake cost across many requests.
- **CDN edge termination:** Terminate TCP/TLS close to the user, use a persistent backbone connection to the origin.
- **Keep-alive connections:** Avoid repeating steps 2–3 for every request.

**Source:** <https://www.rfc-editor.org/rfc/rfc9000>
<https://developer.mozilla.org/en-US/docs/Web/Performance/Understanding_latency>

<AnkiTags performance latency RTT TLS TCP optimization intermediate advanced/>

# What is the difference between bufferbloat and congestion, and how does it affect latency?

**Congestion** occurs when the network's actual capacity is exceeded — packets are dropped because queues are full.

**Bufferbloat** occurs when network devices have **oversized buffers** that queue packets instead of dropping them when congested — this prevents TCP's loss-based congestion control from detecting congestion early, leading to **excessive queuing delay** rather than packet loss.

```
Without bufferbloat: queue fills → packet dropped → TCP detects loss
                      quickly → backs off → latency stays low

With bufferbloat:    queue fills → packets queue up (not dropped,
                      buffer is huge) → TCP keeps sending more →
                      latency grows to seconds → eventually queue
                      overflows anyway, but only after huge delay
                      was already inflicted on every flow sharing
                      that link
```

**Real-world impact:** A home router with a large buffer can turn a 20ms baseline ping into a 1000ms+ ping under load (e.g., during a large upload), even though no packets were technically lost.

**Mitigations:** Active Queue Management (AQM) algorithms like **CoDel** and **fq_codel** intelligently drop/mark packets before buffers fill completely, preserving low latency.

**Source:** <https://www.rfc-editor.org/rfc/rfc8290>
<https://www.bufferbloat.net/projects/codel/wiki/>

<AnkiTags bufferbloat congestion latency AQM performance advanced/>

# What is the difference between throughput-optimised and latency-optimised network tuning?

Backend systems often need different tuning depending on the workload's priority:

**Throughput-optimised (bulk data transfer — backups, replication, batch jobs):**

```
- Larger TCP buffers (net.ipv4.tcp_rmem, tcp_wmem)
- TCP window scaling enabled
- Jumbo frames (MTU 9000) within the datacentre
- TCP Cubic or BBR congestion control (high BDP friendly)
- Parallel connections to better utilise available bandwidth
```

**Latency-optimised (interactive APIs, real-time systems):**

```
- TCP_NODELAY (disable Nagle's algorithm)
- Smaller, more frequent flushes rather than buffering for throughput
- Connection pooling / keep-alive to avoid handshake RTTs
- Application-level heartbeats to detect failures fast
- HTTP/2 or HTTP/3 to reduce HOL blocking
- Co-locating services to minimise physical RTT
```

**The trade-off:** Many throughput optimisations (large buffers, batching) directly increase latency (bufferbloat-like effects); many latency optimisations (small packets, frequent flushes) reduce throughput efficiency. Tune based on the dominant workload characteristic of each specific service.

**Source:** <https://man7.org/linux/man-pages/man7/tcp.7.html>
<https://www.rfc-editor.org/rfc/rfc5681>

<AnkiTags performance throughput latency tuning TCP advanced/>

# What is a network MTU and what is MTU discovery / fragmentation?

**MTU (Maximum Transmission Unit)** is the largest packet size a network link can carry without fragmentation. Standard Ethernet MTU is **1500 bytes**.

```
If a packet exceeds the MTU of a link along its path:

IPv4: routers can FRAGMENT the packet into smaller pieces
      (unless the "Don't Fragment" (DF) bit is set)
IPv6: routers do NOT fragment — only the SOURCE can fragment;
      if a packet is too large, the router sends back an
      ICMPv6 "Packet Too Big" message
```

**Path MTU Discovery (PMTUD):** Sends packets with the DF bit set; if a router along the path can't forward without fragmenting, it replies with `ICMP Fragmentation Needed` (IPv4) or `Packet Too Big` (IPv6), and the source reduces its packet size accordingly.

**Practical issue — "MTU black holes":** Some firewalls block all ICMP, including the `Fragmentation Needed` messages. This breaks PMTUD silently — large packets simply vanish with no error, causing mysterious connection hangs (commonly seen with VPN tunnels, which reduce effective MTU due to encapsulation overhead).

```bash
# Common VPN-related MTU issue: tunnel overhead reduces usable MTU
# Standard Ethernet: 1500   IPsec/VPN overhead: ~50-80 bytes
# Effective MTU inside tunnel: ~1420-1450
```

**Source:** <https://www.rfc-editor.org/rfc/rfc1191>
<https://www.rfc-editor.org/rfc/rfc8201>

<AnkiTags MTU fragmentation PMTUD ICMP performance intermediate advanced/>

# What is TCP Fast Open (TFO) and how does it reduce connection latency?

TCP Fast Open allows data to be sent in the **SYN packet itself**, skipping the wait for the handshake to complete on subsequent connections to a previously-visited server.

```
Standard TCP:
Client → SYN
Client ← SYN-ACK
Client → ACK + data        ← data sent after 1 full RTT

TCP Fast Open (after first connection):
Client → SYN + data + TFO cookie  ← data sent immediately!
Client ← SYN-ACK + response data
```

**How it works:**

1. On the **first** connection, the server issues a **TFO cookie** (an encrypted token) in its SYN-ACK.
2. On **subsequent** connections, the client includes this cookie in its initial SYN, proving it previously completed a valid handshake.
3. The server validates the cookie and, if valid, processes the data in the SYN immediately rather than waiting for the ACK.

**Security note:** Similar to TLS 0-RTT, TFO data is susceptible to **replay attacks** (an attacker could resend a captured SYN+data packet) — should only be used for idempotent operations.

```bash
sysctl net.ipv4.tcp_fastopen = 3   # enable for both client and server
```

**Source:** <https://www.rfc-editor.org/rfc/rfc7413>

<AnkiTags TCP TCP-Fast-Open TFO latency performance advanced/>

# What is jitter and why does it matter for real-time applications?

**Jitter** is the **variation in packet latency** over time — not the latency itself, but how inconsistent it is.

```
Packet 1: 50ms
Packet 2: 52ms
Packet 3: 48ms      → low jitter (~4ms variance) — smooth
Packet 4: 51ms

vs.

Packet 1: 50ms
Packet 2: 120ms
Packet 3: 45ms       → high jitter (~75ms variance) — choppy
Packet 4: 200ms
```

**Why it matters:**

- **VoIP / video calls:** High jitter causes choppy audio/video even if average latency is acceptable — packets arrive at uneven intervals, disrupting smooth playback.
- **Online gaming:** Inconsistent input lag is often more disruptive than consistently higher (but stable) latency.
- **Live streaming:** Jitter buffers smooth out variance by holding packets briefly before playback, trading a small fixed delay for consistency.

**Causes:** Queuing delay variation, route changes, congestion, Wi-Fi interference.

**Mitigation:** Jitter buffers (receiver-side), QoS prioritisation for real-time traffic, using UDP-based protocols that tolerate some loss rather than TCP's strict ordering (which can amplify perceived jitter through retransmission delays).

**Source:** <https://www.rfc-editor.org/rfc/rfc3393>
<https://www.cloudflare.com/learning/performance/glossary/what-is-jitter/>

<AnkiTags jitter latency real-time VoIP performance intermediate advanced/>

# What is exponential backoff with jitter, and why is it the standard retry strategy?

When a network request fails due to a transient error, retrying immediately — and at a fixed interval — tends to make things worse. Exponential backoff with jitter is the standard way to retry safely.

**Exponential backoff:** increase the wait time multiplicatively after each failed attempt, giving the failing service time to recover instead of hammering it.

```
delay = min(max_delay, base_delay * 2 ** attempt)

attempt 0 → 1s
attempt 1 → 2s
attempt 2 → 4s
attempt 3 → 8s   (capped at max_delay)
```

**Jitter:** add randomness to each delay. Without it, many clients that failed at the same moment will retry in lockstep (1s, 2s, 4s...), re-synchronising into repeated traffic spikes — a "retry storm" or "thundering herd" that can keep a recovering service down.

```
# Full jitter — randomise the entire delay window
sleep = random_between(0, min(max_delay, base_delay * 2 ** attempt))
```

**The other essential guardrails interviewers look for:**

- **Cap the number of retries** (and/or a total time budget) — never retry forever.
- **Retry budget:** limit retries to ~10–20% of normal traffic so retries can't dominate load.
- **Only retry transient failures** (timeouts, 429, 503) — never retry a 400 or 401.
- **Combine with timeouts:** each attempt needs its own timeout, shorter than the backoff interval.

**Source:** <https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/retry-backoff.html>

<AnkiTags retries exponential-backoff jitter resilience backend intermediate advanced/>

# What is the circuit breaker pattern, and what are its three states?

A circuit breaker protects a system from repeatedly calling a downstream dependency that is failing — it "trips" to fail fast instead of letting callers pile up waiting on a service that's already down, preventing **cascading failures**.

It's a state machine with three states:

```
        failures exceed threshold
CLOSED ──────────────────────────► OPEN
  ▲                                  │
  │ success                          │ after a cooldown timeout
  │                                  ▼
  └──────────── HALF-OPEN ◄──────────┘
        (a few trial requests allowed through)
```

| State         | Behaviour                                                                                                                                |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| **Closed**    | Normal operation — requests flow through. Failures are counted; if they cross a threshold, the breaker trips to Open.                    |
| **Open**      | Requests **fail immediately** without calling the downstream (fail fast). After a cooldown period, it moves to Half-Open.                |
| **Half-Open** | A limited number of trial requests are allowed through. If they succeed, the breaker resets to Closed; if they fail, it returns to Open. |

**Why it matters:** retries alone can amplify an outage — if a service is genuinely down, retrying just adds load. A circuit breaker is the complement to retries: retries handle brief blips, the breaker handles sustained failures by stopping traffic entirely until the dependency recovers. Netflix's Hystrix popularised the pattern (now in maintenance mode; resilience4j is a common modern successor).

**Source:** <https://learn.microsoft.com/en-us/azure/architecture/patterns/circuit-breaker>

<AnkiTags circuit-breaker resilience cascading-failure backend intermediate advanced/>

# Which HTTP requests are safe to retry, and what are idempotency keys?

Whether a failed request can be safely retried depends on whether repeating it could cause **duplicate side effects**.

```
Safe to retry (idempotent — repeating has the same effect as once):
  GET, HEAD, OPTIONS    — read-only, no state change
  PUT                   — sets a resource to a value (same result each time)
  DELETE                — resource ends up deleted either way

Dangerous to retry (non-idempotent — each call is a new side effect):
  POST                  — e.g. "create an order", "charge a card"
                          a retry after a network timeout may double-charge
```

**The classic trap:** a `POST` that times out is ambiguous — the request may have **succeeded** on the server even though the client never got the response. Blindly retrying risks a duplicate (a second order, a double charge).

**Idempotency keys** solve this for non-idempotent operations. The client generates a unique key (e.g. a UUID) and sends it with the request:

```
POST /payments
Idempotency-Key: 8f14e45f-ea1b-4f6e-9c2d-...

Server logic:
1. Look up the key. Seen before? → return the STORED result of the
   original request (no second charge).
2. Never seen? → process the payment, store the result against the key
   (typically with a TTL, e.g. 24h in Redis or the DB), then respond.
```

This makes a `POST` effectively idempotent, so clients can retry safely. Stripe, for example, requires idempotency keys on mutation requests and stores them for 24 hours.

**Source:** <https://docs.stripe.com/api/idempotent_requests>
<https://www.rfc-editor.org/rfc/rfc9110>

<AnkiTags idempotency idempotency-key retries HTTP backend intermediate advanced/>

# What is a port number, and what are well-known ports?

A port is a **16-bit number (0–65535)** that identifies a specific application or service on a host. An IP address gets a packet to the right machine; the port number gets it to the right process on that machine.

```
A connection is uniquely identified by a 4-tuple:
  (source IP, source port, destination IP, destination port)

203.0.113.5 : 51000  →  93.184.216.34 : 443
^client IP   ^client    ^server IP      ^server port (HTTPS)
             (ephemeral)
```

**Port ranges:**

| Range       | Name                 | Use                                                                 |
| ----------- | -------------------- | ------------------------------------------------------------------- |
| 0–1023      | **Well-known ports** | Standard services; binding usually requires elevated privileges     |
| 1024–49151  | Registered ports     | Assigned to specific applications by IANA                           |
| 49152–65535 | **Ephemeral ports**  | Temporary, OS-assigned source ports for outbound client connections |

**Common well-known ports worth memorising:**

```
20/21  FTP        25   SMTP        443  HTTPS
22     SSH        53   DNS         587  SMTP (submission)
23     Telnet     80   HTTP        3306 MySQL
                  110  POP3        5432 PostgreSQL
                  143  IMAP        6379 Redis
```

**Why ephemeral port exhaustion matters for backends:** a server making many outbound connections draws from the ~28,000 ephemeral ports per destination tuple — under high connection churn these can run out (tying back to TIME_WAIT and connection pooling).

**Source:** <https://www.rfc-editor.org/rfc/rfc6335>

<AnkiTags ports well-known-ports transport-layer fundamentals junior/>

# What are the common network troubleshooting tools, and what does each diagnose?

When debugging a network or connectivity issue, these are the standard command-line tools and what each is for:

| Tool                     | What it diagnoses                                                                             |
| ------------------------ | --------------------------------------------------------------------------------------------- |
| `ping`                   | Basic reachability + round-trip latency to a host (ICMP echo)                                 |
| `traceroute` / `tracert` | The path (each router hop) to a host, and where latency/loss is introduced                    |
| `dig` / `nslookup`       | DNS resolution — what a name resolves to, which record types exist, which server answered     |
| `ss` / `netstat`         | Local socket state — listening ports, established connections, counts in TIME_WAIT/CLOSE_WAIT |
| `curl` / `wget`          | HTTP(S) request behaviour — status codes, headers, TLS handshake, timing breakdown            |
| `tcpdump` / Wireshark    | Packet capture — inspect actual packets on the wire for deep protocol-level debugging         |
| `mtr`                    | Combines ping + traceroute — continuous per-hop loss/latency over time                        |
| `iperf`                  | Measures achievable throughput/bandwidth between two endpoints                                |

```bash
# A typical top-down debugging flow:
ping api.example.com               # is the host reachable at all?
dig api.example.com                # does DNS resolve correctly?
curl -v https://api.example.com    # does the HTTP/TLS layer work? what status?
ss -tan | grep :443                # are connections piling up in a bad state?
traceroute api.example.com         # where along the path is it breaking?
tcpdump -i any host api.example.com # last resort: inspect raw packets
```

**Interview framing:** strong answers move **layer by layer** — confirm reachability (L3), then name resolution (DNS), then transport (TCP/port), then the application (HTTP/TLS) — rather than jumping straight to packet capture.

**Source:** <https://man7.org/linux/man-pages/man8/ss.8.html>
<https://www.rfc-editor.org/rfc/rfc792>

<AnkiTags troubleshooting tools diagnostics ping traceroute dig tcpdump intermediate/>
