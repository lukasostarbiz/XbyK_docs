---
source: https://docs.kentico.com/guides/architecture/content-modeling/content-modeling-guide/model-taxonomies
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Content modeling](/guides/architecture/content-modeling)
  * [Content modeling guide](/guides/architecture/content-modeling/content-modeling-guide)
  * Model taxonomies 


# Model taxonomies
## About this guide
This page is a part of the **Content modeling guide** which you should follow sequentially from [beginning to end](/guides/architecture/content-modeling/content-modeling-guide). You can also follow this series in [Content modeling guide](/modules/content-modeling-guide), which helps you keep track of your progress as you move through this sequential material.
## What is taxonomy
Taxonomy refers to a system of classification, categorization, and organization your team uses to categorize and group content based on shared characteristics or attributes.
Taxonomies provide a structured way to organize and label content within content management systems. A taxonomy typically comprises a hierarchical arrangement of categories, subcategories, and associated terms or tags. During content modeling, your team can define taxonomies based on various criteria, such as subject matter, topics, audience segments, product categories, or any other relevant classification scheme that suits the business needs for their digital content. These categories and tags help classify and describe content, making it easier to search, navigate, and filter information.
## Types of taxonomies in general
You can model taxonomies in Xperience using one of the following ways
  * **Controlled Vocabulary** – A _controlled vocabulary_ is the simplest taxonomy type. Editor teams define specific words or terms that represent topics shared across different content pieces and agree to use only these terms to describe categories of topics within their digital content.
  * **Hierarchical taxonomy** – This taxonomy type enables you to establish hierarchies between tags and show their relationships. Hierarchical taxonomies help your team structure complex content, allowing you to create logical parent-child relationships between topics. This enhances content organization and user navigation, especially in large content sets.


