# APEX SOC Lab — SSH Threat Detection & Incident Response

## Project Overview

APEX SOC Lab is a hands-on cybersecurity project demonstrating SSH security monitoring, threat detection, automated IP blocking, and incident investigation using Ubuntu Linux.

The project simulates repeated failed SSH authentication attempts in an isolated test network and demonstrates how Fail2Ban and nftables work together to detect and contain suspicious activity.

## Lab Environment

| Component | Technology |
|---|---|
| Virtualization | Oracle VirtualBox |
| Operating System | Ubuntu Linux 24.04 LTS |
| Lab Hostname | APEX-LNX-01 |
| SSH Server | OpenSSH |
| Detection and Response | Fail2Ban |
| Firewall | nftables |
| Log Investigation | journalctl and Fail2Ban logs |
| Test Environment | Linux network namespace and virtual Ethernet interfaces |

## Project Objectives

- Configure and validate SSH authentication monitoring.
- Detect repeated failed SSH login attempts.
- Automatically ban suspicious source IP addresses.
- Verify firewall enforcement.
- Correlate authentication logs with security response events.
- Document a simulated security incident.

## Lab Architecture

Windows Host (Management)
        |
        | SSH management connection
        v
Ubuntu VM — APEX-LNX-01
        |
        | OpenSSH + Fail2Ban + nftables
        |
        +---- Isolated Test Network
                  |
                  +---- soc-test namespace
                        Source: 192.168.50.2
                        Target: 192.168.50.1:22

The isolated test environment was removed after the simulation.

## Security Configuration

The Fail2Ban SSH jail was configured with:

| Setting | Value |
|---|---|
| maxretry | 3 |
| findtime | 600 seconds |
| bantime | 600 seconds |

Fail2Ban monitored SSH authentication events and applied nftables blocking rules after the configured failure threshold was reached.

## Incident INC-001 — Simulated SSH Brute-Force Activity

**Date:** November 24, 2026

**Target account:** socadmin

**Source IP:** 192.168.50.2

**Destination:** 192.168.50.1:22

### Investigation Timeline

| Time | Event |
|---|---|
| 21:42:01 | First failed SSH password |
| 21:45:35 | Second failed SSH password |
| 21:46:58 | Third failed SSH password |
| 21:46:59 | Fail2Ban automatically banned the source IP |
| 21:53:39 | Source IP manually unbanned |

Times are based on the Ubuntu VM logs.

### Detection and Response

1. Generated controlled failed SSH login attempts.
2. Verified authentication failures using journalctl.
3. Confirmed Fail2Ban detected the failed attempts.
4. Observed automatic banning of 192.168.50.2.
5. Verified the source IP was added to the nftables blocking set.
6. Confirmed a subsequent SSH connection was refused.
7. Unbanned the test IP and removed the isolated test network.

## Results

- SSH authentication monitoring: Passed
- Failed login detection: Passed
- Automatic Fail2Ban banning: Passed
- nftables firewall rule verification: Passed
- Connection blocking verification: Passed
- Incident evidence preservation: Completed
- Incident investigation report: Completed

## Incident Documentation

See [INC-001 Incident Report](incidents/INC-001-SSH-Brute-Force/incident-report.md).

Raw log evidence is retained privately until reviewed and sanitized for publication.

## Skills Demonstrated

- Linux security administration
- SSH authentication monitoring
- Security event detection
- Fail2Ban configuration and analysis
- nftables firewall verification
- Log correlation and incident investigation
- Automated security response validation
- SOC incident documentation

## Security Recommendations

- Prefer SSH key-based authentication.
- Restrict SSH access to trusted networks where feasible.
- Review authentication logs and Fail2Ban alerts regularly.
- Tune ban thresholds to match security requirements.
- Maintain incident evidence and documented response procedures.

## Disclaimer

This project was conducted in an authorized, isolated lab environment for educational and portfolio purposes. No external systems were targeted.
