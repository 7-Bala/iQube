
# Introductory Networking

Networking is the base for everything in cybersecurity

Every attack happens through a protocol running over TCP/UDP using an IP address and a port

Need to understand how data travels before learning Nmap or exploiting services

---

## OSI Model (7 Layers)

OSI is a reference model used to understand network communication

| Layer | Name | Main purpose |
|---|---|---|
| 7 | Application | User interacts with network (HTTP, FTP, DNS, SMTP) |
| 6 | Presentation | Encryption, compression, translation (SSL/TLS) |
| 5 | Session | Starts, manages and ends communication sessions |
| 4 | Transport | Reliable delivery, ports (TCP, UDP) |
| 3 | Network | IP addressing and routing |
| 2 | Data Link | MAC addressing, local communication |
| 1 | Physical | Sends raw bits through cables or wireless |

Mnemonic:

All People Seem To Need Data Processing

### Encapsulation

Data moves down the OSI layers before being sent

Application Data → Segment → Packet → Frame → Bits

Receiving device removes these headers in reverse (De-encapsulation)

---

## TCP/IP Model

Real internet follows TCP/IP model

| TCP/IP Layer | OSI Layers |
|---|---|
| Application | 5,6,7 |
| Transport | 4 |
| Internet | 3 |
| Network Access | 1,2 |

Layer 4 = TCP/UDP

Layer 3 = IP

Layer 2 = MAC

---

## MAC Address

48-bit physical address of a network card

Example:

00:1A:2B:3C:4D:5E

First 3 bytes = Manufacturer (OUI)

Last 3 bytes = Unique device ID

Only used inside the same local network

Routers replace MAC addresses at every hop

---

## Ethernet

Layer 2 protocol

An Ethernet frame contains

- Destination MAC
- Source MAC
- EtherType
- Payload
- FCS (error checking)

---

## ARP (Address Resolution Protocol)

Converts IP address → MAC address

Steps

1. Host knows IP but not MAC
2. Sends ARP Request (Broadcast)
3. Correct device replies with its MAC
4. MAC gets stored in ARP cache

Windows

`arp -a`

Linux

`ip neigh`

### ARP Spoofing

Fake ARP replies

Can redirect traffic through attacker

Used in Man-in-the-Middle attacks

---

## Networking Devices

### Hub

Layer 1

Sends incoming data to every port

Everything shares one collision domain

Rarely used today

### Switch

Layer 2

Learns MAC addresses

Sends frames only to correct device

Each port has its own collision domain

### Router

Layer 3

Connects different networks

Uses IP addresses

Usually performs NAT

### Collision Domain

Area where packets can collide

Mostly seen with hubs

### Broadcast Domain

Devices receiving broadcast traffic

Routers stop broadcasts

---

## IPv4 Addressing

IPv4 = 32-bit address

Example

192.168.1.10

Contains

- Network portion
- Host portion

---

## IPv4 Classes

| Class | Range | Default Mask |
|---|---|---|
| A | 1-126 | /8 |
| B | 128-191 | /16 |
| C | 192-223 | /24 |
| D | 224-239 | Multicast |
| E | 240-255 | Reserved |

127.0.0.1 = Loopback (your own machine)

---

## Private IP Ranges

10.0.0.0/8

172.16.0.0/12

192.168.0.0/16

Used inside local networks

Not accessible directly from the internet

Require NAT

---

## APIPA

169.254.x.x

Assigned automatically if DHCP fails

Means device couldn't contact DHCP server

---

## CIDR

CIDR tells how many bits belong to the network

Example

192.168.1.0/24

/24 = Mask 255.255.255.0

256 total addresses

254 usable hosts

### Common CIDR

| CIDR | Mask | Usable Hosts |
|---|---|---|
| /24 | 255.255.255.0 | 254 |
| /25 | 255.255.255.128 | 126 |
| /26 | 255.255.255.192 | 62 |
| /27 | 255.255.255.224 | 30 |
| /28 | 255.255.255.240 | 14 |
| /30 | 255.255.255.252 | 2 |

