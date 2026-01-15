# Introduction to data protection
  * [ Copy page link ](modules/data-protection#) | [Get HelpService ID](modules/data-protection#)
Core MVC 5


[✖](modules/data-protection# "Close page link panel") [Copy to clipboard](modules/data-protection#)
This module takes a detailed look at Data protection in Xperience by Kentico, diving into complex examples.
We’ll go over how to build all of the following:
  * A mapping between consents and custom cookie levels
  * A widget allowing visitors to consent to different cookie levels
  * A tracking consent banner to accept all consent levels at once
  * A Page builder section that hides its contents from visitors who have not consented to tracking
  * An identity collector to gather contacts associated with a given email
  * A data collector to collect personal information associated with provided contacts
  * A data eraser to remove all data associated with provided contacts


Some of the materials in this module are not exclusive, and may look familiar if you’ve already completed other modules.
### Prerequisites
This module’s hands-on materials require the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [29.6.1](documentation/changelog) or higher. 
Some features covered in the Training guides may not work in older versions. 
  * Basic experience using [Page Builder widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder).


The example presented in this [Data protection series](guides/development/data-protection) is a **valid implementation of data protection for[Contacts](documentation/business-users/digital-marketing/contact-management)** in Xperience by Kentico. (Note that it does not cover the collection and erasure of [Members](documentation/business-users/members) and their associated data.)
You can copy-paste the code samples into your own solution.
However, if you choose to do so, **make sure to consult your legal team** to determine whether the implementation, texts, and consent levels **meet the requirements of your region and market**.
**Code samples**
You can find a project with completed, working versions of code samples from this module and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
Previous page
1 of 13
[ Mark complete and continue ](modules/data-protection/define-cookie-consent-module)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/data-protection)
[](https://docs.kentico.com/modules/data-protection)[](https://docs.kentico.com/modules/data-protection)