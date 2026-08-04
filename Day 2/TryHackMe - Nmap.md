# Nmap — Full Study Guide

Source module: TryHackMe "Network Exploitation Basics" → Room: Nmap (furthernmap)

**Legal/ethical note first, because it matters:** Nmap is a scanning tool that actively probes hosts. Only ever run it against systems you own or are explicitly authorized to test (like TryHackMe's lab machines). Scanning systems you don't have permission to test is illegal in most jurisdictions (e.g. under the UK Computer Misuse Act or the US CFAA).

---

## 1. What Nmap is

Nmap ("Network Mapper") is the industry-standard tool for host discovery and port scanning. Given a target, it tells you: which hosts are alive, which ports are open/closed/filtered, what service and version is running on each open port, sometimes the OS, and — via its scripting engine — can run deeper enumeration and even vulnerability checks. Install on Debian/Ubuntu-based systems with `sudo apt install nmap`.

## 2. Core terminology

- **Open**: an application is actively accepting connections on that port.
- **Closed**: the port is reachable (host responds) but nothing is listening — TCP replies with RST.
- **Filtered**: Nmap can't tell if the port is open because a firewall/ACL/router is dropping probes (no response, or an ICMP error) — the most ambiguous and often the most interesting state, because it hints a firewall is present.
- **Unfiltered**: reachable but Nmap can't determine open/closed (rare, seen with ACK scans).
- **Open|filtered**, **Closed|filtered**: Nmap can't distinguish between the two states, usually because no response was received (common in UDP scanning).

## 3. Host discovery vs port scanning

By default Nmap first tries to determine if a host is "up" (ping-like discovery: ICMP echo, TCP SYN to port 443, TCP ACK to port 80, ICMP timestamp request) before bothering to port-scan it. If a target's firewall silently drops all of these probes, Nmap will report the host as down and skip it — even though it might genuinely be reachable on a specific port. `-Pn` disables host discovery and forces Nmap to treat every target as up, going straight to port scanning. This is one of the most important flags to remember on CTF/pentest targets that block ICMP.

## 4. Scan types — how Nmap actually probes a port

| Flag | Name | Needs root? | How it works | Notes |
|---|---|---|---|---|
| `-sS` | TCP SYN scan ("half-open") | Yes | Sends SYN; if SYN-ACK comes back, port is open — Nmap sends RST instead of completing the handshake | Default scan when run as root; fast and relatively stealthy since it never completes a full connection (less likely to be logged by the application) |
| `-sT` | TCP Connect scan | No | Uses the OS's normal `connect()` system call — completes the full three-way handshake | Default when *not* root; slower and more likely to be logged, since it's a full legitimate connection |
| `-sU` | UDP scan | Yes (for best results) | Sends UDP probes; open ports may respond or stay silent, closed ports typically trigger an ICMP "port unreachable" | Much slower than TCP scans (UDP has no handshake to confirm state, and ICMP rate-limiting slows Nmap way down); often combined with `-sS` via `-sU -sS` |
| `-sN` | TCP Null scan | Yes | Sends a packet with **no flags set at all** | Relies on RFC 793: closed ports must reply RST, open ports should stay silent. Doesn't work reliably against Windows, which ignores the RFC here |
| `-sF` | TCP FIN scan | Yes | Sends a packet with only the FIN flag set | Same RFC 793 logic as Null scan; used to slip past simple stateless firewalls that only block SYN packets |
| `-sX` | TCP Xmas scan | Yes | Sends a packet with FIN, PSH, and URG flags set ("lit up like a Christmas tree") | Same evasion logic as Null/FIN |
| `-sA` | TCP ACK scan | Yes | Sends only ACK — used to map firewall rule sets (stateful vs stateless), not to determine open/closed directly | Reports "unfiltered" not "open" |
| `-sV` | Version detection | No | Probes open ports with protocol-specific queries and matches responses against Nmap's `nmap-service-probes` database | Tells you *what* is running (e.g. "vsftpd 2.3.4") not just that a port is open — critical for finding known CVEs |
| `-O` | OS detection | Yes | Fingerprints TCP/IP stack quirks (TTL, window size, options ordering) to guess the target OS | Educated guess, not certain |
| `-sC` | Default script scan | No | Runs the NSE scripts flagged "default" (safe, generally useful) against discovered open ports | Shortcut for `--script=default` |
| `-A` | Aggressive scan | Yes (for full effect) | Combines `-sV -sC -O --traceroute` | Convenient but noisy/slow — great for CTFs, loud for real pentests |

**Why the Null/FIN/Xmas scans can bypass simple firewalls**: many basic stateless packet filters are configured only to block incoming packets with the SYN flag set (since that's what "starts" a connection). A packet with no SYN — like a Null, FIN, or Xmas probe — can sail straight through that rule even though it's still probing the port. Modern stateful firewalls generally aren't fooled by this, but it's a classic technique worth understanding.

## 5. Port specification

| Flag | Meaning |
|---|---|
| `-p 80` | Scan just port 80 |
| `-p 80,443,8080` | Scan a specific list |
| `-p 1-1000` | Scan a range |
| `-p-` | Scan all 65535 ports |
| `-p U:53,111,T:21,25,80` | Mix UDP and TCP port sets in one scan |
| `--top-ports 100` | Scan Nmap's 100 statistically most common ports |
| (no `-p`) | Default: the 1000 most common TCP ports |

A full `-p- -sV -sC` scan of every port is slow but is the only way to be sure you haven't missed a service running on an unusual port — very common in CTF machines, where the "real" service is hidden on a high port precisely to make you run a full scan.

## 6. The Nmap Scripting Engine (NSE)

NSE scripts (written in Lua) extend Nmap far past "is this port open." Scripts live in categories:

| Category | Purpose |
|---|---|
| `default` | Safe, broadly useful scripts run by `-sC`/`-A` |
| `discovery` | Gather more info about the target/network |
| `safe` | Won't crash the target or use excessive bandwidth |
| `intrusive` | May crash services or trip IDS — use with care |
| `vuln` | Checks for specific known vulnerabilities |
| `exploit` | Actually attempts exploitation |
| `auth` | Tests for authentication-related issues (e.g. default creds) |
| `brute` | Brute-forces credentials |
| `version` | Assists version detection |

Usage:

```
nmap --script default -sV <target>
nmap --script vuln <target>
nmap --script smb-os-discovery,smb-enum-shares -p 445 <target>
nmap --script "http-*" -p 80 <target>
```

You'll use targeted scripts constantly in the Network Services rooms — e.g. `smb-vuln-*` for SMB CVEs, `ftp-anon` to check for anonymous FTP login, `smtp-commands`/`smtp-enum-users` for mail server enumeration, `mysql-info` for MySQL banners.

## 7. Timing templates

| Flag | Name | Behavior |
|---|---|---|
| `-T0` | Paranoid | Extremely slow, one probe at a time with large delays — for IDS evasion |
| `-T1` | Sneaky | Very slow, IDS evasion |
| `-T2` | Polite | Slower than default, reduces load on the target |
| `-T3` | Normal | Default speed |
| `-T4` | Aggressive | Faster, assumes a reliable/fast network — commonly used on CTFs and internal pentests |
| `-T5` | Insane | Maximum speed, sacrifices accuracy, easily missed ports on slow links |

`-T4` is the practical default for lab/CTF environments where you're not worried about detection. Fine-grained control also exists via `--min-rate`, `--max-rate`, `--scan-delay`, and `--max-retries`.

## 8. Firewall and IDS evasion

| Flag | Purpose |
|---|---|
| `-f` | Fragments probe packets into smaller pieces to slip past packet-inspecting firewalls/IDS that don't reassemble fragments |
| `-D decoy1,decoy2,ME,...` | Sends decoy packets from spoofed source IPs alongside your real scan, so the target's logs show many "attackers" | 
| `-S <spoofed-ip>` | Spoofs the source IP of your scan (mostly only useful if you control the return path, e.g. same LAN) |
| `--spoof-mac <mac/vendor/0>` | Spoofs your MAC address for local-network scans |
| `-sI <zombie-host>` | Idle/zombie scan — bounces the scan off a third idle host to fully hide your own IP, using that host's IP ID sequence to infer port states |
| `--data-length <n>` | Appends random data to packets to change their signature |
| `-g <port>` / `--source-port <port>` | Forces a specific source port (e.g. 53 or 88), sometimes trusted implicitly by poorly configured firewalls |

## 9. Output formats

| Flag | Format |
|---|---|
| `-oN file.txt` | Normal human-readable output |
| `-oX file.xml` | XML (machine-parseable, feeds into other tools) |
| `-oG file.gnmap` | Grepable format (legacy but still handy for quick `grep`/`awk`) |
| `-oA basename` | All three formats at once, using the same base filename |

Saving output is good practice on any real engagement (or long CTF) so you don't have to re-scan.

## 10. Verbosity and useful miscellaneous flags

| Flag | Meaning |
|---|---|
| `-v` / `-vv` | Increase verbosity (see results as they're found, not just at the end) |
| `-n` | Skip DNS resolution (faster) |
| `-Pn` | Skip host discovery, treat target as up |
| `--reason` | Show *why* Nmap decided a port is in a given state (e.g. "syn-ack" vs "no-response") |
| `--open` | Only show ports found open (hides closed/filtered noise) |
| `-6` | Scan using IPv6 |

## 11. A realistic scanning workflow

A methodology that generalizes well, e.g. for the Network Services rooms:

1. **Quick scan** to get oriented: `nmap -sV -sC -T4 <target>` — top 1000 TCP ports with version and default scripts.
2. **Full port scan** to make sure nothing is hidden on an unusual port: `nmap -p- -T4 <target>` (or add `--min-rate 1000` to speed it up on a stable link).
3. **Feed discovered ports back in** for full detail on exactly those ports: `nmap -sV -sC -p <comma-separated-ports> <target>`.
4. **UDP scan** on top ports, since it's slow: `nmap -sU --top-ports 20 <target>`.
5. **Targeted NSE scripts** once you know the service, e.g. `--script smb-vuln* -p 445 <target>` if SMB is open.

Example command putting several flags together:

```
nmap -sS -sV -sC -O -p- -T4 -oA fullscan 10.10.10.10
```

This is a SYN scan, with version detection, default scripts, OS detection, across all 65535 ports, at aggressive timing, saving output to `fullscan.*`.

---

## Key takeaways

Nmap answers four questions in increasing detail: is the host up, which ports are open, what's running on them, and (via NSE) are there known issues with what's running. The scan type you choose (`-sS` vs `-sT` vs `-sU`) determines *how* Nmap decides open/closed; timing and evasion flags control *how loud* the scan is; NSE turns Nmap from a port scanner into a lightweight vulnerability scanner. Everything you find here — an open port 21, 445, 2049, 25, or 3306 — is the entry point for the next two rooms.
