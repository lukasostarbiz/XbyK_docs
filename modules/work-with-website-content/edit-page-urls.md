---
source: https://docs.kentico.com/modules/work-with-website-content/edit-page-urls
scrape_date: 2026-01-26
---

Module: Work with website content
15 of 20 Pages
# Edit page URLs
You may not be able to edit URL settings of every page. This depends on the configuration of the page’s underlying content type. If you are not sure, consult with your site’s administrator.
When you create a new page, the slug is automatically generated from the page’s title. Past this point, any changes to the page title do not get reflected in the slug. You need to change it manually if required:
  1. Open a website channel application.
  2. In the content tree, select the page you want to edit.
  3. Make sure the page is in the _Draft_ status or another [custom workflow step](/documentation/developers-and-admins/configuration/workflows).
     * When a page is _Published_ or _Unpublished_ , you need to select **Edit page** or **Create new version** in the **Content** view mode. This transitions the page to the _Draft_ step and enables editing and publishing of the page.
  4. On the **URLs** tab, edit (**System URL**.
  5. Edit the **URL slug** in the dialog window and **Save** it.
     * **Note** : Changing URL slug of a page may also affect URLs of all of the page’s descendants.
     * Some characters may be forbidden from use in URLs (e.g., ‘#’, ‘/’, ‘$’, ‘&’), depending on the settings of your site. Administrators can adjust these settings in **Settings → URLs and SEO → URL format**.
  6. **Save** the change.
[![Save changes](/docsassets/documentation/manage-page-urls/save-urls.png)](/docsassets/documentation/manage-page-urls/save-urls.png)


You have now successfully changed the URL slug of the page. Note that the page is now a _Draft_. The page needs to be [published](/documentation/business-users/website-content/edit-and-publish-pages) in order to be accessible under the new system URL.
## Manage vanity URLs of pages
You may not be able to edit URL settings of every page. This depends on the configuration of the page’s underlying content type. If you are not sure, consult with your site’s administrator.
Vanity URLs are optional for pages and can be set at any time after a page is created. Once created, a vanity URL can be set as the [canonical URL](#select-the-canonical-url-of-pages) of the page. A page can have multiple vanity URLs. You can see all vanity URLs used across the system in the **URLs** application.
### Add vanity URLs to pages
  1. Open a website channel application.
  2. In the content tree, select the page you want to edit.
  3. Make sure the page is in the _Draft_ status or another [custom workflow step](/documentation/developers-and-admins/configuration/workflows).
     * When a page is _Published_ or _Unpublished_ , you need to select **Edit page** or **Create new version** in the **Content** view mode. This transitions the page to the _Draft_ step and enables editing and publishing of the page.
  4. Select **Add vanity URL** in the **Current URLs** section.
  5. Enter the **Vanity URL path** and confirm with **Add**.
     * Some characters may be forbidden from use in URLs (e.g., ‘#’, ‘/’, ‘$’, ‘&’), depending on the settings of your site. Administrators can adjust these settings in **Settings → URLs and SEO → URL format**.
  6. **Save** the change.
[![Save changes](/docsassets/documentation/manage-page-urls/save-urls.png)](/docsassets/documentation/manage-page-urls/save-urls.png)


You have now successfully added the new vanity URL of the page. Note that the page is now a _Draft_. The page needs to be [published](/documentation/business-users/website-content/edit-and-publish-pages) in order to be accessible under the new URL.
### Edit vanity URLs of pages
To edit a vanity URL of a page:
  1. Navigate to the page whose vanity URL you want to edit. You can do so by:
    1. Opening the corresponding website channel application and selecting the page in the content tree.
    2. Going to the **Vanity URLs** tab in the **URLs** application and opening (
  2. Make sure the page is in the _Draft_ status or another [custom workflow step](/documentation/developers-and-admins/configuration/workflows).
     * When a page is _Published_ or _Unpublished_ , you need to select **Edit page** or **Create new version** in the **Content** view mode. This transitions the page to the _Draft_ step and enables editing and publishing of the page.
  3. On the **URLs** tab, edit (**Vanity URL** you want to change.
  4. Edit the **Vanity URL path** in the dialog window and **Save** it.
     * Some characters may be forbidden from use in URLs (e.g., ‘#’, ‘/’, ‘$’, ‘&’), depending on the settings of your site. Administrators can adjust these settings in **Settings → URLs and SEO → URL format**.
  5. **Save** the change.
[![Save changes](/docsassets/documentation/manage-page-urls/save-urls.png)](/docsassets/documentation/manage-page-urls/save-urls.png)


You have now successfully changed the vanity URL of the page. Note that the page is now a _Draft_. The page needs to be [published](/documentation/business-users/website-content/edit-and-publish-pages) in order to be accessible under the new URL.
### Delete vanity URLs of pages
  1. Open a website channel application.
  2. In the content tree, select the page that you want to edit.
  3. Make sure the page is in the _Draft_ status or another [custom workflow step](/documentation/developers-and-admins/configuration/workflows).
     * When a page is _Published_ or _Unpublished_ , you need to select **Edit page** or **Create new version** in the **Content** view mode. This transitions the page to the _Draft_ step and enables editing and publishing of the page.
  4. On the **URLs** tab, delete (**Vanity URL** and confirm the deletion.
  5. **Save** the change.
[![Save changes](/docsassets/documentation/manage-page-urls/save-urls.png)](/docsassets/documentation/manage-page-urls/save-urls.png)


You have now successfully deleted the vanity URL of the page. Note that the page is now a _Draft_. The page needs to be [published](/documentation/business-users/website-content/edit-and-publish-pages) to make it inaccessible under the deleted vanity URL.
## Select the canonical URL of pages
You may not be able to edit URL settings of every page. This depends on the configuration of the page’s underlying content type. If you are not sure, consult with your site’s administrator.
Canonical URL is the preferred URL of a page, i.e., the URL that is displayed to the user in the browser URL bar and is intended to be indexed by search engines.
  * All URLs representing a page redirect to the canonical URL of the page via a “301 Moved Permanently” redirect.
  * All system features (e.g., [URL selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#url-selector), [rich text editor](/documentation/business-users/rich-text-editor#format-text), [page URL retriever](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls), [email template macros](/documentation/developers-and-admins/digital-marketing-setup/email-templates#content-type-field-macros)) use and return the canonical URLs of pages.
  * By default, the system URL is the canonical URL for each page.


To set a URL as the canonical URL:
  1. Open a website channel application.
  2. In the content tree, select the page that you want to edit.
  3. Make sure the page is in the _Draft_ status or another [custom workflow step](/documentation/developers-and-admins/configuration/workflows).
     * When a page is _Published_ or _Unpublished_ , you need to select **Edit page** or **Create new version** in the **Content** view mode. This transitions the page to the _Draft_ step and enables editing and publishing of the page.
  4. On the **URLs** tab, select the **Canonical** option for the URL you want to make canonical. You can select from the page’s [vanity](#manage-vanity-urls-of-pages) URLs and its system URL.
  5. **Save** the change.
[![Save changes](/docsassets/documentation/manage-page-urls/save-urls.png)](/docsassets/documentation/manage-page-urls/save-urls.png)


The selected URL is now configured as the canonical URL of the page. Note that the page is now a _Draft_. The page needs to be [published](/documentation/business-users/website-content/edit-and-publish-pages) to apply the change.
**Canonical URL of the home page**
The canonical URL of the [home page](/documentation/developers-and-admins/development/routing/content-tree-based-routing/enable-content-tree-based-routing#optional-routing-configuration) of your website channel is, regardless of what URL is selected as canonical by the user, always the root URL of your website (i.e., _https://example.com/_) for the primary [language](/documentation/developers-and-admins/configuration/languages) and _https://example.com/ <language_name>/_ for other languages.
## Manage redirects from unpublished pages
You can edit active and scheduled redirects from [unpublished pages](/documentation/business-users/website-content/edit-and-publish-pages#unpublish-pages) on the **URLs** tab of the corresponding page. Active redirects can also be edited in the **URLs** application on the **Former URLs** tab. The advantage of the _URLs_ application is that you can see and edit all redirects in one place. If no redirect was created when the page was unpublished, you can add it only on the _URLs_ tab of the corresponding unpublished page.
### Add redirects from unpublished pages
To add a redirect from an unpublished page with no redirect:
  1. Open a website channel application.
  2. In the content tree, select the unpublished page you want to edit.
  3. On the **URLs** tab, select **Add redirect** in the **Redirect** section.
  4. In the sidebar, use **Select existing page** to choose the target page of the redirect.
  5. Confirm the target with the **Select** button.
  6. **Save** the redirect.


You have now successfully added the redirect. When visitors try to reach the unpublished page, they are redirected to the page you selected.
### Edit redirects from unpublished pages
To edit an existing redirect on the **URLs** tab:
  1. Open a website channel application.
  2. In the content tree, select the unpublished page you want to edit.
  3. On the **URLs** tab, select **Edit** in the **Redirect** section.
  4. In the sidebar, use **Select existing page** to change the target of the redirect to another page.
  5. Confirm the target with the **Select** button.
  6. **Save** the changes.


Alternatively, you can edit existing redirects in the **URLs** application:
  1. Open the **URLs** application.
  2. Go to the **Former URLs** tab.
  3. Select the former URL representing the redirect you want to edit.
  4. Use **Select existing page** to select the new target of the redirect.
  5. Confirm the target with the **Select** button.
  6. **Save** the changes.


You have now successfully edited the redirect. When visitors try to reach the unpublished page, they are redirected to the page you selected.
### Delete redirects from unpublished pages
If you want to delete a redirect from an unpublished page, you need to publish the page and unpublish it again without selecting a page to redirect to.
## Manage redirects from deleted pages
As deleted pages are no longer available in the system, redirects from deleted pages can be edited and deleted only in the **URLs** application. If no redirect was created when the page was deleted, a redirect from such a page can be added only via [code](/documentation/developers-and-admins/development/routing/custom-redirects).
### Edit redirects from deleted pages
To edit a redirect from a deleted page:
  1. Open the **URLs** application.
  2. Go to the **Former URLs** tab.
  3. Select the former URL you want to edit.
  4. Use **Select existing page** to select the new target of the redirect.
  5. Confirm the changes with the **Select** button.
  6. **Save** the changes.


You have now successfully edited the redirect. When visitors try to reach the unpublished page, they are redirected to the page you selected.
### Delete redirects from deleted pages
Once a redirect from a deleted page has been deleted, it cannot be added again in the UI. Such redirects can be added only via [code](/documentation/developers-and-admins/development/routing/custom-redirects), for example, by developers.
To remove a redirect from a deleted page:
  1. Open the **URLs** application.
  2. Go to the **Former URLs** tab.
  3. Remove (


The redirect is now permanently removed.
[ Previous page ](/modules/work-with-website-content/manage-page-urls)
15 of 20
[ Mark complete and continue ](/modules/work-with-website-content/prevent-url-collision)
![]()
[]()[]()
