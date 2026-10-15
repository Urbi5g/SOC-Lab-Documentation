# Indicators of Compromise

## Confirmed Evidence-Supported Indicators

| Indicator | Type | Evidence | Significance |
|---|---|---|---|
| 192.168.127.129 | IP Address | Evidence/auth.log | Source of confirmed SSH activity to Kali |
| kali | Username | Evidence/auth.log, Evidence/cron_changes.txt | Account used during SSH authentication and Cron modification |
| /usr/bin/crontab | Process | Evidence/cron_changes.txt | Process responsible for modifying the crontab |
| /home/kali/dummy_check.sh | File Path | Evidence/current_crontab.txt | Scheduled Cron target configured to run every 5 minutes |
| TCP/22 | Network Service | Evidence/auth.log | SSH service used for remote access |

## Potential / Requires Correlation

| Indicator | Type | Evidence | Reason |
|---|---|---|---|
| /beacon | URI | Internal Web access.log | HTTP beacon-style activity observed in the lab; requires correlation with the SSH/cron timeline |

## Benign / Contextual Lab Activity

| Indicator | Type | Evidence | Reason |
|---|---|---|---|
| 192.168.127.128 | IP Address | Evidence/auth.log | Multiple SSH sessions originated from Kali itself; these are not evidence of external lateral movement |
| Failed SSH attempt from 192.168.127.128 | Authentication Event | Evidence/auth.log | Self-SSH lab/test activity; source was the same host |

## Notes

No malware hash or cryptographic IOC was established from the available Linux evidence.

The indicators above should be treated as case-specific investigation artifacts,
not as production threat intelligence without additional validation.
