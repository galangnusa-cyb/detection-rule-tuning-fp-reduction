# Detection Rule Tuning & False Positive Reduction (Splunk)

This project builds **initial (noisy) detection rules** for:
- SSH brute-force activity (from Linux auth logs)
- Network scanning / reconnaissance (from Suricata eve.json flows)

Then it **tunes** these rules to reduce false positives while keeping true positive detection.

## Lab Setup
- Attacker: Kali Linux (Nmap / brute-force simulation)
- Target: Ubuntu (SSH logs in `/var/log/auth.log`)
- Network telemetry: Suricata (`eve.json`)
- SIEM: Splunk

## Data Sources
- Linux auth logs: `Failed password`, `authentication failure`, `Accepted password`
- Suricata `eve.json`: `event_type=flow` fields such as `src_ip`, `dest_ip`, `dest_port`

## Detections
### Baseline (intentionally sensitive / noisy)
- `ssh_bruteforce_baseline.spl`: triggers on **>=5 failures within 5 minutes** per source IP
- `scan_flow_baseline.spl`: triggers on **unique destination ports** within a short window (scan-like behavior)

### Tuned (reduced noise)
- `ssh_bruteforce_tuned.spl`
  - raises thresholds
  - adds **distinct user count** to catch password spraying
  - supports allowlisting known-good sources (jumphost/scanner)
- `scan_portsweep_tuned.spl`
  - detects **port sweep**: 1 target host + many unique ports
- `scan_hostsweep_tuned.spl`
  - detects **host sweep**: many target hosts + small number of ports

## Tuning Summary (what changed)
See `notes/tuning-log.md` for the full change log. Typical tuning steps:
- Threshold adjustments (count/time window)
- Distinct count logic (dc(user), dc(dest_port), dc(dest_ip))
- Allowlist / suppression for known benign scanners
- Severity bucketing (low/medium/high)
- Throttle to prevent alert spam

## How to Reproduce (quick)
### Nmap scan (Kali)
```bash
nmap -sS -p- --min-rate 2000 <TARGET_IP>
```
