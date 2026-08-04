# Introductory Networking — Full Study Guide

Source module: TryHackMe "Network Exploitation Basics" → Room: Introductory Networking

This is the foundation room. Everything in Nmap, Network Services, and Network Services 2 assumes you understand how devices find each other, how data is packaged for the wire, and how TCP/UDP actually behave. Read this one slowly.

---

## 1. Why networking matters for security

Every attack against a "network service" is really an attack against a protocol running on top of TCP or UDP, reachable through an IP address and a port. Before you can attack (or defend) anything, you need a mental model of: how a packet gets from your machine to a target, what layer a vulnerability lives at, and how tools like Nmap or Wireshark represent what's happening on the wire. That's what this room builds.

## 2. The OSI Model (7 layers)

The OSI (Open Systems Interconnection) model is a conceptual framework that breaks network communication into seven layers. Real protocols don't respect it perfectly, but it's the shared vocabulary the entire industry uses to talk about "where" something happens.

| Layer | Name | Unit of data (PDU) | Purpose | Example protocols/devices |
|---|---|---|---|---|
| 7 | Application | Data | Interface between the network and the actual application/user | HTTP, FTP, SMTP, DNS, SMB |
| 6 | Presentation | Data | Translation, encryption, compression — formats data for the application layer | SSL/TLS, JPEG, ASCII |
| 5 | Session | Data | Establishes, manages, and tears down sessions between hosts | NetBIOS, RPC, sockets |
| 4 | Transport | Segment (TCP) / Datagram (UDP) | End-to-end delivery, reliability, flow control, ports | TCP, UDP |
| 3 | Network | Packet | Logical addressing and routing between networks | IP, ICMP, routers |
| 2 | Data Link | Frame | Physical addressing (MAC), delivery within a single local network | Ethernet, ARP, switches |
| 1 | Physical | Bits | The actual electrical/optical/radio transmission of raw bits | Cables, radio waves, NICs, hubs |

Mnemonic for remembering top-to-bottom: **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing.

**Encapsulation**: as data travels down the stack on the sending host, each layer wraps ("encapsulates") the data from the layer above with its own header (and sometimes trailer). Application data becomes a Segment (Transport header added) → becomes a Packet (Network/IP header added) → becomes a Frame (Data Link header + trailer added) → becomes Bits (Physical). The receiving host does the reverse — **de-encapsulation** — stripping each header as it climbs back up the stack. When you look at a packet in Wireshark, you're literally looking at these nested headers.

## 3. The TCP/IP Model (4 layers)

The TCP/IP model is the practical, implemented model the real Internet runs on. It compresses OSI's 7 layers into 4:

| TCP/IP Layer | Maps to OSI layers | Examples |
|---|---|---|
| Application | Application, Presentation, Session (5-7) | HTTP, FTP, DNS, SMTP, SMB |
| Transport | Transport (4) | TCP, UDP |
| Internet | Network (3) | IP, ICMP, ARP (debated layer) |
| Network Access (Link) | Data Link, Physical (1-2) | Ethernet, Wi-Fi |

When people say "Layer 4 problem" they mean TCP/UDP; "Layer 3" means IP/routing; "Layer 2" means MAC addressing/switching.

## 4. MAC addresses and Ethernet

A **MAC (Media Access Control) address** is a 48-bit address burned into (or spoofable on) a network interface card. Written as six hex octets, e.g. `00:1A:2B:3C:4D:5E`. The first 3 octets (24 bits) are the **OUI (Organizationally Unique Identifier)** identifying the manufacturer; the last 3 octets are unique to that device. MAC addresses only matter for delivery *within* the same local network (same broadcast domain) — routers strip and rewrite Layer 2 headers at every hop, but the IP header underneath stays the same end-to-end.

