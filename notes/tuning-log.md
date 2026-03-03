# Tuning Log (Baseline → Tuned)

## SSH Brute-force
**Baseline:**
- Condition: >=5 failed attempts / 5m per src_ip
- Issue: triggers on benign password mistakes, shared NAT IPs, admin activity

**Tuned changes:**
- Raised thresholds (>=10 / 5m)
- Added password spraying signal: fails>=6 AND dc(user)>=3
- Added severity bucketing (medium/high)
- Optional allowlist for jumphost/scanner
- Recommended: alert throttle 10–15 minutes per src_ip

## Network Scan (Suricata Flow)
**Baseline:**
- Condition: unique dest ports >=5 per 5m per src_ip → dest_ip
- Issue: can flag monitoring/service discovery

**Tuned changes:**
- Split into two detections:
  1) Port sweep: 1 dest_ip + many ports (>=30 per 2m)
  2) Host sweep: many dest_ip (>=20 per 2m) with few ports (<=5)
- Added severity bucketing for higher confidence
- Optional allowlist for internal scanner/monitoring sources
- Recommended: alert throttle 10–15 minutes per src_ip

## Result Summary (fill in after you measure)
- SSH alerts/day: Baseline __ → Tuned __ (reduction: __%)
- Scan alerts/day: Baseline __ → Tuned __ (reduction: __%)
- True positives validated by: Nmap scan + brute attempts in lab
