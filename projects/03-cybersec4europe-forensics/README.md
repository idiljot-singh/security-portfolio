# CyberSec4Europe Forensics Exercise

Incident-response investigation of a simulated breach against a fictional organization's ("Kybereo") corporate intranet, combining phishing-email analysis, web-server log forensics, and vulnerability-scan correlation.

**Tools:** Nessus, Nmap, Nikto, manual Apache log analysis (`grep`/`cut`/`sort`/`uniq -c`)

## Investigation

1. **Phishing triage:** identified a typo-squatted sender domain and traced the phishing link to a page hosted on the *real* corporate server (not a lookalike domain) — a more credible and harder-to-spot setup than a classic phishing site, since it loads over valid HTTPS with no browser warnings.
2. **Credential-theft mechanism:** inspected the phishing page's source and found JavaScript sending entered credentials via `XMLHttpRequest` to a PHP endpoint on the legitimate server — meaning the attacker needed no separate infrastructure to harvest logins.
3. **Log analysis:** filtered the Apache HTTPS access log for the phishing path, then used `cut`/`sort`/`uniq -c` on the extracted IPs to separate 3 legitimate internal visitors from 1 external IP with a disproportionately high request count — the attacker.
4. **Attack reconstruction:** isolated all log entries from that IP, found `sqlmap` user-agent strings against a WordPress plugin, then filtered *out* the sqlmap noise to see what came after — POST requests to `/wp-admin/` consistent with creating or elevating an administrator account.
5. **Root-cause correlation:** cross-referenced Nessus, Nmap, and Nikto scan output against the log findings to confirm the SQL-injection vulnerability in the "Safe Search" WordPress plugin as the pivot point, plus supporting misconfigurations (HTTP TRACE enabled, missing HSTS/X-Frame-Options headers, an untrusted TLS certificate, and weak cipher suites: SWEET32/3DES, RC4, SSH CBC-mode).

## Outcome

A full root-cause report tying the incident together end to end: phishing → credential harvesting → reconnaissance → SQL-injection exploitation → privilege escalation → full CMS compromise — with each step backed by a specific log line or scan finding, not inference alone.

**Takeaway:** infrastructure-level scanning (Nessus/Nmap) missed the one vulnerability that actually mattered — the application-layer SQL injection in a CMS plugin. Both layers need to be checked; neither one alone is sufficient.
