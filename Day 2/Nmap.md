abbreviation - network mapper 

provides information about open ports, operating systems, and other details.

use man page to know more about flags in nmap

`nmap -h` / `man nmap`

## TCP Connect Scans

tcp - 3 way handshake -{ syn - {syn and ack} from the server - ack} 
![[Pasted image 20260723182716.png]]

 TCP Connect scan works by performing the three-way handshake with each target port or in other words Nmap tries to connect to each specified TCP port, and determines whether the service is open by the response it receives.
