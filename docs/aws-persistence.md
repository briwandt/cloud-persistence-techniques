# AWS Identity Persistence Techniques

## Overview

AWS environments are frequently targeted by threat actors seeking long-term access after an initial compromise. Unlike traditional malware persistence, cloud persistence often relies on IAM identities, access keys, trust relationships, and role assumptions.

This document examines common AWS persistence techniques from a threat intelligence and threat hunting perspective.

---

## Threat Model

### Adversary Objectives

- Maintain access after password resets
- Survive account remediation efforts
- Establish alternate access paths
- Access cloud resources without triggering endpoint detections

### Target Assets

- IAM Users
- IAM Roles
- Access Keys
- Lambda Functions
- EC2 Instances
- S3 Buckets

---

## Technique 1: Create Additional Access Keys

### Description

An attacker with IAM permissions may generate additional access keys for an existing user.

### Why It Matters

Organizations often rotate passwords while forgetting to revoke API keys. An attacker can continue accessing AWS resources through the AWS CLI, SDKs, or automation tools even after password changes occur.

### Detection Opportunities

- New access key creation
- Access key usage from new IP addresses
- Access key usage from unusual geographic locations
- Multiple active keys associated with a single user

### Relevant Logs

- AWS CloudTrail
- GuardDuty Findings

### MITRE ATT&CK Mapping

- T1098 – Account Manipulation

---

## Technique 2: Create New IAM User

### Description

An attacker creates a new IAM user and grants elevated permissions.

### Attack Flow

Threat Actor

↓

Create IAM User

↓

Attach AdministratorAccess Policy

↓

Generate Access Keys

↓

Persistent Access

### Detection Opportunities

- CreateUser events
- AttachUserPolicy events
- AdministratorAccess assignments
- Newly created users with console access

### Relevant Logs

- AWS CloudTrail

### MITRE ATT&CK Mapping

- T1136.003 – Create Cloud Account

---

## Technique 3: Backdoor IAM Role Trust Policies

### Description

An attacker modifies IAM trust relationships so an external AWS account can assume privileged roles.

### Example

Original Trust Relationship:

```json
{
  "Principal": {
    "AWS": "123456789012"
  }
}
```

Modified Trust Relationship:

```json
{
  "Principal": {
    "AWS": [
      "123456789012",
      "999999999999"
    ]
  }
}
```

### Why It Matters

Trust policy manipulation can allow attackers to maintain access without relying on compromised credentials.

### Detection Opportunities

- UpdateAssumeRolePolicy events
- New trusted external accounts
- Unexpected AssumeRole activity
- Cross-account authentication events

### MITRE ATT&CK Mapping

- T1098 – Account Manipulation

---

## Technique 4: Lambda Function Persistence

### Description

An attacker deploys a Lambda function capable of recreating deleted users, restoring access keys, or modifying IAM policies automatically.

### Benefits to Adversaries

- Serverless execution
- Difficult to notice
- Event-driven automation
- Survives endpoint remediation efforts

### Detection Opportunities

- CreateFunction events
- UpdateFunctionCode events
- New EventBridge triggers
- Lambda executions modifying IAM resources

### Relevant Logs

- CloudTrail
- CloudWatch Logs
- Lambda Logs

### MITRE ATT&CK Mapping

- T1505.003 – Serverless Execution

---

## Technique 5: Cross-Account Persistence

### Description

An attacker compromises a secondary AWS account and establishes trust relationships between accounts.

### Example Scenario

1. Compromise Account A
2. Create role trust relationship
3. Allow Account B to assume privileged roles
4. Maintain access through cross-account authentication

### Detection Opportunities

- AssumeRole events
- Cross-account trust relationships
- New external principals
- Unusual role assumptions

### Relevant Logs

- AWS CloudTrail
- GuardDuty

### MITRE ATT&CK Mapping

- T1098 – Account Manipulation

---

## CloudTrail Detection Examples

### Detect New IAM Users

```text
eventName = CreateUser
```

### Detect New Access Keys

```text
eventName = CreateAccessKey
```

### Detect Trust Policy Changes

```text
eventName = UpdateAssumeRolePolicy
```

### Detect Policy Attachments

```text
eventName = AttachUserPolicy
```

### Detect New Lambda Functions

```text
eventName = CreateFunction
```

---

## Threat Hunting Checklist

Review the following activities:

- Recently created IAM users
- Recently created access keys
- Recently modified trust policies
- New Lambda functions
- Cross-account role assumptions
- AdministratorAccess assignments
- Suspicious CloudTrail activity
- Unusual geographic login patterns

---

## Real-World Threat Relevance

Cloud persistence has become increasingly common among advanced threat actors, ransomware groups, and nation-state operators.

Common objectives include:

- Maintaining access after incident response actions
- Establishing redundant access mechanisms
- Leveraging cloud-native services for stealth
- Avoiding endpoint-based security controls

Organizations frequently focus on endpoint remediation while overlooking IAM-based persistence.

---

## MITRE ATT&CK Mapping Summary

| Technique | ATT&CK ID |
|------------|------------|
| Account Manipulation | T1098 |
| Create Cloud Account | T1136.003 |
| Serverless Execution | T1505.003 |
| Valid Accounts | T1078 |
| Cloud Service Discovery | T1526 |

---

## Key Research Takeaways

Cloud persistence differs significantly from traditional endpoint persistence. Instead of malware or registry modifications, attackers often leverage legitimate cloud administration features.

Security teams should prioritize monitoring:

1. IAM user creation
2. Access key generation
3. Trust relationship modifications
4. Lambda deployments
5. Cross-account authentication
6. Administrator privilege assignments
7. CloudTrail audit events

These activities frequently appear in cloud intrusions conducted by modern ransomware operators, financially motivated threat actors, and nation-state groups.

Understanding cloud identity abuse is critical for both threat intelligence analysts and cloud security practitioners.