# Lab 000: Wireshark PCAP Analysis (NetSupport RAT alert)

## Summary
A SOC alert flagged NetSupport RAT traffic to 93.152.209[.]36:443.
I analyzed the PCAP in Wireshark. Host 10.11.10[.]147 resolved
dasiiiitt[.]com to that IP, then sent HTTP POST requests to
/fakeurl.htm about every 60 seconds (125 POSTs in one TCP stream).
The payload is encrypted. The pattern matches C2 beaconing.
Verdict: suspected True Positive, needs confirmation from IT.

## Source
- Exercise: "The Lucky Turkey" (2026-11-10), malware-traffic-analysis.net
- File: 2026-11-10-traffic-analysis-exercise.pcap (30,182 packets)
- The PCAP is not included in this repo. Download it from the website.
- Tool: Wireshark 4.6.6 on a Kali Linux VM (VMware)

## Environment (from the exercise page)
| Item | Value |
|---|---|
| LAN | 10.11.10[.]0/24 |
| Domain | theluckyturkey[.]com |
| Domain controller / DNS | 10.11.10[.]10 |
| Gateway | 10.11.10[.]1 |

## Findings
| # | Finding | Filter used | Screenshot |
|---|---|---|---|
| 1 | Victim host is 10.11.10[.]147 | ip.addr == 93.152.209.36 | 02 |
| 2 | Host asked DNS for dasiiiitt[.]com and got 93.152.209[.]36 | dns.qry.name contains "dasi" | 03 |
| 3 | TCP handshake to port 443, then plain HTTP POST (not real HTTPS) | ip.addr == 93.152.209.36 | 02 |
| 4 | User-Agent "NetSupport Manager/1.3", server banner "NetSupport Gateway/1.92" | Follow TCP Stream | 04 |
| 5 | 125 POST requests to /fakeurl.htm, about every 60 seconds, same length (286 bytes) after the first three | ip.addr == 93.152.209.36 && http.request.method == "POST" | 05 |
| 6 | Body has CMD=POLL, then CMD=ENCD with encrypted DATA= | Follow TCP Stream | 04 |

## Timeline (seconds from start of capture)
| Time | Event |
|---|---|
| 133.20 | DNS query for dasiiiitt[.]com |
| 134.81 | DNS answer: 93.152.209[.]36 |
| 134.82 | TCP SYN to port 443 |
| 135.04 | First POST /fakeurl.htm |
| 195.68 | POST (then repeats about every 60 s) |
| ... | Last POST time: (fill in from your capture) |

## What I can say
- 10.11.10[.]147 talks to an outside server every ~60 s.
- The traffic uses NetSupport names in the User-Agent and server banner.
- Port 443 carries plain HTTP, which is unusual.

## What I cannot say
- I cannot read the encrypted DATA= content.
- NetSupport is a legitimate remote-support tool. I do not know if the
  IT team installed it.
- I did not check the reputation of the domain or IP (Phase 8 topic).

## MITRE ATT&CK mapping (suggested, verify on attack.mitre.org)
| Behavior | Technique |
|---|---|
| Remote access tool | T1219 |
| Web protocol used for C2 | T1071.001 |
| Encrypted payload | T1573 |
| HTTP on port 443 | T1571 |

## Verdict and recommended action
Suspected True Positive.
1. Ask IT: was NetSupport installed on 10.11.10[.]147 on purpose?
2. If not, escalate to Tier 2 and isolate the host.
3. Block 93.152.209[.]36 and dasiiiitt[.]com at the perimeter.

## Lessons learned
- Wireshark has two bars. The Display filter bar hides packets.
  The Find bar only highlights one packet.
- HTTPS is not the only encrypted traffic. Here the application
  encrypted its own data inside plain HTTP.
- A legitimate tool can be abused. The question is who installed it.
