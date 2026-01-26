---
source: https://docs.kentico.com/modules/work-with-taxonomies/taxonomy-types
scrape_date: 2026-01-26
---

Module: Work with taxonomies
6 of 8 Pages
# Taxonomies deep dive
Taxonomies can serve different purposes in Xperience, depending on how you categorize and retrieve your content. We have identified two primary use cases: **semantic** taxonomies and **functional** taxonomies.
### Semantic taxonomies
_Semantic taxonomies_ are broad, general classifications that organize content by type or theme rather than function. They include categorizing e-shop products like electronics and furniture, or content types such as news, press releases, blog posts, and guides. By grouping related content under high-level themes like “technology,” “health,” or “finance,” these taxonomies help users quickly find relevant information and provide editors with a clearer view of the content landscape. Editors can use the built-in hierarchy of the tree and further break down their topic categories. For example, the _finance_ category can include articles about bank’s _Loan_ products that cover topics such as student loan, personal loan, or home equity loan.
The following image shows the _Loans_ taxonomy from the [Kbank demo site](/guides/architecture/content-modeling/content-modeling-guide/what-is-a-content-type#best-practices-in-kbank-demo-site).
[![Example of a semantic taxonomy on Kbank demo site](/docsassets/guides/model-taxonomies/cm-semantic-taxonomy-finance-group.png)](/docsassets/guides/model-taxonomies/cm-semantic-taxonomy-finance-group.png)
This approach is beneficial for extensive content collections, as it allows editors to present related articles together on a single page or section, making it easier for users to explore similar topics. Additionally, semantic taxonomies enable content recommendations by linking items with shared tags, which enhances user experience and increases content visibility. This can particularly helpful for categorizing media _Assets_ in the _Content hub_.
To sum up, _semantic taxonomies_ provide valuable metadata that editors can use to support content discovery, organization, and maintain a consistent content structure.
### Functional taxonomies
On the other hand, _functional taxonomies_ are designed to help with filtering and data retrieval or to drive specific UI behaviors. For example, you can use _functional taxonomies_ to filter a list of services based on their relevance to a persona or to populate dropdowns with content options in [Page Builder widgets](/documentation/developers-and-admins/development/builders/page-builder). Since these taxonomies are tied closely to functionality, keeping their scope specific to particular content types is essential. For instance, if you’re working on a financial website, you might create a taxonomy for financial product features or regional availability information, each tied to a specific use case.
Each functional taxonomy should have a clear, 1:1 relationship with a specific use case. This will allow content managers to keep the editing experience intuitive and reduce the risk of accidental changes that could disrupt functionality elsewhere in the system. By structuring functional taxonomies into small, clearly defined taxonomy groups, you’re adding a layer of protection that ensures editors only interact with taxonomies relevant to their tasks.
[![Example fo a functional taxonomy on Kbank demo site](/docsassets/guides/model-taxonomies/cm-guide-functional-taxonomy-regional-availability.png)](/docsassets/guides/model-taxonomies/cm-guide-functional-taxonomy-regional-availability.png)
### When to use taxonomies in Xperience
Relationships defined through taxonomies (and their tag collections) in Xperience are considered “soft.” What does it mean?
There’s no built-in safeguard to prevent editors from altering or updating tag collections if they have access to the _Taxonomies_ application. This flexibility is helpful for many content scenarios, but it’s important to remember that Xperience doesn’t tightly control any changes in tag collections.
That’s why solution architects should design the implementation to use taxonomy for content categorization and organization rather than for situations which require strict control over tag and data integrity. If you need to protect your content relationships from unintentional changes or resurfacing information in unwanted scenarios, for example, for information security reasons, we recommend using custom [content type fields](/documentation/developers-and-admins/development/content-types) on individual content or website page types and combining them with securing [access to content items](/documentation/developers-and-admins/configuration/content-hub-configuration).
Taxonomies can drive various content delivery scenarios. For example, you can use taxonomies with [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) to list all items or pages tagged with a specific category, allowing for dynamic content display. However, avoid relying on taxonomies when you need to ensure restricted access to sensitive data.Instead, use custom fields within content types to enforce more secure access control.
The taxonomy selector is built into applications that deal directly with content, including website channels, the _Content hub_ , email, and headless channels. However, it’s unavailable in areas like **Contact Management** or **Contact groups** applications or in [custom classes](/documentation/developers-and-admins/customization/object-types), though this might change [in the future](https://roadmap.kentico.com/c/238-contacts-auto-tagging-powered-by-aira). Taxonomies are not versioned, and there are currently no plans to add versioning capabilities.
### Custom content type to replace built-in taxonomy
In Xperience, tags have predefined fields and currently offer limited customization options. If you need to add custom fields to your taxonomy tags, consider building a custom solution using dedicated content types.
For projects that require more complex content categorization, you can work with developers to build custom functionality. This approach allows you to:
  * Define exactly how editors categorize content within the administration interface.
  * Control how references are stored within a custom application.
  * Specify the taxonomy system’s complexity, including elements like the number of parent-child levels or a tagging structure.


The following image shows a healthcare Provider content type that uses a custom content type to define relationships.
[![Provider content type uses dedicated content types Location and Service to indicate relationships](/docsassets/guides/model-taxonomies/cm-guide-custom-taxonomy-types-on-in-content-item-khealth-demo-site.png)](/docsassets/guides/model-taxonomies/cm-guide-custom-taxonomy-types-on-in-content-item-khealth-demo-site.png)
Developers can display references between the content types on the healthcare provider profile page in the Khealth demo site.
[![Profile page shows content displayed from linked content items](/docsassets/guides/model-taxonomies/cm-guide-custom-taxonomy-types-khealth-demo-site.png)](/docsassets/guides/model-taxonomies/cm-guide-custom-taxonomy-types-khealth-demo-site.png)
The exact relationships between the data can drive faceted filtering on a search page in the Khealth demo site.
[![Taxonomy defined through custom content types drives filtering on Khealth demo site](/docsassets/guides/model-taxonomies/cm-guide-custom-faceted-taxonomy-types-khealth-demo-site.png)](/docsassets/guides/model-taxonomies/cm-guide-custom-faceted-taxonomy-types-khealth-demo-site.png)
[ Previous page ](/modules/work-with-taxonomies/taxonomies-in-general)
6 of 8
[ Mark complete and continue ](/modules/work-with-taxonomies/taxonomies-for-personalization)
![]()
[]()[]()
