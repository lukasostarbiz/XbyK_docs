# Manage page URLs
  * [ Copy page link ](documentation/business-users/website-content/manage-page-urls#) | [Get HelpService ID](documentation/business-users/website-content/manage-page-urls#)
Core MVC 5


[✖](documentation/business-users/website-content/manage-page-urls# "Close page link panel") [Copy to clipboard](documentation/business-users/website-content/manage-page-urls#)
Xperience allows you to modify URLs of web pages on the **URLs** tab when editing a page in a website channel application. The system can also track changes of URLs and store old URLs for redirection purposes. These [former URLs](documentation/business-users/website-content/manage-page-urls#former-urls) can be managed in the **URLs** application.
The following types of URLs are available for pages:
  * [System URL](documentation/business-users/website-content/manage-page-urls#edit-url-slugs-of-pages) – Default URL [generated from the page’s position in the content tree](documentation/developers-and-admins/development/routing/content-tree-based-routing) using the page’s URL slug and URL slugs of the page’s ancestors. The ‘slug’ is the part that comes at the very end of a URL, and refers to a specific page or post. For example, for the URL _example.com/Articles/News/Tea-Blends/_ , the URL slug is _’Tea-Blends_ ’. For more information about how page URLs are generated, see [Page URL generation](documentation/developers-and-admins/development/routing/content-tree-based-routing#page-url-generation).
  * [Vanity URL](documentation/business-users/website-content/manage-page-urls#edit-vanity-urls-of-pages) – Alternative URL that can be configured and customized by marketers. One page can have multiple vanity URLs. All configured vanity URLs across all pages are displayed in the **URLs** application. Vanity URLs can serve multiple purposes: 
    * Marketing and branding – You can create URLs that promote branding of your products and include recognizable words to improve your marketing reach.
    * Sharing – You can create a URL that is short and can be copied and shared easily, for example, on social media.
    * SEO – You can improve the SEO of the page by including SEO keywords in the URL of the page.
  * [Canonical URL](documentation/business-users/website-content/manage-page-urls#select-the-canonical-url-of-pages) – Preferred URL of a page, i.e., the URL that is displayed to the user in the browser URL bar and is intended to be indexed by search engines. All URLs representing a page redirect to the canonical URL of the page via a “301 Moved Permanently” redirect. By default, the system URL is the canonical URL for each page. You can decide to [set a vanity URL as the canonical URL](documentation/business-users/website-content/manage-page-urls#select-the-canonical-url-of-pages) of a page.


You can also create a temporary [shareable preview URL](documentation/business-users/website-content#shareable-preview-urls) and send it to reviewers who can’t access the administration. This URL is not intended for regular live site visitors.
[![URLs tab overview](docsassets/documentation/manage-page-urls/urls-tab-overview.png)](https://docs.kentico.com/docsassets/documentation/manage-page-urls/urls-tab-overview.png)
## Edit URL slugs of pages
You may not be able to edit URL settings of every page. This depends on the configuration of the page’s underlying content type. If you are not sure, consult with your site’s administrator.
When you create a new page, the slug is automatically generated from the page’s title. Past this point, any changes to the page title do not get reflected in the slug. You need to change it manually if required:
  1. Open a website channel application.
  2. In the content tree, select the page you want to edit.
  3. Make sure the page is in the _Draft_ status or another [custom workflow step](documentation/developers-and-admins/configuration/workflows).
     * When a page is _Published_ or _Unpublished_ , you need to select **Edit page** or **Create new version** in the **Content** view mode. This transitions the page to the _Draft_ step and enables editing and publishing of the page.
  4. On the **URLs** tab, edit (**System URL**.
  5. Edit the **URL slug** in the dialog window and **Save** it.
     * **Note** : Changing URL slug of a page may also affect URLs of all of the page’s descendants.
     * Some characters may be forbidden from use in URLs (e.g., ‘#’, ‘/’, ‘$’, ‘&’), depending on the settings of your site. Administrators can adjust these settings in **Settings → URLs and SEO → URL format**.
  6. **Save** the change.
[![Save changes](docsassets/documentation/manage-page-urls/save-urls.png)](https://docs.kentico.com/docsassets/documentation/manage-page-urls/save-urls.png)


You have now successfully changed the URL slug of the page. Note that the page is now a _Draft_. The page needs to be [published](documentation/business-users/website-content/edit-and-publish-pages) in order to be accessible under the new system URL.
## Manage vanity URLs of pages
You may not be able to edit URL settings of every page. This depends on the configuration of the page’s underlying content type. If you are not sure, consult with your site’s administrator.
Vanity URLs are optional for pages and can be set at any time after a page is created. Once created, a vanity URL can be set as the [canonical URL](documentation/business-users/website-content/manage-page-urls#select-the-canonical-url-of-pages) of the page. A page can have multiple vanity URLs. You can see all vanity URLs used across the system in the **URLs** application.
### Add vanity URLs to pages
  1. Open a website channel application.
  2. In the content tree, select the page you want to edit.
  3. Make sure the page is in the _Draft_ status or another [custom workflow step](documentation/developers-and-admins/configuration/workflows).
     * When a page is _Published_ or _Unpublished_ , you need to select **Edit page** or **Create new version** in the **Content** view mode. This transitions the page to the _Draft_ step and enables editing and publishing of the page.
  4. Select **Add vanity URL** in the **Current URLs** section.
  5. Enter the **Vanity URL path** and confirm with **Add**.
     * Some characters may be forbidden from use in URLs (e.g., ‘#’, ‘/’, ‘$’, ‘&’), depending on the settings of your site. Administrators can adjust these settings in **Settings → URLs and SEO → URL format**.
  6. **Save** the change.
[![Save changes](docsassets/documentation/manage-page-urls/save-urls.png)](https://docs.kentico.com/docsassets/documentation/manage-page-urls/save-urls.png)


You have now successfully added the new vanity URL of the page. Note that the page is now a _Draft_. The page needs to be [published](documentation/business-users/website-content/edit-and-publish-pages) in order to be accessible under the new URL.
### Edit vanity URLs of pages
To edit a vanity URL of a page:
  1. Navigate to the page whose vanity URL you want to edit. You can do so by:
    1. Opening the corresponding website channel application and selecting the page in the content tree.
    2. Going to the **Vanity URLs** tab in the **URLs** application and opening (
  2. Make sure the page is in the _Draft_ status or another [custom workflow step](documentation/developers-and-admins/configuration/workflows).
     * When a page is _Published_ or _Unpublished_ , you need to select **Edit page** or **Create new version** in the **Content** view mode. This transitions the page to the _Draft_ step and enables editing and publishing of the page.
  3. On the **URLs** tab, edit (**Vanity URL** you want to change.
  4. Edit the **Vanity URL path** in the dialog window and **Save** it.
     * Some characters may be forbidden from use in URLs (e.g., ‘#’, ‘/’, ‘$’, ‘&’), depending on the settings of your site. Administrators can adjust these settings in **Settings → URLs and SEO → URL format**.
  5. **Save** the change.
[![Save changes](docsassets/documentation/manage-page-urls/save-urls.png)](https://docs.kentico.com/docsassets/documentation/manage-page-urls/save-urls.png)


You have now successfully changed the vanity URL of the page. Note that the page is now a _Draft_. The page needs to be [published](documentation/business-users/website-content/edit-and-publish-pages) in order to be accessible under the new URL.
### Delete vanity URLs of pages
  1. Open a website channel application.
  2. In the content tree, select the page that you want to edit.
  3. Make sure the page is in the _Draft_ status or another [custom workflow step](documentation/developers-and-admins/configuration/workflows).
     * When a page is _Published_ or _Unpublished_ , you need to select **Edit page** or **Create new version** in the **Content** view mode. This transitions the page to the _Draft_ step and enables editing and publishing of the page.
  4. On the **URLs** tab, delete (**Vanity URL** and confirm the deletion.
  5. **Save** the change.
[![Save changes](docsassets/documentation/manage-page-urls/save-urls.png)](https://docs.kentico.com/docsassets/documentation/manage-page-urls/save-urls.png)


You have now successfully deleted the vanity URL of the page. Note that the page is now a _Draft_. The page needs to be [published](documentation/business-users/website-content/edit-and-publish-pages) to make it inaccessible under the deleted vanity URL.
## Select the canonical URL of pages
You may not be able to edit URL settings of every page. This depends on the configuration of the page’s underlying content type. If you are not sure, consult with your site’s administrator.
Canonical URL is the preferred URL of a page, i.e., the URL that is displayed to the user in the browser URL bar and is intended to be indexed by search engines.
  * All URLs representing a page redirect to the canonical URL of the page via a “301 Moved Permanently” redirect.
  * All system features (e.g., [URL selector](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#url-selector), [rich text editor](documentation/business-users/rich-text-editor#format-text), [page URL retriever](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls), [email template macros](documentation/developers-and-admins/digital-marketing-setup/email-templates#content-type-field-macros)) use and return the canonical URLs of pages.
  * By default, the system URL is the canonical URL for each page.


To set a URL as the canonical URL:
  1. Open a website channel application.
  2. In the content tree, select the page that you want to edit.
  3. Make sure the page is in the _Draft_ status or another [custom workflow step](documentation/developers-and-admins/configuration/workflows).
     * When a page is _Published_ or _Unpublished_ , you need to select **Edit page** or **Create new version** in the **Content** view mode. This transitions the page to the _Draft_ step and enables editing and publishing of the page.
  4. On the **URLs** tab, select the **Canonical** option for the URL you want to make canonical. You can select from the page’s [vanity](documentation/business-users/website-content/manage-page-urls#manage-vanity-urls-of-pages) URLs and its system URL.
  5. **Save** the change.
[![Save changes](docsassets/documentation/manage-page-urls/save-urls.png)](https://docs.kentico.com/docsassets/documentation/manage-page-urls/save-urls.png)


The selected URL is now configured as the canonical URL of the page. Note that the page is now a _Draft_. The page needs to be [published](documentation/business-users/website-content/edit-and-publish-pages) to apply the change.
**Canonical URL of the home page**
The canonical URL of the [home page](documentation/developers-and-admins/development/routing/content-tree-based-routing/enable-content-tree-based-routing#optional-routing-configuration) of your website channel is, regardless of what URL is selected as canonical by the user, always the root URL of your website (i.e., _https://example.com/_) for the primary [language](documentation/developers-and-admins/configuration/languages) and _https://example.com/ <language_name>/_ for other languages.
## Manage redirects from unpublished pages
You can edit active and scheduled redirects from [unpublished pages](documentation/business-users/website-content/edit-and-publish-pages#unpublish-pages) on the **URLs** tab of the corresponding page. Active redirects can also be edited in the **URLs** application on the **Former URLs** tab. The advantage of the _URLs_ application is that you can see and edit all redirects in one place. If no redirect was created when the page was unpublished, you can add it only on the _URLs_ tab of the corresponding unpublished page.
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
As deleted pages are no longer available in the system, redirects from deleted pages can be edited and deleted only in the **URLs** application. If no redirect was created when the page was deleted, a redirect from such a page can be added only via [code](documentation/developers-and-admins/development/routing/custom-redirects).
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
Once a redirect from a deleted page has been deleted, it cannot be added again in the UI. Such redirects can be added only via [code](documentation/developers-and-admins/development/routing/custom-redirects), for example, by developers.
To remove a redirect from a deleted page:
  1. Open the **URLs** application.
  2. Go to the **Former URLs** tab.
  3. Remove (


The redirect is now permanently removed.
## URL collision prevention
A site cannot have two pages with identical URLs. Depending on the situation, the system handles collisions in two ways:
  1. By appending the page’s GUID at the end of the URL slug of the page. For example: _https://example.com/Articles/Tea-Blends-**ae64db90387143b1a4562e06b67d1970**_. This occurs when: 
     * You move a page and the destination already contains a page with a matching URL slug.  
[![URL collisions when moving pages](docsassets/documentation/manage-page-urls/URL-collision-suffix.png)](https://docs.kentico.com/docsassets/documentation/manage-page-urls/URL-collision-suffix.png)
  2. By preventing the URL from changing and displaying an error message. This occurs when: 
     * You attempt to change the URL slug or a vanity URL of a page and the resulting URL matches one already in the system.  
[![URL collision when changing the slug](docsassets/documentation/manage-page-urls/URL-collision-message.png)](https://docs.kentico.com/docsassets/documentation/manage-page-urls/URL-collision-message.png)


## Former URLs
The system can automatically keep track of page URL changes by storing old URLs and handling redirects to the current URLs. You can see and manage these redirects in the **URLs** application on the **Former URLs** tab. Redirects created by unpublishing or deleting pages are stored and handled by default. You can additionally configure the system to create former URLs when a URL is edited and to redirect to the current URLs.
When a redirect target of a former URL is changed, all redirects that target the corresponding page are also automatically adjusted.
### Automatically store edited URLs as former URLs
To configure the system to automatically store edited URLs as former URLs:
  1. Open the **Channel management** application.
  2. Select the website channel for which you want to configure Former URLs to store and handle edited URLs.
  3. Open **Channel settings**.
  4. Select the **Store edited URLs as former URLs** setting.
  5. **Save** the changes.


Former URLs are now created when URLs are edited in the given website channel. Every time a former URL is requested, the system returns a “301 Moved Permanently” response and redirects to the new URL of the page.
If the setting is disabled later, former URLs created up to that point will continue redirecting visitors to current URLs unless the former URLs are [removed](documentation/business-users/website-content/manage-page-urls#remove-former-urls-created-by-editing-urls).
To access a list of all stored old URLs for the website, together with the current URL paths to which they are redirected, open the **Former URLs** tab in the **URLs** application.
[![Former URLs tab](docsassets/documentation/manage-page-urls/former-URLs.png)](https://docs.kentico.com/docsassets/documentation/manage-page-urls/former-URLs.png)
### Former URLs scenarios
Former URLs are created when you unpublish or delete a page and also create a redirect from the page. Additionally, you can configure the system to create former URLs when modifying page URLs.
Former URLs are always created when: |  If [configured](documentation/business-users/website-content/manage-page-urls#automatically-store-edited-urls-as-former-urls) to store edited URLs, former URLs are created when:  
---|---  
  * Unpublishing a page and creating a redirect to another page. 
    * If a page is scheduled to be unpublished, the former URL is created when the unpublishing occurs.
  * Deleting a page and creating a redirect to another page.

| 
  * Changing a page’s URL by: 
    * Editing the URL slug for the given page or one of its parent pages. 
      * The former URL is created only after you publish the page with the new URL.
    * Moving the page across the content tree. 
      * In this case the former URL is created immediately.
    * Editing or deleting a page’s vanity URL. 
      * The former URL is created only after you publish the page with the new URL.

  
Former URLs are **NOT** created when:
  * Deleting a page without creating a redirect. 
    * [Deleting a page](documentation/business-users/website-content/delete-pages) without creating a redirect **permanently destroys** **and removes** all former URLs pointing to that page.
  * Unpublishing a page and choosing to serve content in the fallback language.
  * Unpublishing a page without creating a redirect.
  * Administrators perform certain global changes, for example changing the domain of a website channel.
  * Changing a [language](documentation/developers-and-admins/configuration/languages) code name.
  * Changing the _Primary language_ setting for a website channel.


Changing the language code name or the primary language setting breaks all former URLs for the corresponding language variants of pages.
### Remove former URLs created by editing URLs
This action is available only for former URLs [created](documentation/business-users/website-content/manage-page-urls#former-urls-scenarios) by editing a page’s URL or by deleting a page. Their remove button is active (
To remove a former URL:
  1. Open the **URLs** application.
  2. Go to the **Former URLs** tab.
  3. Remove (


The former URL is now permanently removed.