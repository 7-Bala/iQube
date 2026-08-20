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
