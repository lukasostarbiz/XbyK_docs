---
source: https://docs.kentico.com/guides/architecture/upgrade-from-kx13/plan-your-strategy-for-migrating-features
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Upgrade from Kentico Xperience 13](/guides/architecture/upgrade-from-kx13)
  * Plan your strategy for migrating features 


# Plan your strategy for migrating features
Upgrading from **Kentico Xperience 13 (KX13) to Xperience by Kentico (XbyK)** involves varying levels of effort depending on the features you choose to carry over.
Our [Upgrade overview](/guides/architecture/upgrade-from-kx13/upgrade-from-kx13-overview) guide outlines the process consisting of configuring the environment, running the data migration using the [Kentico Migration Tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool) and subsequent adjustments. If you prefer to learn hands-on, follow along with our [walkthrough](/guides/architecture/upgrade-from-kx13/upgrade-walkthrough), which showcases the upgrade of Dancing Goat sample site from KX13 to XbyK.
This guide breaks down the upgrade process **by feature** , offering insights into which areas are relatively straightforward and which ones may demand more time and resources.
It is NOT our goal to provide exact feature parity between KX13 and XbyK, but rather to offer more modern, faster, and more composable tools to deliver value to your customers. Therefore, in addition to notes about the effort, we highlight the benefits your customers gain after the upgrade.
So you can evaluate and plan your upgrade most effectively, we also provide information about features currently on the roadmap.
## Legend
We’ve categorized the amount of feature migration effort into three levels: low, medium, and high. These levels offer a general benchmark to help you estimate the time and resources needed for each feature:
LOW - The Kentico Migration Tool (with proper configuration) transfers all data one-to-one into the target XbyK instance. Minor code adjustments may be required, such as generating new code files.
MEDIUM - The migration tool will transfer data, but a significant amount of developer work is needed for the feature to function as before. For example, implementing a new way of content retrieval or recreating/reconfiguring parts of the feature.
HIGH - Your team will need to rebuild this feature in XbyK, as it is either very different, not supported by the migration tool or not available out-of-the-box in XbyK. We recommend using one of our integrations or opting for a custom implementation. This level includes features that are on the roadmap.
## Content management
### Page Builder
**XbyK feature:** Page Builder
LOW MEDIUM By default, the Kentico migration tool ensures all Page Builder content and custom page templates transfer into the target instance without modifications.
We recommend taking advantage of the option to [configure conversion to the new Page Builder format](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#source-instance-api-discovery). See more details in the migration tool’s [README file](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#page-builder-content).
The widget code needs minor adjustments and Custom page templates migrate as [Preset page templates](/documentation/business-users/website-content/page-templates#page-template-types).
The new Page Builder in XbyK features bigger widget editing side-panel, providing easier and more intuitive experience to your customer’s content editors.
### Sites and pages
**XbyK feature:** Website channels and pages
LOW The Kentico migration tool migrates each _Site_ on the KX13 source to a _Website channel_ in XbyK target instance. Page URLs may need adjustments. Page attachments migrate as content item assets into the Content hub. See more details in the migration tool’s [README file](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#attachments).
### Structured content (Page types)
**XbyK feature:** Content hub (Content types)
LOW MEDIUM Content migrates into the target database. _Page types_ become _Page content types_ in XbyK. Developers need to adjust content retrieval implementation to utilize content query API and check/adjust content type fields.
With [additional configuration](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#reusable-field-schemas) of the Kentico Migration Tool, there’s an option to address inheritance using the _reusable field schemas_ in specific scenarios.
In XbyK, we introduced the [Content hub](/documentation/business-users/content-hub), a central place to store reusable content that can be referenced across channels in your solution. As a future improvement, we recommend adjusting your content model, identifying reusable pieces of content, and storing them in the Content Hub, although this (currently manual) step is not mandatory for the target solution to run.
By default the migration tool migrates all structured content into a website channel. It supports migrating some web page content types to reusable content types if [configured](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/tree/master/Migration.Tool.CLI#convert-pages-to-content-hub).
With the Content hub in XbyK, editors no longer need to store reusable content in the website content tree, and developers don’t have to customize the solution heavily to make content available across channels.
As opposed to content trees, XbyK’s Content hub offers an interface and a flat data structure, eliminating the need for custom tables. Usages show where structured content is reused.
### Digital asset management (Media libraries)
**XbyK feature:** Content hub, Media library
LOW The migration tool will transfer [all media files as asset content items](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#media-libraries) to the Content hub in the target instance.
While we strongly recommend storing your media in the Content hub, you can also [configure the migration tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#convert-attachments-and-media-library-files-to-media-libraries-instead-of-content-item-assets) to migrate them as media libraries in XbyK instead. If you choose to go this route, you can easily upload your assets into the Content hub later using our [mass asset upload](/documentation/business-users/content-hub/content-item-assets#mass-asset-upload) feature.
Storing assets in the Content hub offers your customers a multitude of benefits, including localization of content assets, workflow availability to improve approval processes, easy customization through custom fields, and the option to restrict access behind sign-in.
### Multilingual and localization
**XbyK feature:** Languages
MEDIUM The migration tool will map _Cultures_ from KX13 to _Languages_ in the target XbyK instance, and the localized content will also migrate.
XbyK does not natively allow editors to manage resource strings in the administration UI. Our recommendation is to use a dedicated content type to store the string and the label. Then create a content item for each value in the Content hub (e.g., in a dedicated folder) to allow editors to utilize the language versions of the content items.
If there’s a requirement for editors to manipulate resource strings the way they used to in KX13, your team can consider the [Xperience community localization](https://github.com/nittin-cz/xperience-community-localization) module by Nittin.
Xperience by Kentico features automatic _Language fallbacks_ (covering cases when a translation in the requested language does not exist), which will help editors in poly-lingual situations (e.g., Netherlands-Belgium, DACH region, etc.).
### Workflows and versioning
**XbyK feature:** Workflows and versioning
LOW MEDIUM For pages, the migration tool transfers the _Published_ , _Draft_ and _Archived_ states. See **Pages** section in the migration tool [GitHub documentation](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/docs/Supported-Data.md) for more details.
Your team needs to manually recreate any custom workflows from KX13 in the XbyK instance. In XbyK, you can replicate many linear workflows from KX13. However, your team will need to implement custom behaviors of any custom steps from scratch, including some previously built-in steps (e.g., publishing to social media, translation workflows). See an [example in our documentation](/documentation/developers-and-admins/customization/handle-global-events/set-up-custom-workflow-notifications) about setting up custom workflow notifications.
The editors in XbyK benefit from the new, basic workflow (New/Draft-Published-Archived) that is always automatically enforced.
## Customizations
### Custom modules and classes
**XbyK feature:** Custom modules and classes
MEDIUM The migration tool transfers modules, classes, data, and some custom fields on system classes. Developers need to re-implement the UI. Read more in the migration tool’s [README file](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#custom-modules-and-classes) or the [Modules and classes](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/docs/Supported-Data.md) section in the migration toolkit documentation on GitHub.
### Custom tables
**XbyK feature:** Custom modules
MEDIUM The Kentico Migration Tool migrates all content from custom tables to custom module table in the target database. Your developer team will need to implement the necessary UI, with the option to leverage any UI components XbyK comes with out of the box.
See details in the migration tool’s [README file](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#custom-tables) or the [Custom tables](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/docs/Supported-Data.md) section in the migration toolkit documentation on GitHub.
### Settings
**XbyK feature:** Settings
MEDIUM All [settings supported by XbyK](/documentation/developers-and-admins/configuration/settings) will migrate into the target instance. To accommodate custom settings we recommend that your developers [implement a custom module](/guides/development/customizations-and-integrations/create-basic-module).
Any website-specific settings will also require a custom module. [This guide](/guides/development/customizations-and-integrations/add-channels-to-module) provides an example.
## Data protection
### Consents and consent agreements
**XbyK feature:** Consents and consent agreements
LOW Consents and agreements migrate into the target instance. We recommend verifying the result. Depending on your implementation, your team may need to perform minor adjustments, e.g., in the presentation layer.
In XbyK marketers can easily see all contact’s consent agreements in the contact profile out-of-the-box. They are no longer required to have the permission to the Data protection application.
## Activities and Digital Marketing
### Contact management
**XbyK feature:** Contact management
LOW The migration tool transfers contacts seamlessly to the target instance. If you are using custom contact fields, your development team will have to make minor adjustments.
### Contact groups
**XbyK feature:** Contact groups
MEDIUM HIGH While Contact groups do exist in XbyK, they work differently than they used to in the KX13 implementation. They are not supported by the Kentico migration tool at this time. After migrating contacts, your team will have to reconstruct the contact groups in the XbyK instance. XbyK supports dynamic condition-based groups.
We improved the UI of the condition builder for contact groups in XbyK to provide marketers with more intuitive tools for segmentation.
### Activity Tracking
**XbyK feature:** Activities
LOW The migration tool transfers custom activities into the target XbyK instance. Depending on your implementation, your team may need to perform minor adjustments, e.g., in the presentation layer.
### Marketing automation
**XbyK feature:** Marketing automation
HIGH Xperience by Kentico supports the most common automation scenarios. The [automation feature](/documentation/business-users/digital-marketing/automation) is available with the Xperience by Kentico Advanced license. However the automation processes have to be set up from scratch when upgrading from KX13 to XbyK.
### Content personalization
**XbyK feature:** Content personalization
HIGH Content personalization does not migrate and your team will need to reimplement it in the target XbyK version. See an example of [personalization utilizing Contact groups](/guides/development/advanced-content/work-with-reusable-field-schemas#add-content-personalization) in XbyK.
In Xperience by Kentico, marketers can create different content variants in the Content hub, which they can reuse across channels. They can then display these variants dynamically, e.g., through personalized widget or email content.
Additionally, the editors have the ability to preview personalized pages.
### Email marketing
**XbyK feature:** Email marketing
HIGH Email feeds in KX13 are similar to [Email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) in XbyK.
At this time, the Kentico migration tool will not automatically transfer this data. You can migrate form auto-responders to XbyK manually by copying your HTML code and content into Email templates and Emails. See available email marketing features in [our documentation](/documentation/business-users/digital-marketing/emails).
[Email Builder](/documentation/developers-and-admins/development/builders/email-builder) is fully supported in Xperience by Kentico. However, email components, like widgets and templates, from KX13 will need manual code adjustments, as the XbyK Email Builder uses a different format.
A/B testing for emails is not available in XbyK out-of-the-box at the moment. We recommend that you follow [our roadmap](https://roadmap.kentico.com/tabs/4-planned) for updates.
XbyK Email Builder allows developers to create and manage templates and other components in the application code without the need to sign in to the administration interface. This provides a better debugging experience and makes it easier to manage components with source control.
For email content in XbyK, marketers can use AIRA to generate data from existing content in Content hub, URLs, or custom prompts.
### Search
**XbyK feature:** Integrations
MEDIUM To implement search in XbyK, we offer three existing integrations you can take advantage of: [Lucene](https://github.com/Kentico/xperience-by-kentico-lucene), [Azure AI Search](https://github.com/Kentico/xperience-by-kentico-azure-ai-search), and [Algolia](https://github.com/Kentico/xperience-by-kentico-algolia)
Despite the search functionality not being present natively in XbyK, the migration effort is ranked as MEDIUM because implementing it with the listed integrations is very straightforward and fast, especially for smaller projects.
### Forms
**XbyK feature:** Forms
LOW MEDIUM Forms from KX13 will migrate into the target XbyK instance. All out-of-the-box KX13 form controls will map to XbyK controls [as outlined here](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#migration-details-for-specific-object-types). However, we recommend taking a closer look and adjusting some form components as needed. Your team will have to reimplement all custom controls.
### Personas, and lead scoring
**XbyK feature:** Roadmap
HIGH At the moment, these features are not available in XbyK. For deterministic segmentation you can utilize [contact groups](#contact-groups). If there is a requirement for advanced segmentation or personas and lead scoring as you knew them from KX13, your developer team needs to build a custom implementation. If this is not an immediate need, we recommend to wait and follow our [XbyK roadmap](https://roadmap.kentico.com/tabs/4-planned).
### Social Marketing
**XbyK feature:** Roadmap
HIGH At the moment, this feature is not available in XbyK, but it is one of our goals to build a way for customers to use our API to feed data from Xperience into any channel - including social media. If this is not an immediate need, we recommend to wait and follow [our roadmap](https://roadmap.kentico.com/c/225-custom-channel-types?&utm_medium=social&utm_source=starter_share).
## Digital commerce
**XbyK feature:** Digital commerce, integrations
HIGH The [commerce feature](https://www.kentico.com/discover/blog/experience-first-commerce) is available in XbyK, and has been developed entirely from scratch. Because of this fresh start, the [commerce features](/documentation/business-users/manage-commerce-stores) and entities in XbyK don’t have a direct one-to-one match with the KX13 [E-commerce module](/13/e-commerce-features). As a result, transferring your commerce functionality into Xperience isn’t a simple copy-over.
You’ll need to [set up the commerce functionality](/documentation/developers-and-admins/digital-commerce-setup) in XbyK, [model your product catalog](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog), and then tailor the solution according to your project’s requirements and complexity to adapt it to the new structure and XbyK’s capabilities. As we are extending commerce features in XbyK, follow the [product roadmap](https://roadmap.kentico.com/tabs/6-next-refreshes) to plan for upcoming functionality.
Alternatively, your development team can take advantage of our existing [Shopify integration](https://github.com/Kentico/xperience-by-kentico-shopify).
## Analytics
### A/B testing and Web analytics
**XbyK feature:** Integrations
HIGH Your development team can implement these features through our [tag manager integration](https://github.com/Kentico/xperience-by-kentico-tag-manager). For A/B testing we recommend the integration with [VWO](https://vwo.com/). For web analytics you can use Google Analytics, Google Tag Manager or Microsoft Clarity. Additionally, we plan to leverage AI to help optimize and automate several tasks, including A/B testing. Follow [our roadmap](https://roadmap.kentico.com/c/325-ai-experiments-and-optimization) for details.
### Reporting
**XbyK feature:** Custom module
HIGH Your development team can implement reporting using XbyK Custom modules. The feature is not present out of the box.
Alternatively, you may want to consider tools outside of XbyK, that you can fill with data using SQL queries, such as the [Microsoft Report Builder](https://learn.microsoft.com/en-us/sql/reporting-services/install-windows/install-report-builder?view=sql-server-ver16) for SQL server.
## Configuration
### User management
**XbyK feature:** Users, Memberships
LOW MEDIUM The Kentico migration tool will seamlessly migrate all users with _Privilege level “Editor”_ and above as **Users** in XbyK. All external users (visitors, customers, members) migrate as **Members**. External sign-in information (e.g., Facebook, Google) will NOT migrate. See the [README file](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#members) of the migration tool for more details. Depending on your implementation, your team may need to perform some adjustments to the code after the data migration. We cover this in more detail in our [Adjust your code and adapt your project](/guides/architecture/upgrade-from-kx13/adjust-your-code-and-adapt) guide.
### Role management
**XbyK feature:** Role management
LOW MEDIUM The Kentico Migration Tool transfers roles that have at least one user with _Privilege level “Editor”_ and above. Note that XbyK uses a different permission model, therefore your team has to reconfigure role permissions or any UI personalization settings.
To allow editors mapping roles with members, your developers have to implement custom module and classes.
If you want to use taxonomy tags to determine which content a role’s members can see, your implementation will require additional effort.
In XbyK, we have made significant UX improvements so administrators can more easily define which parts of the admin UI a user can access, and what operations they are authorized to do.
### Categories
**XbyK feature:** Taxonomies and reusable field schemas
LOW All _Categories_ from KX13 instance will migrate as _Taxonomies_ in XbyK. Note that XbyK uses different approach to categorization – see details in the migration tool’s [README file](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#categories).
Taxonomy in XbyK is no longer tied to the Document, it’s an independent object and editors can translate the tags and categories into different languages.
### Multi-factor authentication
**XbyK feature:** Multi-factor authentication
MEDIUM Xperience currently supports [Multi-factor authentication](/documentation/developers-and-admins/configuration/users/user-management#multi-factor-authentication) in the administration interface. Alternatively, your team can build a custom implementation or integration.
### Integration bus
**XbyK feature:** Roadmap
HIGH At the moment this feature is not available out-of-the-box in XbyK. We recommend that you follow [our roadmap](https://roadmap.kentico.com/tabs/4-planned) for updates.
### Admin localization
**XbyK feature:** Admin UI localization
LOW [Admin UI Localization](/documentation/developers-and-admins/customization/admin-ui-localization) is available out-of-the-box in Xperience by Kentico. XbyK uses different resource string keys than older Kentico versions, so new translations may be necessary. AI tools can help speed up this process.
## Now that you’ve got the big picture…
The goal of this article was to provide you with high-level information about KX13 to XbyK upgrade complexity from a feature perspective. We realize that the actual effort needed will vary from project to project. Let’s continue to get a clearer picture of what an upgrade could look like in practice.
## What’s next?
If you want a deeper technical understanding of how these features fit into the upgrade process from beginning to end—as outlined in the [Overview](/guides/architecture/upgrade-from-kx13/upgrade-from-kx13-overview)—check out the next two guides: [Prep for the upgrade and transfer data](/guides/architecture/upgrade-from-kx13/prep-for-upgrade-and-transfer-data) and [Adjust your code and adapt your project](/guides/architecture/upgrade-from-kx13/adjust-your-code-and-adapt).
If you are interested in a concrete example of migrating a specific feature, let us know your feedback and stay tuned for future guides.
![]()
[]()[]()
