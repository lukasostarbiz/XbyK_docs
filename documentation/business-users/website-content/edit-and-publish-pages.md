# Edit and publish pages
  * [ Copy page link ](documentation/business-users/website-content/edit-and-publish-pages#) | [Get HelpService ID](documentation/business-users/website-content/edit-and-publish-pages#)
Core MVC 5


[✖](documentation/business-users/website-content/edit-and-publish-pages# "Close page link panel") [Copy to clipboard](documentation/business-users/website-content/edit-and-publish-pages#)
**Key points**
You can:
  * edit structured content of pages in the **Content** view mode.
  * edit pages built with the Page Builder in the **Page Builder** view mode.
  * edit, rename and publish pages only in the _Draft_ step or a custom workflow step.
  * unpublish _Published_ pages.


## Edit content of structured pages
Structured pages contain data stored within editable page fields. The fields depend on the specific content type. For instance, the fields can be used to store text, numbers, or images.
To edit a structured page:
  1. Open a website channel application.
  2. In the content tree, select the page that you want to edit.
  3. Make sure the page is in the _Draft_ status or another [custom workflow step](documentation/developers-and-admins/configuration/workflows). 
     * When a page is _Published_ or _Scheduled_ , select **Edit page** to move the page to the _Draft_ step.  
[![Edit page button](docsassets/documentation/edit-and-publish-pages/edit_button.png)](https://docs.kentico.com/docsassets/documentation/edit-and-publish-pages/edit_button.png)
     * When a page is _Unpublished_ , select **Create new version** to move the page to the _Draft_ step.  
[![Create new version button](docsassets/documentation/edit-and-publish-pages/create_new_version_button.png)](https://docs.kentico.com/docsassets/documentation/edit-and-publish-pages/create_new_version_button.png)
  4. In the **Content** view mode, edit the fields of the page.
  5. **Save** the changes.


Note that the changes are not visible on the live site until you [publish](documentation/business-users/website-content/edit-and-publish-pages#publish-pages) the page.
## Edit content of Page Builder pages
See [Widgets and Page Builder](documentation/business-users/website-content/widgets-and-page-builder).
## Add tags to pages
Depending on the configuration of your project, pages may support taxonomies and tags.
  1. [Edit](documentation/business-users/website-content/edit-and-publish-pages#edit-content-of-structured-pages) a page that has a field with the tag selector.
  2. **Select tags** that describe the page.
  3. **Save** the changes.


[![Tag selector](docsassets/documentation/edit-and-publish-pages/tag_selector.png)](https://docs.kentico.com/docsassets/documentation/edit-and-publish-pages/tag_selector.png)
The selected tags are now stored with the page. Developers can use the information to [display content based on tags](documentation/developers-and-admins/configuration/taxonomies#retrieve-content-items-with-specified-tags).
## Share page previews
If you need to share a page preview with external contributors before publishing, use a [shareable preview URL](documentation/business-users/website-content#shareable-preview-urls).
## Publish pages
Pages can be published under the following conditions:
  * If the page’s content type is under [workflow](documentation/developers-and-admins/configuration/workflows), the page must be in the last step of the workflow, or you need to belong to a role with full control for the given workflow (this allows you to publish pages from any step).
  * For pages that are not under workflow, the page must be in the _Draft_ step.


In order to publish a page:
  1. Open a website channel application.
  2. In the content tree, select the page that you want to publish.
  3. Make sure the page has the _Draft_ status, or is in a [workflow step](documentation/developers-and-admins/configuration/workflows) from which you are allowed to publish the page. 
     * If a page is _Unpublished_ , select **Create new version** to move the page to the _Draft_ step.  
[![Create new version button](docsassets/documentation/edit-and-publish-pages/create_new_version_button.png)](https://docs.kentico.com/docsassets/documentation/edit-and-publish-pages/create_new_version_button.png)
  4. Select **Publish**. For pages under workflow, select **Change workflow step** and choose the **Schedule / Publish** step.  
[![Publish button](docsassets/documentation/edit-and-publish-pages/publish_button.png)](https://docs.kentico.com/docsassets/documentation/edit-and-publish-pages/publish_button.png)
  5. Choose when you want to publish the page: 
     * **Publish now**
     * **Schedule publish** – select a date and time in the future. The system will automatically publish the page at the given time (see [Scheduled publishing](documentation/business-users/website-content/edit-and-publish-pages#scheduled-publishing)).
  6. Select **Confirm**. 
     * If the page has fields or [Page Builder widgets](documentation/business-users/website-content/widgets-and-page-builder) linking to reusable content items or pages, the cascade publishing dialog opens. You can view all linked items that are currently not _Published_. Select any of the listed items to publish them together with the page. To learn more, see [Cascade publishing](documentation/business-users/content-hub/content-items#cascade-publishing).
Cascade publishing is only supported for reusable content items (managed in _Content hub_) and pages linked through the _Combined content selector_ component. Pages linked through fields or widget properties using the _Page selector_ component are not included in the cascade publishing dialog. 


Once _Published_ , the page is available on the live site.
### Scheduled publishing
When publishing pages, you can choose to postpone the publish action to a later time. Select the **Schedule publish** option while publishing, and then pick a date and time when you want the system to automatically publish the page.
[![Scheduling the time when a page will be published](docsassets/documentation/edit-and-publish-pages/pages_schedule_publish.png)](https://docs.kentico.com/docsassets/documentation/edit-and-publish-pages/pages_schedule_publish.png)
When scheduling the publish of a page together with [cascade publishing](documentation/business-users/content-hub/content-items#cascade-publishing) of linked content items or pages, the selected linked items are scheduled to be published at the same time. This overwrites the publish time for any linked items that are already scheduled to be published.
Once a page is _Scheduled_ , it can no longer be edited (like a _Published_ page). However, the page is not available on the live site until it actually becomes published.
If you need to edit a scheduled page, you have the following options:
  * Select the **Edit page** action – cancels the scheduled publish and moves the page to the first step in the page’s workflow cycle (_Draft_).
  * Expand the **Edit page** menu and select **Cancel scheduled publish** – cancels the scheduled publish and moves the page to the final step in the page’s workflow cycle (_Draft_ for pages without workflow).


**Scheduled publish timing**
We cannot guarantee that pages and content items will be published exactly at the scheduled time. The system checks if there are items scheduled to be published once per minute. If there’s a significant volume of content scheduled to be published at the same time, there may be a delay before all items are published (up to several minutes when publishing many thousands of items).
During the publishing process, [reusable content items](documentation/business-users/content-hub/content-items#publish-content-items) are always published **before** pages to ensure that content is displayed consistently.
## Revert changes
After creating a new version of a page, it goes through a new editing cycle with the _Draft_ step and optionally other [custom workflow steps](documentation/developers-and-admins/configuration/workflows).
At any time before the new version is published, you can revert the page back to the last _Published_ status. All changes made in any workflow step will be lost.
  1. Open a website channel application.
  2. In the content tree, select the page whose changes you want to revert.
  3. **Revert to published** the changes.


[![Discard button](docsassets/documentation/edit-and-publish-pages/pages_revert_to_published.png)](https://docs.kentico.com/docsassets/documentation/edit-and-publish-pages/pages_revert_to_published.png)
## Unpublish pages
An unpublished page is not available on the live site, but you can still access it from the administration. You can redirect visitors who try to reach the unpublished page to another page.
If you unpublish a page targeted by redirects, the redirect configuration of this page can affect other pages. See section [Unpublishing or deleting pages targeted by redirects](documentation/business-users/website-content/edit-and-publish-pages#unpublishing-or-deleting-pages-targeted-by-redirects) for details.
To unpublish a page (and thus remove it from the live site):
  1. Open a website channel application.
  2. In the content tree, select the page that you want to unpublish. 
     * You can only unpublish pages that are already [published](documentation/business-users/website-content/edit-and-publish-pages#publish-pages) or [scheduled to be published](documentation/business-users/website-content/edit-and-publish-pages#scheduled-publishing) in the future.
  3. Expand the **Edit page** menu and select **Unpublish options**.  
[![Unpublish a page](docsassets/documentation/edit-and-publish-pages/unpublish_button.png)](https://docs.kentico.com/docsassets/documentation/edit-and-publish-pages/unpublish_button.png)
  4. Choose when you want to unpublish the page: 
     * **Unpublish now**
     * **Schedule unpublish** – select a date and time in the future. If the page is scheduled to be published, the unpublish time must always be set _after the publish time_. The system will automatically unpublish the page at the given time.
  5. Choose where to redirect visitors when they try to reach the unpublished page. Your options depend on the page’s language variants. 
     * If a page variant in a fallback language is available, select one of the two options below: 
       1. **Serve content in fallback language**
       2. **Redirect to a page**
          * If chosen, continue with **Select existing page**.
          * Navigate to the target page for the redirect in the content tree on the left.
          * Select the page in the list on the right.
          * Confirm the configuration with the **Select** button.
     * If there’s no fallback available, you can: 
       1. Create a redirect using **Select existing page**. 
          * Navigate to the target page for the redirect in the content tree on the left.
          * Select the page in the list on the right.
          * Confirm the configuration with the **Select** button.
       2. Leave the page without a redirect. Visitors will receive a [404 error](https://en.wikipedia.org/wiki/HTTP_404) when they try to reach the unpublished page.
  6. Select **Confirm**.


Once a page is _Unpublished_ , it is no longer available. Depending on the option you chose, visitors trying to access the page are either redirected, receive an error, or are served content in a fallback language.
To edit or re-publish an unpublished page, you first need to select **Create new version**. This transitions the page back to the _Draft_ workflow step.
### Change scheduled unpublish options
If you wish to cancel scheduled unpublishing for a page, expand the **Edit page** menu and select **Cancel scheduled unpublish**. To change the unpublish date and time, use the same approach to cancel the scheduled unpublish and then schedule it again. When you cancel a scheduled unpublishing with a [scheduled redirect](documentation/business-users/website-content/edit-and-publish-pages#scheduled-redirects), the redirect is also canceled. Scheduled redirects can be edited on the **URLs** tab of the corresponding page.
For pages that have a scheduled unpublish time while they are still [scheduled for publishing](documentation/business-users/website-content/edit-and-publish-pages#scheduled-publishing), canceling the scheduled publish (or editing the page) also **automatically cancels the scheduled unpublish**.
### Redirects from unpublished and deleted pages
When you unpublish or delete a page, you can redirect visitors who try to access it to another page. Redirects can be used to ensure that visitors land on an alternative page and don’t get a [404 error](https://en.wikipedia.org/wiki/HTTP_404).
All [URLs](documentation/business-users/website-content/manage-page-urls) (system URL, vanity URLs, former URLs) of the given page are redirected when a redirect is created.
The table below shows options available when unpublishing or deleting a page.
|  Redirect set up |  No redirect set up  
---|---|---  
Page with a fallback language variant |  Redirect to the target page |  Serve content in a fallback language  
Page without a fallback language variant |  Redirect to the target page |  404 error  
#### Changing redirects
The following table describes where you can change redirect configuration. 
|  Redirects from unpublished pages |  Redirects from deleted pages  
---|---|---  
Edit a redirect |  [URLs application and the URLs tab](documentation/business-users/website-content/manage-page-urls#edit-redirects-from-unpublished-pages) corresponding to the page where the redirect is coming from |  [URLs application](documentation/business-users/website-content/manage-page-urls#edit-redirects-from-deleted-pages)  
Add a redirect |  [URLs tab](documentation/business-users/website-content/manage-page-urls#add-redirects-from-unpublished-pages) |  Not possible in the admin UI, redirects can be [added](documentation/developers-and-admins/development/routing/custom-redirects) by developers.  
Delete a redirect |  Create a new version of the page and go through the workflow again. |  [URLs application](documentation/business-users/website-content/manage-page-urls#delete-redirects-from-deleted-pages)  
#### Unpublishing or deleting pages targeted by redirects
If a page that is targeted by a redirect is unpublished or deleted, the target of the original incoming redirect is automatically adjusted according to the option chosen when the page is unpublished/deleted. The table below shows what happens for each option that can be chosen.
Option chosen for the page targeted by a redirect |  Adjusted behavior of the incoming redirect  
---|---  
Serve content in fallback language |  The target of the redirect stays the same, and the content in a fallback language is automatically served. This behavior can be disrupted by further actions, see [Redirects to untranslated pages](documentation/business-users/website-content/edit-and-publish-pages#redirects-to-untranslated-pages).  
Redirect |  The target of the incoming redirect is now the same as the target of the redirect from the newly unpublished/deleted page. The system doesn’t keep track of previous redirects.  
No redirect |  The incoming redirect is deleted, and visitors get a 404 error. Deleted redirects from deleted pages cannot be added again via the admin UI. These redirects can be added only via code, for example by developers.  
Former URLs are automatically updated according to the changes that occur.
Redirects that target pages that are scheduled to be unpublished are adjusted when the page they are targeting is unpublished. Until then, they keep their original configurations.
#### Republishing pages
Redirects from unpublished/deleted pages reflect the final target of the redirect. If a page targeted by redirects is unpublished, redirected to another page, and later published again, the redirects that originally targeted this page will keep their last target and won’t be rolled back.
For example, a page called _Christmas sale_ was unpublished and redirected to a page called _January sale_. Later, the page _January sale_ was also unpublished and redirected to the _Home_ page. Both _Christmas sale_ and _January sale_ now redirect to the _Home_ page. The page _Christmas sale_ is no longer redirected to the page _January sale_. If the _January sale_ page is published again, the _Christmas sale_ page is still redirected to the _Home_ page.
#### Redirects to untranslated pages
When a redirect is created to an untranslated page, visitors are automatically served a fallback language variant of this page if available. If no fallback language variant is available, visitors receive a 404 error. This applies even if the last fallback variant of the page was unpublished or deleted with a redirect.
If a redirect targeting an untranslated page is created and the page is later translated, visitors accessing the page via the redirect will be served the translated variant.
#### Scheduled redirects
When a page is scheduled to be unpublished, you can also create a redirect. The redirect is scheduled and applied once the page is unpublished. The scheduled redirect is not listed as a [former URL](documentation/business-users/website-content/manage-page-urls#former-urls) in the URLs application until the page is unpublished. The scheduled redirect can be edited only on the **URLs** tab of the corresponding page. If the scheduled unpublishing is canceled, the scheduled redirect is also canceled.
A page can have an active redirect and a scheduled redirect. This can happen in the following situation: A page was unpublished with a redirect. Subsequently, the page was scheduled to be published and then scheduled to be unpublished again with a new scheduled redirect. The **Redirect** section on the **URLs** tab shows only the active redirect. The scheduled redirect can’t be edited in this situation. You need to cancel the scheduled unpublishing and then schedule the unpublishing with a new redirect configuration.
#### Unpublishing or deleting the home page
Unpublishing or deleting a home page can cause problems if not handled properly. Make sure that you’re aware of all consequences before proceeding with this action.
If you want unpublish or delete your current home page and create a redirect to a new one, you also need to [configure](documentation/developers-and-admins/development/routing/content-tree-based-routing/enable-content-tree-based-routing#optional-routing-configuration) the new home page. If you create a redirect without configuring the new home page, the root domain will be inaccessible, and visitors will receive a 404 error.
## Rename pages
To rename a page:
  1. Open a website channel application.
  2. Navigate to the web page you want to rename.
  3. In the **Content** view mode, select the edit (
  4. Enter the new name and **Save** the changes.


[![Rename pages button](docsassets/documentation/edit-and-publish-pages/rename_pages.png)](https://docs.kentico.com/docsassets/documentation/edit-and-publish-pages/rename_pages.png)
The change of the page name is immediately reflected in the page tree.
## Move pages
**Possible side-effects of moving pages**
Depending on how your website is implemented, page structure and order in the content tree can represent more than a simple visual structure within the website channel application. Also, moving pages may affect what you and other editors are [allowed to do with them](documentation/developers-and-admins/configuration/users/role-management/page-permission-management).
Consult with your website administrator if you are not sure about how moving pages in the content tree reflects on your website.
To move or reorder pages in the content tree:
  1. Open a website channel application.
  2. In the content tree, select the page you want to move. The drag handle icon (
  3. Drag the page to the desired location. To move a page under a different parent page, hover over the dropzone to the left of the new parent page:
[![Reordering pages](docsassets/documentation/edit-and-publish-pages/content-tree-reorder-pages.png)](https://docs.kentico.com/docsassets/documentation/edit-and-publish-pages/content-tree-reorder-pages.png)


After moving or reordering pages, the changes are immediately saved and reflected on the live site.
## Restore previous versions of pages
See [Content versioning](documentation/business-users/content-versioning#restore-previously-published-versions) for more information. You can restore previously published versions of pages only when versioning is [configured](documentation/developers-and-admins/configuration/content-versioning-configuration).
## Change URLs of pages
See [Manage page URLs](documentation/business-users/website-content/manage-page-urls).