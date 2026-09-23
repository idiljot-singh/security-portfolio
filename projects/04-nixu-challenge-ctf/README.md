# Nixu Challenge (CTF)

Two independent challenges from Nixu's public CTF: a network-forensics packet-capture analysis ("Phishcap") and a Windows memory-forensics investigation ("Bad Memories").

## Part 1 — Phishcap (network forensics)

**Tools:** Wireshark, CyberChef

- Opened the provided `challenge.pcap` and used Wireshark's byte-search for a hint keyword ("cleartext") to jump straight to the relevant TCP stream instead of reading every packet by hand.
- Followed the stream to an interactive PowerShell session and pulled an encoded flag out of a `type cleartext.txt` command's output.
- Decoded it with **CyberChef**: a ROT13-style Caesar shift revealed the first flag.
- Filtered the capture to the identified attacker IP and exported every HTTP object (File → Export Objects), reconstructing the full attacker toolkit dropped on the victim: a phishing lure document, a payload archive, multiple reverse-shell variants, `Invoke-Mimikatz.ps1`, and a recon script.
- One reverse-shell script was obfuscated (`[Text.Encoding]::Unicode.GetBytes` + `IEX`) — executed it in a sandboxed PowerShell session to print the real, de-obfuscated source, which revealed hardcoded C2 parameters (IP, port, and a custom XOR-style encryption function for command exchange).
- Found a second flag hidden as a Base64 string in a code comment inside that de-obfuscated script — CyberChef's "Magic" auto-detection recipe decoded it directly.

## Part 2 — Bad Memories (memory forensics)

**Tools:** Volatility Framework, GIMP (for raw-image recovery)

- Used `pslist` and `sessions` on a Windows 7 memory dump to map two active sessions: system services (including an unexpectedly-running SSH daemon) and an interactive user session.
- Used `getsids` to attribute each process to its owning account and confirmed the SSH service was running under an **Administrator**-privileged account — meaning compromising the SSH daemon would mean instant admin-level access to the whole system.
- Ran `netscan` to confirm SSH was listening on `0.0.0.0:22` (exposed to every interface, not just localhost).
- Recovered an in-progress design file from a still-running `mspaint.exe` process by dumping its memory region and reconstructing it as a raw image in GIMP, iterating on pixel format/offset parameters until the image resolved.
- Used `hashdump` to extract NTLM password hashes and identify weak patterns (two accounts sharing an identical hash), then used `lsadump` to recover **plaintext** LSA secrets stored in memory — one of which was the challenge flag itself, and the other the SSH service account's real password.

## Reflection

Both parts reinforced the same lesson from different angles: attackers (and forensic investigators) rarely need to break strong cryptography — most real findings come from weak obfuscation, predictable encoding, and secrets stored somewhere they shouldn't be (a code comment, a memory-resident credential store).
