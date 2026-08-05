Reconnaissance - info gathering b4 any attack / pentest 

info gathered include - versions - so look for any known CVEs in that version 
- what it is, how it's built, what's exposed, who runs it

---
2 types :
- passive - gathering info without directly interacting with the target (so they don't notice you). Public records, search engines, social media, DNS history, leaked data, etc.

- active - directly probing the target (port scans, sending requests, poking the app). Noisier, detectable, but gets deeper technical info.

passive first to map the attack surface quietly, then active to confirm and dig deeper.

---

#### 1. General / Software recon

- **OSINT** (Open Source Intelligence): company info, employee names/emails (for phishing/social engineering), GitHub repos (leaked keys, internal logic), job postings (reveal tech stack — "looking for a Django + AWS engineer" tells you a lot)
- **Google dorking**: `site:`, `filetype:`, `inurl:` to find exposed files, admin panels, docs
- **Metadata extraction**: PDFs/images often leak usernames, software versions, internal paths (`exiftool`)
- **Version/tech fingerprinting**: identifying frameworks, languages, libraries in use — old versions = known CVEs

#### 2. Web app recon

- **Subdomain enumeration**: `subfinder`, `amass`, crt.sh (certificate transparency logs) — finds forgotten/staging subdomains that are often less secured
- **Directory/endpoint brute-forcing**: `gobuster`, `ffuf`, `dirsearch` — finds hidden pages, admin panels, API routes
- **Technology fingerprinting**: `whatweb`, `Wappalyzer` — CMS, JS frameworks, server headers
- **robots.txt / sitemap.xml**: often lists paths devs didn't want indexed (ironically a recon goldmine)
- **JS file analysis**: bundled JS often contains API endpoints, hardcoded keys, internal comments
- **Wayback Machine / archive.org**: old versions of the site may expose since-removed but still-live functionality
- **Burp Suite** (you already use this): spidering/crawling the app to map every request it makes, parameters, cookies, auth flow

#### 3. Desktop app recon

- **Static analysis**: decompiling/disassembling the binary (Ghidra, IDA, dnSpy for .NET) to read logic without running it
- **Strings analysis**: `strings binary.exe` — often leaks hardcoded credentials, URLs, debug messages
- **Dependency/library enumeration**: what DLLs/libraries it links against, their versions (known vulns?)
- **Dynamic analysis**: running it under a debugger/sandbox, monitoring file system + registry + network calls it makes (Process Monitor, Wireshark)
- **Config/installer inspection**: install directories often leave config files, logs, or credentials in plaintext

#### 4. Hardware recon

- **Physical inspection**: chip identification (reading part numbers off ICs), locating debug headers (JTAG, SWD, UART pins) on the PCB
- **Datasheet hunting**: once you ID a chip, its datasheet tells you memory layout, protocols, known weaknesses
- **Interface probing**: multimeter/logic analyzer to find unlabeled pins, identify voltage levels and protocols
- **Firmware extraction points**: flash chips can often be dumped directly (chip-off) or via exposed programming interfaces
- **Side-channel surfaces**: power/EM analysis points, timing behavior — mostly advanced, but recon starts with just knowing they exist on the board

#### 5. Embedded systems recon (this overlaps with hardware + software)

- **Firmware analysis**: extract and unpack firmware images (`binwalk`) to pull out filesystems, configs, hardcoded creds, private keys
- **Communication protocol identification**: what does it speak — UART, I2C, SPI, CAN, Zigbee, BLE, MQTT? Each has its own attack surface
- **Bootloader/update mechanism recon**: how does it receive firmware updates — signed? over-the-air? Can it be intercepted or downgraded?
- **Debug interface discovery**: JTAG/SWD often left enabled in production (huge oversight) — gives full memory/register access
- **Known chipset vulnerabilities**: since embedded devices reuse SoCs (ESP32, STM32, etc. — you already work with ESP32), checking public CVEs for that specific chip/module is a recon step in itself