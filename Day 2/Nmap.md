abbreviation - network mapper 

provides information about open ports, operating systems, and other details.

use man page to know more about flags in nmap

`nmap -h` / `man nmap`

## TCP Connect Scans

tcp - 3 way handshake -{ syn - {syn and ack} from the server - ack} 
![[Pasted image 20260723182716.png]]

 TCP Connect scan works by performing the three-way handshake with each target port or in other words Nmap tries to connect to each specified TCP port, and determines whether the service is open by the response it receives.

if a port is closed i.e request arrives at a specific port on the target machine(for eg port 80) but there is no application (like a web server) listening or waiting for traffic on that port then a reset is sent in response to any incoming segment except another reset. If a packet arrives at a closed port, the system automatically replies with an **`RST`** packet to tell the sender to go away.
**The Exception:** It will _not_ reply with an `RST` if the incoming packet is _already_ an `RST` packet—this rule prevents two computers from getting caught in an infinite loop of sending `RST` packets back and forth to each other.

![[Pasted image 20260723184204.png]]


Many firewalls are configured to simply **drop** incoming packets. Nmap sends a TCP SYN request, and receives nothing back. This indicates that the port is being protected by a firewall and thus the port is considered to be _filtered_.

That said, it is very easy to configure a firewall to respond with a RST TCP packet. For example, in IPtables for Linux, a simple version of the command would be as follows:

`iptables -I INPUT -p tcp --dport <port> -j REJECT --reject-with tcp-reset`

## SYN Scans

known as half open scans / stealth scans 
flag used : `-sS`

Where TCP scans perform a full three-way handshake with the target, SYN scans sends back a RST TCP packet after receiving a SYN/ACK from the server (this prevents the server from repeatedly trying to make the request). In other words, the sequence for scanning an **open** port looks like this:

- **You:** `SYN` (_"Hello, can we talk?"_)
    
- **Server:** `SYN/ACK` (_"Yes, I'm listening!"_)
    
- **You:** `RST` (_"Never mind, cancel!"_)


![](https://assets.tryhackme.com/additional/imgur/cPzF0kU.png)