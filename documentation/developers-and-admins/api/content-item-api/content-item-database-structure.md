---
source: https://docs.kentico.com/documentation/developers-and-admins/api/content-item-api/content-item-database-structure
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [API](/documentation/developers-and-admins/api)
  * [Content API](/documentation/developers-and-admins/api/content-item-api)
  * Content item database structure 


# Content item database structure
This page introduces the database structure of various system entities used to manage content in Xperience.
## Content items
Content items, managed via the **Content Hub** application and the `IContentItemManager` [management API](/api/), are composed of the following entities:
Database table |  Description  
---|---  
CMS_ContentItem |  Stores system information about the content item.
  * The item’s content type is stored by the **ContentTypeId** column.
  * The **ContentItemIsReusable** flag determines whether the item can be linked across multiple channels (i.e., is a reusable content item managed via the _Content Hub_ application) or is tied to a specific channel (i.e., a [page](#pages) or an [email](#emails)).

  
CMS_ContentItemCommonData |  Stores content item properties and data of Page Builder widgets (for page content items) and [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) fields. The table stores a separate record for each [language variant](/documentation/developers-and-admins/configuration/languages) of an item.  
CMS_ContentItemLanguageMetadata |  Contains data about the item displayed within the admin UI, such as the display name, creator, and last updated/modified date. The table stores a separate record for each language variant of an item.  
_ContentItemData_ tables |  Store [content type fields](/documentation/developers-and-admins/development/content-types). Created together with the corresponding content types. The tables use the following naming structure: **< ContentTypeNamespace>.<ContentTypeName>** For example, a _DancingGoat.Coffee_ content type has a table named _DancingGoat_Coffee_. The table’s columns correspond with the [fields](/documentation/developers-and-admins/customization/field-editor) defined for the content type.
  * Records in the data tables are bound to content items via the **CMS_ContentItemCommonData** table.
  * The table stores a separate record for each language variant of an item.

  
CMS_ContentItemReference |  Stores references between items, enabling [content composition](/guides/architecture/content-modeling/content-modeling-guide).  
When managing content items in code, the API abstracts work with all these entities behind a conventional CRUD manager (`IContentItemManager`).
Similarly, when consuming the data – to display on a website, for example – the content item query API merges all entities into a single record from which all data and meta information can be extracted.
The following diagram illustrates the relationship between entities composing a single content item. Only the most important columns are highlighted.
[![Content item database composition](/docsassets/documentation/content-item-database-structure/CiStructure.png)](/docsassets/documentation/content-item-database-structure/CiStructure.png)
## Pages
Pages, managed via website channel applications and the `IWebPageManager` [API](/api/), extend the [content item structure](#content-items) with additional entities that provide web-specific, contextual data and metadata.
Database table |  Description  
---|---  
CMS_Channel |  Unlike standalone content items, pages must be tied directly to website channels.  
CMS_WebsiteChannel |  Stores data about the [website channel](/documentation/developers-and-admins/configuration/website-channel-management) – main domain, primary language, default [cookie level](/documentation/developers-and-admins/data-protection/cookies), etc.  
CMS_WebPageItem |  Stores metadata about pages, such as the location and order in the page content tree (**TreePath** and **Order** columns).  
CMS_WebPageUrlPath |  Stores the relative URL path to pages. The table stores a separate record for each language variant of a page.  
CMS_WebPageAcl |  Stores information about content tree sections to which a specific set of [page permissions](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management) is applied.  
CMS_WebPageAclRole |  Stores information about which roles are present in which sets of page permissions.  
CMS_WebPageAclRolePermission |  Stores specific page permissions granted to roles.  
The following diagram illustrates the relationship between entities composing a single page. Only the most important columns are highlighted.
[![Page database entity composition](/docsassets/documentation/content-item-database-structure/PageStructure.png)](/docsassets/documentation/content-item-database-structure/PageStructure.png)
## Emails
Emails, managed via email channel applications, extend the [content item structure](#content-items) with additional entities that provide email-specific, contextual data and metadata.
Database table |  Description  
---|---  
CMS_Channel |  Unlike standalone content items, emails must be tied directly to email channels.  
EmailLibrary_EmailChannel |  Stores data about the [email channel](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) – sending domain, language, etc.  
EmailLibrary_EmailConfiguration |  Stores the configuration of individual emails (email name, assigned template, etc.).  
EmailLibrary_SendConfiguration |  Stores the sending configuration of individual regular emails (scheduled send date, etc.).  
The following diagram illustrates the relationship between entities composing a single email. Only the most important columns are highlighted.
[![Email content item database entity composition](/docsassets/documentation/content-item-database-structure/EmailStructure.png)](/docsassets/documentation/content-item-database-structure/EmailStructure.png)
## Headless items
Headless items, managed via headless channel applications, extend the [content item structure](#content-items) with additional entities that provide headless-specific, contextual data and metadata.
Database table |  Description  
---|---  
CMS_Channel |  Unlike standalone content items, headless items must be tied directly to headless channels.  
CMS_HeadlessChannel |  Stores data about the [headless channel](/documentation/developers-and-admins/configuration/headless-channel-management) – preview URL, primary language, etc.  
CMS_HeadlessItem |  Stores the configuration of individual headless items (headless item name, its headless channel ID, etc.).  
The following diagram illustrates the relationship between entities composing a single headless item. Only the most important columns are highlighted.
[![Headless content item database entity composition](/docsassets/documentation/content-item-database-structure/HeadlessStructure.png)](/docsassets/documentation/content-item-database-structure/HeadlessStructure.png)
![]()
[]()[]()
