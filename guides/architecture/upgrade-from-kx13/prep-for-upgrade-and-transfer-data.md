---
source: https://docs.kentico.com/guides/architecture/upgrade-from-kx13/prep-for-upgrade-and-transfer-data
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Upgrade from Kentico Xperience 13](/guides/architecture/upgrade-from-kx13)
  * Prep for the upgrade and transfer data 


# Prep for the upgrade and transfer data
Preparing to upgrade your site can seem daunting and uncertain. To help mitigate this uncertainty in the **Kentico Xperience 13 (KX13) to Xperience by Kentico (XbyK) upgrade** , we’re doing our best to break down the process into digestible steps and provide a [toolkit](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico/migration-toolkit) of utilities like the [_Xperience by Kentico: Kentico Migration Tool_](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/).
This guide will provide more details about the points from the first two top-level sections of the [upgrade overview](/guides/architecture/upgrade-from-kx13/upgrade-from-kx13-overview).
## Set up your environment
### Hotfix to Kentico Xperience 13 Refresh 5 or newer
The [Kentico migration tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/) relies on the presence of specific fields in system classes. For this reason, it requires Kentico Xperience 13.0.64 (Refresh 5) or newer.
If your Kentico Xperience 13 instance is currently running on a lower version, take a look at the [hotfix instructions](/13/installation/hotfix-instructions-xperience-13). Note that some hotfix versions contain manual steps to fix data or code affected by older bugs.
### Install a compatible version of Xperience by Kentico
Because the migration tool uses the Xperience by Kentico NuGet packages to interact with its APIs, each release of the migration tool’s library corresponds to a specific Xperience by Kentico version.
You can find a compatibility version matrix in the [README file](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/README.md) of the migration tool.
When you use the dotnet command line to install Xperience by Kentico, it defaults to the newest version, which may not always correspond to a migration library version.
First, make sure your hosting environments meets the [requirements for Xperience by Kentico](/documentation/developers-and-admins/installation/system-requirements). Then, you can follow the instructions in [this guide](/guides/development/get-started/install-a-specific-version-of-xperience-by-kentico) to install a specific version of Xperience by Kentico.
### Download the migration tool
You can use Git to clone the [Xperience by Kentico: Kentico Migration tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool) or simply download its code.
The environment where you install each Kentico platform must be accessible from where you install the tool.
Make sure your environment uses .NET 8.0 and meets any other requirements specified in the repository.
Before trying to build the project, make sure to restore all NuGet packages in the solution.
## Migrate your data
### Plan for an iterative process
The Migration CLI supports several [command parameters](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#migrate-command-parameters) that allow you to specify which objects from KX13 should be migrated. Options include Sites, Custom modules, Users, Members, Forms, Media libraries, Page attachments, Page types, Pages, Settings keys, Contact management-related objects, and Data protection-related objects.
Some parameters include related objects, and may depend on other parameters. For example, the `media-libraries` parameter requires that sites, custom modules, and users be migrated as well, and includes _Media files_ alongside _Media libraries_.
When you run the migration tool multiple times, the migrated object type determines whether the tool overwrites existing objects or replaces them. This is because the tool uses bulk queries for some object types, while iterating one-by-one through others.
If you use the CLI parameters to include the following object types in multiple runs, you must delete them in between:
  * **Contacts** , from the `--contact-management` parameter
  * **Activities** , from the `--contact-management` parameter
  * **Consent agreements** , from the `--data-protection` parameter
  * **Form submissions** , from the `--forms` parameter
  * **Custom module class data** , from the `--custom-modules` parameter


The tool will automatically overwrite other object types, including parents and other relatives of the listed object types, so you don’t need to worry about them piling up.
We recommend to create a database backup between the data migration iterations and use them to restore the database to the latest valid state as necessary.
### Configure the settings
The Kentico Migration Tool provides configuration options through the _appsettings.json_ file of the [Migration.Tool.CLI](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/tree/master/Migration.Tool.CLI) project. Settings in this file let you provide details about how to connect your source and target environments to the migration tool and adjust various aspects of the data migration. Here are a few examples:
  * Specify an inherited page type in KX13 that should be converted to a [Reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas).
  * Specify web page content types that should migrate as reusable content types.
  * Determine which columns from `User` and `UserSettings` in KX13 are added to the `Member` class in XbyK.
  * Exclude specific objects from data migration.
  * Explicitly decide which IDs should be used as the primary key for specific objects.


The next section on this page will dive into a particularly notable configuration in this file, but you can find details about all of them in the [Migration CLI README](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md).
### Set up Page Builder conversion
Page Builder components like _Widgets_ , _Page templates_ , and _Sections_ can have properties that enable editors to determine their appearance and functionality. In order to specify the user interface for each property, such as a text box or dropdown list, you assign **form components** to the properties.
Xperience by Kentico introduces a **new format** for [UI form components](https://docs.kentico.com/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components), meaning they save their data with a different structure in the database compared to their KX13 counterparts.
While XbyK supports the old format for backward compatibility, the migration tool gives you the option to convert Page Builder data with a feature called [Source instance API discovery](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#source-instance-api-discovery). It automatically converts serialized Page Builder data from the old format to the new format, so that you can use the new components.
The steps in the above link guide you through setting up an endpoint in your Xperience by Kentico 13 live site. This endpoint helps the migration tool convert your page builder data to the new format. Once it is in place, you only need to configure the migration tool to connect to the endpoint, and it will change the format of page builder data when you migrate pages.
### Customize the migration tool
To fit the specific requirements of your project, Kentico Migration Tools offers a variety of [customization options](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.Extensions/README.md). You can create custom mappings for migrating fields, widgets, widget properties and classes.
#### Adapt your content model
Xperience by Kentico offers new content modeling features that can help you modernize your project’s structure.
It allows channel-specific items like web pages and mobile app screens to hold fields their channel needs, while referencing reusable content stored in a central [Content hub](/documentation/business-users/content-hub).
For example, a reusable content type called **Article** would hold fields like _ArticleTitle_ and _ArticleText_. By contrast, a web page content type called **ArticlePage** would hold fields like _ArticlePageKeywords_ and _ArticlePageOGTitle_ , with a _Content items_ field called _ArticlePageArticle_ to reference a reusable _Article_ item.
Re-platforming is a great opportunity to **adjust your content model** to take advantage of these new features, and the migration tool’s customizations, especially the [custom class mapping](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.Extensions/README.md#custom-class-mappings), empower you to do so.
## What’s next?
The next guide in this series will explore later sections of the overview, covering [how to convert your existing code](/guides/architecture/upgrade-from-kx13/adjust-your-code-and-adapt) to work in the new platform, and how to embrace new features.
![]()
[]()[]()
