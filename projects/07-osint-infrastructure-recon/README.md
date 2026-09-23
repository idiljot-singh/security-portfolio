# OSINT Infrastructure Reconnaissance

Passive, unauthenticated OSINT reconnaissance of a public university domain (my own institution, HAMK — used with the course's explicit scope as a real-world but low-risk target), covering DNS, certificate transparency, and internet-facing service fingerprinting. All data sources are public and passive — no active scanning, no authentication bypass, no data accessed beyond what any visitor's browser already requests.

**Tools:** Google Admin Toolbox Dig, MXToolbox, national WHOIS registry, Censys (certificate transparency), ipinfo.io, Shodan (read-only banner data only)

## Method & Findings

- **DNS enumeration:** resolved A/MX/NS records and mapped subdomains (Moodle, ADFS, exam system, student-portal backend) against their hosting providers — revealing a mixed footprint spanning the national research network, multiple commercial clouds, and a CDN for public-facing marketing pages.
- **Certificate transparency (Censys):** found 1,063 certificates associated with the domain, including several Let's Encrypt *staging* certificates — a signal that test/staging environments exist and may be discoverable.
- **Shodan (banner-only):** identified an exposed VPN endpoint's service banner on a non-standard port — read-only reconnaissance, no connection attempted.
- **Email security:** the domain's DMARC record was set to `p=none` (monitor-only, not enforcing) — combined with a predictable `firstname.lastname@` email convention, this is a concrete, low-cost phishing/spoofing exposure worth flagging, not exploiting.
- **DNSSEC:** found disabled, and one authoritative name server returned a registry-level technical error during delegation checks.

## Why this is presented as recon, not disclosure

This was coursework conducted under an academic scope, using only public, passive data sources (DNS, certificate transparency logs, WHOIS, and read-only service banners). No credentials, exploits, or non-public data were involved. It's included here as a demonstration of **structured passive-recon methodology** — the kind of first-phase reconnaissance that precedes any real engagement — not as a disclosure of an active vulnerability.
