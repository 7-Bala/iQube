# General Skills

## Undo 

Approach : 

first run `nc foggy-cliff.picoctf.net 59915`
				|
		step 1 flag appears 

base64 encoded string -> decode it -> `base64 -d`

reverse it - rev 

to replace - tr 'target' 'string to be replaced'
eg : `tr '-' '_'`
tr '()' '{}' 

==rot13== - ==a simple letter substitution cipher that replaces a letter with the 13th letter after it in the alphabet==

`tr 'A-Za-z' 'N-ZA-Mn-za-m'`

![[Pasted image 20260806181323.png]]



## MY GIT

checked out the readme.txt on the home dir , but the intended readme was inside challenge folder so, `cd challenge -> cat readme.md`


Only flag.txt pushed by ```root:root@picoctf``` will be updated with the flag.
						|
so config user and user mail provided user :root ,mail :root@picoctf
						|
			git config user.name "root"
			git config user.email "root@picoctf"
			file name shouldbe strictly file.txt push it and write inside it echo "hi" > flag.txt
						|
			stage the files : git add flag.txt
			commit the files : git commit -m "commit msg"
			push it to the remote : git push origin master

## bytemancy 1

```
python3 -c "print('e' * 1751)" | nc foggy-cliff.picoctf.net 56090
```
-c is used to run without saving the file 