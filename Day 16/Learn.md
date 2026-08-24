# Study Notes
# Absolute vs Relative Paths

```
/ -> root dir
so every filesystem starts from the root dir

absolute path - /home/bala/Documents/project.txt
starts from / and describes the complete location

relative path - Documents/project.txt
doesnt start with /

Difference:

Absolute:
/home/bala/Documents/project.txt

Relative:
Documents/project.txt

The absolute path says: start at /

The relative path says: start wherever i currently am.

~ - home directory

to return to home dir:
cd
cd ~
```

|Feature|Absolute|Relative|
|---|---|---|
|Starts from|`/`|Current directory|
|Depends on where you are?|No|Yes|
|Can point to same file?|Yes|Yes|
|Convenient for nearby files?|Less|More|
|Good for exact location?|Yes|Not always|

# Files vs Directories

```
file - named object that stores data

touch file.txt
creates an empty file

echo "Hello Linux" > file.txt
puts the text inside the double quotes into the file

cat file.txt
prints the contents of the file

directory - filesystem object used to organize and reference other filesystem objects

mkdir folder
creates a directory

ls
shows the contents of a directory

filesystem - method and data structure used by the OS to store, organize and retrieve data
```

# Hidden Files

```
files/directories starting with . are hidden

.example
.config

ls
doesnt show hidden files

ls -a
shows hidden files also

ls -la
shows hidden files + permissions, owner, group, size, date/time and name
```

# File Ownership

```
every file/directory has:

owner
group
others

owner - user who owns the file

group - group associated with the file

others - everyone else

ls -l
shows owner and group
```

Example:

```
-rw-r--r-- 1 bala staff 42 Aug 18 18:30 notes.txt

bala  -> owner
staff -> group
42    -> file size in bytes
Aug 18 18:30 -> modified date and time
notes.txt -> file name
```

# Users

```
user - account that can log into or use the Linux system

each user has:
username
UID (user ID)

whoami
shows the current user

id
shows user ID and group information
```

# Groups

```
group - collection of users

used to give permissions to multiple users at once

primary group - default/main group of a user

secondary group - additional group a user belongs to

groups
shows the groups the current user belongs to

id
shows user + group information
```

# Permissions

```
r -> read
w -> write
x -> execute

permissions are divided into:

owner
group
others
```

Example:

```
-rw-r--r--
```

```
-   -> normal file

rw- -> owner
r-- -> group
r-- -> others
```

```
owner -> read + write
group -> read
others -> read
```

For directories:

```
r -> list directory contents
w -> create/delete/rename entries
x -> enter/traverse the directory
```

# chmod

```
chmod -> change permissions

symbolic:

u -> owner
g -> group
o -> others
a -> all

+ -> add
- -> remove
= -> set exactly
```

Example:

```
chmod u+x file.txt
adds execute permission for owner

chmod g-w file.txt
removes write permission from group
```

Numeric:

```
r = 4
w = 2
x = 1

7 = rwx
6 = rw-
5 = r-x
4 = r--
0 = ---
```

Example:

```
chmod 755 file

755
│││
││└-> others
│└--> group
└---> owner
```

```
755 -> rwxr-xr-x
```

# chown

```
chown -> change ownership

changes the owner of a file/directory

sudo chown user file

change owner and group:

sudo chown user:group file

chgrp -> change group

sudo chgrp group file
```

# umask

```
umask -> default permission restriction for newly created files/directories

doesnt change existing files

file starts from 666
directory starts from 777

umask removes permissions from these defaults
```

Example:

```
umask 022

file:
666 -> 644 -> rw-r--r--

directory:
777 -> 755 -> rwxr-xr-x
```

Another example:

```
umask 077

file:
666 -> 600 -> rw-------

directory:
777 -> 700 -> rwx------
```

```
umask -> affects NEW files/directories

chmod -> changes EXISTING permissions
```

# SUID

```
SUID -> Set User ID

mainly used on executable files

when a SUID executable runs:
-> it uses the effective user identity of the file owner

SUID -> User

SUID numeric value:
4000
```

Normal:

```
-rwxr-xr-x
```

SUID:

```
-rwsr-xr-x
```

```
s -> SUID + execute
S -> SUID without execute
```

Example:

```
chmod 4755 program

4 -> SUID
755 -> normal permissions
```

# SGID

```
SGID -> Set Group ID

SGID -> Group

on executable:
-> program uses the effective group identity of the file group

on directory:
-> new files/directories inherit the directory's group

SGID numeric value:
2000
```

Normal directory:

```
drwxrwxr-x
```

SGID directory:

```
drwxrwsr-x
```

```
s -> SGID + execute
S -> SGID without execute
```

Example:

```
chmod 2775 project/

2 -> SGID
775 -> normal permissions
```

# SUID vs SGID

```
SUID -> User
SGID -> Group

SUID = 4000
SGID = 2000

SUID executable
-> uses file owner's effective user identity

SGID executable
-> uses file group's effective group identity

SGID directory
-> new files inherit the directory's group
```