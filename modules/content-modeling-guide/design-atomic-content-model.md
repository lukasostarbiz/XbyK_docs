---
source: https://docs.kentico.com/modules/content-modeling-guide/design-atomic-content-model
scrape_date: 2026-01-22
---

Module: Content modeling guide
20 of 28 Pages
# Design atomic content models
## Deliver multi-channel experience with atomic content model
The **atomic content model** breaks content into small, reusable elements – _titles and summaries_ , _images_ , _features_ , _benefits_ , or _call to actions_ that you can link and compose into larger semantic types like **Product** , **Article** , or **Event**.
### Store data in reusable content types
Instead of duplicating content across different channels, editors store it once in **reusable content types** that they can reassemble wherever needed. This keeps the [structured data](/modules/content-modeling-guide/structured-and-unstructured-content) without any presentation information and makes it easy to maintain.
### Keep digital content channel-neutral
This _atomic_ approach keeps your content channel-neutral. The same _structured product data_ can appear consistently across your website, email campaigns, headless applications, mobile apps, or any future channels you adopt.
Editors don’t need to rewrite or duplicate content when expanding to a new channel. Instead, they can _refer to the same reusable data_ and present it with the appropriate channel context.
### Simplify content management workflow across teams
Working with _reusable content_ gives editors other advantages. They manage their reusable content in the _Content hub_ and layer on channel-specific context using dedicated, channel-specific content types that we refer to as channel-specific [wrappers](#use-wrappers-to-display-atomic-content-across-multiple-channels) for _website pages_ , [headless channel](/modules/content-modeling-guide/design-headless-content#populate-headless-channels-with-headless-specific-content) or [emails](/modules/content-modeling-guide/design-email-content#reusable-content-in-emails).
### Content becomes a single source of truth for reusable content
Different departments can deliver consistent customer experiences across channels through single-source-of-truth management and ensure fast and reliable updates. Each team can manage different parts of the content or its fragments if the project requires it. Teams maintain clear ownership over their data, and the atomic content model helps the company simplify content workflows and approvals across the organization.
### Atomic data allows your team to grow
The _atomic content model_ makes the content ready for future expansion. The company’s content operation will not require a full re-architecture each time it adds a new channel or introduces a new marketing touch point. The data structure remains flexible and supports editors, marketers, and developers in delivering personalized and scalable digital experiences.
## Compare atomic content modeling with traditional approaches
In traditional content models, teams often use large, single-purpose content types, like a _Product_ page, that contain _title_ , _descriptions_ , _images_ , _benefits_ , _CTAs_ , _Open Graph_ , or _SEO metadata_ within a single item. The [page-based](/modules/content-modeling-guide/design-page-based-content-model) approach can feel intuitive and straightforward, especially for single-channel, website-driven experiences.
The traditional approach stores data in _pages_ , which ties the content to a specific presentation layer, most commonly a website.
While this approach can be easy to start with, it can slow down delivering marketing projects as the project grows and the team adds more channels, changes how they manage content, or introduces new ways they need to display data. In the extreme case of [page-based content model](/modules/content-modeling-guide/design-page-based-content-model) with all content in _Page Builder_ widgets, they will _need more time to update or remove outdated content_ as editors need to find and update each page where similar or the same content piece appears.
## See how atomic content modeling works in practice
Let’s look at an example from the Kbank _Business Banking_ demo site. Suppose you have a **Product** content type.
The **Product** includes a _Core schema fields_ , such as _Title_ , _Description_ , _Product summary_ , and some basic _taxonomy_. In [page-based content model](/guides/development/developer-kickstart/define-a-page-content-type), you might have stored everything else besides the _Core content schema_ into the _Product_ page, as the data describes the product and its capabilities. In _the atomic content model_ , instead of storing everything in one place, you can **split related information into separate content types** :
  * Core product data in [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas)
  * Product features
  * Product benefits
  * Product images


[![Product content model diagram](/docsassets/guides/design-atomic-content-model/cm-guide-product-content-model-diagram.png)](/docsassets/guides/design-atomic-content-model/cm-guide-product-content-model-diagram.png)
See other examples of [semantic content types](/modules/content-modeling-guide/work-with-semantic-content-types#aim-for-semantic-content-types) you can use to translate content objects into a predictable structure.
## Use linked items to keep your content reusable
Each of these smaller content types stores reusable information. So if two products share the benefit of _Fast access to funds_ , editors don’t duplicate the information. Instead, they link the _benefit_ to the _products_.
This gives them the power to reuse content, not duplicate it. When an editor updates the _benefit’s description_ or _icon_ in a content item, all products (or _Page Builder_ widgets) referencing it get the update automatically. Developers can [query](/documentation/developers-and-admins/api/content-item-api/content-retriever-api#configurable-retrieval-behavior) these linked items in custom code to retrieve collections of references, display the data through widgets, or pull additional data like _Call to actions_ to accompany these benefits.
### Approach every content type like a Media asset
Images and other assets, such as videos, infographics, or podcasts, are expensive to create, and it’s natural that businesses want to reuse them as much as possible.
This makes [assets](/guides/architecture/content-modeling/content-modeling-guide/store-files) the ultimate example of _content reusability_ that you store in the _Content hub_. Editors refer to content item assets, images, or documents to display them in pages, emails, or headless content. You can find more information about building dedicated content types in [material about storing files](/guides/architecture/content-modeling/content-modeling-guide/store-files).
Break content into dedicated reusable pieces and have editors refer to a single item to improve content governance. Identify which pieces of content can be stored into _smaller content items_ or combined into _content fragments_ that convey a semantic meaning, such as a collection of _FAQ elements_ , _product features_ , _benefits_ , _testimonials_ provided by similar-size customers and other content that editors will want to share in the same combination across different channels, or content items that you want to feature together, e.g., in a slideshow. 
Linked items make it easier to govern who can edit what. For example, your _financial services team_ can manage _benefits_ , while the _product team_ focuses on _product specs_.
## Leverage the combined content selector
Reusing content items improves content maintenance. When an editor updates the referenced item, the content changes are automatically promoted to every other item that uses this content, making content management a breeze. When defining the content model, discuss which types can be reused and create them accordingly.
Editors will use the _content item_ with the **Combined content selector** form component to ensure reusability. The [ContentRetriever API](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-linked-content-items) allows developers to query the linked items in their custom code and return [a collection](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items) of [references to content items](/documentation/developers-and-admins/api/content-item-api/content-item-database-structure), which they can further process. For example, they can retrieve URLs of web pages to which editors want to guide their audience. You can also configure the _Combined content selector_ to specify [which content types editors can refer to](/documentation/developers-and-admins/development/content-types#add-the-option-to-link-content-items). Specifying allowed content types creates predictable structures and improves the editing and development experience.
## Track and manage language variants of content
_Atomic content model_ in Xperience supports multilingual delivery. It allows editors to store reusable content items in one language and track which language variants exist or are missing.
When editors create a language variant of their content, Xperience indicates whether all other content items that compose this content item are translated or not.
[![Xperience warns editor that a linked content item isn’t localized into a target language](/docsassets/guides/design-atomic-content-model/cm-translation-warning-not-translated.png)](/docsassets/guides/design-atomic-content-model/cm-translation-warning-not-translated.png)
[Language fallback](/documentation/developers-and-admins/configuration/languages#language-fallbacks) allows shared content to remain consistent across languages: Editors don’t need to duplicate it, which reduces translation workload.
Teams can choose which linked items, such as _local-specific CTAs_ or _product descriptions_ that depend on the market variants, should be translated while leaving others, like globally defined _disclaimers_ , _taxonomies_ , or _brand visuals_ , shared across all language versions. This simplifies managing multilingual sites and helps content stay consistent across markets.
## Use wrappers to display atomic content across multiple channels
The _atomic content model_ keeps the data clean of channel-specific details. To deliver the reusable content in a specific channel, editors will add necessary channel-specific context by using _wrapper pages_ for headless or website delivery.
A **wrapper** is a content item (_webpage content type_ in website channels or* headless content type _). Editors use wrappers to refer to a specific item in the_ Content hub* to get global, universal data, for example, for a _product_ , and then layer on the details needed for a specific channel, such as your website, email campaigns, or a headless application. Adding the _wrapper page_ into the content tree also ensures the content can be navigable through [a page URL](/documentation/business-users/website-content/manage-page-urls).
[![Page wrapper with linked item and channel specific information](/docsassets/guides/design-atomic-content-model/cm-page-wrapper-with-linked-item-and-channel-specific-info.png)](/docsassets/guides/design-atomic-content-model/cm-page-wrapper-with-linked-item-and-channel-specific-info.png)
### How page wrappers work on websites
For example, when displaying a _product_ on your website, editors don’t store _Open Graph_ metadata, _SEO metadata_ , _canonical URL_ , or the _page publish date_ inside the **Product** content type. Instead, they:
  * Put a reusable _Product_ content item with valid data for any presentation layer to the _Content hub_.
  * Use a dedicated _wrapper_ page to add a _Product page_ in the website’s content tree.
  * Refer to the _Product_ content item stored in the _Content hub_ using a selector.
  * Add channel-specific fields in the page wrapper, such as: 
    * _Open Graph title_ and _description_ for social sharing.
    * _SEO title_ and _meta description_ for search engines.
    * _Publish date_ and _author_ for blog-like content.
    * Channel-specific _hero images_ or _banners_ used only for website channels.


[![SEO and OpenGraph data on a product page wrapper in Kbank demo site](/docsassets/guides/design-atomic-content-model/cm-example-seo-and-og-data-in-resuable-field-schema.png)](/docsassets/guides/design-atomic-content-model/cm-example-seo-and-og-data-in-resuable-field-schema.png)
This approach ensures your _atomic content_ remains clean, reusable, and consistent while allowing your website to display it with the metadata and context needed for that channel.
### How wrappers work in headless delivery
In headless scenarios, editors can create a headless wrapper for their content. The _headless wrapper_ references the reusable content items. It allows editors to add channel-specific calls to action, hero banners, or tracking tags needed for the app or headless site, without modifying the underlying structured data.
For example, the marketing team can add a seasonal CTA to the headless wrapper while the Product content remains unchanged in the Content hub. We discuss approaches to [designing headless content](/modules/content-modeling-guide/design-headless-content) in a dedicated guide.
[![Example of a headless article in Kbank demo site](/docsassets/guides/design-atomic-content-model/cm-example-headless-content-type.png)](/docsassets/guides/design-atomic-content-model/cm-example-headless-content-type.png)
### Why content wrappers matter
_Page_ (or _content_) _wrappers_ allow you to:
  * Maintain a single source of truth for your core content while adapting it for each channel’s needs.
  * Avoid duplicating data just to add metadata necessary for a particular channel.
  * Support flexible delivery across channels, ensuring your website, headless apps, and other customer touch points present the same content.
  * Empower editors to enhance content items within their channel without changing the underlying _core reusable data_.


This approach makes your content model scalable and helps marketing, product and other teams collaborate to deliver content across different digital marketing channels.
## Plan content lifecycle workflows and governance
The _atomic model_ works best with clear rules for who owns which content. Different teams can manage specific reusable parts without stepping on each other’s work. For example, product managers can own and update the _core product data_ , the marketing team can manage and optimize _campaign CTAs_ , and the legal team maintains _disclaimers_ to ensure compliance.
Using [workspaces](/documentation/developers-and-admins/configuration/users/role-management/workspaces), [page permissions](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management), [role permissions](/guides/configuration/work-with-users-and-roles/work-with-users/users-and-roles-overview), and content [workflows](/guides/digital-marketing/work-with-workflows/workflows) in Xperience helps you enforce this structure.
You can set up [workspaces](/documentation/developers-and-admins/configuration/users/role-management/workspaces) so each team only sees and edits the content they are responsible for. Combine _workflows_ with granular [permissions](/documentation/developers-and-admins/configuration/users/role-management#assign-permissions-to-roles) to ensure editors can access content they are supposed to work with without accidentally modifying it. Content [workflows](/guides/digital-marketing/work-with-workflows/workflows) add an approval layer, ensuring changes are reviewed by the right people before they go live.
Good governance ensures that **editors don’t accidentally break the structure or duplicate data across** the system. It keeps your content model clean and sustainable, enabling your teams to collaborate confidently while protecting the integrity of your reusable content.
## Know the challenges before you start
_Atomic models_ are powerful but require more planning. This means that they work well for mostly experienced teams. Teams must cooperate during the [content modeling sessions](/modules/content-modeling-guide/content-modeling-process) to ensure the project’s success. They need to agree on many different things, from content composition to the content governance rules, up to a detailed understanding of the content lifecycle. Editors working with the atomic content model will also need detailed training on how to _compose larger content types from smaller linked items_.
If the project operates across several channels from the beginning, it is worth investing the time and other resources to define a reusable atomic content model upfront.
### Manage the complexity in planning and modeling sessions
Atomic content requires careful planning to define _reusable content types_ , _naming conventions_ , and _relationships_ between them. The model can become confusing and slow to implement without an explicit agreement between business stakeholders, content managers, and developers. They need to agree on how they’ll compose different content types, which content needs to be reusable or one-off, and especially the content model handover workflows. Editors need to test if the way they’ll compose the content from atomic pieces using the suggested approach will work for them.
Start by identifying high-value, obviously reusable content pieces, such as [content types](/modules/content-modeling-guide/what-is-a-content-type) described in [schema.org](https://schema.org). Then break down these large content types into smaller content types that still represent semantic units, for example, _benefits_ , _features_ , _author bios_ , and _CTAs_ , before breaking all content into _atomic pieces_.
Workshop your content model with stakeholders to identify reusable elements early and map clear ownership before building the project.
Share a _test environment_ with the stakeholders from the content teams during the initial implementation, where they can experiment with the suggested model. This will help teams gain experience with what working with reusable content looks like in Xperience and experience the benefits of reusing, not duplicating, content.
## Avoid making the model excessively granular
It is easy to overdo _atomic modeling_ , create too many micro content types, and multiply the complexity of the content model without meaningful benefits. Each project differs, and you need to find a balance between reusability and practical editorial workflows.
An easy way to test the complexity is to require your developers (and other solutions architects) to create and then adjust several content items to test the editing experience. Once they put themselves in the editor’s shoes, they’ll discover any friction in the suggested flow and identify where to optimize it.
### Manage linked dependencies during publishing
Atomic content creates dependencies between content items. If an editor updates a reusable _benefit_ , it impacts all products using that benefit. Xperience supports [cascade publishing](/documentation/business-users/content-hub/content-items#cascade-publishing), which allows publishing all unpublished linked (or associated) items with the content item editors work with. Implement _review and approval processes_ for shared content items, so updates to reusable elements are deliberate and vetted by the right stakeholders before they appear across channels. This protects consistency while supporting flexibility for updates.
While _cascade publishing_ helps ensure consistent content, editors might also accidentally publish unintended changes. This happens when teams don’t align on governance, and the project doesn’t have correctly set content management workflows and workspaces.
### Consider potential query and performance issues
Composing content dynamically from multiple linked items at several levels will require additional [query optimization](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query#content-query-parameterization) and [caching strategies](/documentation/developers-and-admins/development/caching) during delivery, especially on high-traffic applications with complex content relationships.
Your developers will need to ensure that they poll exactly the data they need to a specific level to avoid query performance hits. Work closely with developers to optimize queries and caching for your atomic content structures. Atomic models can lead to more complex queries, and planning performance strategies early ensures your site or app remains fast and scalable. If the content types share the same content properties, consider storing them in [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) to consolidate the content type’s complexity and improve querying.
### Define safeguards in content governance
Atomic content modeling works best when teams have clear _roles_ and governance responsibilities. Define [workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces) and permission configurations to avoid accidental content modifications.
Collaborate with the [stakeholders](/modules/content-modeling-guide/content-modeling-process#who-needs-to-be-involved) to define clear ownership and workflows for reusable content. Ensure each team knows which content they manage, who approves changes, and how updates flow across linked items. This reduces friction and prevents conflicts when editors reuse content across multiple channels.
### Address the editorial learning curve
Composing pages or products from multiple linked items can initially feel fragmented, especially if the team is used to working with monolithic, _page-based content types_. Editor teams, not just the stakeholders on the content modeling team, will need training sessions and a sandbox environment to learn how to build content from smaller, linked content items instead of editing almost everything in one place.
Make sure there’s enough time in the project timeline to _train editors and reviewers_ on working with their content items. Editors need to understand how to assemble pages and other content artifacts using reusable content pieces and what the new workflow will look like. Use [explanation texts](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components) or _tooltips_ to provide editor guidance, context, and instructions to clarify what data and format they should input into different content types and what the output will look like once the content is displayed. And supply _extensive documentation_ as part of the project deliverables.
[ Previous page ](/modules/content-modeling-guide/design-page-based-content-model)
20 of 28
[ Mark complete and continue ](/modules/content-modeling-guide/design-email-content)
![]()
[]()[]()
