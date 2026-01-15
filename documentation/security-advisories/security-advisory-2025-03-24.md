# Security advisory 2025-03-24
  * [ Copy page link ](documentation/security-advisories/security-advisory-2025-03-24#) | [Get HelpService ID](documentation/security-advisories/security-advisory-2025-03-24#)
Core MVC 5
  * [ Subscribe to product releases ](feeds/xbyk-releases.xml)
  * [ Subscribe to security updates ](feeds/xbyk-security.xml)


[✖](documentation/security-advisories/security-advisory-2025-03-24# "Close page link panel") [Copy to clipboard](documentation/security-advisories/security-advisory-2025-03-24#)
## Account lockout causes temporary lockout of valid users
**CVSS** : 6.9  
**Affected versions** : 29.7.0 - 30.2.2  
**Category** : Denial of service
### Summary
An issue with the [account lockout](documentation/developers-and-admins/configuration/users/user-management#account-lockout) feature could affect access for administration users under certain conditions. As a result, valid users could experience issues with unexpected sign-out and become temporarily locked out of the system on projects with the account lockout feature enabled.
### How to fix
Update to the latest version. See [Update Xperience by Kentico projects](documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) for detailed instructions.