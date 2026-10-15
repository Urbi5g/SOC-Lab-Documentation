# MITRE ATT&CK Mapping

## T1021.004 — Remote Services: SSH

Evidence:
- Evidence/auth.log
- Source IP: 192.168.127.129
- Destination: 192.168.127.128
- Successful SSH authentication for user `kali`

Confidence: High

Reasoning:
The evidence directly shows an SSH connection from Windows-01 to Kali
followed by successful authentication.

---

## T1053.003 — Scheduled Task/Job: Cron

Evidence:
- Evidence/cron_changes.txt
- Evidence/current_crontab.txt
- `/usr/bin/crontab`
- `key="cron_changes"`
- `*/5 * * * * /home/kali/dummy_check.sh`

Confidence: High

Reasoning:
Auditd confirms modification of the user's crontab, and the preserved
crontab contains a scheduled job for `/home/kali/dummy_check.sh`.

---

## T1078 — Valid Accounts

Evidence:
- Evidence/auth.log
- Successful authentication for the `kali` account

Confidence: Medium

Reasoning:
A valid local account was successfully used for SSH authentication.
However, the evidence does not establish whether the account usage was
authorized or malicious.

---

## T1059 — Command and Scripting Interpreter

Evidence:
- Evidence/auth.log shows an SSH command session.
- Evidence/process_exec_scenario.txt contains process execution telemetry.

Confidence: Low / Requires Validation

Reasoning:
A remote command session is confirmed, but the preserved evidence does
not conclusively identify the exact command executed during that session.

---

## T1110 — Brute Force

Confidence: Not Supported

Reasoning:
A failed SSH authentication exists in the evidence, but it originated
from 192.168.127.128 (Kali itself), not from the Windows source
192.168.127.129. The available evidence therefore does not establish
brute-force activity against Kali.
