---
source: https://docs.kentico.com/documentation/security-advisories/security-advisory-2025-08-28
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Security advisories](/documentation/security-advisories)
  * Security advisory 2025-08-28 


# Security advisory 2025-08-28
## Magick.NET dependency update to 14.8.1
**CVSS** : 0 (multiple issues with various scores)  
**Affected versions** : 22.0.0 - 30.9.0  
**Category** : Security
### Summary
Xperience by Kentico utilizes the [Magick.NET](https://github.com/dlemstra/Magick.NET) library (**Magick.NET-Q8-AnyCPU** package), which has recently been flagged for multiple vulnerabilities, ranging from **Moderate to High severity**.
As part of our ongoing commitment to platform security, we regularly and automatically update third-party dependencies without explicitly mentioning them in the [changelog](/documentation/changelog) or security advisories.
This advisory is issued to provide transparency and reinforce confidence in our proactive approach to dependency management and platform security.
Accordingly, the **Magick.NET-Q8-AnyCPU** package was updated in the following ways:
  * To version **14.7.0** in Xperience by Kentico **30.8.0**
  * To version **14.8.1** in Xperience by Kentico hotfix **30.9.1**


### How to fix
Update to the latest Xperience by Kentico version. See [Update Xperience by Kentico projects](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) for detailed instructions. Optionally, you can temporarily add **Magick.NET-Q8-AnyCPU** version **14.8.1** as a direct package reference to your project.
![]()
[]()[]()
