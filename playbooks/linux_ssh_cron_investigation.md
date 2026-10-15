# Linux SSH + Cron Investigation Playbook

## 1. Alert

Start with an alert related to:

- SSH authentication
- Successful login after failures
- Suspicious cron modification
- Suspicious process execution

---

## 2. Initial Triage

Identify:

- Source IP
- Destination host
- Username
- Timestamp
- Authentication result
- Related process or command
- Whether the activity is expected

Primary evidence:
- auth.log
- audit.log
- syslog

---

## 3. Authentication Investigation

Review SSH authentication events.

Questions:

- Was authentication successful?
- Were there previous failed attempts?
- What source IP initiated the session?
- Which account was used?
- Is the source IP expected?

Example SPL:

```spl
index=resilient_linux_auth host=kali
("Failed password" OR "Accepted password")
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| table _time src_ip user _raw
| sort 0 _time
