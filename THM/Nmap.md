# Nmap

Nmap (Network Mapper) is used for host discovery and port scanning

Used to find

- Live hosts
- Open ports
- Running services
- Service versions
- OS information
- Vulnerabilities (using NSE)

Only scan systems you own or have permission to test

Install

```bash
sudo apt install nmap
```

---

## Port States

### Open

Service is actively listening

### Closed

Host is reachable

Nothing is listening on the port

TCP replies with RST

### Filtered

Firewall blocks the probe

Nmap can't determine if port is open

### Unfiltered

Port is reachable

Usually seen in ACK scans

### Open|Filtered

Nmap can't distinguish between open or filtered

Common in UDP scans

### Closed|Filtered

Can't determine if closed or filtered

---

## Host Discovery

Nmap first checks if host is alive

Uses

- ICMP Echo
- TCP SYN
- TCP ACK
- ICMP Timestamp

If firewall blocks these probes

Nmap may think host is down

### -Pn

Skip host discovery

Treat target as alive

Useful when ICMP is blocked

---

## Scan Types

### -sS (TCP SYN Scan)

Needs Root

Half-open scan

Sends SYN

SYN-ACK = Open

RST = Closed

Fast

Stealthier than Connect Scan

Default when running as root

---

### -sT (TCP Connect Scan)

No Root needed

Completes full TCP handshake

Slower

More likely to be logged

Default for normal users

---

### -sU (UDP Scan)

Scans UDP ports

Slower than TCP

No handshake

Often combined with SYN scan

Example

```bash
nmap -sS -sU <target>
```

---

### -sN (Null Scan)

No TCP flags set

Closed port → RST

Open port → No response

Doesn't work well on Windows

---

### -sF (FIN Scan)

Only FIN flag is set

Can bypass simple firewalls

---

### -sX (Xmas Scan)

FIN + PSH + URG flags

Packet looks "lit up"

Same purpose as FIN scan

---

### -sA (ACK Scan)

Maps firewall rules

Doesn't tell if port is open

Shows Unfiltered

---

### -sV (Version Detection)

Finds software version

Example

vsftpd 2.3.4

Very useful for finding CVEs

---

### -O (OS Detection)

Guesses operating system

Uses TCP/IP fingerprinting

Not always accurate

---

### -sC (Default Scripts)

Runs safe default NSE scripts

Shortcut

```bash
--script=default
```

---

### -A (Aggressive Scan)

Includes

- Version Detection
- Default Scripts
- OS Detection
- Traceroute

Equivalent to

```bash
-sV -sC -O --traceroute
```

Good for CTFs

Very noisy

---

## Why FIN, Null and Xmas Scans Work

Some simple firewalls only block SYN packets

FIN, Null and Xmas scans don't use SYN

Can sometimes bypass those firewalls

Modern firewalls usually detect them

---

## Port Selection

### Single Port

```bash
-p 80
```

### Multiple Ports

```bash
-p 80,443,8080
```

### Range

```bash
-p 1-1000
```

### All Ports

```bash
-p-
```

Scans all 65535 ports

### Mixed TCP and UDP

```bash
-p U:53,T:80,443
```

### Top 100 Ports

```bash
--top-ports 100
```

Without `-p`

Nmap scans the 1000 most common TCP ports

---

## NSE (Nmap Scripting Engine)

Uses Lua scripts

Can enumerate services

Can detect vulnerabilities

Can even attempt exploitation

### Script Categories

default

Safe useful scripts

discovery

Collect information

safe

Won't crash target

intrusive

May crash services

vuln

Checks for vulnerabilities

exploit

Attempts exploitation

auth

Authentication checks

brute

Brute-force attacks

version

Helps version detection

### Examples

Default scripts

```bash
nmap --script default -sV <target>
```

Vulnerability scan

```bash
nmap --script vuln <target>
```

SMB Enumeration

```bash
nmap --script smb-os-discovery,smb-enum-shares -p 445 <target>
```

HTTP Scripts

```bash
nmap --script "http-*" -p 80 <target>
```

Useful scripts

ftp-anon

smb-vuln*

smtp-enum-users

mysql-info

---

## Timing Templates

### -T0

Paranoid

Very slow

IDS evasion

### -T1

Sneaky

Slow

### -T2

Polite

Reduces target load

### -T3

Normal

Default

### -T4

Aggressive

Fast

Best for CTFs

Most commonly used

### -T5

Insane

Maximum speed

May miss ports

---

## Firewall & IDS Evasion

### -f

Fragment packets

Can bypass some firewalls

### -D

Uses decoy IPs

Target sees multiple attackers

### -S

Spoof source IP

### --spoof-mac

Spoof MAC address

### -sI

Idle (Zombie) Scan

Uses another host to hide your IP

### --data-length

Adds random data to packets

### -g

Choose source port

Example

53

88

---

## Saving Output

### Normal

```bash
-oN file.txt
```

### XML

```bash
-oX file.xml
```

### Grepable

```bash
-oG file.gnmap
```

### All Formats

```bash
-oA scan
```

Creates

scan.nmap

scan.xml

scan.gnmap

---

## Useful Flags

### -v

Verbose

### -vv

More verbose

### -n

Skip DNS resolution

Faster

### -Pn

Skip host discovery

### --reason

Shows why Nmap marked port as open/closed

### --open

Only display open ports

### -6

IPv6 scanning

---

## Common Workflow

### 1. Initial Scan

```bash
nmap -sV -sC -T4 <target>
```

Quick overview

---

### 2. Full Port Scan

```bash
nmap -p- -T4 <target>
```

Find hidden services

---

### 3. Detailed Scan

```bash
nmap -sV -sC -p <ports> <target>
```

Gets version info

Runs scripts

---

### 4. UDP Scan

```bash
nmap -sU --top-ports 20 <target>
```

Checks common UDP services

---

### 5. Run Targeted NSE Scripts

Example

```bash
nmap --script smb-vuln* -p 445 <target>
```

---

## Full Example

```bash
nmap -sS -sV -sC -O -p- -T4 -oA fullscan 10.10.10.10
```

Meaning

-sS → SYN Scan

-sV → Version Detection

-sC → Default Scripts

-O → OS Detection

-p- → Scan all ports

-T4 → Aggressive timing

-oA → Save all output formats

---

## Important Points

Nmap is used for

- Host discovery
- Port scanning
- Service enumeration
- OS detection
- Vulnerability detection

Most common flags

`-sS`

`-sV`

`-sC`

`-O`

`-Pn`

`-p-`

`-T4`

Typical workflow

Quick Scan → Full Port Scan → Service Enumeration → NSE Scripts