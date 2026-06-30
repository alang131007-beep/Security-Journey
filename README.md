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

## Cybersecurity — Phase 2: Nmap and Network Scanning

**Date:** 23/06/26

### Concepts learned
I learned scanning local network, IPS and MAC addresses, I learned identify real threats on my router, identify crucial ports in my terminal, analysis services in real time, Capture DNS live and discovery hosts 

### Hands-on practice
in this practice I use "ip a | grep "inet " | grep -v 127" as discovery my personal IP and my network range, "nmap -sn 192.168.0.0/24 | grep -E "Host|MAC"" this command to list  the IPS and MAC adresses in my house, "nmap -sV 192.168.0.1" this command show the open ports and finally "ss -tuln" with this command I see my machine connections

### Key takeaways
- ss shows open ports from inside your own machine. 
  nmap shows what an outsider would see. Both perspectives 
  together is how a blue teamer thinks.
- UPnP on routers is a real risk — any infected device 
  on your network can ask the router to open ports 
  without your knowledge.
- nmap identified my machine by hostname (Tachyon.local) 
  without any special configuration — that's what an 
  attacker inside the network would also see.

### Next session
Phase 2: Linux hardening — SSH hardening, ufw/iptables, logs and monitoring.

## Cybersecurity — Phase 2: SSH Hardening

**Date:** [25/06/26]

### Concepts learned
I used fail2ban, this program allow rules what define who monitor, how many failed attempts allow and for how long time ban the ip , the passphrase is a unique password with which enter to see the secret key, 
I added three lines, first, AllowUSers alanlinux, with it only allow my user, two, ClientAliveInterval 300, after 300 seconds the server send a signal with which verify if the client still here

### Hands-on practice
sudo sshd -T | grep -E "permitrootlogin|passwordauthentication|pubkeyauthentication|port" 
with this command verify if I have the basic configuration for cibersecurity
 
sudo nano /etc/ssh/sshd_config 
-with this command i protected my machine changing my port 22 to port 2222 and permitroot login yes to permitroot login no

sudo systemctl restart sshd
-this is a simple command with which restart SSH 

sudo apt install fail2ban, sudo systemctl status fail2ban
-install and verify that application saved in my machine

sudo nano /etc/fail2ban/jail.local
-with this command I created and configured the jail and used "maxretry = 3", "bantime = 1h" and "findtime = 10m", first command, this command allow only three attempts,after 3 fail attempts the sistem ban the ip, the second command sets the time of ban which is 1 hours and three command, the three trys must be in 10 minutes

sudo fail2ban-client status sshd
-verify with this command the jail is active



### Configuration changes made
i configured three finall commands which limit who connect via SSH and one idle timeout which if one session remains open this automatic close 

1.AllowUsers alanlinux 
only my user can connect via SSH with this command, any other user it gets blocked

2.ClientAliveInterval 300
the server send a sign which verify if the client remains here

3.ClientAliveCountMax 2 
if the client doesn't respond 2 times in a row, close the session 

### Key takeaways
I dont forget my passphrase, A private key without a passphrase is a security risk, if someone steals the file, they can use it directly.

### Next session
Phase 2: Firewall configuration with ufw — rules, policies, and enterprise documentation approach.

# Phase 2 — Linux Hardening (Tachyon-01)

## 1. SSH Hardening
- I had to change my original port 22 to 2222 for security reasons. Port: 22 → 2222
- Disabled RootLogin because I want to prevent brute-force login attempts. PermitRootLogin: no
- The only user allowed is me. AllowUsers: alanlinux
- This is a preventive security, when the session exceeds 10 minutes of inactivy, it is automatically, the sesion ends. ClientAliveInterval: 300 / ClientAliveCountMax: 2 (10 min timeout)
- disable password authentication, using SSH keys instead, this prevents brute-force login attempts. PasswordAuthentication: no
- Generating an Ed25519 SSH key paired with a strong passphrase is the modern standard for secure authentication Key. ed25519 with passphrase

## 2. fail2ban
- With this command allow only 3 attempts and only 10 minutes in the session, after 3 attempts the session automatically ban the session for 1 hour. jail.local: maxretry=3, bantime=1h, findtime=10m, port=2222

## 3. ufw
- Default policy: deny incoming, deny outgoing (whitelist approach)
- Allowed IN: 80/tcp (anywhere), 2222/tcp (192.168.0.0/24, rate limited)
- Allowed OUT: 53/udp, 80/tcp, 443/tcp, 2222/tcp
- Logging: on (low)
- Concept applied: defense in depth, least privilege

## 4. Log Monitoring
### Log sources explored
- Records authentication events: sudo usage, login attempts, PAM activity. /var/log/auth.log —
- Providing visibility into blocked connetions attempts /var/log/ufw.log —
- Primary log file where records detected attack and banned IPs. /var/log/fail2ban.log —
- Records HTTP request and errors from the web server. /var/log/nginx/ —

### btmp (failed logins)
- Ubuntu 24 migrated away from legacy utmp/wtmp/btmp stack
- Reason: Y2038 problem, username length limits, corruption-prone format
- Modern replacements: wtmpdb, pam_lastlog2, systemd-logind
- Commands last / utmpdump not available by default in Ubuntu 24

### journalctl
- journalctl -u ssh → show all recents logs, clean log: 2 sucessful ED25518 logins from localhost zero failed attempts 
- journalctl -p err → show only logs error, VMX disabled by BIOS (not a security issue), repeated snapd noise from udev 
- journalctl -k → kernel 7.0.0-22, NX protection active, VMX disabled by BIOS

## 5. auditd
### What it does
-is a kernel-level subsyestem with which monitors and records everything activities, file access, acts as a forensic black box-happened even after an attacker clears shell history
### Installation
- sudo apt install auditd audispd-plugins

### Rules configured (/etc/audit/rules.d/hardening.rules)
- /etc/passwd, /etc/shadow, /etc/group → key: user_changes
- /etc/sudoers, /etc/sudoers.d/ → key: sudo_changes
- /etc/ssh/sshd_config → key: ssh_config
- /var/log/ → key: log_tampering

### Verified
- sudo touch /etc/passwd → ausearch -k user_changes confirmed event captured
- Fields logged: syscall, exe, uid, auid, path, cwd, timestamp

### Key concepts
- auid vs uid: auid is the original id user which login sesion in the system. uid is the id which execute everything process, auid never changes even when running sudo, uid is the effective UID at exeuction time
- defense in depth: is a strategy with which keep sure the server. ban suspect ips, using else ufw, SSH hardening and auditd in set 
- post-compromise detection: with auditd records all modifications to critical files so there is forensic evidence of what was touched, when, and by which process and everything suspect, logs in archives, passwd etc..

## Pending — Phase 2
- [ ] AppArmor
- [ ] CIS Benchmarks
