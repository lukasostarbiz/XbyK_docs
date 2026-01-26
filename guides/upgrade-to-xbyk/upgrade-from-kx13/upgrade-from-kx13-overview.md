---
source: https://docs.kentico.com/guides/upgrade-to-xbyk/upgrade-from-kx13/upgrade-from-kx13-overview
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Upgrade to Xperience by Kentico](/guides/upgrade-to-xbyk)
  * [Upgrade from Kentico Xperience 13](/guides/upgrade-to-xbyk/upgrade-from-kx13)
  * Upgrade overview 


# Upgrade overview
If you’re in charge of moving project from **Kentico Xperience 13 (KX13)** to **Xperience by Kentico (XbyK)** , or considering doing so, you may have some questions about what that process looks like at a high level.
You’ve probably come across the [_Kentico Migration Tool_](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool) for Xperience by Kentico, but as you might imagine, simply running the tool is probably not the only magical step to completely migrate your whole solution.
Let’s take a look at the process together. There are four main parts to consider:
  1. [Set up your environment](#set-up-your-environment) and configure the migration tool
  2. [Migrate your data](#migrate-your-data) and binary files using the migration tool
  3. [Adjust your code](#adjust-your-code) and any custom artifacts not covered by the migration tool
  4. [Think about the future](#think-about-the-future) and explore the full potential of Xperience by Kentico


[![Diagram of the upgrade process](/docsassets/guides/upgrade-from-kx13-overview/Upgrade-diagram.jpg)](/docsassets/guides/upgrade-from-kx13-overview/Upgrade-diagram.jpg)
In case you’re looking for more explanation than some orange pill shapes on a diagram, let’s break down each point.
## Set up your environment
The first step of the upgrade is to make sure your environment supports the migration tool and both platforms, and to prepare them for the upgrade.
### Hotfix to Kentico Xperience 13 Refresh 5 or newer
Make sure to your Kentico Xperience 13 instance is running on Refresh 5 (hotfix 13.0.64) or newer. If not, make sure to [apply a hotfix](/13/installation/hotfix-instructions-xperience-13) within the supported range.
### Install a compatible version of Xperience by Kentico
Check the [Library Version Matrix](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/README.md#library-version-matrix) in the _Xperience by Kentico: Kentico Migration Tool_ repository to see which versions of Xperience by Kentico and the migration tool are compatible.
### Download the migration tool
Clone or download the [Xperience by Kentico: Kentico Migration Tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool). Make sure your environment meets the requirements to run the solution specified in the repository.
## Migrate your data
Once the environment is ready, you can migrate data from _Kentico Xperience 13_ to _Xperience by Kentico_.
Thanks to the [CLI parameters](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#migrate-command-parameters) that allow you to specify which objects to migrate, you can iteratively migrate data in multiple runs before adjusting code. Alternatively, you can do so in parallel with code updates.
### Configure the settings
Follow the guidance in the [Usage guide](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/docs/Usage-Guide.md) and the [CLI README](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md) to configure the details of your data migration.
### Set up Page Builder conversion
If you use Page Builder in your Kentico Xperience 13 instance, consider paying special attention to the section about [Source instance API discovery](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#source-instance-api-discovery). By adding a special endpoint to your current live site, you can enable the tool to convert your Page Builder data to a format that uses Xperience by Kentico’s new [UI form components](https://docs.kentico.com/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).
## Adjust your code
While past Kentico releases were built as expansions on previous versions, Xperience by Kentico is a brand new product, built from the ground up. It shares many similarities with legacy Kentico products, but also has some important differences you will have to account for when transferring your code.
### Update content querying
Xperience replaces the [DocumentQuery and MultiDocumentQuery APIs](/13/custom-development/working-with-pages-in-the-api) from legacy Kentico versions with the [Content item API](/documentation/developers-and-admins/api/content-item-api/content-item-query-api). Your team will need to update code that retrieved page data to work with the new API.
### Adjust Page Builder components
If you choose to enable [Source instance API discovery](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#source-instance-api-discovery), you will need to change the editing components used by the properties of your Page Builder components to the new admin UI components.
### Re-home custom tables
Xperience by Kentico does not have a direct analog for the [Custom tables](/13/developing-websites/defining-website-content-structure/managing-custom-tables) functionality in legacy Kentico products.
The Kentico Migration Tool transfers each custom table to a [custom object type](/documentation/developers-and-admins/customization/object-types), but does not provide a user interface. Your team must [create a UI](/guides/development/customizations-and-integrations/create-basic-module#build-the-module-ui) for each class, or alternatively, convert the data to [reusable content items](/documentation/business-users/content-hub) that editors can manage through the _Content hub_.
### Convert custom module pages
Xperience’s admin UI is no longer built with web forms, so your team will have to recreate any [custom module pages](/13/custom-development/creating-custom-modules) as [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) in the new admin.
### Alter auth and user management
Where legacy Kentico products utilized the User entity to represent users of the administration interface and the live site, Xperience by Kentico has a separate [Member](/documentation/business-users/members) object type representing those who sign in to the live site. Your team must change [old registration and authentication code](/13/managing-users/user-registration-and-authentication) to [work with members.](/documentation/developers-and-admins/development/registration-and-authentication)
### Choose a search integration
Xperience by Kentico does not include search functionality out-of-box, but rather provides three integration options: [Lucene](https://github.com/Kentico/xperience-by-kentico-lucene), [Azure AI search](https://github.com/Kentico/xperience-by-kentico-azure-ai-search), and [Algolia](https://github.com/Kentico/xperience-by-kentico-algolia). You need to recreate search functionality through one of these services.
### Address differences in localization
#### Live site localization
Xperience by Kentico supports localization, with the migration tool automatically transferring multilingual pages and Cultures (as [Languages](/documentation/developers-and-admins/configuration/languages)). However, some features are not covered.
Kentico Xperience 13 allowed you to [add custom resource strings](/13/multilingual-websites/setting-up-a-multilingual-user-interface/working-with-resource-strings#adding-your-own-strings) to the database through the administration interface. Xperience by Kentico does not include this feature out-of-box, so your team must [use a resource file](/guides/development/multilingual/display-your-website-content-in-multiple-languages#translate-other-application-texts), or store custom strings as content items of a specific type in the [Content hub](/documentation/business-users/content-hub).
Alternatively, you can use or modify Nittin’s [Xperience community localization](https://github.com/nittin-cz/xperience-community-localization) module for this functionality, or implement your own [custom module](/guides/development/customizations-and-integrations/create-basic-module).
#### Admin localization
Like Kentico Xperience 13, Xperience by Kentico supports [localization of the admin UI](/documentation/developers-and-admins/customization/admin-ui-localization). However, many of the key names and values are different from those in the KX13 UI, so you will need to create new resource files for XbyK.
### Migrate commerce
Xperience by Kentico supports [commerce](/documentation/business-users/manage-commerce-stores), but the system has been redesigned from scratch. You’ll start by [setting up the built-in commerce features](/documentation/developers-and-admins/digital-commerce-setup) in Xperience, and then extend and customize the rest according to your project needs.
As an alternative, you can utilize the [Shopify integration](https://github.com/Kentico/xperience-by-kentico-shopify).
### Handle marketing changes
Xperience by Kentico has many marketing features, but it **does not aim for feature parity** with Kentico Xperience 13, so there are some differences you’ll need to consider when transferring your project. Xperience by Kentico does not support personas or A/B tests natively, and it does not cover all possible [marketing automation](/documentation/business-users/digital-marketing/automation) scenarios.
However, we do offer pathways to help your team handle these potential challenges. Learn more in our [Plan your strategy for migrating features](/guides/upgrade-to-xbyk/upgrade-from-kx13/plan-your-strategy-for-migrating-features) guide.
## Think about the future
Depending on your constraints, you may want to consider ways of improving your project with Xperience’s new offerings.
### Adapt your content model
Xperience by Kentico boasts a modern, flexible [content model](/guides/architecture/content-modeling) where [reusable items](/documentation/business-users/content-hub) are referenced from multiple different channels, such as web sites, microsites, mobile applications, and email campaigns.
In order to take advantage of this headless-enabling flexibility, we recommend that you consider adapting your content to work in this way, leaving web-specific metadata in a [website channel](/documentation/developers-and-admins/configuration/website-channel-management) while making the meat of your structured content reusable.
### Utilize new capabilities
As discussed earlier, we developed Xperience by Kentico from the ground up as a new product. This means that it contains features that work differently than analogous functionality in Kentico Xperience 13, or that didn’t exist in that version altogether. Here are some examples of new features you can take advantage of in Xperience by Kentico:
  * [Taxonomies](/documentation/developers-and-admins/configuration/taxonomies) and their tags represent hierarchical structures of categorization, and cover more advanced content scenarios than tags and categories in Kentico Xperience 13.
  * [Smart folders](/documentation/business-users/content-hub/content-hub-folders#smart-folders) allow your editors to dynamically filter and group content items, and can feed API queries on the live site.
  * [Reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) allow for groups of fields to shared across many content types, with changes reflecting across all content types that use them immediately.


## What’s next?
The next guides in this series will dive into a bit more detail about each of the points highlighted in this overview. They will cover [high-level looks at analogous features](/guides/upgrade-to-xbyk/upgrade-from-kx13/plan-your-strategy-for-migrating-features) between platforms, [preparation and data migration](/guides/upgrade-to-xbyk/upgrade-from-kx13/prep-for-upgrade-and-transfer-data), and [code conversion and new features](/guides/upgrade-to-xbyk/upgrade-from-kx13/adjust-your-code-and-adapt).
You can also find more information in [the documentation](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico) and the [Xperience by Kentico: Kentico Migration Tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/tree/master) repository.
Visit our [FAQ page](/guides/upgrade-to-xbyk/upgrade-from-kx13/upgrade-faq) for answers to frequently asked questions about the upgrade, migration tool or deployment considerations for upgraded projects.
![]()
[]()[]()
