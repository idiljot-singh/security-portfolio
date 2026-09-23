# Metasploitable2 Penetration Test

Full black-box penetration test against Metasploitable2, a deliberately vulnerable Linux training target, run in an isolated VirtualBox NAT lab. Followed the standard pentest methodology: environment validation → reconnaissance → scanning → enumeration → exploitation → reporting.

**Tools:** Nmap, Metasploit Framework, smbclient, showmount (NFS), Nikto, netcat

## Scanning & Enumeration

A full TCP SYN scan (`nmap -p- -sS --open`) surfaced over 20 open ports; a follow-up service/version scan (`nmap -sV`) identified the exact software versions needed to match known exploits — including `vsftpd 2.3.4` and an exposed `bindshell` service. Enumeration then validated what scanning only suggested: anonymous FTP access, an NFS export of `/` to all hosts (`*`), and unauthenticated SMB share listing.

## Validated Findings (10-12 vulnerabilities)

| # | Finding | Severity | Method |
|---|---|---|---|
| 1 | Exposed root bind shell (TCP/1524) | Critical | `netcat` connect → immediate `uid=0` |
| 2 | vsftpd 2.3.4 backdoor (TCP/21) | Critical | Metasploit `vsftpd_234_backdoor` → root shell in ~12 seconds |
| 3 | Insecure NFS export (`/` to `*`) | High | `showmount -e` |
| 4 | SMB anonymous share access | High | `smbclient -L // -N` |
| 5 | Samba `usermap_script` RCE | Critical | Metasploit → command shell |
| 6 | DistCC exposed service | High | Confirmed exposed; exploit attempt logged even when a session wasn't obtained |
| 7 | Java RMI registry RCE | Critical | Metasploit → Meterpreter session |
| 8 | Apache Tomcat Manager, default creds (`tomcat:tomcat`) | Critical | Authenticated to Web Application Manager |
| 9 | rlogin trust-based root access | Critical | `rlogin -l root` → no password prompt |
| 10 | MySQL remote root, no password | High | `mysql --skip-ssl -u root` |
| 11 | PostgreSQL legacy SSL/auth exposure | Medium-High | Confirmed reachable, outdated protocol handling |
| 12 | UnrealIRCd backdoor RCE | Critical | Metasploit → command shell |

## Reporting

Delivered as a full report: executive summary, scope/rules of engagement, per-finding walkthroughs (identification → steps → result → risk/impact → recommendation), an overall risk rating, and prioritized remediation (urgent: disable bind shell/rlogin, rotate Tomcat credentials, disable SMB anonymous access; short-term: patch outdated software; long-term: network segmentation and baseline hardening).

**Takeaway:** a single exploited legacy service (vsftpd) is enough to fully compromise a host — patch management is the highest-leverage defensive control here, not any single clever technique.