In Xperience, you can build _controlled vocabularies_ and _hierarchical taxonomies_ , which offers your team greater flexibility when organizing your digital content.
### Other taxonomy types
Xperience doesn’t support advanced taxonomies such as [semantic networks](https://en.wikipedia.org/wiki/Semantic_network) or [ontologies](https://en.wikipedia.org/wiki/Ontology_\(information_science\)). These more sophisticated taxonomies often require specialized systems that can model relationships and provide an easy-to-navigate way through the classification schemes.
For more information on taxonomies and how to define them, take a look at [The Accidental Taxonomist](https://www.hedden-information.com/accidental-taxonomist/) by Heather Hedden. This book categorizes taxonomies into the types discussed above.
## Classification options in Xperience
At its core, taxonomy serves the purpose of content classification. Xperience by Kentico provides several features that aid in categorizing and organizing your content, each with a distinct purpose and functionality.
### Taxonomies application
Xperience’s **Taxonomies** application is [a standalone feature](/documentation/developers-and-admins/configuration/taxonomies) that operates independently of other objects, such as languages or pages. This separation is particularly relevant for users familiar with previous product versions, as it represents a shift to a more modular approach. _Taxonomies_ in Xperience create what is described as “weak relationships” — associations that aren’t inherently tied to other objects. It’s essential to consider this when designing your content model or project structure.
### Reusable field schemas
Reusable field schemas allow you to classify content at the data level. These schemas can include fields specifically for taxonomy tags, enabling cross-content-type categorization. By implementing reusable field schemas, you can create collections of fields that make it easier to group related data across various content types, streamlining the content management process.
### Smart folders in the Content hub
Xperience _smart folders_ allow content editors to save complex content views. With the assistance of developers, editors can use [](/documentation/business-users/content-hub/content-hub-folders#smart-folders) to dynamically deliver content into various channels based on the saved views.
For example, an editor can configure a “Five latest articles” smart folder to group the five latest published articles automatically. Developers prepare an article listing widget for editors and ensure they can use it on any website channel. An editor then places a listing widget on their website page and configures it to display content from the “Five latest articles” smart folder. When anyone on the team publishes a new article, Xperience automatically adds this article to the “Five latest articles” smart folder and refreshes the content presentation. In this example, the latest published article automatically replaces the oldest article in the listing widget.
### Content folders in the Content hub
[Folders](/documentation/business-users/content-hub/content-hub-folders#content-folders) in the **Content hub** facilitate the filtering and grouping of content within a flat hierarchy. This feature helps your team label their digital content and assign each item to a single folder. Folders in _Content hub_ are designed for content organization but do not support nested hierarchies, nor are they intended for access control within the _Content hub._
### Integrating taxonomies with other classification features
Since taxonomies are designed to organize content, editors can use them with Xperience’s classification features. Here are a few practical ways to use taxonomies:
  * **Reusable field schemas** : Assign taxonomy tags within [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) to help identify and group data with tags that indicate the same topics.
  * **Smart folders:** - Your team can filter content within [smart folders](/documentation/business-users/content-hub/content-hub-folders#smart-folders) using taxonomy tags as one of the filtering criteria. _Smart folders_ allow your editors dynamically deliver or filter content they want to, for example, [display on website channels](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-content-items-from-smart-folders).
  * **Content folders:** You can manually store content marked with the same [taxonomy tags](/documentation/developers-and-admins/configuration/taxonomies#enable-editors-to-tag-content) into dedicated [content folders](/documentation/business-users/content-hub/content-hub-folders#content-folders) to streamline content organization. However, remember that content items cannot exist in more than one _Content hub_ folder.


By understanding and using taxonomies within Xperience, you can achieve a more organized, efficient, and adaptable content structure, making it easier for editors and users to find and interact with relevant content.
## Taxonomies in Xperience by Kentico
Built-in [Taxonomies](/documentation/developers-and-admins/configuration/taxonomies) application will help create and manage content categories using [tag hierarchy](/documentation/developers-and-admins/configuration/taxonomies#enable-editors-to-tag-content).
A **taxonomy** or **taxonomy group** is a collection of associated tags organized around a common theme or attribute. These groups allow you to categorize content by creating logical connections among related tags. Xperience will enable you to make as many taxonomy groups as needed.
Within a taxonomy group, **tags** are arranged in a **taxonomy tree** , which uses a parent-child hierarchy to show relationships between tags. This hierarchy allows you to break down broader categories into more specific subcategories and helps you structure your taxonomy. The hierarchical structure also helps with navigating through extensive collections of tags.
At the most detailed level, individual **tags** act as keywords that describe particular content attributes. A content item can have one or more tags, allowing you to categorize it and highlight its connections to other content. Xperience supports assigning tags to content both manually and through the API.
Xperience allows you to control editor access to the **Taxonomies** application. You can manage permissions to ensure editors can create, update, or delete tags—or restrict these actions as needed.
## Two approaches to taxonomies in Xperience
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
## Structure taxonomies
Xperience allows editors to structure taxonomies in two main ways: by building a deep tag hierarchy within a single taxonomy or by creating multiple taxonomies.
### Single taxonomy with deep multi-level hierarchies
During the content modeling session, stakeholders agree to create one large taxonomy group to store all their tags and hierarchies. A single taxonomy offers all tags in one place, which simplifies tag management. For example, adding new tag hierarchy doesn’t require code changes since all tags are in one taxonomy.
Using a large single tag collection can have its drawbacks. If the editors decide to use parent tags to build semantically distinguished tag collections, they’ll need to follow strict content editing rules to avoid potential issues. As the taxonomy collection grows, different editors might see groups of unrelated tags new content which might slow them down a bit when adding new content. At the same time, developers might need to set up a complex querying logic to safeguard against unwanted changes.
When your content needs grow, you can break this single large taxonomy collection into smaller hierarchies if needed.
### Multiple taxonomies
During the content modeling team, stakeholders define multiple taxonomy groups that developers use in the content types.
Using multiple taxonomies (or taxonomy groups) allows for more specific tag contexts, but it comes with tradeoffs. More taxonomies mean more fields and, therefore, more complex queries. Extending or adding new taxonomy groups requires code changes, such as updating content type fields, rebuilding content types, and modifying data retrieval logic. You’ll also need to adjust components like Page Builder widgets to accommodate new fields and properties.
### Plan for changes and permissions
During project planning, consider that adding new taxonomy groups will lead to code adjustments. Developers must define taxonomy groups during content type setup, manage querying, and display data accordingly. If editors have CRUD access to the _Taxonomy_ application, they can create or modify tags, which may disrupt existing structures. Xperience doesn’t come with granular access-level permissions that you can use to restrict editor’s access to specific taxonomies and their tag collections.
## Work with taxonomies
Taxonomies let you categorize and link content, making building relationships between items and managing content organization easier.
### Building content relationships
Taxonomy tags create relationships between tags and content objects, allowing you to filter and retrieve content based on specific labels. When editors assign a tag to an item, you can use that tag to filter content that shares similar attributes.
For instance, a medical clinic might tag its type, like a _General Hospital_ , _Community Hospital_ , or _Retirement Community_ , under a _Location Type_ taxonomy. This makes it easy to retrieve a list of clinics that belong to a specific type, display a label to indicate the type of clinic, or filter out healthcare providers available at the location. By defining a content query, developers can access this data from either side of the relationship, ensuring flexibility in how you present related content.
The following image shows the _Location Type_ taxonomy.
[![Location taxonomy with one of the tags](/docsassets/guides/model-taxonomies/cm-guide-taxonomy-khealth-location-type.png)](/docsassets/guides/model-taxonomies/cm-guide-taxonomy-khealth-location-type.png)
[![Faceted search using the location taxonomy in Khealth demos site](/docsassets/guides/model-taxonomies/cm-guide-taxonomy-listing-khealth-demo-site.png)](/docsassets/guides/model-taxonomies/cm-guide-taxonomy-listing-khealth-demo-site.png)
The image above shows a faceted search against a specific location type.
### Taxonomies and multilingual content
In Xperience, tags and taxonomies are fully localizable. This means the tags aren’t tied to a specific language version of a content document (unlike the old categories in previous Kentico versions). Once administrators add a language in the Xperience [Languages](/documentation/developers-and-admins/configuration/languages) application, editors can localize taxonomy tags in any of those languages. When a tag is translated, content in that language will automatically display the localized tag. If a tag isn’t translated but is still used within a target language environment, Xperience will display the tag in its default language.
While Xperience supports general language fallbacks, taxonomies don’t inherently include fallback capabilities. So, if you need a tag to appear in multiple languages, translating it for each is the best way to ensure consistency.
### Use taxonomies for semantic categorization
Taxonomies can be a powerful tool for semantic categorization, especially for complex business services. In a banking institution, for example, you might create a taxonomy to classify different types of financial products. This taxonomy could categorize services by target audience—such as individual consumers or business clients—and then drill down further based on specific product features. You might have categories for personal checking accounts, savings accounts, and credit cards, each tailored to a different user type. This level of categorization allows users to quickly find products that meet their needs while providing a clear structure for managing these services within the system.
### Use taxonomies for personalization
You can set up a taxonomy in which each tag represents a different audience type, such as a business user, developer, or administrator. Then, create contact groups that correspond to each persona.
Label your materials based on the intended audience to personalize content for different users. You can link these persona tags to users via contact groups or custom member data fields, which are filled out based on information customers submit during registration.
Here are two ways to filter content based on the visitor’s persona:
#### Personalization Based on Contact groups via widget
Editors will tag content items with the relevant persona to personalize content based on personas. Developers will prepare widgets with properties that include options for selecting default content items, persona tags, and content items specifically for personalization. Then, they will use logic like _ContactIsInContactGroup_ to ensure that content displays according to the visitor’s group membership, allowing for targeted, persona-based content delivery.
#### Personalization based on member data (Behind sign-in)
As part of the registration process, prompt users to provide information like their job title or field of expertise. This data will help determine the specific type of content they see on the site.
In the Page Builder widgets, you can enable editors to add content tailored to each persona based on this information. This setup allows for a more personalized user experience, as each user’s role or field directly influences the materials and resources presented to them, ensuring that they see the content most relevant to their needs.
When setting up page templates for this approach to content personalization, note that template properties apply globally. However, other Page Builder components will only reflect these changes once editors configure the page, so you may need to implement a check to ensure the correct content is displayed based on visitor type.
### Use taxonomies to build search interfaces
Typically, customers build their faceted search experience using the search indexes and list key terms.
In Xperience, you can use the _Taxonomies_ application to create a faceted search experience that lets users filter content based on specific categories or attributes. By tagging content items with taxonomy terms—such as product type, region, or topic—you provide structured filters that allow users to narrow down search results. For example, users can apply multiple filters at once to find content that meets all their criteria. This approach makes it easy to locate relevant information quickly, especially in large content collections.
For more detailed examples of what it’s like to use taxonomies as a content editor, explore the two following guides [Taxonomies](/guides/digital-marketing/work-with-taxonomies/taxonomies) and [work with taxonomies](/guides/digital-marketing/work-with-taxonomies/work-with-taxonomies-in-kbank).
## What’s next?
The [next page](/guides/architecture/content-modeling/content-modeling-guide/store-content) explains how to store your data in Xperience. Understanding available options will help you identify the best approach to defining (or modeling) the content types you identified for your project during the content audit and modeling sessions.
![]()
[]()[]()
