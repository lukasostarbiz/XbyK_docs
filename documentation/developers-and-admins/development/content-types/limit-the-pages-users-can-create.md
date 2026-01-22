---
source: https://docs.kentico.com/documentation/developers-and-admins/development/content-types/limit-the-pages-users-can-create
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Content types](/documentation/developers-and-admins/development/content-types)
  * Limit the pages users can create 


# Limit the pages users can create
Xperience allows you to limit the pages users can create, making it easier for editors to create new pages on your website. You can control which pages are created in certain sections of your [website channels](/documentation/developers-and-admins/configuration/website-channel-management) and more universally under certain [content types](/documentation/developers-and-admins/development/content-types). There are two complementary ways how you can achieve this:
  * [Allowed page content types](#define-allowed-page-content-types) – define which content types users can place under a given content type. This is a universal setting that doesn’t take into account the path that users place the content types under. This setting can be defined in the **Content types** application.
  * [Content type scopes](#define-content-type-scopes) – define which of the allowed page content types users can use when creating new pages under specified pages or folders. You can use scopes to distinguish certain sections of a given website channel and restrict the pages that users can create in them. This setting can be defined in the **Channel management** application.


[Allowed content types](/documentation/developers-and-admins/configuration/website-channel-management#add-allowed-content-types) need to be added for a given website channel beforehand, to allow configuration of a website channel’s root scope. Any other sections (pages, folders) use the allowed page content types configuration, which can be further limited using scopes. The final set of allowed page content types includes only those that are present in both scopes and the allowed page content types of a given page’s content type.
The configuration of content types and scopes is only applied to new pages and when moving pages in the content tree.
## Define allowed page content types
You can control which content types users can use to create pages. There are two ways of setting the allowed content types for a specific content type.
  * Allowed child content types – Allows you to control which child content types are allowed under a page of a specific content type.
  * Allowed parent content types – Allows you to control which parent content types are allowed for a page of a specific content type.


The configuration of allowed content types is independent of the tree path structure in a given [website channel](/documentation/developers-and-admins/configuration/website-channel-management).
### Add allowed child content types
  1. Open the **Content types** application.
  2. Select the content type you want to configure.
  3. Go to the **Allowed content types** tab.
  4. On the **Allowed children** tab, select **Add content types**.
  5. Select the content types you want to allow as children.
  6. **Save** your changes.


You have successfully added allowed child content types.
When you add an _allowed child content type_ , the system also automatically adds a visual representation of the same relationship from the other direction to the **Allowed parents** tab of the child content type that you are allowing.
### Remove allowed child content types
  1. Open the **Content types** application.
  2. Select the content type you want to configure.
  3. On the **Allowed children** tab, **Remove** (


You have successfully removed a content type from the allowed child content types.
## Define content type scopes
You can configure allowed content types for scopes to define which of the allowed child/parent content types can be used under specific pages or folders of a given website channel. This configuration adds an additional layer of control on top of the [allowed page content types](#define-allowed-page-content-types) settings.
### Create scopes for content types
  1. Open the **Channel management** application.
  2. Select your website channel.
  3. On the **Scopes for content types** tab, select **New scope**.
  4. Select existing pages for which to apply the scope.
  5. Select content types you want to allow in the scope. 
     * You can allow content types directly in your scope or [allow a content type in existing scopes](#allow-content-types-in-scopes).  
[![Create a scope](/docsassets/documentation/limit-the-pages-users-can-create/create_scope.png)](/docsassets/documentation/limit-the-pages-users-can-create/create_scope.png)
  6. Select or clear the **Apply for all subitems** checkbox. 
     * This setting applies the given scope to all direct and non-direct subitems.
  7. **Save** your changes.


You have successfully created a scope for your content types.
### Allow content types in scopes
Content types can be allowed in scopes in two ways, either directly in a given website channel’s **Scopes for content types** tab by allowing existing content types in a scope, or by allowing a content type in existing scopes. To allow a content type in scopes:
  1. Open the **Content types** application.
  2. Select the content type you want to allow.
  3. On the **Allowed in scopes** tab, select **Add scopes**
  4. Select the scopes you want to allow the content type in.
  5. **Save** your changes.


You have successfully allowed a content type in your scopes.
### Delete scopes for content types
  1. Open the **Channel management** application.
  2. Select your website channel.
  3. Go to the **Scopes for content types** tab.
  4. **Delete** (  
[![Create a scope](/docsassets/documentation/limit-the-pages-users-can-create/delete_scope.png)](/docsassets/documentation/limit-the-pages-users-can-create/delete_scope.png)


You have successfully deleted a scope from your website channel.
![]()
[]()[]()
