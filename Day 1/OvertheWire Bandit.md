## L0 to L1

ls - list files in the working dir
cat - display full content of the file
pass: 6y2kwnwK6grgvwvpvLaa2T1cpFEKOhNR

## L1 to L2

`cat ./-` : to open a file with name - (hyphen) (dashed filenames)
pass: PK8fYLZg2hnHSz83plBL1iEPKdD3QToB

## L2 to L3

`cat "./--spaces in this filename--"` : display the content of the filename with dashes and spaces
pass: 7ZZ2LFrykP2zEyvBl4m3clcL7tGYJPME

## L3 to L4

`ls -a` : to reveal hidden files
`cat .hiddenfilename` 
pass: xzTXq1rDJQVVAzdv5cHq1TQytTWufAMq

## L4 to L5

`cat ./-file07` : this has the ascii text 
pass: 6C7h9GD8M6ai5nr7wo1RonrzFjj9yIrG

## L5 to L6

`bandit5@bandit:~$ cd inhere`
`bandit5@bandit:~/inhere$ cd maybehere07`
`bandit5@bandit:~/inhere/maybehere07$ cat .file2`
`pXa26xhMWaC2SvDotA4r9EgZkulOeSBW`

pass: pXa26xhMWaC2SvDotA4r9EgZkulOeSBW

## L6 to L7

`find / -type f -user bandit7 -group bandit6 -size 33c` 
`cat /var/lib/dpkg/info/bandit7.password`

pass: Bmnnvf82KzQlfxgAI2d1zYbr1u9pr3E3

## L7 to L8

`grep millionth data.txt`
grep {target word} {file name}
pass: VR1ljMayciFxbnUokuQmJFw6QC9VKtub

## L8 to L9

use | to skip multi line commands
`cat data.txt | sort | uniq -u`

pass: EjmOSvuAu7sGAHqHVcBDPirRe9T03kxl

## L9 to L10

`cat data.txt | strings -e s | grep ==`
-e : encoding , s : ascii (7 bit)

pass : B0s2khmbT9u0geKuOoVGW3JZKhndE3BG

## L10 to L11

`base64 data.txt -d`

-d : decode
pass: pYfOY6HwUsDj5rL9UvyhU7MCmv8vN5Ro

## L11 to L12

cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'

`tr` command maps characters one-to-one based on their **exact position** in the first string to their position in the second string.

pass: GROozWPO8QyN0mGrjUkID0WCYkZiQxrN

## L12 to L13

😪😵
file {filename}- file type
mv {existing file} {new file name}

For decompressing it, 
`tar -xf data6`
`gzip -d data8.gz`
`bzip2 -d data6.bz2`

pass: qQYQiHOBPR8zR61qxYqX45quvihF2uzk

## L13 to L14

