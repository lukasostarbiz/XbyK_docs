# Security advisory 2025-03-27
  * [ Copy page link ](documentation/security-advisories/security-advisory-2025-03-27#) | [Get HelpService ID](documentation/security-advisories/security-advisory-2025-03-27#)
Core MVC 5
  * [ Subscribe to product releases ](feeds/xbyk-releases.xml)
  * [ Subscribe to security updates ](feeds/xbyk-security.xml)


[✖](documentation/security-advisories/security-advisory-2025-03-27# "Close page link panel") [Copy to clipboard](documentation/security-advisories/security-advisory-2025-03-27#)
## SQL Injection in kentico-xperience-dbmanager tool
**CVSS** : 6.4  
**Affected versions** : 30.3.0 and lower  
**Category** : SQL injection
### Summary
The `kentico-xperience-dbmanager` tool used during the [Xperience installation process](documentation/developers-and-admins/installation) to install and maintain project databases was vulnerable to SQL injection in the `--database-name` (`-d`) parameter. The issue was fixed by correctly sanitizing the user-provided name.
### How to fix
Update to the latest version. See [Update Xperience by Kentico projects](documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) for detailed instructions.