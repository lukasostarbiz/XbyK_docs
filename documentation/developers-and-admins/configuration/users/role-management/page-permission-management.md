---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/users/role-management/page-permission-management
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * [Users](/documentation/developers-and-admins/configuration/users)
  * [Role management](/documentation/developers-and-admins/configuration/users/role-management)
  * Page permission management 


# Page permission management
This page describes permissions that are applied only within the Xperience administration. For restricting access to pages on the live site, see [Secure pages](/documentation/business-users/website-content/secure-pages).
As your Xperience websites grow, your content editors may start to form multiple teams, each responsible for a different part of a website. To prevent editors from unintentionally affecting the work of others in unexpected ways (e.g., by accidentally moving a page in the content tree), letting users access and edit only the content they are responsible for may become necessary. Moreover, your website may contain pages with sensitive content that not all editors should have access to.
Page permissions allow you to grant permissions to roles for website channels, specific content tree sections, and individual pages and folders in the form of an [access-control list](https://en.wikipedia.org/wiki/Access-control_list) (ACL). The permissions are always propagated from a parent page, with the [website’s root](#allow-users-to-work-with-website-channel-content) representing the prime parent, to all its children until a child page [breaks](#edit-page-permissions) this inheritance.
You can grant the following types of page permissions:
Permission |  Description  
---|---  
Display |  Allows users to see pages and folders in the content tree and see their names in the [page selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#page-selector) and other [UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).  
Read |  Allows users to view the content of pages and link to them through the page selector and other UI form components. The permission is required on the _source location_ when [moving pages](#page-permissions-for-moving-pages). Users with the permission can also create [shareable preview URLs](/documentation/developers-and-admins/configuration/shareable-preview-urls) for the given page.  
Create |  Allows users to create new pages and folders, including their language variants, in the content tree under the current item. The permission is required on the _target location_ when [moving pages](#page-permissions-for-moving-pages).  
Update |  Allows users to modify pages, folders and their properties other than page permissions. The permission is required on the _source location_ when [moving pages](#page-permissions-for-moving-pages).  
Delete |  Allows users to delete pages and folders.  
Synchronize |  Allows users to [synchronize](/documentation/business-users/content-sync) pages from one instance of Xperience to another.  
## Allow users to work with website channel content
To allow users to access and work with pages and folders in a website channel:
  1. Allow users to access the website channel application by [granting](/documentation/developers-and-admins/configuration/users/role-management#assign-permissions-to-roles) the _Access channel_ permission in the **Role management** application to the desired roles.
  2. Open the website channel application.
  3. On the website’s root, select the **Properties - > Channel permissions** tab.
  4. **Add roles** that should have access to the website channel. 
The [Administrator role](/documentation/developers-and-admins/configuration/users/role-management) and roles with the _Manage permissions_ permission always have all page permissions for all pages in a channel. 
  5. In the matrix, select the desired permissions for each role.
  6. **Save permissions**.


The users in the selected roles can now access and work with all pages in the channel according to the specified permissions.
[![Page permissions for channel](/docsassets/documentation/page-permission-management/channel_permissions.png)](/docsassets/documentation/page-permission-management/channel_permissions.png)
## Edit page permissions
Based on your project’s requirements and [setup of page permissions](#allow-users-to-work-with-website-channel-content), you may need to change the permissions of certain roles for specific pages or content tree subsections. You can define new page permissions by _breaking the inheritance_ of permissions on a particular page. The newly defined permissions are then propagated to all the page’s children in the content tree.
While editing page permissions, you can remove previously added roles (or add new ones) only for the content tree subsection. Just note that when adding new roles, the users in the role **need to have** the _Display_ permission granted to the website’s root via at least one of their roles.
To edit page permissions for a subsection of the content tree:
  1. In the website channel application, select the page on which you want to break the inheritance of page permissions.
  2. Select the **Properties - > Permissions** tab.
  3. **Break inheritance**.
  4. Add or remove roles and select their permissions.
  5. **Save permissions**.


The new page permissions are now set for the current page and all its children until a child page breaks this new inheritance.
[![Edit page permissions](/docsassets/documentation/page-permission-management/break_inheritance.png)](/docsassets/documentation/page-permission-management/break_inheritance.png)
After breaking the inheritance, the page and its children won’t be affected by any changes to the permissions of any pages higher up in the content tree hierarchy. Therefore, when making updates to page permissions that should affect the entire channel, you need to update the permissions on the website’s root and all the pages that break the inheritance. To keep the management of page permissions as easy as possible, we recommend breaking the inheritance only where it’s necessary and [restoring the inheritance](#restore-page-permission-inheritance) whenever possible.
**Role hierarchy**
You can create a hierarchy of roles whose page permissions build upon each other.
For example, you may have an _Editor_ role in your project that has privileges to manage most of the website’s content. However, not all editors should have permission to delete and update existing product pages. To navigate this, you can create and assign a _Products editor_ role on top of the general _Editor_ role to picked users and grant the new role only the required permissions.
[![Add special roles for pages permissions](/docsassets/documentation/page-permission-management/special_page_permissions_role.png)](/docsassets/documentation/page-permission-management/special_page_permissions_role.png)
## Restore page permission inheritance
The requirements for page permissions used in your website channels may change over time. When a page that [breaks the inheritance](#edit-page-permissions) no longer needs to do so, we recommend that you make the page inherit the permissions from its parent again by _restoring the inheritance_. This will help you keep the number of pages on which you need to manage page permissions to the necessary minimum.
To restore the inheritance of page permissions for a page and its children:
  1. In the website channel application, select the page whose inheritance you want to restore.
  2. Select the **Properties - > Permissions** tab.
  3. **Restore inheritance**.


The page and its children now inherit the permissions of the page’s parent until a child page breaks the restored inheritance.
## Page permissions for moving pages
The system doesn’t provide a dedicated page permission for moving pages. Instead, users need to have the following permissions granted to move a page:
  * _Read_ and _Update_ for the page they want to move (source location). The users also need to have these permissions for all the children of the page.
  * _Create_ page permission on the page’s new parent (target location).


Under certain circumstances, moving a page may affect the permissions of the page and its children:
  * If the page had permissions different from those of its parent (it was breaking the inheritance), the page and its children preserve their permissions.
  * If the page inherited permissions from its parent, the page and all its children now inherit permissions from the new parent. If the _new parent_ has permissions different from those of the _old parent_ , this also changes which roles have access and other permissions for the page and its children.


![]()
[]()[]()
