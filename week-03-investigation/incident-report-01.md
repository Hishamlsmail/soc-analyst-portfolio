# Incident Report #001
**Date:** 2026-09-17
**Analyst:** Hesham Ismail
**Severity:** Medium

## Summary
Suspicious account "CodexSandboxOffline" performed 
mass permission enumeration at 01:46 AM.

## Timeline
- 01:46 AM: Activity started
- 01:48 AM: Activity stopped
- Total Events: 100+ in 2 minutes

## Indicators
- Account: CodexSandboxOffline
- EventCode: 4798
- Time: 01:46-01:48 AM (outside business hours)
- Machine: precision5550

## MITRE ATT&CK
- Tactic: TA0007 - Discovery
- Technique: T1069 - Permission Groups Discovery

## Verdict
TRUE POSITIVE — Suspicious enumeration activity

## Recommendation
1. Investigate what CodexSandboxOffline is
2. Check if any privilege escalation followed
3. Escalate to L2 for deep investigation
4. ## Evidence

### Suspicious Account Activity
<img width="3837" height="2132" alt="Screenshot 2026-09-18 071513" src="https://github.com/user-attachments/assets/e19206e5-3cb0-4315-b165-1ccaf2c895bf" />


### Timeline of Events  

<img width="3837" height="2112" alt="Screenshot 2026-09-18 071531" src="https://github.com/user-attachments/assets/3c8b223e-74ad-44d6-9eb1-235c49237bf5" />

## Recommendation
Escalate to L2 to verify whether the attacker 
used the enumerated permissions to gain 
higher privileges on the system.
____________________________________________________________

## Investigation Results

I searched for Event ID 4672 (Special Privileges).
Found 94 events — all from known accounts:
- HISHAM (me)
- Splunkd
- DWM-1, DWM-2, DWM-3
- LOCAL SERVICE, NETWORK SERVICE

No suspicious accounts found.
Conclusion: Normal activity — no unauthorized 
privileged access detected.
<img width="3837" height="2225" alt="Screenshot 2026-09-18 072920" src="https://github.com/user-attachments/assets/2c9f8a74-f776-4fbc-83ab-f4f399bbd91e" />
<img width="3837" height="2225" alt="Screenshot 2026-09-18 072920" src="https://github.com/user-attachments/assets/66e3b24d-9049-4ea6-bfef-a7a5bf60d992" />
_______________________________________________________

# Incident Report #002 — Phishing Analysis
**Date:** 2026-09-18
**Analyst:** Hesham Ismail
**Severity:** High 🔴

## Summary
A phishing email was received claiming to be 
from PayPal. The sender domain was "paypa1.com" 
(typosquatting). The email urged the user to 
click a malicious link by claiming their account 
would be suspended.

## Indicators of Compromise (IOCs)
- Sender: security@paypa1.com
- Fake domain: paypa1.com (typosquatting paypal.com)
- Malicious URL: http://paypa1.com/verify
- VirusTotal: 13/90 vendors flagged as Phishing

## MITRE ATT&CK
- Tactic: TA0001 - Initial Access
- Technique: T1566 - Phishing

## Verdict
TRUE POSITIVE — Confirmed Phishing Attack

## Recommendations
1. Block sender domain: paypa1.com
2. Warn the employee not to click the link
3. Check if employee already clicked the link
4. Escalate to L2 if link was clicked

## Evidence
<img width="3837" height="2107" alt="image" src="https://github.com/user-attachments/assets/6b69461b-fc63-489e-909f-f0feeb434c4f" />
___________________________________________________________________________
## Sysmon Key Event IDs

| Event ID | What it logs | Why SOC cares |
|----------|-------------|---------------|
| 1 | Process Created | Detects new processes — suspicious when unexpected parent/child relationship exists |
| 3 | Network Connection | Detects network connections — suspicious when normal apps connect to unknown IPs or ports |
| 11 | File Created | Detects file creation — suspicious when Office apps create executable files |
| 13 | Registry Modified | Detects registry changes — suspicious when malware adds itself to Run key for persistence |
