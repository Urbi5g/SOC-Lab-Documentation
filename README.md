# Resilient SOC — Case #001
## Operation Blackout

## الهدف
بناء وتحليل Incident أمني داخل بيئة SOC تدريبية معزولة، بداية من
جمع الـTelemetry وحتى الـDetection والتحقيق والتوثيق.

## البيئة

- Windows-01: 192.168.127.129
- Linux-01 / Kali: 192.168.127.128
- Ubuntu / Splunk: 192.168.127.130
- Splunk Receiving: 192.168.127.130:9997

## مصادر البيانات

### Linux
- auth.log
- audit.log
- syslog

### Windows
- Sysmon
- Windows Security
- PowerShell

### Internal Web
- access.log

## السيناريو

Windows-01
↓
SSH
↓
Linux/Kali
↓
Remote Session
↓
Cron Modification
↓
Cron Persistence

## أهم النتائج

تم تأكيد:
- SSH من Windows إلى Kali.
- Successful Authentication لحساب kali.
- Remote Command Session.
- تعديل Cron بواسطة /usr/bin/crontab.
- إعداد Cron Job لتشغيل /home/kali/dummy_check.sh كل 5 دقائق.

لم يتم إثبات:
- تنفيذ dummy_check.sh بشكل قاطع.
- Data Exfiltration.
- Data Destruction.
- Business Impact.

## MITRE ATT&CK

- T1021.004 — Remote Services: SSH
- T1053.003 — Scheduled Task/Job: Cron
- T1078 — Valid Accounts (Medium Confidence)
- T1059 — Command and Scripting Interpreter (Requires Validation)

T1110 — Brute Force غير مدعوم بالأدلة الحالية.

## المخرجات

- Evidence
- Timeline
- Attack Chain
- MITRE Mapping
- IOC Analysis
- Splunk Detections
- Investigation Playbook
- Incident Report

## حالة المشروع

Investigation and Detection completed.
Final quality review and packaging pending.

