
## Nmap (Cont.)

recap: covered upto SYN scans last session. continuing from here onward.

## UDP Scans
flag: -sU
udp - stateless, no handshake, just fires the packet and hopes

open - actually got a reply back (rare)
open|filtered - no reply at all, could mean the port's open and ignoring it, or a firewall dropped it silently. nmap can't tell the difference so it lumps both together
closed - target sends back an ICMP "port unreachable"

slow as hell, ~20 min just for the first 1000 ports. always scope it down:
`sudo nmap -sU --top-ports 20 <target>`

## NULL / FIN / Xmas Scans
stealthier scans, all expect a RST back if the port's closed

NULL (-sN) - no flags set at all
FIN (-sF) - just the FIN flag (normally closes a connection gracefully)
Xmas (-sX) - PSH + URG + FIN together, lights up like a christmas tree in wireshark, hence the name

open port = no response at all, same ambiguity as UDP. so these only ever show open|filtered / closed / filtered, never a clean "open"

windows and a lot of cisco gear ignore the RFC and RST everything regardless, so these scans are useless against those

point of these = bypass firewalls that specifically block incoming SYN packets

## ICMP / Ping Sweep
used to map which hosts on a network are actually alive

flag: -sn (skips port scanning entirely)
`nmap -sn 192.168.0.0/24`

also fires a TCP SYN to port 443 and a TCP ACK (or SYN if not root) to port 80, alongside the ICMP echo

if root + on the same local subnet, nmap uses ARP instead - way more reliable since ARP can't really be firewalled off locally

## NSE Scripts
nmap scripting engine, scripts written in Lua. does everything from basic recon to full exploits

categories: safe, intrusive, vuln, exploit, auth, brute, discovery

running: `--script=<category>` or `--script=<name>`, comma separated for multiple
scripts needing args: `--script-args`, format is `script.argument`
built in help: `nmap --script-help <script-name>`

scripts live at `/usr/share/nmap/scripts`
finding one: `grep "ftp" /usr/share/nmap/scripts/script.db` or `ls -l /usr/share/nmap/scripts/*ftp*`
missing a script: `sudo apt install nmap` usually covers it, or manually wget the .nse file into the folder + run `nmap --script-updatedb` after

## Firewall Evasion
windows blocks ICMP by default -> nmap thinks the host is dead and skips scanning it entirely

fix: `-Pn` - skip the ping check, just assume the host's alive (slower, but gets around the block)

other evasion switches:
`-f` - fragments packets, harder for firewall/IDS to inspect
`--mtu <n>` - same idea, lets you control fragment size (must be a multiple of 8)
`--scan-delay <time>ms` - spaces out packets, dodges time-based IDS triggers
`--badsum` - sends a bad checksum. real hosts drop it, but lazy firewalls reply anyway - tells you one's there

## Practical
ran everything hands on against the lab machine - ping check, xmas scan, SYN scan on first 5000 ports, wireshark capture of a TCP connect scan, ftp-anon script against port 21

room done. good base for enumeration before moving into actual exploitation rooms
