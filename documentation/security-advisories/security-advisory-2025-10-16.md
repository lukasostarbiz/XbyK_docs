# Security advisory 2025-10-16
  * [ Copy page link ](documentation/security-advisories/security-advisory-2025-10-16#) | [Get HelpService ID](documentation/security-advisories/security-advisory-2025-10-16#)
Core MVC 5
  * [ Subscribe to product releases ](feeds/xbyk-releases.xml)
  * [ Subscribe to security updates ](feeds/xbyk-security.xml)


[✖](documentation/security-advisories/security-advisory-2025-10-16# "Close page link panel") [Copy to clipboard](documentation/security-advisories/security-advisory-2025-10-16#)
## Recommendation on .NET Framework Update – CVE-2025-55315
**CVSS** : 9.9  
**Affected versions** : none  
**Category** : Recommendation
### Summary
Microsoft has recently disclosed a vulnerability in the [ASP.NET Core](http://asp.net/) framework, tracked as CVE-2025-55315, with a CVSS score of 9.9. This issue affects environments running .NET 8 and later and may allow authenticated users with low privileges to access sensitive data, modify files, or disrupt system availability.
**Impact on Kentico Products**
Kentico products do not contain or bundle the .NET runtime. However, if you have deployed Xperience by Kentico on infrastructure that you manage using ASP.NET Core (.NET 8 or newer), your hosting environment may be affected by this vulnerability.
### Recommended action
If you manage your own hosting infrastructure and have deployed Kentico products on .NET 8 or newer, we recommend prioritizing updates to your [ASP.NET Core](http://asp.net/) framework to the latest versions provided by Microsoft.
No action is required if:
  * You are using [Xperience by Kentico SaaS](documentation/developers-and-admins/saas/saas-overview)
  * You are using an Azure hosting provider (or other managed hosting where the provider maintains the .NET runtime)


For Microsoft’s official guidance and patch details, see [Microsoft Security Update Guide – CVE-2025-55315](https://msrc.microsoft.com/update-guide/en-US/vulnerability/CVE-2025-55315).