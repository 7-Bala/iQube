## OSI Model

7 layers, standardised model for how networking theory works. real world actually runs on tcp/ip (more condensed) but osi's easier to learn from since it splits things up more

mnemonic - Anxious Pale Shakespeare Treated Nervous Drunks Patiently (7 -> 1)

7 - Application - interface for apps to talk to the network, hands data down to presentation

6 - Presentation - standardises the format, handles encryption/compression

5 - Session - opens n maintains the connection, keeps sessions separate (why multiple tabs dont mix up data)

4 - Transport - picks tcp or udp

tcp - connection based, reliable, resends lost data - used when accuracy > speed (file transfer, web pages)
udp - no connection, just fires n hopes - used when speed > accuracy (video/voice streaming, hence pixelation on bad connection)

splits data into segments (tcp) or datagrams (udp)

3 - Network - logical addressing (ip), figures out best route. ipv4 is the common one rn

2 - Data Link - physical addressing (mac). also adds a trailer - checks for corruption on receive, cant be tampered with without breaking the trailer (bonus security). mac is burned in by the manufacturer, cant be changed but can be spoofed

1 - Physical - actual hardware, converts to electrical signals n back

## Encapsulation

adding headers as data goes down each layer (+ a trailer, but only at layer 2)

naming changes depending on the layer -

7/6/5 -> data
4 -> segment (tcp) / datagram (udp)
3 -> packet
2 -> frame
1 -> bits

receiving side reverses all this - de-encapsulation - strips headers going back up 1 -> 7

this exact same process happening on every device is *why* any 2 network devices can talk to each other regardless of vendor/os

## TCP/IP Model

the actual model used in real life (RFC1122, 1982, made by the DoD). osi came later from ISO, mainly for teaching

condenses osi's 7 layers into 4 -

application -> covers osi 7, 6, 5
transport -> covers osi 4
internet -> covers osi 3
network interface -> covers osi 2, 1

(some versions split network interface back into data link + physical = 5 layer version. not officially defined but fine to use either)

named after its 2 main protocols - tcp (controls the flow) n ip (controls addressing/routing)

### 3 way handshake

tcp is connection based so this has to happen before any data gets sent

syn (client) -> syn+ack (server) -> ack (client)

connection's up after this, data flows reliably, anything lost/corrupted gets resent

## Networking Tools

### ping

tests if a target's reachable. runs on icmp -> network layer (osi) / internet layer (tcp/ip)

`ping <target>`

bonus - resolves n shows you the ip of whatever hostname you gave it

switches -
`-i <sec>` - interval between requests
`-4` - force ipv4 only
`-v` - verbose output

### traceroute

shows every hop between you n the destination

`traceroute <destination>`

windows tracert defaults to icmp (same as ping), linux traceroute defaults to udp. both changeable w flags

`-i <interface>` - pick the interface
`-T` - use tcp syn instead of default

windows traceroute runs on the internet layer by default since the default proto is icmp

### whois

domains exist so we dont have to remember ips. leased from registrars

whois looks up who owns/registered a domain

`whois <domain>`

install if missing on debian - `sudo apt update && sudo apt-get install whois`

eu domains = personal info usually redacted (gdpr), other regions can leak way more

### dig

manually queries dns servers

`dig <domain> @<dns-server-ip>`

dns resolution order (the actual important part) -

1. hosts file - local, manual mappings, checked first, old but still takes priority
2. local dns cache
3. recursive server - auto known via router/isp, or public ones like google/opendns - has its own cache too
4. root server - 13 original ips pre-2004, still the same 13 ips today, just load balanced across way more physical servers now - points to the right tld server
5. tld server - split by extension (.com, .co.uk etc) - points to the right authoritative server
6. authoritative server - actual source of truth for that domain, sends back the real answer

dig output - care about the ANSWER section mostly, also shows TTL (in seconds) - how long to trust the cached record before asking again

google's 2 public dns servers - 8.8.8.8 n 8.8.4.4

24hr ttl = 86400 in dig's output

## Practical

didnt have login for tryhackme so couldnt submit on the platform itself, just worked through the theory + ran the commands manually on the vm instead

pinged n traceroute'd a few targets, ran whois on a couple domains, used dig to actually watch the resolution chain happen instead of just reading about it

good primer before going deeper into ports/protocols next
