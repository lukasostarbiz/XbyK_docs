---
source: https://docs.kentico.com/documentation/developers-and-admins/installation/support-policy
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Installation](/documentation/developers-and-admins/installation)
  * Xperience by Kentico support policy 


# Xperience by Kentico support policy
Updates for Xperience by Kentico are released regularly as:
  * **Hotfixes** – weekly fixes of issues and reported security vulnerabilities. May include improvements that increase the system’s security against potential threats.
  * **Refreshes** – larger updates that add new features, introduce changes, and fix issues. Refreshes are planned for every month, but the release schedule may vary depending on the features included in each refresh.


We recommend staying up-to-date on the latest release. The [update process](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) is designed to work seamlessly, requiring no or minimal manual adjustments of your project. Additionally, Kentico supports a range of older releases, giving you time to transition on your own terms.
## What does it mean that a release is supported?
Using a supported Xperience release gives you full access to Kentico [Support](https://www.kentico.com/services/support) and [Consulting](https://www.kentico.com/services/consulting) services. Our team will provide guidance and recommend the best course of action if you run into any issues.
However, Kentico always adds fixes and new features as part of the **latest release** of Xperience by Kentico, even if an issue is reported for an older release. The [update process](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) allows you to quickly move to a current release that contains the features or fixes that you need.
## What can happen when using an unsupported release?
If you continue to use an Xperience release after it becomes unsupported, Kentico may limit the scope of provided support services. Technical issues reported for unsupported releases may be resolved with only a recommendation to update to a newer release, along with guidance on how to perform the update.
For [Kentico SaaS](/documentation/developers-and-admins/saas/saas-overview) projects, we cannot guarantee that deployment to the SaaS environment will be possible when using an unsupported release.
For projects in a [private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud) environment, running with an unsupported release for an extended amount of time can result in license compatibility issues, as the license format may need to change over time. An incompatible license could cause unavailability of content delivered via channels and limited functionality of the project’s administration.
## Which Xperience releases are supported?
Releases |  Support status  
---|---  
29.5.3 or older |  Unsupported  
29.6.0 – 30.10.3 |  Supported until **October 31, 2026**  
30.11.0 or newer |  Supported until **October 31, 2027**  
Xperience by Kentico releases are supported for a varying length of time, based on an annual cycle that starts on **November 1**. Each release is supported for at least 12 months from its release date, up to a maximum of 24 months for releases in October.
When a new cycle starts in November, support continues for all releases from the previous cycle, as well as the final Refresh release from the cycle before that (typically the October Refresh). Support ends for all older releases.
**Example** : In June 2024, all releases starting from the October 2022 Refresh are supported. On November 1, 2024, support is dropped for all releases before the October 2023 Refresh.
The following visualization shows the window of supported releases as time moves forward:
Your browser does not support the video tag. 
## What is the backward compatibility policy for features and code?
As part of the Xperience by Kentico development lifecycle, certain features and parts of the API may become deprecated and obsolete. To mitigate the impact of such changes, Xperience will maintain **backward compatibility** for at least **12 months** after a feature is announced as deprecated or an API member is marked as obsolete.
In most cases, the product will provide a replacement feature or API with a clearly described transition path. The replacement will exist side-by-side with the deprecated item for the duration of the compatibility period. This will give you time to make any necessary changes.
There may be rare exceptions of breaking changes that occur either without prior notice or within the 12-month compatibility period. For example, if Kentico needs to address a security vulnerability, or make unavoidable architectural changes required for the product’s future evolution. However, Kentico will always provide guidance or automated solutions to keep updates as smooth as possible.
## How do third-party dependencies affect the support lifecycle?
Xperience by Kentico relies on various external frameworks, tools and dependencies, such as [.NET](https://dotnet.microsoft.com/en-us/) and Microsoft SQL Server. These dependencies are managed by third-party companies and have their own lifecycle. See [Third party software licenses](https://community.kentico.com/documentation/third-party-software-licenses) to view a list of dependencies and the licenses under which they are used.
When a new version of a dependency is released, Kentico will introduce support for it as part of a new Xperience release. However, compatibility with this newer dependency will **not be added to all currently supported Xperience releases** , even after the older version of the dependency eventually becomes unsupported by its publisher. In these cases, you can easily [update](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) to the latest Xperience release, which ensures compatibility.
Assume a scenario where certain older supported Xperience by Kentico releases only work with .NET 8.0, and later releases are compatible with both .NET 8.0 and the newer .NET 10.0 version. The older Xperience releases that only run on .NET 8.0 will not be patched to work with .NET 10.0, even after Microsoft ends support of .NET 8.0. You need to transition to a compatible Xperience release sometime during the period when both .NET versions are supported.
For more on how third-party dependencies are handled from a security perspective, see [Vulnerabilities in transitive dependencies](/documentation/developers-and-admins/security-guidelines/vulnerabilities-in-transitive-dependencies).
![]()
[]()[]()
