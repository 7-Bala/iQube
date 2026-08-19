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

```
d rwx r-x ---
│ │   │   │
│ │   │   └── others
│ │   └────── group
│ └────────── owner
└──────────── directory
```

So:

```
d    → directory
rwx  → owner permissions
r-x  → group permissions
---  → others permissions
```

### Compare with a file

```
-rw-r--r--  bala  developers  notes.txt
drwxr-x---  bala  developers  myfolder
```

The difference is the **first character**:

```
- → regular file
d → directory
```



## chmod 

chmod = change mode
syntax - chmod [permissions] [file/directory]

For example:

-rw-r--r--

can be changed to:

-rwxr-x---

2 methods:
- numeric - chmod 755 script.sh
- Symbolic - chmod u+x script.sh

in symbolic : + add permission, - remove permission, + overwrites the permission
chmod o+r file
chmod g-w file
chmod u=rw file.txt

multiple permissions:
chmod u+rw file

also multiple categories:
chmod ug+rw file

a means everyone

==Instead of: chmod u+r,g+r,o+r file==

==you can use: chmod a+r file==

## Numeric mode

r = 4
w = 2
x = 1
![[Pasted image 20260819190443.png]]

7 → rwx
6 → rw-
5 → r-x
4 → r--
3 → -wx
2 → -w-
1 → --x
0 → ---

chmod

symbolic:
u = owner
g = group
o = others
a = all

+ = add
- = remove
= = set exactly

numeric:
r = 4
w = 2
x = 1

