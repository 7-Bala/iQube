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