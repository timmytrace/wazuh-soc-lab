# Lab Architecture

## Current SOC Lab

```text
                    Microsoft 365
                         │
                         │ Audit activity
                         ▼
               Microsoft Purview Audit
                         │
                         ▼
          Office 365 Management Activity API
                         │
                         ▼
               Wazuh Office365 Module
                         │
                         ▼
Ubuntu Server ─────► Wazuh Manager
   │                     │
   │                     ▼
   │                 Filebeat
   │                     │
   │                     ▼
   │                Wazuh Indexer
   │                     │
   │                     ▼
   └──────────────► Wazuh Dashboard
```

## Ubuntu Host Responsibilities

The Ubuntu server currently hosts the Wazuh single-node Docker deployment and other homelab services. The Wazuh portion contains:

- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard
- Filebeat
- Microsoft 365 audit ingestion

## Monitoring Scope

Current focus:

- Ubuntu security telemetry
- Microsoft 365 audit activity
- Exchange activity
- SharePoint / OneDrive activity
- Entra-related audit events exposed through the Office 365 Management Activity API
- Wazuh rule-based alerting and investigation

## Portfolio Direction

Future labs will extend this architecture with additional endpoint telemetry, custom detections, File Integrity Monitoring, vulnerability detection, and controlled break/fix scenarios.

## Security Principle

Architecture diagrams intentionally omit public IP addresses, secrets, tokens, passwords, private keys, and other sensitive information.
