# SSH Brute-Force Attack & Detection Lab

**Category:** Blue Team / Password Attacks / Log Analysis
**Environment:** Self-hosted Debian 13 VM (UTM), isolated lab network only
**Tools used:** `nmap`, `nikto`, `John the Ripper`, `Hydra`, `fail2ban`, `unshadow`

## Objective

Simulate an end-to-end offensive-to-defensive workflow against a locally hosted SSH service: crack a weak password offline, replay the attack online against the live service, then measure and harden the service against that exact attack pattern.

## Scope & Authorization

All testing was performed exclusively against a personally owned, isolated virtual machine with no production data or external network exposure. No real, third-party, or production systems were targeted at any point.

## Methodology

### 1. Offline password cracking (John the Ripper)

- Created a test account (`testuser`) with a deliberately weak password (`password123`) to simulate a real-world weak-credential scenario.
- Combined `/etc/passwd` and `/etc/shadow` using `unshadow` to produce a crackable hash file.
- Debian 13 defaults to the `yescrypt` hashing scheme, which the standard `john` package does not auto-detect — resolved by explicitly specifying `--format=crypt` to delegate hash verification to the system's own `crypt()` function.
- Ran a dictionary attack using a custom 15-entry wordlist of common weak passwords.

**Result:** Password cracked in under 1 second.

```
password123     (testuser)
1g 0:00:00:00 100% 3.125g/s 50.00p/s 100.0c/s 100.0C/s 123456
```

### 2. Online credential attack (Hydra)

- Confirmed the target SSH service was active (`systemctl status ssh`) and listening on port 22.
- Reused the same wordlist to launch a live, network-based brute-force attempt against the SSH login itself — a fundamentally different attack surface from the offline hash-cracking above.

```
hydra -l testuser -P mylist.txt ssh://127.0.0.1
```

**Result:**
```
[22][ssh] host: 127.0.0.1   login: testuser   password: password123
1 of 1 target successfully completed, 1 valid password found
```

### 3. Detection & hardening (fail2ban)

- Installed and configured `fail2ban` with a custom SSH jail to detect and automatically block repeated failed login attempts, directly countering the attack pattern demonstrated in step 2.

```ini
[sshd]
enabled = true
port = ssh
maxretry = 3
bantime = 600
findtime = 600
```

## Key Findings

| Stage | Finding | Real-World Implication |
|---|---|---|
| Password strength | An 11-character but dictionary-based password was cracked in <1 second offline | Length alone does not equal strength; complexity/randomness does |
| Offline vs. online attack | Offline hash cracking has no rate limit; online (Hydra) attacks depend entirely on the target service's defenses | Password policy alone is insufficient — service-level controls (rate limiting, lockout) are mandatory compensating controls |
| Detection gap | Before hardening, unlimited SSH login attempts were possible from a single source with no alerting or blocking | Confirms the need for brute-force detection/prevention as a baseline control on any externally reachable authentication service |

## Skills Demonstrated

- Password hash extraction and offline cracking (John the Ripper, `unshadow`, hash format troubleshooting)
- Live credential-attack simulation against a network service (Hydra)
- Service hardening and intrusion-prevention configuration (fail2ban jail tuning)
- Reading and interpreting tool output to validate attack success/failure

## GRC Relevance

This exercise directly maps to control areas typically assessed in vendor/client security questionnaires and internal policy reviews:
- **Access Control / Authentication** — password complexity requirements alone are insufficient without account lockout and rate-limiting controls.
- **Logging & Monitoring** — the absence of brute-force detection prior to `fail2ban` represents a monitoring gap that would be flagged in a security assessment.
- **Incident Response readiness** — this scenario is used as the basis for a companion incident report (see `Incident-Report-SSH-Bruteforce.docx`), demonstrating how a technical finding is translated into GRC-facing documentation.
