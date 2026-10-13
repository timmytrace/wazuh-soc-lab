# Wazuh SOC Lab

Hands-on SOC and SIEM portfolio focused on Wazuh, Microsoft 365 monitoring, detection engineering, investigations, and break/fix troubleshooting.

## Goals

This repository documents practical security work using a repeatable workflow:

**Build → Verify → Break → Investigate → Fix → Verify → Document**

The aim is to develop transferable SOC skills rather than only learn where buttons are in one SIEM.

## Current Environment

- Ubuntu Server host
- Docker / Docker Compose
- Wazuh 4.14.5 single-node deployment
  - Wazuh Manager
  - Wazuh Indexer
  - Wazuh Dashboard
- Microsoft 365 Business Standard
- Microsoft Purview Audit
- Microsoft Entra app registration
- Microsoft 365 audit ingestion into Wazuh

## Portfolio Labs

| Lab | Topic | Status |
| --- | --- | --- |
| 01 | Wazuh deployment and architecture | Planned |
| 02 | Linux endpoint monitoring | Planned |
| 03 | Microsoft 365 audit integration | Complete |
| 04 | Office 365 alert investigation | In progress |
| 05 | Custom Wazuh rules | Planned |
| 06 | File Integrity Monitoring | Planned |
| 07 | Authentication detections | Planned |
| 08 | Vulnerability detection | Planned |
| 09 | Active Response | Planned |

## Break/Fix Cases

- Microsoft 365 invalid client secret
- Wazuh indexer authentication mismatch during password rotation
- Ubuntu network connectivity / Wi-Fi configuration

## Security Note

This repository intentionally excludes real passwords, client secrets, Wi-Fi credentials, private keys, tokens, and other sensitive values. Example configurations use placeholders such as `REDACTED`.

## Skills Demonstrated

- SIEM deployment and administration
- Microsoft 365 audit ingestion
- Wazuh rule analysis
- Linux troubleshooting
- Docker troubleshooting
- Log validation and pipeline verification
- SOC alert triage
- Root-cause analysis
- Break/fix recovery
- Detection engineering

## Workflow Used in Each Lab

1. Objective
2. Environment
3. Architecture
4. Configuration
5. Validation
6. Break scenario
7. Symptoms
8. Investigation
9. Root cause
10. Fix
11. Verification
12. SOC lessons learned

---

This repository is a living portfolio and will grow as new Wazuh detections, investigations, integrations, and recovery scenarios are completed.
