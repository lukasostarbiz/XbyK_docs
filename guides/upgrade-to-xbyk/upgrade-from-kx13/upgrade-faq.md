---
source: https://docs.kentico.com/guides/upgrade-to-xbyk/upgrade-from-kx13/upgrade-faq
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Upgrade to Xperience by Kentico](/guides/upgrade-to-xbyk)
  * [Upgrade from Kentico Xperience 13](/guides/upgrade-to-xbyk/upgrade-from-kx13)
  * Upgrade - Frequently asked questions 


# Upgrade - Frequently asked questions
This FAQ page addresses common questions about using the Kentico Migration Tool to upgrade from **Kentico Xperience 13 (KX13)** to **Xperience by Kentico (XbyK)**. It covers migration scope, testing strategies, and technical challenges that solution architects and tech leaders typically encounter during the upgrade process.
## Migration tool and the process
### What is the difference between the documentation in the GitHub repo and on the docs.kentico.com site?
#### GitHub repository documentation
The migration tool [GitHub repository](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/tree/master) is the official tool reference and public issue tracker. It’s a **public** repository, where you’ll find information about CLI usage, configuration, supported objects and mapping rules, plus release notes and known limitations.
If you encounter an error or a bug during data migration, we encourage you to open an issue in the GitHub repo or look for an existing issue matching your problem **before** contacting our support or consulting.
Support will likely recommend going through the repo anyway, as it’s the single source of truth for tool behavior and bugs, and public issues let everyone see existing reports, workarounds, and progress.
To avoid sharing sensitive client data, you can use the [community anonymizer tool](https://github.com/kentico-ericd/xperience-community-database-anonymizer) before submitting your issue.
#### Upgrade resources on Learn portal (docs.kentico.com)
Our [Learn portal (docs.kentico.com)](docs.kentico.com) provides end‑to‑end guidance on _how_ to use the Kentico Migration Tool. Our materials focus on implementation scenarios, upgrade planning, environment preparation, iterative data migrations, and other specifics.
As a starting point, see [Upgrade from Kentico Xperience 13](/guides/upgrade-to-xbyk/upgrade-from-kx13). For advanced technical topics, like remodeling, check out our [Upgrade deep dives for developers](/guides/upgrade-to-xbyk/upgrade-deep-dives) for developers.
### Can I run the Kentico Migration Tool against my project multiple times?
Yes, data migration is designed to be iterative. Many teams run the tool, review the outcome, adjust the configuration, and run it again. Our [overview guide](/guides/upgrade-to-xbyk/upgrade-from-kx13/upgrade-from-kx13-overview) illustrates the high‑level flow. Keep the scope small and run in batches - see the [GitHub README file](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#migrate-command-parameters) for list of parameters to migrate only certain objects.
**Make sure to create a database backup between data migration runs.** With a backup, you can easily reset your environment and start with a clean slate. Avoid manually deleting partially migrated content, as errors during migration can prevent the admin UI from working correctly. Restoring from backup is **the safest way** to undo failures or incorrectly migrated data.
Be mindful of **chain dependencies** between migrated objects. For example, to migrate _custom modules_ or _page types_ , you first need to migrate _sites_.
The migration tool performs automatic checks of the dependencies and will alert you to issues.
You can bypass the automatic checks using the `--bypass-dependency-check` parameter - see details in the [Migration.Tool.CLI README file in GitHub](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#migrate-command-parameters).
### How can I restrict which page types to (re)migrate?
You can configure selective migration in the `Migration.Tool.CLI/appsettings.json` file before each migration tool run. See in our [step‑by‑step walkthrough](/guides/upgrade-to-xbyk/upgrade-walkthrough/migrate-data-and-binary-files), where the configuration fits into the process.
For scope control, use the [`EntityConfigurations` option](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#configuration) to **exclude** objects by code name (block‑list). At this time, the tool does not support a native allow‑list; you can emulate it by excluding everything except the target page type(s).
For one‑type runs, temporarily exclude other page types, run the migration, review results, adjust, and repeat.
For re‑migration, restore a pre‑run database backup and run again with updated exclusions. Avoid manual partial deletes to prevent dangling references.
**Be mindful of dependencies and links**. Include required parents, taxonomies, media, and custom module data in the same run (or migrate in the correct order) to avoid errors during migration.
The tool will warn you about missing chain dependencies (e.g., you’re migrating _page types_ without _sites_). If you are absolutely sure the dependencies have been migrated beforehand, you can use the [`--bypass-dependency-check` parameter](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#migrate-command-parameters) to skip the automatic checks.
### Which data can and cannot be migrated?
You can migrate the majority of objects from KX13, including custom object types and custom classes and their data, pages, sites, contacts (and activities), and forms with submitted data. See the full list with details and limitations in the [Migration.Tool.CLI README in GitHub](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#migration-details-for-specific-object-types).
Some objects have different names in XbyK (e.g., sites -> websites), and some objects map one-to-one, while others don’t, expect to review mappings and adjust.
For content types, most fields map automatically, though some limitations apply (no page type inheritance, no macros in field defaults, no field categories). See [the GitHub documentation](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#migration-details-for-specific-object-types) for details.
If your solution uses any of the unsupported field types heavily, we recommend auditing your content model early and budgeting additional time for rebuilding these fields using alternative approaches in XbyK.
You need to manually migrate or reimplement emails (templates, emails/campaigns) and marketing email recipient lists.
For planning trade‑offs and feature differences, see our [capability comparison guide](/guides/upgrade-to-xbyk/upgrade-from-kx13/plan-your-strategy-for-migrating-features).
### Can I change the structure of my content during data migration?
Yes and we encourage auditing and re-evaluating your content model.
The migration tool allows you to optimize your content model, converting pages into reusable content items, or using objects newly introduced in XbyK, for example, reusable field schemas (RFS).
The RFS are optional and not required to complete the migration; use them for shared or repetitive fields when remodeling.
See all available [customization options to remodel data](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.Extensions/README.md) during migration.
For concrete scenarios and hands-on learning, check out our [technical deep dives](/guides/upgrade-to-xbyk/upgrade-deep-dives).
### Do I have to update my KX13 site before migration?
For the migration to run correctly, your source KX13 project [has to run on Refresh 5 or newer](/guides/upgrade-to-xbyk/upgrade-from-kx13/prep-for-upgrade-and-transfer-data#hotfix-to-kentico-xperience-13-refresh-5-or-newer). If your source instance runs on a lower version, yes, you need to update before data migration.
We recommend updating all content you want to transfer into a “published” state. For content you don’t want to migrate, either configure the migration tool to skip it or remove/archive it beforehand.
### Can I continue adding to my KX13 site while we are attempting the migration or do we need a content freeze?
You can keep adding content in KX13 and run the migration again when your XbyK project is ready. The migration tool does not remove existing data in the target. However, the tool does not track what has and has not been migrated in the past runs, so defining the delta is your team’s responsibility.
When you run the data migration iteratively, we highly recommend taking database backups between runs; see the [_Can I run the Kentico Migration Tool against my project multiple times?_ question](#can-i-run-the-kentico-migration-tool-against-my-project-multiple-times).
Be mindful of dependencies when you perform additional migration runs: include or migrate required parents and related data (e.g., sites/channels, content types, taxonomies, media, custom module data) in the correct order, or in the same run. The migration tool performs dependency checks and will warn you if something is missing.
Before the final cut‑over, plan a short content freeze window (or schedule one last delta migration) to avoid losing last‑minute changes.
## Planning and content strategy
### How much time should I allocate for upgrade planning and the upgrade itself?
Timelines vary based on your solution’s features, customization level, project size, remodeling requirements, and team experience.
Plan adequate time for familiarizing yourself with XbyK, understanding the migration tool, and preparing for development, testing, and deployment.
We’ve prepared a [guide to help you estimate the migration complexity](/guides/upgrade-to-xbyk/upgrade-from-kx13/plan-your-strategy-for-migrating-features) based on the features your solution uses.
If your project uses e-commerce, see [our commerce project timeline considerations](#what-are-the-current-estimates-regarding-timeline-and-costs-for-transitioning-from-kx13-to-xbyk-content-digital-commerce).
### What are the most common causes of project delays during migration?
Migration projects often take longer than expected due to teams underestimating the complexity of the migration process. Common reasons for slow migration include limits of the migration tool, lack of resources, and underestimating the time needed to learn how to use the migration tool for content changes and testing.
Problems often happen when partners do not spend enough time learning what the tool can do, do not check carefully which KX13 features are available in XbyK that leads to knowledge gaps and surprises, and developers are not familiar with the migration steps. Another issue is that teams often do not plan enough time to replace missing features from KX13 that are not available in XbyK.
**Mitigation strategies** :
  * Plan for an iterative approach with multiple migration runs and database backups between each run.
  * Allocate time for your developers to learn the Kentico Migration Tool using our hands-on [upgrade walkthrough](/guides/upgrade-to-xbyk/upgrade-walkthrough), [technical deep-dive scenarios](/guides/upgrade-to-xbyk/upgrade-deep-dives), and the [GitHub documentation](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/README.md).
  * If your team encounters a bug or missing feature in the migration tool, notify us immediately by submitting a [GitHub issue](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/issues/)—our team will respond promptly.
  * Plan adequate time and budget for adapting KX13 customizations, as many will need to be redesigned or rebuilt to work with XbyK’s different architecture.
  * Budget extra time for testing and iteration cycles.
  * Start performance testing early in the process.


### What should I consider when planning my content model for the upgrade?
We recommend performing an audit of your content model before an upgrade rather than performing a direct migration. The most critical part of a successful project is designing a content model that fits XbyK’s architecture and your project goals.
**We strongly recommend completing your content model planning before beginning migration.** Direct migrations from KX13 often result in deep tree-based relationships that mix pages and non-navigable content which is not recommended for XybK. Insufficient planning can lead to projects going “way over budget” due to extra code and missed optimization opportunities.
Consider XbyK’s unique features when planning content model granularity, such as:
  * [Content hub](/documentation/business-users/content-hub) for reusable, structured content
  * [Reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) for SEO-relevant fields and shared components
  * [Taxonomies](/guides/digital-marketing/work-with-taxonomies/taxonomies) to organize your content in advanced ways
  * [Customer journey tracking](/documentation/business-users/digital-marketing/customer-journeys) for digital marketing optimization


### How should we handle tree-based relationships from KX13?
XbyK favors flat content relationships over deep tree structures. Depending on your project and requirements, consider flattening tree structures into content relationships using reusable content with linked items.
Kentico Migration Tool out-of-the-box provides ways to [transfer the hierarchy into Content hub](/guides/upgrade-to-xbyk/upgrade-deep-dives/transfer-page-hierarchy-to-content-hub) or turning [child pages into content items referenced in widgets](/guides/upgrade-to-xbyk/upgrade-deep-dives/convert-child-pages-to-widgets).
Planning for Content hub structure that doesn’t rely on content tree hierarchy in the long run significantly improves editor experience, increases developer efficiency, and provides better performance and maintainability.
### Should I migrate media libraries to Content hub or keep them separate?
[Media libraries are obsolete](/documentation/business-users/media-libraries) in XbyK and you should definitely migrate to Content hub. This provides significant advantages but requires planning for the architectural changes. See more details in our [Plan your strategy for migrating features](/guides/upgrade-to-xbyk/upgrade-from-kx13/plan-your-strategy-for-migrating-features#digital-asset-management-media-libraries) page.
### Will my links to attachments and media files still work after they are converted to assets during the upgrade?
Yes, if your solution uses **permanent links** to a media library file, the migration tool will automatically convert them into references to asset content items with [the correct configuration](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#convert-text-fields-with-media-links).
Note that this conversion is only supported for permanent links.
If you are using links to media library files in a third-party system or a document, you will have to either update the links or set up redirects.
### Which KX13 features don’t have direct XbyK equivalents?
Xperience by Kentico now covers most functionality that KX13 offered. With modernization, some concepts work differently in XbyK. Additionally, XbyK includes new features not present in KX13 that can greatly benefit your project and your client’s business, such as customer journeys, AI capabilities, and Content hub.
For a detailed comparison of both systems, refer to our [Plan your strategy for migrating features](/guides/upgrade-to-xbyk/upgrade-from-kx13/plan-your-strategy-for-migrating-features) guide.
Kentico partners can find a detailed [feature comparison overview in the Partner Portal](https://partner.kentico.com/Resources/Xperience-by-Kentico/Sales-Materials).
## Deployment and production
### Are there special considerations when deploying an upgraded project to production for the first time?
Plan how you will host the Administration UI and set up your deployment pipeline before the first cut-over.
#### Administration UI
In XbyK, the administration UI is not on a separate domain by default.
If you require a different domain for the administration, a common setup is to run two application instances against the same database: one with the Admin enabled on a restricted domain for editors, and one [without the Admin](/documentation/developers-and-admins/deployment/deploy-to-private-cloud/deploy-without-the-administration) for the public site domain.
For more guidance, see our documentation on [how to configure the administration domain correctly](/documentation/developers-and-admins/configuration/administration-domain-configuration).
#### Continuous delivery
XbyK supports [Continuous deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) and [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration). Set up your CI/CD pipelines (e.g., Azure DevOps, GitHub Actions) before your first production deployment to move schema and configuration changes through code and simplify long‑term maintenance.
#### Security and operations
Consider how you can protect the Admin endpoint (use a separate domain if required, IP allowlisting, SSO/MFA, HTTPS), validate web‑farm configuration when running multiple instances, and test the whole pipeline in a staging environment before go‑live.
### Are there special considerations when performing an upgrade using Xperience by Kentico SaaS?
In short, deploy to SaaS the same way as any other Xperience project. Follow the [SaaS deployment steps](/guides/configuration/saas/saas-walkthrough). Ensure your target Xperience project was installed with the [`--cloud` parameter](/documentation/developers-and-admins/installation#create-a-project) so it includes cloud‑specific configuration and deployment scripts.
If your deployment package exceeds 2 GB, utilize the [custom restore](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-a-large-amount-of-data-during-initial-deployments) feature to upload your data instead.
Deploying via [deployment packages](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-with-a-deployment-package) in Saas currently triggers downtime–we are [working toward minimizing this](https://roadmap.kentico.com/c/307-apply-read-only-mode-to-admin-ui-during-deployment-outages).
If you choose to use [Content sync](/documentation/business-users/content-sync), note that you should carefully configure it ahead of time. For example, you must deploy your content types with a _deployment package_ before you can sync content.
### Can users log in to XbyK administration with their KX13 credentials after migration?
No, all users (including editors and your customer users) will need to create new passwords using the [invitation](/guides/configuration/work-with-users-and-roles/work-with-users/invite-new-user) or [password recovery](/documentation/developers-and-admins/configuration/users/user-management#reset-password) flow. KX13 used ASP.NET Forms authentication with different security and hashing mechanisms than the ASP.NET Core Identity system used in XbyK.
## Project management and client relations
### Do we need to engage a Kentico partner to support with project upgrade?
It depends on your team’s expertise and experience with Kentico project migrations and complex upgrade projects. If you’re comfortable handling these internally, you may not need additional support. However, Kentico offers several resources to help ensure a smooth and successful transition:
  * **Validation of your approach**. The Kentico Consulting team can review your migration plan and confirm that it aligns with best practices.
  * **Upgrade audit**. We provide an audit service to assess the upgradability of your existing project and identify potential risks.
  * **Technical guidance**. We can help you spot obstacles early and recommend proven practices based on our experience with similar projects.


Client communication support: Kentico Customer Success Manager can assist in explaining technical details to your clients and help you clearly communicate changes and upgrade flow throughout the process.
Engaging Kentico can give you confidence that your migration strategy is sound and reduce the risk of unexpected challenges.
### How do I manage client expectations about XbyK differences and upgrade complexity?
Clients are often surprised by the scope and complexity of upgrading to XbyK compared to previous Kentico version upgrades. Proactive communication about XbyK’s architectural differences is essential for managing expectations.
**Common client questions and concerns** :
  * Why is XbyK so different from previous versions?
  * Why does this feel like a Greenfield project rather than an upgrade?
  * Why are certain KX13 features missing or work differently?
  * Why is the migration taking longer than expected?


**Effective communication strategies** :
  * **Set expectations early** : Explain that XbyK represents a fundamental platform change, not just a version update.
  * **Focus on business benefits** : Emphasize improved performance, modern architecture, and long-term maintainability.
  * **Provide context** : Help clients understand that this is an industry-wide transition to modern technology stacks.
  * **Leverage expert support** : Consider having Kentico Customer Success Managers join client calls to provide authoritative explanations and manage expectations.


The key is to frame the migration as an investment in future-proofing their digital presence rather than just a technical upgrade.
### What are the pros and cons of the lift-and-shift vs. complete remodeling upgrade approaches?
While clients may prefer lift-and-shift due to budget constraints, complete remodeling often provides better long-term value.
**Lift-and-shift reality** :
  * Often results in suboptimal performance and poor editor experience
  * Creates expensive technical debt
  * The migration tool transfers the data into target instance, but editing team will miss XbyK benefits


**Complete remodeling benefits** :
  * Optimized for XbyK architecture
  * Better editor experience
  * Improved performance and maintainability
  * Reduced long-term costs


If you are working on an upgrade for a client, it is important to educate them on what they’re sacrificing with the lift-and-shift approach.
### What if a client plans to start migration to XbyK in 2026 but will not finish before KX13 reaches end-of-life?
We **do not recommend** delaying migration beyond the end-of-life date for KX13, which is scheduled for **2026**. After this date, we **do not plan to extend full product support** , including security updates and critical fixes.
If your client has signed a quote to begin migration in 2026 but expects the project to continue past the end-of-life, we strongly encourage discussing options with the **Kentico Partnership Team**. They can help explore strategies to minimize risk and ensure continuity during the transition.
Why moving earlier helps your project:
  * You’ll gain **access to new features and improvements** in XbyK sooner.
  * Your XbyK project will remain **fully supported and secure** , avoiding gaps in critical updates.
  * Early migration **reduces pressure and risk** as the end-of-life date approaches.


For personalized guidance, please reach out to your Customer Success Manager or the Partnership Team. They can help you plan a smooth and future-proof transition.
## Xperience Commerce FAQs
Got questions about our commerce capabilities or wondering about moving from KX13 to XbyK? This section provides clear, concise answers on XbyK’s capabilities, integration strategies, and upgrade pathways.
### Does Xperience have e-commerce?
Yes, **commerce is a core feature** of XbyK and is **production-ready**. However, it’s important to note that the current capabilities are **not identical to previous Kentico versions**. Before starting your project, we recommend reviewing which features you need, to ensure they are available in XbyK out-of-the-box, and plan for **custom implementation** of any missing functionality.
Key points to keep in mind:
  * Commerce is not **an extra paid channel** ; it’s part of the system’s core feature set.
  * You need **Advanced license** tier to leverage XbyK [Commerce features](/documentation/developers-and-admins/digital-commerce-setup).
  * We are **actively developing additional commerce capabilities** , so watch the [product roadmap for updates)](https://roadmap.kentico.com/tabs/6-next-refreshes).


For guidance on what’s currently supported and best practices for implementation, refer to the [XbyK documentation](/documentation/developers-and-admins/digital-commerce-setup).
### What are the current estimates regarding timeline and costs for transitioning from KX13 to XbyK (content & digital commerce)?
It’s not possible to provide a universal estimate for timeline or costs, as these vary significantly based on several factors:
  * **Project complexity**. Larger or more intricate sites require more planning and effort.
  * **Commerce scope**. The number of products, product variants, and related data impacts migration effort.
  * **Level of customization**. – Extensive customizations in Kentico 13 will require additional work to adapt to XbyK’s architecture.
  * **Team experience**. – Familiarity with Kentico and migration processes can speed up delivery.


For accurate planning, we recommend conducting **a migration audit** and discussing your specific scenario with Kentico’s Consulting team or your Customer Success Manager. They can help you assess complexity and outline a realistic timeline and cost estimate.
### Is there a change to the licensing model now when XbyK contains native commerce?
No, there is no change to the [licensing](/documentation/developers-and-admins/installation/licenses). Commerce is a core product functionality, and you can leverage it in any of your [digital marketing channel](/guides/digital-marketing/work-with-channels/digital-marketing-channels) within your digital marketing mix. You can [purchase additional channels](https://partner.kentico.com/Resources/Xperience-by-Kentico/Sales-Materials) if needed.
  * For full details on XbyK licensing, please refer to the official [Kentico documentation](/documentation/developers-and-admins/installation/licenses).
  * Kentico partners can also find [comprehensive licensing information](https://partner.kentico.com/Resources/Xperience-by-Kentico/Sales-Materials) in the [Partner Portal](https://partner.kentico.com).


If you have any questions or need clarification, we recommend reaching out to your Customer Growth Manager—we’re happy to guide you through the details.
### What is the recommended approach for migrating commerce widgets to XbyK, and what limitations should we expect?
It is technically possible to migrate commerce widgets from previous Kentico versions. However, the level of customization required during the upgrade process depends on both the Kentico version and the customizations implemented in the Kentico source project.
  * **Review the project**. Consult the [Upgrade overview](/guides/upgrade-to-xbyk/upgrade-from-kx13/upgrade-from-kx13-overview) for best practices, supported scenarios, and detailed steps to ensure a smooth data transition.  

  * **Data source changes**. To make widgets functional, you will need to [adapt their data sources and retrieval](/guides/upgrade-to-xbyk/upgrade-from-kx13/adjust-your-code-and-adapt) to align with XbyK’s API and data model.
  * **KX13 widgets**. Migrating commerce widgets from KX13 follows the same process as other widget migrations. You can transfer the data using Kentico Migration Tool, and then update your code that [retrieves the data](/guides/upgrade-to-xbyk/upgrade-deep-dives/upgrade-content-retrieval). For general recommendations about migrating widgets, see [Upgrade widgets](/guides/upgrade-to-xbyk/upgrade-deep-dives/upgrade-widgets-introduction) guide.
  * **Kentico 12 Portal Engine**. You cannot migrate [Portal Engine widget code](/k12sp/developing-websites/preparing-widgets-for-users) as it was built on a different version of the .NET framework than XbyK. However, you can migrate supported widget data using the [Kentico Migration Tool](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico/migration-toolkit#kentico-migration-tool).


Recommended approach: Instead of migrating KX13 widgets “as-is”, consider [remodeling and rebuilding the widgets](/guides/upgrade-to-xbyk/upgrade-deep-dives#work-with-widgets-during-upgrade) during migration to ensure a consistent look and feel, better performance, and long-term maintainability. You can [leverage AI to assist you](/guides/development/kenticopilot) in extending [extending existing or implementing new commerce widgets](/guides/development/kenticopilot/implement-widgets-kenticopilot).
### Will support of KX13 commerce bridge be extended?
No, support for the [KX13-XbyK commerce bridge](https://github.com/Kentico/xperience-by-kentico-k13ecommerce) will not be extended. The **Bridge** was introduced as a temporary solution to help projects transition before XbyK gained full native commerce capabilities.
With XbyK, you can now handle all scenarios previously orchestrated by the bridge natively, using built-in commerce features.
Moving to native commerce functionality in XbyK offers several benefits:
  * Better performance and scalability thanks to a modern architecture.
  * Long-term support and updates as part of the core product.
  * Reduced project complexity by eliminating dependency on temporary integrations.


If you have any concerns about migrating from the bridge or need guidance on implementing native commerce features, please reach out to the Kentico Customer Success Manager or Consulting teams. We’re here to help ensure a smooth transition.
### Is it possible to have website-specific customers and orders (multi-store) in XbyK, as in KX13?
No, multi-store scenarios are not supported in XbyK. XbyK is designed as a single-tenant solution, meaning customer and order entities are global across the entire application. There is currently [no roadmap for introducing multistore capabilities](https://roadmap.kentico.com/tabs/4-planned).
What are your options?
  * If your project requires strict separation of customers and orders per store, we recommend running multiple instances of XbyK, one for each store. 
    * Each instance will be fully isolated, with separate data and functionality.
    * Editors will manage content independently in each environment.
    * Please note: each instance requires its own Kentico license and separate hosting (or isolated containers).
  * If your requirement is less strict, for example, you only need to track which channel a customer or order came from, you can implement a custom solution: 
    * Add a field to the customer or order entity to store the originating channel or website.
    * This approach allows you to filter and report on data by channel, but does not prevent access across channels.


**Important distinction:**
If your scenario involves store managers who must not access data from other stores, then full data isolation via separate instances is the recommended approach.
### Can custom development done on KX13 be moved to XbyK? What should be the approach?
Yes, you can migrate your backend code to XbyK, but the effort depends on the type and extent of customization:
  * **Backend logic**. If your custom code targets .NET Standard 2 or .NET Core (8+), it can generally be reused in XbyK. However, you may need to update any data or content queries to align with the new API.
  * **UI customizations**. If you customized the administration interface or built custom modules, these will need to be recoded to fit the new React-based framework used to build the XbyK administration interface.
  * **Validation and adjustments**. Review your code for dependencies on Kentico 13 APIs or features that have changed. Plan for adjustments to ensure compatibility with the new architecture.


In short, backend tasks often migrate with minimal changes, while UI customizations require a full rewrite. We recommend auditing your existing customizations to estimate the effort and avoid surprises during the upgrade.
### Is there a plan to deliver a product inventory application similar to Products in KX13?
No, there is currently no dedicated product inventory application in XbyK. Instead, we recommend modeling your product catalog using the content storage options provided by Xperience.
You can store products either as **reusable content items in the Content hub or as pages in a website channel** , depending on your scenario. Note that Xperience does not include dedicated commerce fields (such as price, currency, or stock).
For detailed guidance, please refer to the [Content modeling guide](/guides/architecture/content-modeling/content-modeling-guide) and explore commerce [best practices](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog) for structuring your product catalog.
### How do we migrate SKUs from COM_SKU to XbyK content types?
The [Kentico Migration Tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool) does not natively support migrating KX13 SKUs to XbyK.
To transfer product data from KX13, you might want to consider customizing the Universal Migration Tool to correctly migrate SKU data into the new content model in XbyK. Please note that the Universal Migration Tool provides a good starting point, but it may not be sufficient for all projects.
For complex scenarios, we recommend building custom functionality to handle the migration and leverage the XbyK [Content Management API](/api/content-management) for precise control and automation.
### How can we ensure data integrity when migrating customized commerce objects from Kentico Xperience 13 to Xperience by Kentico?
XbyK is a new product, not a direct continuation of KX13. Its data structures particularly for commerce entities, such as orders or customers, differ significantly from previous versions.
In KX13, it was common for partners and clients to **customize default commerce objects** by adding additional fields to meet specific business needs. These customizations will **not automatically carry over** during migration.
To ensure your XbyK instance includes all necessary fields and maintains data integrity:
  * **Review the KX13 source instance data** to identify all commerce fields, including default KX13 data and all custom ones.
  * Identify **default data fields** in XbyK commerce-related objects.
  * **Manually create missing commerce fields** in XbyK before migration. This will include some of the default commerce KX13 data fields and all custom.


For best results, thoroughly **review your current data model** and **plan your migration** based on both the source Kentico version and the extent of customizations in that version.
### Is there a reference architecture or sample implementation for migrated commerce sites?
Currently, we provide **a sample implementation** of commerce functionality in the **Dancing Goat** project, which developers can [install locally](/documentation/developers-and-admins/installation#install-project-templates-package). This example demonstrates the commerce features supported by XbyK and serves as a helpful starting point for building your own solution.
However, please note:
  * We **do not have a complex commerce demo site** (similar to the Dancing goat commerce demo in KX13) that showcases a full end-to-end commerce implementation.
  * For advanced scenarios, you will need to **design and implement custom solutions** based on your project requirements.


For guidance, refer to the **Dancing Goat sample project** , the official [Xperience by Kentico documentation](/documentation/developers-and-admins/digital-commerce-setup), or feel free to reach out to the Kentico Consulting team for additional support.
### Is there a recommended structure for modeling product variants?
Yes, we recommend modeling product variants as child items of a parent product. The parent holds shared information, while each variant contains only the fields that differ (e.g., size or color). You can define dedicated content types for variants and use reusable field schemas to efficiently manage shared and variant-specific data.
For full guidance and examples, please refer to the official documentation about [modeling product variants](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog).
### How do we handle product categorization without COM_Department and COM_Collection?
In XbyK, product categorization is handled differently than in previous versions. Instead of using COM_Department or COM_Collection, you can organize and categorize your product catalog using:
  * **Content types** with [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas).
  * **Taxonomies** (or a combination of both).


While the **content tree** can be used for organizing products, it’s only recommended for very small catalogs—not for categorization purposes.
Additionally, the Content hub offers powerful tools like **workspaces, folders, and smart folders** to help structure your catalog effectively.
For more details and best practices, please refer to the [Kentico documentation](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog).
### How do we handle tax calculation?
By default, tax is not calculated automatically. XbyK provides a flexible interface for handling tax calculations that requires some configuration. Your developers need to implement a custom tax calculation step to apply the correct tax logic for your business. Once set up, the system will automatically apply tax during price calculations, including unit prices, subtotals, shipping, and final totals.
Refer to a sample implementation in the [Dancing Goat](/documentation/developers-and-admins/installation#install-project-templates-package) demo project that shows how to calculate and apply taxes in real-world scenarios.
### What if our project relies heavily on KX13’s discount engine?
XbyK contains built-in [promotions](/documentation/developers-and-admins/digital-commerce-setup/promotions) with [catalog discounts](/documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts) (product-level price reductions), [order discounts](/documentation/developers-and-admins/digital-commerce-setup/promotions/order-discounts) (cart-level promotions), and general gift cards that use generic codes like “DISCOUNT25OFF” available to all customers.
Additional discount capabilities are on the roadmap, including unique per-user coupons, [Buy X, Get Y](https://roadmap.kentico.com/c/368-digital-commerce-shipping-buy-x-get-y-promotions) promotional rules, and extensibility options that allow developers to customize the built-in discount engine for project-specific business logic.
What’s currently available:
  * Store admins can define and manage catalog and order discounts through the administration UI, including defining discount values and configuring eligibility and availability rules.
  * Developers have APIs to programmatically work with discounts and integrate discount logic into [checkout process](/documentation/developers-and-admins/digital-commerce-setup/checkout-process).
  * The [pricing calculation service](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization) applies eligible discounts to ensure accurate pricing calculations across shopping carts and orders.


For complex pricing scenarios beyond the built-in discount engine, such as customer-segment-specific pricing, complex volume-based tier pricing, or dynamic pricing based on external factors, you can model custom pricing fields in your product content types, develop a dedicated custom module, or integrate a 3rd-party discount engine into the [pricing calculation](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization).
### Does the migration tool support order and customer migration (mapping)?
Yes, [Kentico Migration Tool](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico/migration-toolkit) supports migrating orders and customers since January 2026.
### How do we handle customer credit history and balance tracking in XbyK?
XbyK does not include built-in support for customer credit history like KX13 did. However, you can implement this functionality through custom development.
To replicate features such as credit balance tracking, transaction history, using credit as a payment method, and refund processing, you’ll need to extend the customer profile and create a custom interface for managing credit events.
A typical credit event entry might include:
  * **Event name** – A short label for the transaction (e.g., _Refund_ , _Manual adjustment_).
  * **Event credit change** – The amount to add or deduct (use a minus sign for deductions).
  * **Event date** – When the credit change occurred or will occur.
  * **Event description** – Optional details about the transaction.


This approach allows you to tailor credit handling to your specific business rules and integrate it with other systems if needed.
## Did you find the answers you were looking for?
Did this page address your concerns or are you missing some information? Let us know using the **Send us feedback button**.
![]()
[]()[]()
