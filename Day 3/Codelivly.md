## Network Architectures

Client-Server Model:

- **The Client:** The device that _requests_ a service or data. (e.g., Your web browser asking for a webpage).
- **The Server:** The powerful, centralized machine that _provides_ the service or data. (e.g., The machine hosting the website).

Peer-to-Peer (P2P) Model:

- There is no central server
-  has equal power and acts as **both a client and a server simultaneously**.

**Real-World Example:** BitTorrent. When you download a movie via a torrent, you aren't downloading it from one central server. You are downloading tiny pieces of the file from dozens of other users, while simultaneously uploading pieces you already have to other users.

diff b/w inter&intranet:

the Internet is a public, global network open to everyone, while an Intranet is a private, restricted network for a specific group or organization

## Layers

1. **Application Layer:** The data is just called **Data** (or a Message).
2. **Transport Layer:** Once TCP/UDP adds its header (including the Port numbers), the PDU is called a **Segment** (for TCP) or a **Datagram** (for UDP).
3. **Internet Layer:** Once IP adds its header (including the IP addresses), the PDU is called a **Packet**.
4. **Network Access Layer:** Once Ethernet adds its header and trailer (including the MAC addresses), the PDU is called a **Frame**. Finally, the frame is converted into **Bits** for the wire.

An IP address is logical; it can change depending on what network you are connected to. A **MAC (Media Access Control) address**, however, is physical.

A MAC address is a 48-bit number burned directly into the firmware of your Network Interface Card (NIC) at the factory. It is the permanent, unchangeable fingerprint of your physical hardware.

