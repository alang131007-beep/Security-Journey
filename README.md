# Security-Journey
"In this repo I record my beginner experience with cybersecurity  and my progress toward a career in AI infrastructure security."

Cybersecurity — Phase 1: Networking Fundamentals
Date: June 22, 2026
Concepts learned
OSI and TCP/IP models

OSI has 7 layers. Each attack type lives at a specific layer — ARP spoofing is layer 2, port scanning is layer 4, SQL injection is layer 7.
TCP/IP is the real-world implementation: Application, Transport, Internet, Network Access.
OSI mnemonic top to bottom: All People Seem To Need Data Processing.

IP and subnetting

IPv4 = 32 bits across 4 octets. Private ranges: 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16.
CIDR: the number after the slash indicates fixed bits. Usable hosts = 2^(32-mask) - 2.
/24 → 254 hosts. /30 → 2 hosts (point-to-point links).
Tool: ipcalc 192.168.1.0/24

Key protocols

DNS: resolves names to IPs. Flow: local cache → resolver → root → TLD → authoritative. Port 53, runs over UDP. Tool: dig google.com.
HTTP/S: HTTP is plain text, HTTPS encrypts with TLS. Key status codes: 200, 301, 403, 404, 500.
SSH: handshake using host key verified against ~/.ssh/known_hosts. Default port 22. Never ignore host key change warnings.

Hands-on practice

Installed Wireshark and captured live DNS traffic on interface wlp3s0 (Wi-Fi).
Identified primary DNS server: 187.185.14.2 (Telmex/ISP).
Observed full DNS flow: outgoing query + response with resolved IP, visible across Ethernet → IP → UDP → DNS layers.
Installed nmap and scanned localhost.
Open ports found: 80/tcp nginx 1.28.3 and 631/tcp CUPS 2.4.
Learned banner grabbing concept — nmap exposes service versions to any external scanner.

Recommended resources

Professor Messer Network+ (YouTube) — OSI and TCP/IP theory
Practical Networking "Subnetting Mastery" (YouTube) — subnetting from zero to advanced
David Bombal Wireshark tutorial (YouTube) — live protocol capture

Next session

Phase 2: Linux hardening — SSH hardening, ufw/iptables, logs and monitoring.
Pending concept: how to hide service versions (banner grabbing mitigation).
