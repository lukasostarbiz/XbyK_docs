---
source: https://docs.kentico.com/guides/architecture/xperience-implementation-handbook/security-checklists/security-checklist-deployment-phase
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Xperience implementation handbook](/guides/architecture/xperience-implementation-handbook)
  * [Security checklists](/guides/architecture/xperience-implementation-handbook/security-checklists)
  * Security checklist - deployment phase 


# Security checklist - deployment phase
If you don’t want to spend time configuring and maintaining deployments on your own, consider leveraging our [SaaS](/documentation/developers-and-admins/saas) offering, where Kentico takes care of security, compliance, and infrastructure management for you.
## Web.config and application settings
  * Make sure to disable debug and developer modes to prevent leaks of sensitive information.
  * Ensure that the application and server error messages do not expose detailed information to end users.
  * Store sensitive configuration values (like connection strings) securely using a secrets manager or encrypted storage. Never store secrets in plain text.
  * Forbid access to sensitive directories to protect the servers against data harvesting attacks.
  * Set _HttpOnly_ , _Secure_ , and _SameSite_ flags for [cookies](/documentation/developers-and-admins/data-protection/cookies#set-and-work-with-cookies).
  * Enforce HTTPS and enable HSTS.


## IIS and hosting environment best practices
  * Disable directory browsing and unnecessary HTTP methods.
  * Restrict script execution in upload and export folders using IIS Handler Mappings.
  * Apply the latest patches for IIS, .NET, and your operating system.
  * Enable request filtering to block dangerous file types and large payloads.
  * Enable Dynamic IP Restrictions to mitigate brute-force attacks.
  * Enforce HTTPS with TLS 1.2+. Disable legacy protocols and weak cipher suites.
  * Use least-privilege Application Pool identities (one per application – do not share identities between different instances).
  * Configure rate limiting or throttling for high-workload endpoints.
  * Enable advanced logging and monitor anomalies.


## Cloud and deployment security
  * Use HTTPS with TLS 1.2+.
  * Use managed identities and apply the least-privilege access level.
  * Store secrets in [Azure Key Vault](https://learn.microsoft.com/en-us/azure/key-vault/secrets/secure-secrets) or similar.
  * Enable Web Application Firewall (WAF).
  * Enable logging and monitoring via [Azure Monitor](https://learn.microsoft.com/en-us/azure/azure-monitor/fundamentals/overview) or similar.
  * Harden your infrastructure to ensure high-availability and security of all resources.
  * Establish disaster recovery plans and validate them periodically.


![]()
[]()[]()
