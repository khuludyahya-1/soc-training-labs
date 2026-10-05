# SOC Training Labs

Hands-on cybersecurity exercises completed on a personally owned, isolated lab environment, documenting a self-directed path toward SOC analyst skills with a GRC (Governance, Risk, and Compliance) perspective.

## ⚠️ Scope & Authorization

All exercises in this repository were performed exclusively against a personally owned, isolated virtual machine (Debian 13, local lab network only), with no production systems, third-party services, real user accounts, or external websites involved at any stage. Nothing here was performed against systems the author does not own or have explicit authorization to test.

## Labs in this Repository

### 1. [SSH Brute-Force Detection Lab](./SSH-Bruteforce-Detection-Lab.md)
End-to-end exercise covering offline password-hash cracking (John the Ripper), an online brute-force credential attack (Hydra), and remediation via fail2ban rate-limiting. Includes key findings, skills demonstrated, and mapped GRC relevance.

### 2. [Log Analysis — SSH Brute-Force Finding](./Log-Analysis-SSH-Bruteforce-Finding-EN.md)
Follow-up log-analysis exercise using the systemd journal (`journalctl -u ssh`) to locate the exact evidence of the brute-force attack above, and to derive a plain-language, reusable detection rule for distinguishing automated attacks from legitimate logins.

## Skills Demonstrated Across This Repository

- Offline and online password attack techniques, and the distinction between them
- Authentication log analysis (systemd journal) and manual detection-rule derivation
- Security control implementation and validation (fail2ban rate-limiting)
- Mapping technical findings to recognized frameworks (ISO 27001 Annex A, NIST SP 800-63B)
- Documenting the full detect → document → remediate cycle expected of a SOC/GRC-blended analyst

## About

Maintained as a running record of self-paced, hands-on security training. More labs will be added as the training program progresses.
