---
source: https://docs.kentico.com/modules/members
scrape_date: 2026-01-22
---

Module: Members
1 of 12 Pages
# Implementing membership using Page Builder widgets
This module covers the process of developing membership functionality for your Xperience by Kentico website channels.
It includes technical guides about:
  * Creating widgets for flexible member registration and sign-in
  * Specifying a relationship between members and contacts, and synchronizing these entities
  * Building a profile page, where members can update their personal information
  * Designating and enforcing member-only areas of the website, which cannot be viewed by unauthenticated visitors


It also includes an introduction to the community-built [XperienceCommunity.MemberRoles](https://github.com/KenticoDevTrev/MembershipRoles_Temp/tree/master) package, which enables you to have roles that distinguish different groups of members and which content they can access.
The community member roles package includes some alternative approaches that do not match the out-of-box approach, so we recommend [reading about it](/modules/members/apply-roles-to-your-members-community-package) before completing the other guides if you plan to use it in your project. This way, you can avoid using functionality that the package bypasses.
## Before you start
These hands-on materials require the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [29.6.1](/documentation/changelog) or higher. 
Some features covered in the Training guides may not work in older versions. 
  * Basic knowledge of [_.NET Identity_](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity).
  * Understanding how the Xperience [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) works and what developing [Page Builder components](/guides/development/page-builder) looks like.


**Code samples**
You can find a project with completed, working versions of code samples from this module and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository. This branch also includes membership functionality that is not associated with any guides.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with these materials.
The code samples in this series are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
Some of the examples in this series tie in with custom fields from our [training guide about customizing contacts](/guides/development/customizations-and-integrations/add-custom-contact-field).
If you have not already gone through this guide, feel free to consider those parts optional.
Previous page
1 of 12
[ Mark complete and continue ](/modules/members/groundwork-registration-widget)
![]()
[]()[]()
