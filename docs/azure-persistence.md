# Azure / Microsoft Entra Persistence Techniques

## Overview

Cloud identity platforms provide multiple mechanisms that can be abused by threat actors to establish persistence within a tenant.

This lab demonstrates how application registrations, delegated permissions, and client secrets can be used to maintain access in Microsoft Entra ID.

## Lab Environment

* Microsoft Entra ID
* Research-App Application Registration
* Microsoft Graph API
* Delegated Permissions
* Client Secret Authentication

## Technique 1: Malicious Application Registration

Attackers may create a new application registration within a tenant.

Benefits:

* Independent authentication mechanism
* Separate from user passwords
* Can be granted Microsoft Graph permissions

MITRE ATT&CK:

* T1098 Account Manipulation

## Technique 2: Client Secret Persistence

A client secret was created for the Research-App application.

Client secrets act similarly to passwords for applications.

If compromised, an attacker can authenticate as the application without requiring the user's password.

MITRE ATT&CK:

* T1550 Use Alternate Authentication Material

## Technique 3: Delegated Microsoft Graph Permissions

The following permissions were granted:

* User.Read
* openid
* profile
* email
* offline_access

These permissions allow applications to obtain identity and access tokens on behalf of users.

## Technique 4: Refresh Token Abuse

The offline_access permission enables issuance of refresh tokens.

Threat actors frequently target refresh tokens because they:

* Survive browser sessions
* Enable long-term access
* May bypass traditional password resets

MITRE ATT&CK:

* T1528 Steal Application Access Token

## Detection Opportunities

Defenders should monitor:

* New application registrations
* New client secret creation
* Admin consent grants
* Unusual Microsoft Graph activity
* OAuth permission changes

## Conclusion

Application registrations and OAuth permissions represent a significant cloud persistence vector. Monitoring identity activity is critical for detecting and responding to cloud-based intrusions.
