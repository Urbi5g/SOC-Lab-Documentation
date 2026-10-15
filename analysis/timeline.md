# Incident Timeline

| Timestamp | Host | User | Source IP | Event | Evidence | Significance |
|---|---|---|---|---|---|---|
| 2026-08-26 04:33:12 -04:00 | Kali | kali | 192.168.127.129 | Incoming SSH connection to TCP/22 | auth.log | Initial remote access attempt from Windows-01 |
| 2026-08-26 04:33:23 -04:00 | Kali | kali | 192.168.127.129 | Successful SSH authentication | auth.log | Confirms successful remote authentication |
| 2026-08-26 04:33:23 -04:00 | Kali | kali | 192.168.127.129 | SSH command session started | auth.log | Indicates a remote command/session was initiated |
| 2026-08-26 04:33:23 -04:00 | Kali | kali | 192.168.127.129 | SSH session closed | auth.log | Remote command session ended |
| 2026-08-26 04:37:08 -04:00 | Kali | kali | Local | Cron modification | cron_changes.txt | User kali modified the crontab using /usr/bin/crontab |
| 2026-08-26 04:37:08 -04:00 | Kali | kali | Local | Persistence configured | current_crontab.txt | Cron entry configured to execute /home/kali/dummy_check.sh every 5 minutes |
