---
source: https://docs.kentico.com/documentation/security-advisories/security-advisory-2025-01-09
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Security advisories](/documentation/security-advisories)
  * Security advisory 2025-01-09 


# Security advisory 2025-01-09
## Broken access control between contact groups and recipient lists
**CVSS** : 5.3  
**Affected versions** : 24.0.0 - 30.0.1  
**Category** : IDOR
### Summary
An authorization issue allowed the modification of [contact group](/documentation/business-users/digital-marketing/contact-groups) objects from the _Recipient lists_ application and [recipient list](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) objects from the _Contact groups_ application, even if the user didn’t have permissions for the related application. After applying the fix, permissions are validated correctly, and only objects belonging to the given application can be modified.
### How to fix
Update to the latest version. See [Update Xperience by Kentico projects](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) for detailed instructions.
![]()
[]()[]()