**Ethernet** is the Layer 2 protocol that frames data for transmission over wired LANs. An Ethernet frame contains: destination MAC, source MAC, EtherType (what's inside, e.g. IPv4), the payload, and a Frame Check Sequence (CRC) trailer for error detection.

**ARP (Address Resolution Protocol)** is how a host discovers which MAC address owns a given IP address on the local network. Process:
1. Host A wants to talk to IP 192.168.1.5 but only knows the IP, not the MAC.
2. Host A broadcasts an ARP Request ("Who has 192.168.1.5? Tell 192.168.1.2") to `FF:FF:FF:FF:FF:FF` (broadcast MAC).
3. The host that owns that IP replies with an ARP Reply containing its MAC address, sent directly (unicast) back to Host A.
4. Host A caches the mapping in its **ARP table** (`arp -a` on Windows, `ip neigh` or `arp -n` on Linux) for a short time so it doesn't have to ask again.

Security note: because ARP has no authentication, **ARP spoofing/poisoning** (sending forged ARP replies) lets an attacker on the same LAN redirect traffic through themselves — the basis of many man-in-the-middle attacks. This room only introduces the concept; exploitation of it is covered elsewhere on TryHackMe.

## 5. Networking devices

- **Hub**: a dumb Layer 1 device. Repeats every incoming signal out every other port. Creates one big collision domain — inefficient and effectively broadcasts everything to everyone. Rarely used today.
- **Switch**: a Layer 2 device. Learns which MAC address lives on which physical port (building a MAC address table) and forwards frames only to the correct port, rather than flooding everywhere. Each switch port is its own collision domain, but by default all ports on a switch are in one broadcast domain (unless VLANs segment it).
- **Router**: a Layer 3 device. Connects separate networks (different IP subnets) and forwards packets between them based on IP addresses and its routing table. Each router interface is a separate broadcast domain. This is also where NAT typically happens.
- **Collision domain**: a network segment where data packets can collide with one another when sent simultaneously (relevant mostly to old shared-media/hub networks and half-duplex links).
- **Broadcast domain**: a set of devices that will all receive a broadcast frame (destination MAC `FF:FF:FF:FF:FF:FF`) sent by any one of them. Routers do not forward broadcasts between networks; switches do (within the same VLAN).

## 6. IP addressing (IPv4)

An IPv4 address is a 32-bit number, written as four decimal octets (0-255) separated by dots, e.g. `192.168.1.10`. It has two conceptual parts: the **network portion** and the **host portion**, split by the subnet mask.

### Address classes (historical, still asked about)

| Class | First octet range | Default mask | Typical use |
|---|---|---|---|
| A | 1–126 | 255.0.0.0 (/8) | Huge networks |
| B | 128–191 | 255.255.0.0 (/16) | Medium networks |
| C | 192–223 | 255.255.255.0 (/24) | Small networks |
| D | 224–239 | — | Multicast |
| E | 240–255 | — | Experimental/reserved |

(127.x.x.x is reserved for loopback, e.g. `127.0.0.1` = "this machine".)

### Private (non-routable) address ranges — RFC 1918

| Range | CIDR |
|---|---|
| 10.0.0.0 – 10.255.255.255 | 10.0.0.0/8 |
| 172.16.0.0 – 172.31.255.255 | 172.16.0.0/12 |
| 192.168.0.0 – 192.168.255.255 | 192.168.0.0/16 |

These are used inside private LANs and are not routable on the public Internet — devices using them reach the Internet via **NAT** (see §9).

`169.254.x.x` is **APIPA** (Automatic Private IP Addressing) — a host self-assigns this when DHCP fails, signalling "I couldn't reach a DHCP server."

### CIDR notation and subnet masks

CIDR (Classless Inter-Domain Routing) notation, e.g. `192.168.1.0/24`, states how many bits (from the left) are the network portion. `/24` = 24 bits of network = subnet mask `255.255.255.0` = 256 total addresses (254 usable hosts, since the first address is the network address and the last is the broadcast address).

Common CIDR-to-hosts reference:

| CIDR | Subnet mask | Total addresses | Usable hosts |
|---|---|---|---|
| /24 | 255.255.255.0 | 256 | 254 |
| /25 | 255.255.255.128 | 128 | 126 |
| /26 | 255.255.255.192 | 64 | 62 |
| /27 | 255.255.255.224 | 32 | 30 |
| /28 | 255.255.255.240 | 16 | 14 |
| /30 | 255.255.255.252 | 4 | 2 (used for point-to-point links) |

**Worked subnetting example**: given `192.168.1.0/26`, the block size is 256 − 192 = 64 (since /26 = mask 255.255.255.**192**, and 256−192=64). So subnets are: `192.168.1.0/26` (hosts .1–.62, broadcast .63), `192.168.1.64/26` (hosts .65–.126, broadcast .127), `192.168.1.128/26`, `192.168.1.192/26`. This "magic number" trick (256 − last non-255 octet of the mask) is the fast way to subnet by hand.

Key terms: **network address** (all host bits 0 — identifies the subnet, not assignable to a device), **broadcast address** (all host bits 1 — sends to every host on that subnet), **default gateway** (the router interface a host sends traffic to when the destination isn't on its local subnet), **loopback** (127.0.0.1, always refers to the local machine itself).

### IPv6 (brief)

128-bit addresses written as eight groups of 4 hex digits separated by colons, e.g. `2001:0db8:0000:0000:0000:ff00:0042:8329`, commonly abbreviated by dropping leading zeros and collapsing one run of zero groups with `::` (e.g. `2001:db8::ff00:42:8329`). Solves IPv4 exhaustion; not the focus of this module but good to recognize.

## 7. DHCP — how a host gets an IP automatically

DHCP (Dynamic Host Configuration Protocol) automates IP assignment via the **DORA** process:

1. **Discover** — client broadcasts "I need an IP" (UDP port 67 destination, 68 source).
2. **Offer** — a DHCP server responds with a proposed IP, mask, gateway, DNS servers, and lease time.
3. **Request** — client broadcasts back requesting that specific offer (broadcast, because there could be multiple DHCP servers).
4. **Acknowledge (ACK)** — the server confirms and the client configures itself.

DHCP uses UDP ports 67 (server) and 68 (client).

## 8. DNS — how names become IP addresses

DNS (Domain Name System) translates human-readable domain names (e.g. `example.com`) into IP addresses. It runs primarily over UDP port 53 (TCP 53 for larger responses/zone transfers).

Resolution process (simplified, "recursive" resolution): your machine asks a **recursive resolver** (often your ISP's or a public one like 1.1.1.1/8.8.8.8) → that resolver asks a **root server** → which points to the **TLD server** (e.g. for `.com`) → which points to the **authoritative name server** for that specific domain → which returns the actual IP. The answer is cached along the way (respecting TTL) so future lookups are faster.

Common DNS record types:

| Record | Purpose |
|---|---|
| A | Maps a hostname to an IPv4 address |
| AAAA | Maps a hostname to an IPv6 address |
| CNAME | Alias — points one hostname to another hostname |
| MX | Mail exchange — where email for the domain should be delivered |
| NS | Delegates a domain/subdomain to specific name servers |
| TXT | Arbitrary text — often used for domain verification, SPF/DKIM email security |
| PTR | Reverse lookup — IP to hostname |

## 9. TCP, UDP, and the Three-Way Handshake

Both TCP and UDP live at the Transport layer and add **port numbers** so multiple applications on one host can be reached independently, on top of the one IP address that host has.

### Ports

A port is a 16-bit number (0–65535) identifying a specific process/service.
- **0–1023**: well-known/system ports (require root/admin to bind on most OSes) — e.g. 21 FTP, 22 SSH, 23 Telnet, 25 SMTP, 53 DNS, 80 HTTP, 443 HTTPS, 445 SMB.
- **1024–49151**: registered ports.
- **49152–65535**: dynamic/ephemeral ports, typically used as the *source* port for outgoing client connections.

A **socket** is the combination of IP address + port + protocol that uniquely identifies one end of a connection (e.g. `192.168.1.5:443/TCP`).

### TCP (Transmission Control Protocol) — connection-oriented, reliable

TCP guarantees ordered, reliable, error-checked delivery. It achieves this via the **three-way handshake** before any application data is sent:

1. Client → Server: **SYN** (synchronize) — "I want to connect, here's my initial sequence number."
2. Server → Client: **SYN-ACK** — "Acknowledged, here's my initial sequence number too."
3. Client → Server: **ACK** — "Acknowledged." Connection is now established.

Termination is a **four-way handshake** using FIN/ACK: each side independently sends a FIN (finished) and receives an ACK for it.

**TCP flags** you'll see constantly in Wireshark/Nmap output:

| Flag | Meaning |
|---|---|
| SYN | Synchronize — initiate a connection |
| ACK | Acknowledge receipt of data/a SYN |
| FIN | Finish — gracefully close a connection |
| RST | Reset — abruptly abort/refuse a connection (a closed TCP port replies with RST) |
| PSH | Push — deliver buffered data to the application immediately |
| URG | Urgent — prioritize this data |

Why this matters for scanning: Nmap's SYN scan sends a SYN and inspects the reply — SYN-ACK means open, RST means closed, no reply (after retries) usually means filtered by a firewall.

### UDP (User Datagram Protocol) — connectionless, unreliable

UDP just sends datagrams with no handshake, no guaranteed delivery, no ordering, and no automatic retransmission. It's faster and lower-overhead, used where speed matters more than reliability or where the application handles reliability itself: DNS, DHCP, SNMP, streaming, VoIP, and many game protocols. Because there's no handshake, UDP port scanning is inherently slower and less reliable — you often infer "open" only by the *absence* of an ICMP "port unreachable" error.

### TCP vs UDP summary

| | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented (handshake) | Connectionless |
| Reliability | Guaranteed delivery, retransmits | Best-effort, no retransmission |
| Ordering | Ordered | Not guaranteed |
| Speed | Slower (overhead) | Faster |
| Use cases | HTTP/S, FTP, SSH, SMB, email | DNS, DHCP, streaming, VoIP |

## 10. NAT (Network Address Translation)

NAT lets many devices on a private network (RFC 1918 addresses) share a single public IP address to reach the Internet. The router/firewall rewrites the source IP (and often source port) of outgoing packets to its own public IP, keeps a translation table, and reverses the mapping on the way back. This is why an internal host at `192.168.1.10` appears to the outside world as your router's public IP — and why unsolicited inbound connections normally can't reach internal hosts unless port forwarding is configured.

## 11. Wireshark — the basics

Wireshark is a packet capture/analysis tool that lets you see every field of every packet crossing an interface — a direct, visual way to see everything described above.

Core workflow: pick an interface to capture on → traffic streams in, each row is one frame/packet → the three panes show (top) the packet list, (middle) the protocol layers of the selected packet exactly matching OSI/TCP-IP encapsulation, (bottom) the raw hex/ASCII bytes.

Common display filters:

| Filter | Shows |
|---|---|
| `ip.addr == 10.0.0.5` | Traffic to/from that IP |
| `tcp.port == 80` | Traffic on TCP port 80 |
| `http` | Only HTTP protocol packets |
| `dns` | Only DNS queries/responses |
| `tcp.flags.syn == 1 and tcp.flags.ack == 0` | Only SYN packets (connection attempts) |
| `arp` | Only ARP traffic |

Right-click → **Follow → TCP Stream** reconstructs an entire conversation (e.g. a full HTTP request/response, or — notoriously — a plaintext FTP/Telnet login) in readable form. This is exactly how you'd catch credentials sent in cleartext, which becomes very relevant in the Network Services rooms (FTP and Telnet both send credentials unencrypted).

## 12. Intro to Nmap (bridge to the next room)

The room closes with a first taste of Nmap: `nmap <target>` runs a default scan of the 1000 most common TCP ports and reports open/closed/filtered. This is expanded on massively in the dedicated Nmap room (see `02_Nmap.md`).

## 13. Practical command reference from this room

| Command | Purpose |
|---|---|
| `ipconfig` / `ifconfig` / `ip a` | Show your host's IP configuration (Windows / older Linux / modern Linux) |
| `ping <ip>` | Test reachability via ICMP echo request/reply |
| `tracert <ip>` / `traceroute <ip>` | Show every router hop between you and the destination |
| `arp -a` / `ip neigh` | Show the local ARP cache |
| `netstat -an` | Show active connections and listening ports on your own machine |
| `nslookup <domain>` / `dig <domain>` | Manually query DNS |

---

## Key takeaways

Networking is a layered system where each layer only needs to trust the layer directly below it. An attacker (or defender) needs to know at which layer a given tool or vulnerability operates: MAC/ARP issues are Layer 2, IP/routing issues are Layer 3, TCP/UDP behavior and port scanning are Layer 4, and the actual service being exploited (FTP, SMB, SMTP, MySQL, etc., covered in later rooms) is Layer 7. Wireshark lets you observe all of this directly; Nmap automates probing it remotely.
