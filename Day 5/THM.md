
## Nmap (Cont.)

recap - covered upto syn scans last sesh. continuing from here

## UDP Scans

flag -sU

udp - stateless , no handshake , just fires the packet n hopes

open - reply actually came back (rare)

open|filtered - no reply at all , could mean port open n ignoring it , or firewall silently dropped it. nmap cant tell diff so both get lumped together

closed - target sends back ICMP "port unreachable"

very slow , ~20 min just for first 1000 ports. always scope it down

`sudo nmap -sU --top-ports 20 <target>`

## NULL Scan

flag -sN

no flags set at all

if port closed -> target sends RST back

if port open -> no response at all (same ambiguity as udp, so nmap just shows it as open|filtered)

## FIN Scan

flag -sF

just the FIN flag set (normally used to close a connection gracefully)

same as NULL - RST if closed, no response if open|filtered

## Xmas Scan

flag -sX

PSH + URG + FIN flags set together

looks like a lit up christmas tree in wireshark hence the name

same as NULL - RST if closed, no response if open|filtered

windows + cisco gear ignore the rfc n RST everything regardless

useless against those systems

point of all these = bypass firewalls blocking incoming SYN packets specifically

## ICMP Ping Sweep

maps which hosts on a network actually alive

flag -sn ( skips port scan entirely )

`nmap -sn 192.168.0.0/24`

also fires TCP SYN to port 443 + TCP ACK (or SYN if not root) to port 80 , along with the ICMP echo

if root + same subnet , nmap uses ARP instead - more reliable , cant really firewall ARP on local net

## NSE Scripts

nmap scripting engine , scripts written in Lua. recon to full exploits

categories - safe , intrusive , vuln , exploit , auth , brute , discovery

running - `--script=<category>` or `--script=<name>` , comma separated for multiple

scripts needing args - `--script-args` , format is `script.argument`

built in help - `nmap --script-help <script-name>`

scripts live at `/usr/share/nmap/scripts`

finding one - `grep "ftp" /usr/share/nmap/scripts/script.db`

or `ls -l /usr/share/nmap/scripts/*ftp*`

missing a script - `sudo apt install nmap` usually fixes it

or wget the .nse file manually into the folder + run `nmap --script-updatedb` after

windows blocks ICMP by default -> nmap thinks host dead , skips scanning it entirely

fix - `-Pn` , skip the ping check , just assume host alive ( slower tho )

other evasion switches -

`-f` - fragments packets , harder for firewall or IDS to inspect

`--mtu <n>` - same idea , control fragment size ( must be multiple of 8 )

`--scan-delay <time>ms` - spaces out packets , dodges time based IDS triggers

`--badsum` - sends bad checksum. real hosts drop it but lazy firewalls reply anyway - tells u one's there

## Practical

ran everything hands on against lab machine -

ping check

xmas scan

SYN scan on first 5000 ports

wireshark capture of a tcp connect scan

ftp-anon script against port 21

room done. good base before moving into actual exploitation rooms