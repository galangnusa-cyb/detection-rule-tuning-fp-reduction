# Test Plan

## SSH Brute-force
1) Generate benign noise:
- 2–3 failed SSH logins from a legitimate admin source (low volume)

2) Generate attack-like behavior:
- Repeated failed logins (>=10 within 5 minutes) from attacker IP
- Optional: simulate password spraying (attempt multiple usernames)

Expected:
- Baseline triggers easily
- Tuned triggers mainly on attack-like patterns

## Network Scan (Nmap)
1) Port sweep test:
```bash
nmap -sS -p- --min-rate 2000 <TARGET_IP>
