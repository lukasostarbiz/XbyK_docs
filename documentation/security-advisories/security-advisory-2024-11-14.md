---
source: https://docs.kentico.com/documentation/security-advisories/security-advisory-2024-11-14
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Security advisories](/documentation/security-advisories)
  * Security advisory 2024-11-14 


# Security advisory 2024-11-14
## Self-cross-site scripting (XSS) attack via Rich text editor
**CVSS** : 4.8  
**Affected versions** : 22.0.0 - 29.6.3  
**Category** : XSS
### Summary
The [rich text editor](/documentation/business-users/rich-text-editor) in the administration was vulnerable to self-cross-site scripting attacks (XSS) due to improper input validation when switching between _Text_ and _Code View_ mode. To eliminate this vulnerability, additional sanitization was added to the switch of the view mode action.
### How to fix
Update to the latest version. See [Update Xperience by Kentico projects](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) for detailed instructions.
![]()
[]()[]()
