# Windows Privilege Escalation

## Content

- Enumeration
- System Information
- User Enumeration
- Privileges
- Processes
- Services
- Scheduled Tasks
- Registry
- File Permissions
- Unquoted Service Paths
- DLL Hijacking
- AlwaysInstallElevated
- Credential Hunting
- Password Recovery
- Kernel Exploits
- Token Impersonation
- Active Directory Checks
- Tools
- References

---

# 1. Enumeration

Before exploiting anything, gather information:

```cmd
whoami
whoami /priv
whoami /groups
hostname
systeminfo
```

Check:

- Operating system
- Current user privileges
- Groups
- Installed software
- Running services

---

# 2. System Information

Operating system:

```cmd
systeminfo
```

Hostname:

```cmd
hostname
```

Environment variables:

```cmd
set
```

Check architecture:

```cmd
echo %PROCESSOR_ARCHITECTURE%
```

Look for:

- Windows version
- Patch level
- Architecture
- Domain information

---

# 3. User Enumeration

Current user:

```cmd
whoami
```

Detailed information:

```cmd
whoami /all
```

Users:

```cmd
net users
```

Local administrators:

```cmd
net localgroup administrators
```

Domain users:

```cmd
net user /domain
```

---

# 4. Privilege Enumeration

Check user privileges:

```cmd
whoami /priv
```

Interesting privileges:

```
SeImpersonatePrivilege
SeAssignPrimaryTokenPrivilege
SeBackupPrivilege
SeRestorePrivilege
SeDebugPrivilege
```

These can allow privilege escalation.

---

# 5. Process Enumeration

Running processes:

```cmd
tasklist
```

Detailed:

```cmd
tasklist /svc
```

PowerShell:

```powershell
Get-Process
```

Look for:

- Services running as SYSTEM
- Credentials in command lines
- Vulnerable applications

---

# 6. Services Enumeration

List services:

```cmd
sc query
```

Detailed:

```cmd
sc qc <service>
```

PowerShell:

```powershell
Get-Service
```

Look for:

- Services running as SYSTEM
- Weak permissions
- Writable binaries

---

# 7. Scheduled Tasks

List tasks:

```cmd
schtasks /query /fo LIST
```

PowerShell:

```powershell
Get-ScheduledTask
```

Look for:

- Tasks running as Administrator
- Writable scripts
- Weak permissions

---

# 8. File Permissions

Find writable directories:

```cmd
icacls C:\Path
```

Example:

```cmd
icacls "C:\Program Files"
```

Look for:

- Weak permissions
- Writable binaries
- Writable service files

---

# 9. Unquoted Service Paths

A service path without quotes can be abused.

Find services:

```cmd
wmic service get name,displayname,pathname,startmode
```

Example vulnerable path:

```
C:\Program Files\Test Service\service.exe
```

Windows searches:

```
C:\Program.exe
C:\Program Files\Test.exe
```

---

# 10. DLL Hijacking

Applications load DLL files.

Find missing DLLs:

```cmd
procmon.exe
```

Look for:

- DLL NOT FOUND
- Writable directories
- Applications running as SYSTEM

Tools:

- Process Monitor
- PowerUp

---

# 11. AlwaysInstallElevated

Check registry:

```cmd
reg query HKCU\Software\Policies\Microsoft\Windows\Installer
```

```cmd
reg query HKLM\Software\Policies\Microsoft\Windows\Installer
```

If enabled:

```
AlwaysInstallElevated = 1
```

Users can install MSI packages as SYSTEM.

---

# 12. Registry Enumeration

Search registry:

```cmd
reg query HKLM
```

Installed software:

```cmd
reg query HKLM\Software
```

Interesting locations:

```
HKLM\Software\Microsoft
HKCU\Software
```

Look for:

- Credentials
- Passwords
- Configuration files

---

# 13. Credential Hunting

Search files:

```cmd
findstr /si password *.txt *.xml *.ini
```

Search entire disk:

```cmd
findstr /spin "password" C:\*.*
```

Interesting files:

```
web.config
unattend.xml
sysprep.xml
config.xml
```

---

# 14. PowerShell History

Check history:

```powershell
Get-Content (Get-PSReadlineOption).HistorySavePath
```

Look for:

- Passwords
- API keys
- Credentials

---

# 15. Saved Credentials

Windows stores cached credentials.

Check:

```cmd
cmdkey /list
```

Access:

```cmd
runas /savecred /user:<user> cmd.exe
```

---

# 16. Token Impersonation

Check privileges:

```cmd
whoami /priv
```

Interesting:

```
SeImpersonatePrivilege
```

Common exploits:

- JuicyPotato
- RoguePotato
- PrintSpoofer
- GodPotato

Example:

```cmd
PrintSpoofer.exe -i -c cmd
```

---

# 17. Kernel Exploits

Check system:

```cmd
systeminfo
```

Search vulnerabilities:

```
Windows Exploit Suggester
Watson
Sherlock
```

Common vulnerabilities:

- MS10-015
- MS16-032
- PrintNightmare
- EternalBlue

---

# 18. Active Directory Checks

Domain:

```cmd
whoami /domain
```

Domain users:

```cmd
net user /domain
```

Domain groups:

```cmd
net group /domain
```

Check:

- Domain membership
- Privileges
- Group permissions

---

# 19. Useful Tools

## WinPEAS

Enumeration:

```cmd
winpeas.exe
```

Checks:

- Services
- Registry
- Credentials
- Privileges


## PowerUp

PowerShell:

```powershell
Import-Module PowerUp.ps1
Invoke-AllChecks
```


## Other Tools

- WinPEAS
- PowerUp
- Seatbelt
- SharpUp
- PrivescCheck
- AccessChk
- Procmon

---

# Windows Privilege Escalation Workflow

```
Enumeration
    |
System Information
    |
User Privileges
    |
Processes
    |
Services
    |
Scheduled Tasks
    |
Credentials
    |
Token Abuse
    |
Exploit
    |
Administrator/SYSTEM
```

---

# References

- https://github.com/carlospolop/PEASS-ng
- https://github.com/PowerShellMafia/PowerSploit
- https://github.com/GhostPack/Seatbelt
- https://github.com/itm4n/PrintSpoofer
