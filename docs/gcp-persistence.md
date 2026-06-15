# Google Cloud Platform (GCP) Identity Persistence Techniques

## Overview

Google Cloud Platform (GCP) environments rely heavily on IAM roles, service accounts, and API access for authentication and authorization. Threat actors who compromise GCP environments often establish persistence through identity-based mechanisms rather than traditional malware.

This document examines common GCP persistence techniques from a cloud security and threat intelligence perspective.

---

## Threat Model

### Adversary Objectives

- Maintain long-term access
- Survive credential rotation
- Establish alternate access paths
- Abuse cloud-native services
- Avoid endpoint detections

### Target Assets

- Service Accounts
- IAM Roles
- API Keys
- OAuth Tokens
- Cloud Functions
- Compute Engine Instances

---

## Technique 1: Service Account Key Creation

### Description

An attacker creates a new service account key for an existing service account.

### Why It Matters

Service account keys can provide long-term API access without requiring user authentication.

### Detection Opportunities

- Service account key creation events
- New API access from unusual IP addresses
- Unrecognized service account activity

### Relevant Logs

- Cloud Audit Logs

### MITRE ATT&CK Mapping

- T1098 – Account Manipulation

---

## Technique 2: Create New Service Accounts

### Description

An attacker creates a new service account and grants excessive permissions.

### Attack Flow

Threat Actor

↓

Create Service Account

↓

Assign IAM Roles

↓

Generate Key

↓

Persistent Access

### Detection Opportunities

- Service account creation
- IAM role assignments
- New authentication activity

### Relevant Logs

- Cloud Audit Logs

### MITRE ATT&CK Mapping

- T1136.003 – Create Cloud Account

---

## Technique 3: IAM Role Abuse

### Description

An attacker grants elevated IAM permissions to existing users or service accounts.

### Examples

- Owner
- Editor
- Security Admin
- Service Account Admin

### Detection Opportunities

- IAM policy changes
- New role assignments
- Privilege escalation events

### Relevant Logs

- Cloud Audit Logs

### MITRE ATT&CK Mapping

- T1098 – Account Manipulation

---

## Technique 4: Cloud Function Persistence

### Description

An attacker deploys a malicious Cloud Function capable of recreating users, modifying IAM policies, or restoring deleted credentials.

### Benefits to Adversaries

- Serverless execution
- Difficult visibility
- Event-driven automation

### Detection Opportunities

- Function deployments
- Function modifications
- New event triggers

### Relevant Logs

- Cloud Audit Logs
- Cloud Logging

### MITRE ATT&CK Mapping

- T1505.003 – Serverless Execution

---

## Technique 5: OAuth Application Abuse

### Description

An attacker creates or abuses OAuth applications to maintain persistent access to cloud resources.

### Why It Matters

OAuth tokens may remain valid even after password changes.

### Detection Opportunities

- New OAuth applications
- New consent grants
- Unusual token activity

### Relevant Logs

- Google Workspace Audit Logs
- Cloud Audit Logs

### MITRE ATT&CK Mapping

- T1528 – Steal Application Access Token

---

## Detection Examples

### Monitor Service Account Creation

```text
CreateServiceAccount
```

### Monitor Service Account Key Creation

```text
CreateServiceAccountKey
```

### Monitor IAM Policy Changes

```text
SetIamPolicy
```

### Monitor Cloud Function Creation

```text
CreateFunction
```

---

## Threat Hunting Checklist

Review:

- Newly created service accounts
- Newly generated service account keys
- IAM role modifications
- Cloud Function deployments
- OAuth consent grants
- Privileged role assignments
- Unusual API activity

---

## Real-World Threat Relevance

Cloud-focused threat actors increasingly target service accounts and cloud identities because they provide persistence without deploying malware.

Cloud persistence is attractive because:

- Credentials are often overlooked
- Service accounts can be highly privileged
- Cloud-native logging may not be actively monitored
- Access often survives workstation remediation

---

## MITRE ATT&CK Mapping Summary

| Technique | ATT&CK ID |
|------------|------------|
| Account Manipulation | T1098 |
| Create Cloud Account | T1136.003 |
| Serverless Execution | T1505.003 |
| Steal Application Access Token | T1528 |
| Valid Accounts | T1078 |

---

## Key Research Takeaways

GCP persistence commonly revolves around identity abuse rather than malware deployment.

Security teams should prioritize monitoring:

1. Service account creation
2. Service account key generation
3. IAM policy changes
4. Cloud Function deployments
5. OAuth application activity
6. Privileged role assignments

These techniques are frequently observed during cloud intrusions and post-compromise operations.

Understanding service account abuse is critical for threat intelligence, incident response, and cloud security monitoring.