# Cloud Persistence Detection Guide

## Overview

This guide outlines detection opportunities for common cloud persistence techniques across Microsoft Entra ID, AWS, and Google Cloud Platform (GCP).

Threat actors frequently establish persistence through identity systems, application registrations, service accounts, access keys, and trust relationships. Monitoring these activities is critical for effective cloud threat detection and incident response.

---

# Microsoft Entra ID Detection Opportunities

## New Application Registration

### Description

Attackers may create new application registrations to establish persistence and access cloud resources through OAuth.

### Detection Sources

- Entra Audit Logs
- Microsoft Sentinel
- Microsoft Defender XDR

### Indicators

- New application registrations
- Unexpected application names
- Applications created by non-administrators

### Example KQL

```kql
AuditLogs
| where OperationName contains "Add application"
| project TimeGenerated, InitiatedBy, Result
```

---

## Client Secret Creation

### Description

Client secrets provide application authentication and can act as a persistence mechanism.

### Indicators

- New client secret creation
- Secret creation on dormant applications
- Secret creation outside normal business hours

### Example KQL

```kql
AuditLogs
| where OperationName contains "Add service principal credentials"
| project TimeGenerated, InitiatedBy, TargetResources
```

---

## Admin Consent Events

### Description

Administrative consent grants can provide applications access to sensitive resources.

### Indicators

- New admin consent grants
- Consent granted to recently created applications
- High-risk Graph permissions

### Example KQL

```kql
AuditLogs
| where OperationName contains "Consent"
| project TimeGenerated, InitiatedBy, TargetResources
```

---

## Microsoft Graph Abuse

### Description

Threat actors frequently use Microsoft Graph for reconnaissance and persistence.

### Indicators

- User enumeration
- Group enumeration
- Directory discovery
- Excessive API requests

### Relevant ATT&CK

- T1526 Cloud Service Discovery
- T1087 Account Discovery

---

# AWS Detection Opportunities

## New IAM User Creation

### Description

Attackers may create new IAM users for persistence.

### CloudTrail Event

```text
CreateUser
```

### Indicators

- Newly created users
- AdministratorAccess assignments
- Console access enabled

---

## Access Key Creation

### Description

New access keys provide long-term API access.

### CloudTrail Event

```text
CreateAccessKey
```

### Indicators

- Multiple active keys
- New key creation by privileged users
- Access from unusual locations

---

## IAM Policy Attachments

### Description

Privilege escalation and persistence often involve policy modifications.

### CloudTrail Event

```text
AttachUserPolicy
```

### Indicators

- AdministratorAccess assignments
- Sudden privilege increases
- Policy changes by unusual users

---

## Trust Policy Modification

### Description

Attackers may modify trust policies to allow external accounts to assume roles.

### CloudTrail Event

```text
UpdateAssumeRolePolicy
```

### Indicators

- New trusted external accounts
- Cross-account access paths
- Unusual AssumeRole activity

---

## Lambda Persistence

### Description

Lambda functions may be used to automatically restore access.

### CloudTrail Events

```text
CreateFunction
UpdateFunctionCode
```

### Indicators

- New Lambda functions
- Unexpected code updates
- New event triggers

---

# Google Cloud Platform Detection Opportunities

## Service Account Creation

### Description

Threat actors may create service accounts for persistence.

### Audit Log Event

```text
CreateServiceAccount
```

### Indicators

- New service accounts
- Excessive privileges
- Service accounts created by unusual users

---

## Service Account Key Generation

### Description

Service account keys provide long-term access.

### Audit Log Event

```text
CreateServiceAccountKey
```

### Indicators

- New key generation
- Unexpected API activity
- Access from unusual locations

---

## IAM Policy Changes

### Description

Privilege escalation often involves IAM modifications.

### Audit Log Event

```text
SetIamPolicy
```

### Indicators

- Elevated role assignments
- New administrative permissions
- Unexpected policy updates

---

## Cloud Function Persistence

### Description

Cloud Functions may be abused to recreate deleted credentials or modify IAM policies.

### Audit Log Event

```text
CreateFunction
```

### Indicators

- New functions
- New event triggers
- Functions interacting with IAM resources

---

# MITRE ATT&CK Mapping

| Technique | ATT&CK ID |
|------------|------------|
| Account Manipulation | T1098 |
| Valid Accounts | T1078 |
| Create Cloud Account | T1136.003 |
| Cloud Service Discovery | T1526 |
| Account Discovery | T1087 |
| Serverless Execution | T1505.003 |
| Steal Application Access Token | T1528 |
| Use Alternate Authentication Material | T1550 |

---

# Threat Hunting Priorities

Analysts investigating potential cloud persistence should prioritize:

1. New application registrations
2. New client secrets
3. Admin consent grants
4. IAM user creation
5. Access key generation
6. Trust policy modifications
7. Service account creation
8. Service account key generation
9. Serverless function deployments
10. Privileged role assignments

---

# Key Takeaways

Cloud persistence often relies on legitimate identity and access management functionality rather than malware.

Security teams should continuously monitor:

- Identity changes
- Permission assignments
- Authentication material creation
- OAuth applications
- Service accounts
- Cross-account trust relationships

Effective detection of these activities significantly improves an organization's ability to identify and respond to cloud intrusions.