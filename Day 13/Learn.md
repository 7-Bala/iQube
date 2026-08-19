## Topic 6 — Permissions: Read, Write, Execute

r - read
w - write
x - execute


in -rwxr-xr--

-rwx    r-x    r--
│     │      │       │
│     │      │      └── others
│     │     └───────── group
│    └──────────────── owner └────────────────────── file type

### rwx - different for dirs

![[Pasted image 20260819183206.png]]

to overwrite the entire file :
echo "target text" > secret.txt

to append the target text at the end of the file :
echo "target text" >> secret.txt

