---
source: https://docs.kentico.com/documentation/security-advisories/security-advisory-2025-03-27
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Security advisories](/documentation/security-advisories)
  * Security advisory 2025-03-27 


# Security advisory 2025-03-27
## SQL Injection in kentico-xperience-dbmanager tool
**CVSS** : 6.4  
**Affected versions** : 30.3.0 and lower  
**Category** : SQL injection
### Summary
The `kentico-xperience-dbmanager` tool used during the [Xperience installation process](/documentation/developers-and-admins/installation) to install and maintain project databases was vulnerable to SQL injection in the `--database-name` (`-d`) parameter. The issue was fixed by correctly sanitizing the user-provided name.
### How to fix
Update to the latest version. See [Update Xperience by Kentico projects](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) for detailed instructions.
![]()
[]()[]()
