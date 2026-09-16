## My First MITRE Mapping

Alert: New Admin Account on Domain Controller

Tactic: TA0003 - Persistence
Technique: T1136.002 - Create Domain Account

Why suspicious:
- Created by SYSTEM (not a human)
- 02:30 AM - outside business hours
- DC01 = most critical asset in network
______________________________________________
## Alert Triage — Key Lesson

4625 alone is NOT enough to decide.
Context is everything:

| Factor | Suspicious | Normal |
|--------|-----------|--------|
| Time | 03:00 AM | 09:15 AM |
| Username | fakeuser | ahmed.hassan |
| Machine | DC01/Finance | User's own PC |
| Count | 50x in 2 min | 1x |

Rule: True Positive needs multiple 
suspicious factors together.
________________________________________________
## Linux Auth Log Analysis

File: /var/log/auth.log
Command: grep "Failed" /var/log/auth.log

Key log entries:
- sudo session opened = user ran sudo command
- COMMAND= shows exactly what was executed
- "Failed password" = failed login attempt (not found = no attacks)

Key difference from Windows:
- Windows = Event Viewer (GUI)
- Linux = /var/log/auth.log (text file)
- ________________________________________________________
- ## Networking Knowledge (Pre-existing)

### DNS
- Converts domain names to IP addresses
- Without DNS, we'd memorize IPs for every website

### TCP vs UDP
- TCP = slower, has handshake, reliable (HTTP, SSH)
- UDP = faster, no handshake (gaming, video, streaming)

### Suspicious Ports — SOC Rule
| Port | Service | Status |
|------|---------|--------|
| 80 | HTTP | Normal ✅ |
| 443 | HTTPS | Normal ✅ |
| 53 | DNS | Normal ✅ |
| 4444 | Metasploit/Reverse Shell | Suspicious 🔴 |
| 1337 | Common hacker port | Suspicious 🔴 |

### MITRE Mapping
- Port 4444 = T1571 Non-Standard Port
- Tactic: TA0011 Command and Control
