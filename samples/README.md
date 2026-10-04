# Practice data

Everything here is synthetic: made up for practice, not taken from a real network. All
addresses come from the ranges reserved for documentation (RFC 5737: 192.0.2.0/24,
198.51.100.0/24, 203.0.113.0/24) and all names from reserved domains (RFC 2606).

| File | What it is | Skill |
|---|---|---|
| `auth.log` | 66 lines of an SSH server log from one night, the log from session 1 | `/triage-log` |
| `traffic-summary.csv` | 96 rows, one per network connection, one hour; columns: time, source and destination address and port, protocol, service, bytes each way, duration, DNS name asked for | `/traffic-summary` |

The internal network in `traffic-summary.csv` is 192.0.2.0/24; 192.0.2.53 is its DNS
server. Your instructor adds the data for each session's lab here or in `evidence/`.
