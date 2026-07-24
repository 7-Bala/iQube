## Recap: Tasks 1 to 6 (TCP & Core Nmap Fundamentals)

Before diving into UDP, Nmap focuses on how TCP connections work and how we can manipulate them to map out a target network.

- **Task 1 & 2 (Deploy & Introduction):** Nmap (Network Mapper) is the industry-standard tool for network reconnaissance, used to discover live hosts, open ports, running services, and operating systems.
    
- **Task 3 (Nmap Switches):** Command-line flags control how Nmap behaves. Key switches include:
    
    - `-p`: Specify ports (e.g., `-p 80,443`, `-p-` for all 65,535 ports, or `--top-ports 100`).
        
    - `-v` / `-vv`: Increase verbosity to see live updates during the scan.
        
    - `-O`: Enable Operating System detection.
        
    - `-sV`: Enable service/version detection to see what software is running on open ports.
        
    - `-A`: Aggressive mode (combines OS detection, version detection, script scanning, and traceroute).
        
- **Task 4 (Scan Types Overview):** Nmap categorizes ports into three primary states:
    
    - **Open:** A service is actively listening and accepting connections.
        
    - **Closed:** The port is accessible, but no service is listening on it.
        
    - **Filtered:** A firewall or packet filter is blocking traffic, preventing Nmap from determining whether the port is open or closed.
        
- **Task 5 (TCP Connect Scans - `-sT`):**
    
    - Relies on the operating system's network stack to complete a full **3-way handshake** (`SYN` $\rightarrow$ `SYN/ACK` $\rightarrow$ `ACK`).
        
    - Once the connection is established, Nmap gracefully closes it (`RST/ACK`).
        
    - **Pros:** Reliable and does not require root/sudo privileges.
        
    - **Cons:** Very noisy and easily logged by firewalls and target applications.
        
- **Task 6 (SYN "Stealth" Scans - `-sS`):**
    
    - The default scan type when running Nmap with root/sudo privileges (`sudo nmap`).
        
    - Performs a **half-open** scan: It sends a `SYN` packet and waits for a `SYN/ACK`. Instead of sending an `ACK` to finish the connection, Nmap immediately sends a `RST` (Reset) packet to tear it down before the target application logs a completed connection.
        
    - **Pros:** Faster and much stealthier than Connect scans.
        
    - **Cons:** Requires root privileges (to craft raw packets) and can still be detected by modern Intrusion Detection Systems (IDS).



## Cont.

tcp - stateful - 3 way handshake
udp- stateless - fires packets continously - ***HOPE***

## Nmap UDP Scans

1. Open - a udp payload is received - occurance : **Very rare.** If the listening service actually sends back a UDP data packet in reply, Nmap knows with 100% certainty that the port is active and labels it **open**.

2. Open | Filtered - No Response ,Timeout - Because UDP doesn't require acknowledgments, a lack of response could mean two things:
				1. The packet reached an open port, but the service simply ignored it.
				2. A firewall silently dropped the packet.
	since Nmap cannot tell which scenario occurred—even after sending a second check packet—it marks the port as **open|filtered**.

3. Closed - ICMP Port Unreachable 

## Optimize UDP Scans

`sudo nmap -sU --top-ports 20 <target>`

scanning every udp port is very slow , so good practices involve like scanning the frequently used ports , so the flag for that is --top-ports


