# Threat Research Notes

## Research Objective

The objective of this project was to understand how threat actors establish persistence in cloud environments through identity and access management (IAM) systems rather than traditional malware-based techniques.

Modern cloud intrusions frequently focus on identities, tokens, application registrations, service accounts, and trust relationships because they provide durable access while blending into legitimate administrative activity.

---

## Key Observation 1: Identity Is the New Perimeter

Traditional attacks often relied on endpoint persistence mechanisms such as registry modifications, scheduled tasks, services, or malware.

Cloud environments shift the attack surface toward:

* IAM Users
* OAuth Applications
* Service Accounts
* Access Keys
* Trust Relationships
* Refresh Tokens

As organizations move workloads to the cloud, identity becomes one of the most critical security boundaries.

---

## Key Observation 2: Persistence Without Malware

Many cloud persistence techniques do not require malware deployment.

Examples include:

* Creating application registrations
* Generating client secrets
* Creating service account keys
* Establishing cross-account trust relationships
* Granting excessive permissions

These actions often appear as legitimate administrative activity.

---

## Key Observation 3: OAuth and Application Abuse

OAuth applications provide a powerful persistence mechanism.

An attacker who creates or compromises an application can potentially maintain access through:

* Client secrets
* OAuth tokens
* Refresh tokens
* Delegated permissions

This persistence can remain active even after user passwords are changed.

---

## Key Observation 4: Long-Lived Credentials Remain High Risk

Across Azure, AWS, and GCP, attackers frequently target long-lived credentials.

Examples include:

### Azure

* Client Secrets
* Refresh Tokens

### AWS

* Access Keys

### GCP

* Service Account Keys

These credentials provide access without requiring interactive user authentication.

---

## Key Observation 5: Legitimate Features Become Attack Paths

Many cloud persistence techniques leverage intended platform functionality.

Examples include:

* AWS IAM trust policies
* Microsoft Entra application registrations
* GCP service accounts

Because these features are legitimate, detection often requires behavioral analysis rather than signature-based approaches.

---

## MITRE ATT&CK Themes Observed

The following ATT&CK techniques appeared repeatedly across cloud providers:

| Technique                             | ATT&CK ID |
| ------------------------------------- | --------- |
| Account Manipulation                  | T1098     |
| Valid Accounts                        | T1078     |
| Create Cloud Account                  | T1136.003 |
| Steal Application Access Token        | T1528     |
| Use Alternate Authentication Material | T1550     |

This suggests that identity-focused ATT&CK techniques are highly relevant for cloud threat hunting.

---

## Detection Considerations

Defenders should prioritize monitoring:

* New application registrations
* Client secret creation
* OAuth consent grants
* Access key generation
* Service account key creation
* IAM policy modifications
* Trust relationship changes
* Privileged role assignments

Many of these activities occur infrequently in mature environments and may provide strong detection opportunities.

---

## Analyst Takeaways

This research reinforced several important concepts:

1. Cloud persistence is primarily an identity problem.
2. Attackers increasingly abuse legitimate cloud administration features.
3. Long-lived credentials remain a major risk factor.
4. OAuth and application registrations represent significant attack surfaces.
5. Effective cloud threat hunting requires visibility into IAM activity.

Understanding cloud identity abuse is essential for modern threat intelligence, threat hunting, incident response, and cloud security operations.

---

## Future Research Areas

Potential future projects include:

* OAuth Consent Phishing
* Microsoft Graph Abuse
* AWS Cross-Account Persistence
* Azure Managed Identity Abuse
* Service Principal Persistence
* Identity Attack Path Analysis
* Cloud Detection Engineering
