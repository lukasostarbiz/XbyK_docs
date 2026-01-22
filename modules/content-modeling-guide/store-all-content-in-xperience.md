---
source: https://docs.kentico.com/modules/content-modeling-guide/store-all-content-in-xperience
scrape_date: 2026-01-22
---

Module: Content modeling guide
7 of 28 Pages
# Store content
This material explains the options for creating, storing, categorizing, and managing reusable content available in Xperience by Kentico.
Up to this point, we’ve focused on general principles of content modeling that apply to many platforms. Now, we’ll look specifically at how Xperience stores content so you can make informed decisions for your project.
Before building your Xperience project, you should evaluate how the system stores content. The business use cases you identified during your content modeling sessions will help you choose the most suitable approach for storing and organizing the data.
Xperience delivers content through channels and doesn’t allow for tapping directly into reusable content storage and retrieving it. You need to consider your distribution strategy before you decide where and how to store your project’s content.
## Digital content and marketing channels
Marketing channels refer to platforms, tools, and strategies businesses use to communicate with their end consumers, suppliers, partners, and other business entities. In Xperience, a channel represents the platform where you deliver your content to customers, such as your main website, microsites, email channel, or a headless channel.
Marketers often copy content separately to each channel, which creates extra work and potentially leads to content and message inconsistency. With Xperience, you can store your marketing content in one place and distribute it to your digital channels from the same interface. This helps editor teams keep the campaigns and other marketing communications consistent and efficient.
The current version of Xperience allows you to distribute content across websites, email, and headless channels. When preparing the content model, you need to respect each channel’s content requirements. As you continue through the guide, you’ll learn more about these differences and how to model content for each channel.
Find more details about [digital marketing channels](/guides/digital-marketing/work-with-channels/digital-marketing-channels) in a separate guide.
## Keep all digital content in Xperience
From a high-level perspective, you can look at creating and managing content in Xperience in two different ways:
Content is created |  Content is managed |  Description  
---|---|---  
**Inside** Xperience |  **Inside** Xperience |  The Xperience database serves as the single source of truth for all content (e.g., various marketing materials, company-specific information, or product-related documents). Before editors start producing content, developers must prepare relevant data structures (_content types_) in the administration interface.  
**Inside or outside** Xperience |  **Inside or outside** Xperience |  Depending on the project requirements, customers often combine storage options. They keep part of their content inside Xperience and part outside. The content they manage outside of Xperience usually comes from business-specific external systems, e.g., various external systems, such as global statistics databases, [ERP](https://en.wikipedia.org/wiki/Enterprise_resource_planning) or [PIM](https://en.wikipedia.org/wiki/Product_information_management) solutions for managing product catalogs. They synchronize this external content to Xperience using custom-built functionality that polls the source’s API and brings the data to Xperience. We’ve commonly seen many variants of these options across different Xperience projects.
  * The system uses a custom process that creates corresponding content objects in Xperience. At the same time, Kentico editors can manage (_read_ , _update_ , and _delete_) this data in Xperience and synchronize their changes with the origin.
  * If editors need to display some content in their applications, they reference the content stored in the third-party solution. For example, a reference to statistics objects (a codename) or products (SKUs) makes this data available for display across different Xperience-driven applications. Developers then ensure Xperience styles updates the data correctly.
  * Third-party content is automatically displayed in predefined application areas, such as in specific sections of websites. Third-party data is part of the website implementation, and editors cannot opt out of displaying this content.

Note that the last two options introduce multiple content management systems within one setup, which can cause complications.  
**Recommendations - Creating and managing content in Xperience**
  * **Storing and managing content directly in Xperience is recommended** for most projects.
    * It ensures a single source of truth for content distribution across different channels. Moreover, editors know where the content for which they are responsible resides.
    * It prevents synchronization errors with third-party integrations and ensures that editors always have the latest version of the content available in one place.
    * We recommend storing content in Xperience if editors want to leverage digital marketing features, such as [content personalization](/documentation/developers-and-admins/digital-marketing-setup/content-personalization) or segmenting your audience into [contact groups](/documentation/business-users/digital-marketing/contact-groups) for various marketing activities.
  * If you **need to display content from a third-party solution** in an Xperience project:
    * We recommend **building a custom bidirectional content synchronization tool** that automatically updates the data across different systems.
    * Implementation partners should **provide sufficient project documentation** to help editors and marketers locate and efficiently manage their content.
    * Your editors should **manage their content in the production environment** using the default Xperience publishing workflow.


[ Previous page ](/modules/content-modeling-guide/model-taxonomies)
7 of 28
[ Mark complete and continue ](/modules/content-modeling-guide/understand-storing-data-in-content-type)
![]()
[]()[]()