### Important Terms

Network Address

First address in subnet

Cannot assign to devices

Broadcast Address

Last address in subnet

Sends data to every device

Default Gateway

Router used to reach other networks

Loopback

127.0.0.1

Refers to your own machine

---

## IPv6

128-bit addressing

Example

2001:db8::ff00:42:8329

Created because IPv4 addresses are running out

---

## DHCP

Automatically assigns IP addresses

Uses DORA

Discover

Offer

Request

Acknowledge (ACK)

Uses UDP

Server = Port 67

Client = Port 68

---

## DNS

Converts domain names into IP addresses

Runs mainly on UDP Port 53

Uses TCP 53 for large transfers

Lookup process

Client → Recursive Resolver → Root Server → TLD Server → Authoritative Server

Result is cached

### Common DNS Records

A = IPv4

AAAA = IPv6

CNAME = Alias

MX = Mail server

NS = Name server

TXT = Text/SPF/DKIM

PTR = Reverse lookup

---

## Ports

16-bit numbers

Range

0 - 65535

### Port Types

0-1023

Well-known ports

1024-49151

Registered ports

49152-65535

Dynamic/Ephemeral ports

Common Ports

21 FTP

22 SSH

23 Telnet

25 SMTP

53 DNS

80 HTTP

443 HTTPS

445 SMB

Socket = IP + Port + Protocol

---

## TCP

Reliable

Connection-oriented

Ordered delivery

Uses Three-Way Handshake

1. SYN
2. SYN-ACK
3. ACK

Connection closes using FIN and ACK

### TCP Flags

SYN → Start connection

ACK → Acknowledge

FIN → Close connection

RST → Reset connection

PSH → Send immediately

URG → Urgent data

### Nmap

SYN-ACK = Port Open

RST = Port Closed

No Reply = Usually Filtered

---

## UDP

Connectionless

No handshake

No guaranteed delivery

Faster than TCP

Common Uses

DNS

DHCP

VoIP

Streaming

Online Games

UDP scanning is slower because no handshake

---

## TCP vs UDP

TCP

- Reliable
- Handshake
- Ordered
- Slower

UDP

- Fast
- No handshake
- No ordering
- No guarantee

---

## NAT (Network Address Translation)

Allows multiple private devices to share one public IP

Router replaces private IP with public IP

Keeps translation table

Port forwarding is needed for outside devices to directly access internal systems

---

## Wireshark

Packet capture and analysis tool

Shows every packet on the network

Three panes

Top = Packet List

Middle = Protocol Layers

Bottom = Raw Data

### Useful Filters

`ip.addr == 10.0.0.5`

Traffic to/from an IP

`tcp.port == 80`

TCP Port 80 traffic

`http`

HTTP traffic

`dns`

DNS traffic

`arp`

ARP traffic

`tcp.flags.syn == 1 and tcp.flags.ack == 0`

Only SYN packets

Follow → TCP Stream

Shows an entire TCP conversation

Useful for finding plaintext credentials

---

## Nmap

Basic command

`nmap <target>`

Scans the 1000 most common TCP ports

Shows Open, Closed or Filtered ports

---

## Useful Commands

`ipconfig`

Windows IP info

`ifconfig`

Older Linux IP info

`ip a`

Modern Linux IP info

`ping <ip>`

Check connectivity

`tracert <ip>`

Windows route tracing

`traceroute <ip>`

Linux route tracing

`arp -a`

View ARP table (Windows)

`ip neigh`

View ARP table (Linux)

`netstat -an`

Show active connections and listening ports

`nslookup <domain>`

DNS lookup

`dig <domain>`

Advanced DNS lookup

---

## Important Points

Layer 2 = MAC, Ethernet, ARP

Layer 3 = IP, Routing

Layer 4 = TCP, UDP, Ports

Layer 7 = Services like HTTP, FTP, SMB

Wireshark captures and analyzes packets

Nmap scans hosts and discovers open ports