# Lab 04 — Investigating an Office 365 Mailbox Permission Alert in Wazuh

## Objective
Investigate an `Add-MailboxPermission` alert in Wazuh and determine whether the activity is expected, benign, suspicious, or malicious.

## Environment
- Wazuh 4.14.5
- Microsoft 365 Business Standard
- Microsoft Purview Audit (Standard)
- Office 365 Management Activity API
- Wazuh Office 365 integration

## Alert
Initial alert observed in the Wazuh Office 365 dashboard:

- Operation: `Add-MailboxPermission`
- Wazuh rule group: `office365`
- Alert severity: to be confirmed during triage

## Investigation Questions
1. Who initiated the permission change?
2. Which mailbox was affected?
3. Which account or principal received access?
4. What permission was granted (`FullAccess`, `SendAs`, etc.)?
5. What source IP or service initiated the operation?
6. Was the result successful?
7. Were there related events before or after this activity?
8. Is the actor a human administrator, service account, or Microsoft-managed process?

## Triage Workflow
1. Open the event in **Cloud Security → Office 365 → Events**.
2. Expand the raw event details.
3. Record the important fields while redacting personal information before publishing.
4. Search for related events for the same actor, target mailbox, source IP, and time window.
5. Classify the alert.

## Evidence to Capture
> Do not publish passwords, client secrets, tokens, tenant secrets, private IP details that are not needed, or unredacted personal email addresses.

Capture or record:
- `Operation`
- `UserId`
- `ClientIP`
- `ObjectId`
- `ResultStatus`
- `Workload`
- `CreationTime`
- `Parameters`
- Wazuh `rule.id`
- Wazuh `rule.level`
- Wazuh `rule.description`

## Findings
_To be completed during investigation._

## Related Activity
_To be completed during correlation._

## Classification
_To be completed._

Possible outcomes:
- True Positive — Malicious
- True Positive — Benign / Authorized
- False Positive
- Needs Further Investigation

## Root Cause / Explanation
_To be completed._

## Recommended Response
_To be completed._

## SOC Lessons Learned
_To be completed after analysis._
