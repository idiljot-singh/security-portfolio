# Da Vivian Code (CTF Walkthrough)

A full recon-to-flag attack chain against an intentionally vulnerable training target ("DaVivian Code"), run as a first hands-on penetration-testing exercise in an isolated VirtualBox NAT lab.

**Tools:** netdiscover, Nmap, WPScan, Nikto, ExifTool, CyberChef, Metasploit

## Chain of compromise

1. **Discovery:** `netdiscover` (ARP scan) to find the target on the isolated subnet, then `nmap -sV --script vuln` to enumerate open ports (SSH, HTTP) and confirm service stability by comparing repeated scans with `diff`.
2. **Web enumeration:** `wpscan` identified a WordPress install and a valid username; `nikto` found directory indexing enabled on a `/hidden/` and `/images/` path.
3. **Metadata pivot:** found an image (`key.png`) via directory browsing, pulled it locally, and read its EXIF metadata with `exiftool` — the `Comment` field leaked a hidden path to another page.
4. **Multi-layer decoding:** the hidden page contained three separately-encoded strings (Base64, ROT13, and binary-then-Base64), each decoded in **CyberChef** to reveal partial credentials — including a WordPress login.
5. **WordPress pivot:** logged into the WordPress dashboard with the recovered credentials and found a screenshot in the media library referencing an SSH username.
6. **Credential brute-force:** since SSH was open and a large wordlist (`rockyou.txt`) would be too slow, used Metasploit's `auxiliary/scanner/ssh/ssh_login` module with a small challenge-provided wordlist to crack the SSH password in seconds.
7. **Flag capture:** logged into the SSH server with the cracked credentials and found the flag — a file of financial transaction records, demonstrating the real-world impact of a full compromise.

## Reflection

First independent CTF solve. Notable practical takeaway beyond the technical chain: the exercise directly motivated auditing personal password-storage habits (a shared family notes file containing plaintext passwords, card numbers, and crypto backup codes) — moving that data out of an unencrypted digital copy entirely.
