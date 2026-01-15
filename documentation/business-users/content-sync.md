# Content sync
  * [ Copy page link ](documentation/business-users/content-sync#) | [Get HelpService ID](documentation/business-users/content-sync#)
Core MVC 5


[✖](documentation/business-users/content-sync# "Close page link panel") [Copy to clipboard](documentation/business-users/content-sync#)
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Content synchronization allows you to easily transfer new and updated content from one “instance” of Xperience by Kentico to another. In this context, instances are Xperience applications hosting the same website or project, typically on another server and domain.
The purpose of content sync is to help manage content when using multiple environments. For example, your project’s developers may have a local **Development** instance, from which initial content is sent to a hosted **Staging** instance. The staging instance is accessible only by editors and other staff, who use it to prepare content updates. The final content is then synchronized during regular releases to a public-facing **Production** instance.
[![Instances connected using content sync](docsassets/documentation/content-sync/content_sync_topology.png)](https://docs.kentico.com/docsassets/documentation/content-sync/content_sync_topology.png)
**Prerequisites**
Your project’s administrators need to correctly set up content sync for all involved instances. For detailed information, see [Content sync configuration](documentation/developers-and-admins/configuration/content-sync-configuration).
## Supported content
Content sync is supported for:
  * [Reusable content items](documentation/business-users/content-hub/content-items)
  * [Website channel pages](documentation/business-users/website-content)
  * [Forms](documentation/business-users/digital-marketing/forms)


The following data is automatically included when synchronizing content:
Reusable content items  
---  
  * [Content item assets](documentation/business-users/content-hub/content-item-assets) (physical files) 
    * For image assets, only the primary [optimized image](documentation/business-users/content-hub/content-item-assets#optimize-image-assets) is synchronized. [Image variants](documentation/business-users/content-hub/content-item-assets#image-variants) are generated automatically when requested for the first time on the target instance.
  * [Content folders](documentation/business-users/content-hub/content-hub-folders#content-folders) where synchronized items are placed (_Smart folders_ are not synchronized)

  
Pages  
  * [Page URLs](documentation/business-users/website-content/manage-page-urls) (both system and vanity)
  * [Page redirects from unpublished pages](documentation/business-users/website-content/manage-page-urls#manage-redirects-from-unpublished-pages) (Former URLs)
  * Folders in the page tree that contain synchronized pages
  * [Forms](documentation/business-users/digital-marketing/forms) added to [Page Builder content](documentation/business-users/website-content/widgets-and-page-builder) via the _Form_ widget 
    * You do not always need to synchronize forms together with pages. Whenever you synchronize pages or content items, you can choose whether to **Include forms** (if you have sufficient permissions to synchronize forms).
    * Forms that are [rendered](documentation/developers-and-admins/development/builders/page-builder/render-widgets-in-code) directly in the page code (not added in the Page Builder interface) are not synchronized together with pages. Such forms can be [synchronized](documentation/business-users/content-sync#forms) separately from the _Forms_ application.

  
Both content items and pages  
  * [Linked](documentation/business-users/content-hub/content-items#link-content-items) content items and pages 
    * Includes items/pages linked via content fields or [Page Builder component](documentation/business-users/website-content/widgets-and-page-builder) properties, and assets used in [rich text content](documentation/business-users/rich-text-editor) via the _Insert image_ and _Insert link_ options.
    * Includes pages selected in the **After form submission → Redirect to** configuration of [Form widgets](documentation/business-users/digital-marketing/forms/display-forms-on-pages) in the Page Builder content of synchronized pages.
    * The synchronization cascades if the linked items themselves link to other content items or pages.
  * [Taxonomies](documentation/developers-and-admins/configuration/taxonomies) and tags assigned to synchronized items

  
### What is not synchronized?
The following limitations apply when synchronizing content:
  * [Content item lifecycle](documentation/business-users/content-hub/content-items#content-items-lifecycle) and [Page lifecycle](documentation/business-users/website-content#page-lifecycle)
    * Content items or pages that were never published cannot be synchronized. This includes items/pages in the **Draft (Initial)** status or a [custom workflow step](documentation/developers-and-admins/configuration/workflows) before the first publish.
    * For items/pages that were published in the past, but are currently in the **Draft (New version)** status or a [custom workflow step](documentation/developers-and-admins/configuration/workflows), the latest _Published_ version is synchronized.
    * If a content item or page is scheduled to be published or scheduled to be unpublished, this information is **not** included in the synchronization.
  * Unpublished content 
    * Synchronization of [unpublished reusable content items](documentation/business-users/content-hub/content-items#unpublish-content-items) only works for items that already exist on the target instance in a _Published_ state. If you synchronize a “new” content item with the _Unpublished_ status, no changes are made on the target.
    * [Unpublished pages](documentation/business-users/website-content/edit-and-publish-pages#unpublish-pages) can be synchronized without limitations, together with published or unpublished child pages.
  * Deleted content 
    * You cannot “select” a deleted content item or page for synchronization. Content synchronization cannot be used to delete items on the target instance.
  * Multilingual content 
    * Only items/pages from the currently selected [language](documentation/developers-and-admins/configuration/languages) are included in each synchronization. You can switch the language in Content hub or website channel applications, and perform a separate synchronization for each language.
  * Linked pages 
    * [Website channel pages](documentation/business-users/website-content), including pages linked from content items, cannot be synchronized if all of their parent pages are not available for synchronization (i.e., if the parents are not translated in the selected language or have never been published).
  * Forms 
    * [Form](documentation/business-users/digital-marketing/forms/create-and-edit-forms) synchronization does not include [form submission data](documentation/business-users/digital-marketing/forms/manage-form-submissions), [autoresponder](documentation/business-users/digital-marketing/forms/create-and-edit-forms#autoresponders) settings, [automation processes](documentation/business-users/digital-marketing/forms/create-and-edit-forms#automation-processes) related to the form, [form submission notifications](documentation/developers-and-admins/configuration/notifications#form-submission-notifications), or the [visibility configuration](documentation/business-users/digital-marketing/forms/create-and-edit-forms#hide-featured-fields) of featured form fields.
  * Unsupported data 
    * [Smart folders](documentation/business-users/content-hub/content-hub-folders#smart-folders) cannot be synchronized together with content items.


Additionally, the following data is required by content items and/or pages, but is **not** included in content synchronization:
  * Channels that use the content ([website](documentation/developers-and-admins/configuration/website-channel-management), [email](documentation/developers-and-admins/digital-marketing-setup/email-channel-management), [headless](documentation/developers-and-admins/configuration/headless-channel-management))
  * [Content types](documentation/developers-and-admins/development/content-types)
  * [Languages](documentation/developers-and-admins/configuration/languages)
  * [Workspaces](documentation/developers-and-admins/configuration/users/role-management/workspaces)


You or your project’s administrators need to ensure that the target instance has exactly the same set of these objects as the source instance. For example, an error will occur if you try to synchronize a content item for which an exactly matching [content type](documentation/developers-and-admins/development/content-types) is not available on the target.
## Editing process when using content sync
Content sync is designed to be used with only a single source of content. For content that supports content sync, only edit on the first source instance in your content sync chain, and then use the sync feature to transfer content. Any required changes of data that cannot be synchronized must be done by developers using other deployment methods.
If you manually edit content directly on the target instance, you risk losing the changes when content is synchronized from the source. You may also cause incompatibility of required objects, such as content types or languages, which would prevent future synchronization.
For example, if you have a setup with one _Staging_ instance as a source and a public-facing _Production_ instance, only edit content on the staging instance, and use the content sync feature to move changes to production.
### Workflow and content sync
If you use [workflows](documentation/developers-and-admins/configuration/workflows) to control the lifecycle of your content, only set up and apply the workflow on the source instance where you edit. Any workflow applied on the target instance is ignored, as content items are always transferred in the _Published_ or _Unpublished_ status and with a completed workflow cycle.
## Synchronize content
**Recommended content size per synchronization**
We recommend that you do not include over 1000 content items or 200 pages, and 200 MB of related asset files within a single synchronization. The recommended number of pages per synchronization is lower, since pages on most websites often contain more linked items and assets.
Larger synchronizations may still work correctly, depending on the performance of your application’s servers and your connection speed. However, splitting large content updates into multiple synchronizations is generally a better approach. This reduces the chance of issues during the synchronization, for example due to network errors.
### Content items
  1. Open the **Content hub** application.
  2. Select the content items you want to synchronize. 
     * Use the search bar, filter or [folders](documentation/business-users/content-hub/content-hub-folders) to [find content items](documentation/business-users/content-hub/content-items#find-content-items).
  3. Use the **Sync** (  
[![Sync content items](docsassets/documentation/content-sync/sync_content_items.png)](https://docs.kentico.com/docsassets/documentation/content-sync/sync_content_items.png)
  4. Choose [when you want the content to be restored](documentation/business-users/content-sync#choose-the-restoration-time-for-synchronized-content) on the target instance.
  5. Review the content items that will be synchronized. 
     * Any content items that cannot be synchronized are grayed out in the list. For example, this occurs if the synchronization selection includes items that are not translated to the current language or items that have never been published.
     * Keep in mind that [linked content items](documentation/business-users/content-hub/content-items#link-content-items), linked pages and related forms are not displayed in the list, but will also be synchronized.
  6. Select **Sync**.


The content sync dialog displays the progress of the synchronization and informs you about the result.
### Pages
You can synchronize individual pages, or entire subsections of the page tree.
  1. Open a website channel application.
  2. Find the page you want to synchronize in the page tree.
  3. Open the context menu (
  4. Select the **Sync this page** or **Sync with all subpages** (  
[![Sync pages](docsassets/documentation/content-sync/sync_pages.png)](https://docs.kentico.com/docsassets/documentation/content-sync/sync_pages.png)
  5. Choose [when you want the content to be restored](documentation/business-users/content-sync#choose-the-restoration-time-for-synchronized-content) on the target instance.
  6. Review the pages that will be synchronized. 
     * Pages can only be synchronized if you also select all parent pages or folders up to the first level of the page tree. Otherwise the synchronization would result in an invalid page tree on the target instance.
     * Any pages that cannot be synchronized are grayed out in the list. For example, this occurs if the synchronization selection includes pages that are not translated to the current language or items that have never been published.
     * Keep in mind that [linked content items](documentation/business-users/content-hub/content-items#link-content-items), linked pages and related forms are not displayed in the list, but will also be synchronized.
     * Folders in the page tree can only be synchronized together with child pages. Folders cannot be synchronized alone if they do not contain any child pages in the selected language.
  7. Select **Sync**.


The content sync dialog displays the progress of the synchronization and informs you about the result.
**Duration of page content sync**
Pages on many websites often contain a large number of linked items, assets and other pages. For this reason, synchronization may take longer than expected, even if you select only a small number of pages.
#### Sync moved or reordered pages
To correctly synchronize pages that were [moved](documentation/business-users/website-content/edit-and-publish-pages#move-pages) or reordered in the page tree, you need to sync **all items on the same level** of the page tree, for both the original and new location. This ensures that the correct order of all affected pages is synchronized.
To simplify this process, we recommend synchronizing the original and new parents of the moved page together with all direct child pages. However, the root page of a website channel cannot be synchronized – transferring an entire website channel within a single content sync action is not supported.
### Forms
**Impact of synchronizing forms**
If you delete or replace a [field](documentation/business-users/digital-marketing/forms/create-and-edit-forms#add-fields) in a form and then synchronize the form to another environment (production), any data gathered through the field over the form’s lifetime will also be removed in the target environment.
#### Sync forms with pages
Forms can be synchronized together with pages that display them in their [Page Builder content](documentation/business-users/website-content/widgets-and-page-builder). In this scenario, use the synchronization instructions described for [Pages](documentation/business-users/content-sync#pages), with the **Include forms** option enabled. This also applies when synchronizing [Content items](documentation/business-users/content-sync#content-items), where forms can be synchronized via linked pages.
[![Include forms when synchronizing pages and content items](docsassets/documentation/content-sync/content_sync_include_forms.png)](https://docs.kentico.com/docsassets/documentation/content-sync/content_sync_include_forms.png)
Forms are not synchronized together with pages if they are [rendered](documentation/developers-and-admins/development/builders/page-builder/render-widgets-in-code) directly in the page code (not added in the Page Builder UI). Such forms can be [synchronized separately](documentation/business-users/content-sync#sync-forms-individually) in the _Forms_ application.
#### Sync forms individually
You can synchronize individual forms separately, without pages or content items:
  1. Open the **Forms** application.
  2. Use the **Sync** (  
[![Sync a form](docsassets/documentation/content-sync/sync_form.png)](https://docs.kentico.com/docsassets/documentation/content-sync/sync_form.png)
  3. Choose [when you want the form to be restored](documentation/business-users/content-sync#choose-the-restoration-time-for-synchronized-content) on the target instance.
  4. Select **Sync**. 
     * Keep in mind that forms are synchronized **without** [submission data](documentation/business-users/digital-marketing/forms/manage-form-submissions), [autoresponder](documentation/business-users/digital-marketing/forms/create-and-edit-forms#autoresponders) settings, related [automation processes](documentation/business-users/digital-marketing/forms/create-and-edit-forms#automation-processes), [form submission notifications](documentation/developers-and-admins/configuration/notifications#form-submission-notifications), and the [visibility configuration](documentation/business-users/digital-marketing/forms/create-and-edit-forms#hide-featured-fields) of featured form fields.


The content sync dialog displays the progress of the synchronization and informs you about the result.
### Choose the restoration time for synchronized content
Whenever you synchronize content, you can choose when the data will be restored on the target instance:
  * **Restore on the target immediately** – the content will be transferred to the target instance and restored as soon as possible. The restoration may take up to 30 seconds to start, may be delayed by other content sync operations, and may take some time depending on the size of the synchronized content. You can view the progress in the content sync dialog.
  * **Postpone restoration on the target** – select a date and time in the future. The content will be transferred to the target instance and restored at the scheduled time. You can [track scheduled synchronizations](documentation/business-users/content-sync#track-incoming-synchronizations) on the target instance.


[![Choosing when to restore synchronized content](docsassets/documentation/content-sync/content_sync_restore_time.png)](https://docs.kentico.com/docsassets/documentation/content-sync/content_sync_restore_time.png)
**Scheduling multiple synchronizations**
If you are planning multiple scheduled synchronizations, make sure to set the restoration times according to the order in which you want the content to be created or updated. Leave appropriate time intervals for the restoration to finish for each synchronization (at least 15 minutes are recommended).
If you schedule multiple synchronizations with updates for the same content items, pages or forms, the last restored synchronization overwrites the previous ones.
## Track incoming synchronizations
When working on an Xperience instance that is the target of content synchronization, you can monitor synchronizations that recently occurred or are scheduled to take place in the **Content synchronization** application.
[![View planned or performed synchronizations](docsassets/documentation/content-sync/content_sync_scheduled.png)](https://docs.kentico.com/docsassets/documentation/content-sync/content_sync_scheduled.png)
If you need to cancel or reschedule a scheduled restoration, **Delete** (
**Synchronization cleanup**
The system automatically removes synchronizations once per day. The cleanup does not affect synchronizations with the _Ready_ status that are scheduled for restoration in the future.