## Absolute vs relative paths

/ -> root dir
so every filesystem starts from the root dir 

absolute path - /Users/bala/Documents/Tocsin.zip
			starts from /  and decribes the complete location
relative path - Documents/Tocsin.zip
			doesnt start with / 

Difference :
Absolute:
 /home/bala/Documents/project.txt
 

Relative: Documents/project.txt
 
The absolute path says: start at /

The relative path says: start wherever i currently am.

to return to home dir : press cd or cd .. or cd ~ and hit enter

~ - home directory

| Feature                               | Absolute | Relative          |
| ------------------------------------- | -------- | ----------------- |
| Starts from                           | `/`      | Current directory |
| Depends on where you are?             | No       | Yes               |
| Can point to same file?               | Yes      | Yes               |
| Convenient for nearby files?          | Less     | More              |
| Good for exact/unambiguous locations? | Yes      | Not always        |

## Files vs Directories

file - named object that stores data

touch creates an empty file with name specified

echo "Hello Linux" > notes.txt put the text inside the double quotes in the specifed file 

cat prints the output

directory - A **directory** is a filesystem object used to organize and reference other filesystem objects.

filesystem - A file system is ==the method and data structure that an operating system uses to control how data is stored, organized, and retrieved on storage devices like hard drives, SSDs, and USB sticks==. Without it, data would be a single chaotic mass of blocks.

![[Pasted image 20260818185527.png]]
