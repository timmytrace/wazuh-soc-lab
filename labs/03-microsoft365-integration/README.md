# Lab 03 — Microsoft 365 Audit Integration with Wazuh

## Objective

Integrate Microsoft 365 audit activity with Wazuh so cloud identity, Exchange, SharePoint/OneDrive, and general Microsoft 365 events can be investigated from the Wazuh dashboard.

## Environment

- Wazuh 4.14.5 single-node Docker deployment
- Ubuntu Server
- Microsoft 365 Business Standard
- Microsoft Purview Audit
- Microsoft Entra ID app registration

## Architecture

```text
Microsoft 365
   ↓
Purview Audit
   ↓
Office 365 Management Activity API
   ↓
Entra App Registration
   ↓
Wazuh Office365 Module
   ↓
Wazuh Manager
   ↓
Filebeat
   ↓
Wazuh Indexer
   ↓
Wazuh Dashboard
```

## Microsoft 365 Preparation

Unified auditing was verified with Exchange Online PowerShell:

```powershell
Get-AdminAuditLogConfig | Format-List UnifiedAuditLogIngestionEnabled
```

Expected result:

```text
UnifiedAuditLogIngestionEnabled : True
```

An Entra application was created for Wazuh with the Office 365 Management API application permission:

```text
ActivityFeed.Read
```

Admin consent was granted for the tenant.

## Wazuh Configuration

The Office 365 module was added to the Wazuh manager configuration.

Example with sensitive values removed:

```xml
<office365>
  <enabled>yes</enabled>
  <interval>1m</interval>
  <curl_max_size>1M</curl_max_size>
  <only_future_events>yes</only_future_events>

  <api_auth>
    <tenant_id>REDACTED</tenant_id>
    <client_id>REDACTED</client_id>
    <client_secret_path>/var/ossec/etc/m365_client_secret</client_secret_path>
    <api_type>commercial</api_type>
  </api_auth>

  <subscriptions>
    <subscription>Audit.AzureActiveDirectory</subscription>
    <subscription>Audit.Exchange</subscription>
    <subscription>Audit.SharePoint</subscription>
    <subscription>Audit.General</subscription>
  </subscriptions>
</office365>
```

The client secret was stored in a separate protected file rather than directly in `ossec.conf`.

Example Docker bind mount:

```yaml
- ./config/wazuh_cluster/m365_client_secret:/var/ossec/etc/m365_client_secret:ro
```

## Validation

The Wazuh module configuration was tested with:

```bash
docker exec single-node-wazuh.manager-1 /var/ossec/bin/wazuh-modulesd -t
```

The secret mount was validated without exposing its contents:

```bash
docker exec single-node-wazuh.manager-1 ls -l /var/ossec/etc/m365_client_secret
```

Microsoft 365 events were confirmed in Wazuh alerts:

```bash
docker exec single-node-wazuh.manager-1 sh -c 'grep -i office365 /var/ossec/logs/alerts/alerts.json | tail -n 10'
```

The dashboard was filtered using:

```text
rule.groups:office365
```

## Test Activity

Harmless OneDrive activity was generated to create audit events:

- Create a test file
- Rename it
- Delete it

This confirmed that Microsoft 365 audit activity was reaching `alerts.json` and the dashboard.

## Break Scenario — Invalid Client Secret

### Symptom

Wazuh reported an invalid Microsoft client secret.

### Investigation

The application registration, permission, and secret file mount were checked. The secret value was recreated and carefully re-entered.

### Root Cause

The Microsoft client secret value did not exactly match the active secret configured in Entra.

### Fix

A new client secret was created and the protected secret file was updated. The Wazuh manager was recreated.

### Verification

Wazuh reported that the Office 365 tenant connected successfully, and JSON Office 365 alerts appeared again.

## Dashboard Lesson

At one point the dashboard appeared to show only `Add-MailboxPermission` events. The cause was an active severity filter:

```text
rule.level: 7 to 11
```

Removing that filter exposed the wider set of Microsoft 365 events. This demonstrated the importance of checking active SIEM filters before concluding that telemetry is missing.

## SOC Lessons Learned

- Successful API authentication does not prove end-to-end ingestion; verify actual alerts.
- Validate each stage of the pipeline independently.
- Keep client secrets outside the main configuration where possible.
- Dashboard filters can hide healthy telemetry.
- Microsoft audit events may arrive with delay, so ingestion tests should allow time for cloud-side publication.
- Configuration validation before restart reduces the chance of breaking a working SIEM.

## Sensitive Data Handling

No tenant secrets, passwords, tokens, or private credentials are stored in this repository.
