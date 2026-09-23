# Ethical Hacking Lab Course (53-page lab documentation)

A three-phase offensive-security course covering network attacks, gaining access, and maintaining access, run across an isolated VirtualBox lab (Kali attacker, Windows 10 victim, Metasploitable2 server).

## 1. Network Hacking

- **Recon:** `ifconfig`/`ip a`, MAC spoofing, `netdiscover`, and layered Nmap scans (`-sn` host discovery → `-T4 -F` fast scan → `-sV` service/version detection) to map the LAN and fingerprint every host.
- **MITM (ARP spoofing):** ran the attack two ways — the classic two-terminal `arpspoof` (one process per direction) and `bettercap` with a custom caplet script (`net.probe`, `arp.spoof`, `net.sniff`) — verifying the victim's ARP cache flip before and after, and always re-arping on exit to restore the network cleanly.
- **Wireshark:** captured a plaintext HTTP login (DVWA) while the MITM was live and pulled the exact `username=...&password=...` body out of the TCP stream via Follow → HTTP Stream — a direct demonstration of why unencrypted logins are unsafe on any shared network.

## 2. Gaining Access

- **Server-side:** re-confirmed the `vsftpd 2.3.4` version banner with Nmap, then exploited the backdoor via Metasploit for a root shell — read `/etc/shadow` as a demonstration of what root access exposes.
- **Client-side:** built a Windows reverse-HTTPS Meterpreter payload with `msfvenom`, hosted it on a local Apache server, and got a session after the victim ran it. From the Meterpreter session: process listing, remote filesystem browsing, file download (exfiltrated a planted `passwords.txt`), file upload, an interactive `cmd.exe` shell, and a live keylogger (`keyscan_start`/`keyscan_dump`) that captured keystrokes typed in Notepad in cleartext.
- Windows Defender caught the raw `msfvenom` payload immediately (`Trojan:Win32/Meterpreter.O`) — documented as expected behavior, not a failure.

## 3. Maintaining Access

- Used **Veil-Evasion** to re-wrap the Meterpreter payload in Go, producing a binary with a different on-disk signature than stock `msfvenom` output.
- Installed persistence via Metasploit's `windows/local/persistence` module (registry Run-key + VBS launcher).
- **The honest part:** after a reboot, the persistence callback never arrived. Investigated rather than assuming success, and found Windows Defender's scheduled scan had removed the VBS launcher from the default temp path before the Run key could fire (error `800700E1`).

## Why this failure is in here

The persistence install technically "worked" (registry key set, file written, module reported success) — but the actual goal (surviving a reboot undetected) failed, and the write-up says so plainly instead of stopping at "module ran successfully." The conclusion: real persistence tradecraft is about avoiding default temp paths and default launcher signatures, not just running a module — and this is exactly the kind of layered defense (registry layer passed, filesystem layer passed, behavioral-scan layer caught it) that defense-in-depth is meant to produce.
