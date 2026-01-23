---
source: https://docs.kentico.com/documentation/developers-and-admins/upgrade-to-xperience-by-kentico/migration-toolkit
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Upgrade to Xperience by Kentico](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico)
  * Migration Toolkit 


# Migration Toolkit
Xperience by Kentico Migration Toolkit is a collection of tools developed and supported by Kentico. The aim of these tools is to simplify migration from other systems to Xperience by Kentico. All of the tools listed on this page are part of the Xperience by Kentico offering and are therefore subject to the [7-day bug-fix policy](https://www.kentico.com/platforms/kentico-xperience-13/roadmap/product-support-lifecycle). The tools are not included in the product installation, visit their respective repositories for installation instructions. If you need assistance with any of the tools, see the _Support_ section of the _README.md_ file for each tool.
Xperience by Kentico Migration Toolkit consists of the following migration tools:
  * [Kentico Migration Tool](#kentico-migration-tool) - A customizable tool that migrates **older Kentico solutions** to the latest version of Xperience by Kentico.
  * [Sitecore Migration Tool](#sitecore-migration-tool) - A customizable tool that migrates **Sitecore** solutions to Xperience by Kentico.
  * [Sitefinity Migration Tool](#sitefinity-migration-tool) - A customizable tool that migrates **Sitefinity** solutions to Xperience by Kentico.
  * [Universal Migration Tool](#universal-migration-tool) - Underlying technology to enable content and data imports from **any data source** into Xperience by Kentico.


You can also [customize](#customize-the-migration-tools) the tools on top of their out-of-the-box functionality to make them fit your specific migration needs.
[![Overview of the XbyK Migration Toolkit](/docsassets/documentation/migration-toolkit/xperience-by-kentico-migration-toolkit-overview.jpg)](/docsassets/documentation/migration-toolkit/xperience-by-kentico-migration-toolkit-overview.jpg)
## Kentico Migration Tool
The [Xperience by Kentico: Kentico Migration Tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool) enables you to migrate database content and other data from older Kentico versions to Xperience by Kentico. Currently supported versions of the source instance are Kentico Xperience 13, Kentico 12, and Kentico 11.
**Kentico Migration Tool versions**
The [Kentico Migration Tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool) is versioned to correspond to Xperience by Kentico product versions. See the [version matrix](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool?tab=readme-ov-file#library-version-matrix) in the tool’s _README_ file to select the correct version for your project.
Due to differences in the feature set of the products, the Kentico Migration Tool cannot transfer all data available in the older Kentico versions. Certain types of data are migrated to a suitable alternative or discarded.
The Kentico Migration Tool migrates content and objects stored in the database along with related binary data on the file system, such as [media library](/documentation/business-users/media-libraries) files.
The following diagram showcases all available upgrade paths to get from older Kentico versions to Xperience by Kentico using [Kentico upgrade tools](https://devnet.kentico.com/download/upgrades), [community conversion tools](https://github.com/KenticoDevTrev/KX12To13Converter) and the Kentico Migration Tool. You can read more about the community conversion tool in [our blog](https://www.kentico.com/discover/blog/upgrading-from-kentico-12-to-kentico-xperience-13).
[![Overview of available Kentico upgrade tools](/docsassets/documentation/migration-toolkit/xperience-by-kentico-migration-toolkit-kentico-migration-tool-full-embedded.jpg)](/docsassets/documentation/migration-toolkit/xperience-by-kentico-migration-toolkit-kentico-migration-tool-full-embedded.jpg)
  * **Kentico 13** : 
    * Use **Kentico Migration Tool** to migrate structured content and Page Builder content to Xperience by Kentico.
  * **Kentico 12 MVC** offers two options: 
    * Use **Kentico Migration Tool** to migrate structured content to Xperience by Kentico.
    * Use the [Kentico upgrade tools](https://devnet.kentico.com/download/upgrades) to upgrade to Kentico 13 and then use **Kentico Migration Tool** to migrate structured content and Page Builder content to Xperience by Kentico.
  * **Kentico 12/11/10 Portal Engine** offer two options: 
    * Use **Kentico Migration Tool** to migrate structured content to Xperience by Kentico.
    * Use the [Kentico upgrade tools](https://devnet.kentico.com/download/upgrades) to upgrade to Kentico 12 Portal Engine if migrating from Kentico 11/10. Then use the [Kentico Xperience 12 Portal to Kentico Xperience 13 Converter](https://github.com/KenticoDevTrev/KX12To13Converter) community tool to upgrade to Kentico 13. Finally, use **Kentico Migration Tool** to migrate structured content and Page Builder content to Xperience by Kentico.


Code, customizations, and any unsupported content need to be manually transferred to the target Xperience by Kentico solution and adjusted for Xperience by Kentico’s [architecture and API differences](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico#technical-comparison).
For more information and detailed instructions, see the [Xperience by Kentico: Kentico Migration Tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool) GitHub repository.
## Sitecore Migration Tool
The [Xperience by Kentico: Sitecore Migration Tool](https://github.com/Kentico/xperience-by-kentico-sitecore-migration-tool) enables you to automatically export data from Sitecore and import it to your Xperience by Kentico instance using a custom module in the administration interface.
[![Sitecore Migration Tool diagram](/docsassets/documentation/migration-toolkit/xperience-by-kentico-migration-toolkit-sitecore-migration-tool-embedded.jpg)](/docsassets/documentation/migration-toolkit/xperience-by-kentico-migration-toolkit-sitecore-migration-tool-embedded.jpg)
For more information and detailed instructions, see the [Xperience by Kentico: Sitecore Migration Tool](https://github.com/Kentico/xperience-by-kentico-sitecore-migration-tool) GitHub repository.
## Sitefinity Migration Tool
The [Xperience by Kentico: Sitefinity Migration Tool](https://github.com/Kentico/xperience-by-kentico-sitefinity-migration-tool) enables you to automatically export data from Sitefinity and import it to your Xperience by Kentico instance using a custom module in the administration interface.
[![Sitefinity Migration Tool diagram](/docsassets/documentation/migration-toolkit/xperience-by-kentico-migration-toolkit-sitefinity-migration-tool-embedded.jpg)](/docsassets/documentation/migration-toolkit/xperience-by-kentico-migration-toolkit-sitefinity-migration-tool-embedded.jpg)
For more information and detailed instructions, see the [Xperience by Kentico: Sitefinity Migration Tool](https://github.com/Kentico/xperience-by-kentico-sitefinity-migration-tool) GitHub repository.
## Universal Migration Tool
The [Xperience by Kentico: Universal Migration Tool](https://github.com/Kentico/xperience-by-kentico-universal-migration-tool) is a set of libraries, documentation, and samples that enable you to export data from external systems (legacy Kentico versions, other CMS systems) and automatically import it to your Xperience by Kentico instance.
You can either create a JSON document according to [our specifications](https://github.com/Kentico/xperience-by-kentico-universal-migration-tool/blob/main/docs/Model/UMT.schema.json) using a technology of your choice or use [our .NET library](https://www.nuget.org/packages/Kentico.Xperience.UMT) to build your own C# models which represent the JSON structure. The integration also includes a custom module application for the Xperience administration, enabling users to upload the imported JSON files directly through the application’s user interface.
[![Universal Migration Tool diagram](/docsassets/documentation/migration-toolkit/xperience-by-kentico-migration-toolkit-universal-migration-tool-embedded.jpg)](/docsassets/documentation/migration-toolkit/xperience-by-kentico-migration-toolkit-universal-migration-tool-embedded.jpg)
For more information and detailed instructions, see the [Xperience by Kentico: Universal Migration Tool](https://github.com/Kentico/xperience-by-kentico-universal-migration-tool) GitHub repository.
## Customize the migration tools
The migration tools are designed to cover the most common migration cases and help streamline the process of transferring a project to Xperience by Kentico. Since implementation details and content models vary from project to project, the migration tools may not always fully align with your requirements. The tools are open-source for this reason – you can customize them or even create your own tools based on the [Universal Migration Tool](#universal-migration-tool).
If you are [upgrading from Kentico Xperience](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico), you can extend the [Kentico Migration Tool](#kentico-migration-tool) with the use of the [Migration.Tool.Extensions](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/tree/master/Migration.Tool.Extensions) module. The module enables you to remap and optimize the original content model to the new [content model](/guides/architecture/content-modeling/content-modeling-guide) recommended for Xperience by Kentico, remodel widgets, migrate page types to reusable field schemas and more.
![]()
[]()[]()
