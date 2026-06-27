# SSH Hardening
**Project:** Tachyon-01 Production Server  
**Date:** 24/06/2026

---

## What is SSH?
SSH is a network protocol that allow you to control and connetion servers or remote machines through a line commands in an encrypted and secure way

---

## What was configured
First, installed SSH in my terminal, second, I checked the status of SSH, appeared inactive and disable, so I activated it with systemctl, that was when it activated. thrid, I generated SSH Key also Key public and I copied my key in my machine and then i was able to enter ssh without password and to conclude I configured sshd_config to disable auntenticathor password 


---

## Commands used

### Generate SSH keys
```bash
ssh-keygen -t ed25519 -C "alanlinux@Tachyon"

```
### Copy public key to server
```bash
ssh-copy-id alanlinux@localhost
```

### Disable password authentication
```bash
"#PasswordAuthentication yes" to "PasswordAuthentication no"
```

### Verify hardening works
```bash
ssh -o PreferredAuthentications=password alanlinux@localhost
```
---

## Key concepts

| Term | Description |
|------|-------------|
| Private Key | Stay in your machine, never shared |
| Public Key | works like a lock |
| ed25519 | Type of encryption algorith used for the key |
| sshd_config | ssh configuration file |

---

## Important files

| File | Purpose |
|------|---------|
| ~/.ssh/id_ed25519 | Your private key |
| ~/.ssh/id_ed25519.pub | Your public ley |
| /etc/ssh/sshd_config | ssh server configuration |
| ~/.ssh/authorized_keys | Public keys allowed to connect |

---

## Common errors

| Error | Cause | Solution |
|-------|-------|----------|
| Permission Denied | Wrong key or password auth disabled | verify key is with ssh-copy-id |
| SSH inative | Service not started | sudo systemctl enable --now ssh

# UFW Firewall Configuration
**Project:** Tachyon-01 Production Server 
**Date:** 26/06/26

---

## What is UFW?
ufw is a simple interface that allow you to manage the rules of firewalls in Linux, designed for avoid complex configurations in fast ans secure way


---

## What was configured
I configured the rules to allow SSH, and HTTP and enabled ufw, I opened only the necessary ports to achieve the minimun security configurations for network server 


---

## Commands used

### Check firewall status
```bash
sudo ufw status
```

### Allow ports
```bash
sudo ufw allow 2222/tcp
```

### Enable firewall
```bash
sudo ufw enable 
```

### Verify rules
```bash
sudo ufw status verbose ssh alanlinux@localhost
```

---

## Active rules

| Port | Protocol | Action | Reason |
|------|----------|--------|--------|
| 2222 | TCP      | ALLOW  | SSH acces |
| 80   | TCP      | ALLOW  | HTTP web server |
| Any  | Any      | DENY   | Default-block everything else |
---

## Key concepts

| Term | Description |
|------|-------------|
| UFW | Uncomplicated firewall - Frontend for iptables  |
|Inbound | Traffic coming into server |
|Outbound | Traffic leaving the server | 
|Default deny | block all incoming unless explicitly allowed |

---

## Common errors

| Error | Cause | Solution |
|-------|-------|----------|
| locked out of SSH  | Actived ufw before allowing SSH port | Always allow SSH before enabling ufw |
| Wrong port allowed | SSH running on 2222 but allowed 22 | Check actual port with "systemctl status ssh" before adding rules

# Logs and Monitoring
**Project:** Tachyon-01 Production Server  
**Date:** 26/06/26

---

## What are logs?
logs are a register of everything that happens, is they are first tool to detect what happened, why it happened, and who did it, in cibersecurity, is they are first step to review


---

## Important log files

| File | Purpose |
|------|---------|
| /var/log/auth.log | Records login attempts, sudo commands and authentication events |
| /var/log/nginx/access.log | Records every request made to the web server |
| /var/log/nginx/error.log | Records nginx errors and failed requests |

---

## Commands used

### Check auth logs
```bash
sudo cat /var/log/auth.log | tail -20
```

### Filter SSH events
```bash
sudo grep -E "Accepted|Failed" /var/log/auth.log
```

### Check nginx logs
```bash
sudo cat /var/log/nginx/access.log
```

### Real-time monitoring
```bash
sudo journalctl -u ssh -f
ssh -p 2222 alanlinux@localhost
```

---

## What to look for in logs

"127.0.0.1 - - [24/Jun/2026:15:02:12 -0600] "GET / HTTP/1.1" 200 72 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:152.0) Gecko/20100101 Firefox/152.0"
127.0.0.1 - - [24/Jun/2026:15:02:12 -0600] "GET /favicon.ico HTTP/1.1" 404 134 "http://nexus.local/" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:152.0) Gecko/20100101 Firefox/152.0"

In acess.log each lien shows: the visitor ip, the request page, the http status code, and the browser used. A 200 means success, 404 means file not found, 403 means acces denied. In a real server, unknown IPS or repeated 403 code could indicate a scan or attack
---

## SSH Monitor Script

```bash
#!/bin/bash
echo "=== SSH Login Report - $(date) ===" >> ~/ssh-report.log
sudo grep -E "Accepted|Failed" /var/log/auth.log >> ~/ssh-report.log
```

---

## Key takeaways

logs are the most important part in the field of cibersecurity, they are the primary shield and the first step to investigate if the system has been compromised
