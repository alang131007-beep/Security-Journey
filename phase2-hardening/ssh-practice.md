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
