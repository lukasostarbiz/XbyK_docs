---
source: https://docs.kentico.com/documentation/security-advisories/security-advisory-2024-12-05
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Security advisories](/documentation/security-advisories)
  * Security advisory 2024-12-05 


# Security advisory 2024-12-05
## Reflected cross-site scripting (XSS) attack via logger endpoint
**CVSS** : 6.9  
**Affected versions** : 22.0.0 - 29.7.3  
**Category** : XSS
### Summary
The _kenticoactivitylogger_ endpoint was vulnerable to reflected cross-site scripting attacks (XSS) due to improper input validation of the request parameters. To eliminate this vulnerability, additional validation was added to the logger endpoint.
### How to fix
Update to the latest version. See [Update Xperience by Kentico projects](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) for detailed instructions.
![]()
[]()[]()
