# Create pages
  * [ Copy page link ](documentation/business-users/website-content/create-pages#) | [Get HelpService ID](documentation/business-users/website-content/create-pages#)
Core MVC 5


[✖](documentation/business-users/website-content/create-pages# "Close page link panel") [Copy to clipboard](documentation/business-users/website-content/create-pages#)
[Content of your websites](documentation/business-users/website-content) is stored in pages. You can create new pages from the ground up or [clone](documentation/business-users/website-content/create-pages#clone-a-page) existing ones.
## Create a new page
  1. Open a website channel application.
  2. In the content tree, select the page under which you want to place the new page.
  3. Open the context menu (**New page**.  
[![Create a new page](docsassets/documentation/create-pages/create_page_new_page.png)](https://docs.kentico.com/docsassets/documentation/create-pages/create_page_new_page.png)
  4. Enter page name.
  5. Select a content type from the list of available [content types](documentation/developers-and-admins/development/content-types). 
     * Depending on the configuration of your site, you may also need to select a [page template](documentation/business-users/website-content/page-templates) from the list of available page templates.
  6. Enter the content into the required fields.
  7. **Save** the page.


The new page is created as a _Draft_. This means you can [edit the page](documentation/business-users/website-content/edit-and-publish-pages) and it is not visible on the live site until you [publish it](documentation/business-users/website-content/edit-and-publish-pages#publish-pages).
## Clone a page
Cloning a page allows you to create a new page with the same web page data (same [Page Builder](documentation/developers-and-admins/development/builders/page-builder) configuration, same content and same [page template](documentation/business-users/website-content/page-templates)). Page’s [security settings](documentation/business-users/website-content/secure-pages), [page permissions](documentation/developers-and-admins/configuration/users/role-management/page-permission-management) and [URLs](documentation/business-users/website-content/manage-page-urls) are not cloned. You can either clone all language variants of a page or just the selected language variant.
To clone a page:
  1. Open a website channel application.
  2. In the content tree, select the page you want to clone.
  3. Open the context menu (**Clone**.  
[![Clone page](docsassets/documentation/create-pages/clone_page.png)](https://docs.kentico.com/docsassets/documentation/create-pages/clone_page.png)
  4. Enter the page name and [URL slug](documentation/business-users/website-content/manage-page-urls). The default name is _(Clone) - {original name}_ and the default URL slug is _(Clone)-{original-url-slug}_.
  5. If you want to clone only the current language variant of the page, unselect the **Clone all language variants** setting. Otherwise, all language variants are cloned by default.
  6. Select **Clone**.


You can edit the new page on the **Content** tab. In the content tree, the new page is located right under the original page on the same level in the hierarchy. The page is created as a _Draft_. This means you can [edit the page](documentation/business-users/website-content/edit-and-publish-pages) and it is not visible on the live site until you [publish it](documentation/business-users/website-content/edit-and-publish-pages#publish-pages).
If you cloned all language variants of the page, you can switch to the other languages using the selector in the top-left corner. Each language variant needs to be [published](documentation/business-users/website-content/edit-and-publish-pages#publish-pages) separately to be visible on the live site.