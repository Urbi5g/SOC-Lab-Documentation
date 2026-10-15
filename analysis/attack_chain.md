# Attack Chain

## 1. Initial Access / Remote Access

A remote SSH connection was received by Kali from Windows-01
(192.168.127.129) on TCP/22.

Evidence:
- File: Evidence/auth.log
- Timestamp: 2026-08-26 04:33:12 -04:00
- Source IP: 192.168.127.129
- Destination: 192.168.127.128

Confidence: Confirmed

---

## 2. Authentication

The SSH authentication succeeded for user `kali`.

Evidence:
- File: Evidence/auth.log
- Timestamp: 2026-08-26 04:33:23 -04:00
- Event: Accepted password
- Source IP: 192.168.127.129

Confidence: Confirmed

---

## 3. Remote Command Session

The SSH service recorded a command session for user `kali`
originating from 192.168.127.129.

Evidence:
- File: Evidence/auth.log
- Timestamp: 2026-08-26 04:33:23 -04:00
- Event: Starting session: command

Confidence: Confirmed

Note:
The preserved evidence does not conclusively identify the exact command
executed during this remote command session.

---

## 4. Persistence Configuration

The user `kali` modified the crontab using `/usr/bin/crontab`.
Auditd recorded the activity with the key `cron_changes`.

Evidence:
- File: Evidence/cron_changes.txt
- Timestamp: 2026-08-26 04:37:08 -04:00
- Process: `/usr/bin/crontab`
- Key: `cron_changes`
- AUID: `kali`

Confidence: Confirmed

---

## 5. Cron Persistence

The preserved current crontab contains:

`*/5 * * * * /home/kali/dummy_check.sh`

This configures the script to run every five minutes.

Evidence:
- File: Evidence/current_crontab.txt

Confidence: Confirmed

---

## 6. Scheduled Execution

Execution of `/home/kali/dummy_check.sh` could not be conclusively
confirmed from the preserved audit evidence.

Evidence searched:
- Evidence/audit.log
- Evidence/process_exec_scenario.txt

Confidence: Unknown

Conclusion:
Persistence was configured, but actual script execution is not proven
by the available evidence.

---

## 7. Impact

No conclusive evidence of data destruction, exfiltration, privilege
escalation, or other business impact was identified in the reviewed
evidence.

Confidence: Unknown

Statement:
Impact could not be conclusively determined from the available evidence.
