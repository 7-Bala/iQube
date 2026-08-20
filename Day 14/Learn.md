## chown

usually needs sudo

chown = change owner

used to change ownership of the dir 

wkt every file or dir has owner, grp, permissions

basic syntax - chown USER file

Example:

```
sudo chown alice notes.txt
```

new owner for the file notes.txt is alice

to change the owner and group simultaneously 
chown USER:GROUP file

example : sudo chown alice:developers notes.txt
now : owner → alice
	  group → developers

tha above can be also achived with chgrp developers notes.txt

chown alice file
→ change owner

chown alice:developers file
→ change owner + group

chown :developers file
→ change group

chgrp developers file
→ change group

## UMask

Umask (user file-creation mode mask) is ==a command and setting in Unix-like operating systems that defines the default permission bits subtracted from newly created files and directories==. It acts as a safety net to restrict default access rights.

umask = default permission restriction for newly created files/directories

It does NOT change existing files.

File starts from:
666 → rw-rw-rw-

Directory starts from:
777 → rwxrwxrwx

umask removes permission bits from these defaults.

Common examples:

umask 022

File:
666 → 644 → rw-r--r--

Directory:
777 → 755 → rwxr-xr-x


umask 077

File:
666 → 600 → rw-------

Directory:
777 → 700 → rwx------


umask 002

File:
666 → 664 → rw-rw-r--

Directory:
777 → 775 → rwxrwxr-x


chmod → changes permissions of existing files/directories
umask → controls default permissions of newly created files/directories

Check current umask:
umask

Change umask:
umask 077

## SUID

SUID = Set User ID

Used mainly on executable files.

When a SUID program runs:
→ program runs with the effective user identity of the file owner

Example:
file owner = root
SUID program → runs with root's effective privileges

SUID appears in the owner's execute position:

Normal:
-rwxr-xr-x

SUID:
-rwsr-xr-x

s = SUID + execute

SUID without execute:
-rwSr-xr-x

S = SUID without execute

SUID numeric value:
4000

Example:
chmod 4755 program

4755:
4 → SUID
755 → normal permissions

## SGID

SGID = Set Group ID

On executable files:
→ program runs with the effective group identity of the file's group

On directories:
→ new files/directories inherit the directory's group

Normal directory:
drwxrwxr-x

SGID directory:
drwxrwsr-x

s = SGID + execute

SGID without execute:
S = SGID without execute

SGID numeric value:
2000

Example:
chmod 2775 project/

2775:
2 → SGID
775 → normal permissions

##  SUID vs SGID

SUID → User
SGID → Group

SUID = 4000
SGID = 2000

SUID executable:
→ uses file owner's effective user identity

SGID executable:
→ uses file group's effective group identity

SGID directory:
→ new files inherit directory's group