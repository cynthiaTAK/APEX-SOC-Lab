# APEX SOC Lab — Incident Investigation Report

## Incident Information
- Incident ID: INC-001
- Incident Type: Simulated SSH Brute-Force / Repeated Authentication Failures
- Date: November 24, 2026
- System: APEX-LNX-01 (Ubuntu Linux)
- Target Account: socadmin
- Source IP: 192.168.50.2
- Destination IP: 192.168.50.1
- Destination Port: TCP 22
- Status: Resolved — Controlled Lab Simulation

## 1. Executive Summary
A controlled SSH attack simulation was conducted against APEX-LNX-01 using an isolated Linux network namespace. Three unsuccessful password authentication attempts were generated from 192.168.50.2. Fail2Ban detected the failures through SSH logs and automatically banned the source IP using nftables. A subsequent SSH connection was refused, confirming firewall enforcement. The test IP was manually unbanned and the temporary network was removed.

## 2. Incident Timeline
| Time | Event |
|---|---|
| 21:42:01 | First failed SSH password attempt |
| 21:45:35 | Second failed SSH password attempt |
| 21:46:58 | Third failed SSH password attempt |
| 21:46:59 | Fail2Ban automatically banned 192.168.50.2 |
| 21:53:39 | Test IP manually unbanned |

All timestamps are from November 24, 2026, in the Ubuntu VM's local log time.

## 3. Detection and Analysis
The SSH authentication journal recorded three failed password attempts for the socadmin account from 192.168.50.2. Fail2Ban's sshd jail independently logged three detection events for the same source address.

Configured Fail2Ban controls:
- maxretry: 3
- findtime: 600 seconds
- bantime: 600 seconds

The Fail2Ban log recorded the automatic ban after the third failed attempt.

## 4. Containment and Verification
Fail2Ban added 192.168.50.2 to the nftables addr-set-sshd set. The firewall rule rejected TCP connections to destination port 22 from IP addresses in that set.

An SSH connection from the isolated test namespace returned 'Connection refused' while the ban was active. The Windows management connection at 10.0.2.2 remained accessible.

## 5. Resolution
The source IP was manually unbanned. The isolated soc-test network namespace and virtual Ethernet interfaces were removed. The original SSH and Fail2Ban evidence was preserved for investigation.

## 6. Security Recommendations
- Continue monitoring SSH authentication failures and Fail2Ban alerts.
- Prefer SSH key-based authentication and consider disabling password authentication after validating key access.
- Restrict administrative SSH access to trusted management networks where practical.
- Review ban thresholds and durations against operational requirements.
- Preserve relevant authentication and response logs for incident investigation.

## 7. Evidence
- apex-ssh-incident.log — SSH authentication records
- apex-fail2ban-incident.log — Fail2Ban detection, ban, and unban records
- nftables verification — observed during the controlled test

## 8. Conclusion
The controlled simulation demonstrated successful SSH log monitoring, repeated authentication failure detection, automatic source-IP banning, and nftables enforcement. No successful unauthorized authentication was observed in the reviewed evidence. This was an authorized lab exercise, not a real-world compromise.
