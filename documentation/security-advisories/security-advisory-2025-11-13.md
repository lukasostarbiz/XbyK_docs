# Security advisory 2025-11-13
  * [ Copy page link ](documentation/security-advisories/security-advisory-2025-11-13#) | [Get HelpService ID](documentation/security-advisories/security-advisory-2025-11-13#)
Core MVC 5
  * [ Subscribe to product releases ](feeds/xbyk-releases.xml)
  * [ Subscribe to security updates ](feeds/xbyk-security.xml)


[✖](documentation/security-advisories/security-advisory-2025-11-13# "Close page link panel") [Copy to clipboard](documentation/security-advisories/security-advisory-2025-11-13#)
## Timing attack vulnerability in content sync authentication
**CVSS** : 8.3  
**Affected versions** : 22.0.0 - 30.11.3  
**Category** : Side-channel attack
### Summary
A potential vulnerability was identified in the authentication process for the [content synchronization](documentation/business-users/content-sync) feature. The API key verification mechanism was not sufficiently resistant to side-channel attacks, which could theoretically allow an attacker to infer secret values through timing analysis.
Successful exploitation would be highly impractical, requiring an extensive number of requests over a high-latency public network. At this time, we have no indication that the vulnerability has been exploited in any real-world scenario.
This vulnerability was addressed by using a cryptographically secure comparison function, ensuring robust protection against timing and other side-channel attacks.
### How to fix
Update to the latest Xperience by Kentico version. See [Update Xperience by Kentico projects](documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) for detailed instructions.