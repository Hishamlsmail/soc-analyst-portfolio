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
