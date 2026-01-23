---
source: https://docs.kentico.com/documentation/developers-and-admins/upgrade-to-xperience-by-kentico
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * Upgrade to Xperience by Kentico 


# Upgrade to Xperience by Kentico
Kentico offers a set of tools and materials to help you upgrade your projects to Xperience by Kentico, whether you come from a [previous version of Kentico Xperience](#upgrade-from-kentico-xperience) or from a [different platform](#migrate-from-other-platforms).
## Upgrade from Kentico Xperience
To upgrade a project from a previous version of Kentico Xperience to Xperience by Kentico:
  1. [Create a boilerplate](/documentation/developers-and-admins/installation) Xperience by Kentico project.
  2. Migrate your data and content from the old project using the [Kentico Migration Tool](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico/migration-toolkit#kentico-migration-tool).
  3. Manually transfer any code, customizations, and any unsupported content and adjust them for Xperience by Kentico’s [architecture and API differences](#technical-comparison).
  4. Deploy your Xperience by Kentico project either to our [SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) or to your [private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud).


See our series of [guides](/guides/architecture/upgrade-from-kx13) for a more detailed walkthrough of the upgrade process.
### Technical comparison
The following table and diagram outline the main differences in the **architecture** of the Xperience products.
[![Comparison of the Kentico Xperience 13 and Xperience by Kentico architecture](/docsassets/documentation/upgrade-to-xperience-by-kentico/Xperience_Architecture_Comparison.png)](/docsassets/documentation/upgrade-to-xperience-by-kentico/Xperience_Architecture_Comparison.png)
Kentico Xperience 13 |  Xperience by Kentico  
---|---  
Consists of two separate applications – one for the live site and one for the administration. The live site application can be built using [ASP.NET MVC 5](https://docs.microsoft.com/en-us/aspnet/mvc/overview/getting-started/introduction/) or [ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/introduction-to-aspnet-core) (as part of .NET Core 3.1 or newer). The administration application is based on [ASP.NET Web forms](https://learn.microsoft.com/en-us/aspnet/web-forms/). |  Both the live site and administration run as a single application, based on the current .[NET version requirements](/documentation/developers-and-admins/installation/system-requirements). The Xperience administration is integrated into the application via a [NuGet package](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/xperience-by-kentico-nuget-packages). It is possible to [deploy sites without the administration](/documentation/developers-and-admins/deployment/deploy-to-private-cloud/deploy-without-the-administration). To learn more, see the full [System requirements](/documentation/developers-and-admins/installation/system-requirements) and [Website development basics](/documentation/developers-and-admins/development/website-development-basics).  
### Major differences
  * **Sites** – sites are no longer used in Xperience by Kentico. Instead, websites are represented by [website channels](/documentation/developers-and-admins/configuration/website-channel-management).
  * **Cultures** – multilingual functionality and content is based on [languages](/documentation/developers-and-admins/configuration/languages) that users configure in the system.


### API
Xperience by Kentico preserves a large part of the Kentico Xperience 13 API and general code patterns, including:
  * [Info and InfoProvider API](/documentation/developers-and-admins/api/database-table-api) and custom [object types](/documentation/developers-and-admins/customization/object-types)
  * [ObjectQuery](/documentation/developers-and-admins/api/objectquery-api)
  * [Page Builder components](/documentation/developers-and-admins/development/builders/page-builder)
  * [Form Builder components](/documentation/developers-and-admins/development/builders/form-builder)
  * [Event handlers](/documentation/developers-and-admins/customization/handle-global-events) (with differences in the available events, see [Reference - Global system events](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events))


However, the Page API and data structure is completely different. See [Content item query API](/documentation/developers-and-admins/api/content-item-api/content-item-query-api), [ContentRetriever API](/documentation/developers-and-admins/api/content-item-api/content-retriever-api) and [Content item database structure](/documentation/developers-and-admins/api/content-item-api/content-item-database-structure).
Xperience by Kentico currently provides a smaller, more focused set of features than Kentico Xperience 13. Correspondingly, there are differences in the API due to unavailable features, or features with a different scope and capabilities.
**See also** : [Editing components in Xperience by Kentico](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico)
### Administration
The Xperience by Kentico administration is based on .NET and uses [React.js](https://reactjs.org/) as its UI framework. This is a major change from the Kentico Xperience 13 administration built using Web Forms.
The administration can be customized and extended, as described in [Extend the administration interface](/documentation/developers-and-admins/customization/extend-the-administration-interface). However, customizations of the Kentico Xperience 13 administration UI are incompatible due to the different technology stack.
## Migrate from other platforms
You can migrate your projects to Xperience by Kentico from other platforms outside of the Kentico ecosystem, like Sitecore or Sitefinity. To simplify the process, use the respective [Migration tool](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico/migration-toolkit). Each tool can be further customized to satisfy specific project requirements.
![]()
[]()[]()
