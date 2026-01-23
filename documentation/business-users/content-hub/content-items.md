---
source: https://docs.kentico.com/documentation/business-users/content-hub/content-items
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Business users](/documentation/business-users)
  * [Content hub](/documentation/business-users/content-hub)
  * Content items 


# Content items
Content items provide a universal and reusable way of working with content in the [Content hub](/documentation/business-users/content-hub) application and can be reused across channels (website, email, headless). Each item belongs to a single [workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces) and represents a piece of structured content defined by a [content type](/documentation/developers-and-admins/development/content-types).
Examples of content items include testimonials, author details, and FAQ sections. See [Content modeling](/guides/architecture/content-modeling/content-modeling-guide) for more information about the recommended information architecture of projects.
Content items are useful for numerous scenarios:
  * Content reusability
  * Separation of structured content from the presentation layer
  * Multichannel content distribution
  * Headless scenarios


## Find content items
The Content hub will often contain a large number of items. The search bar allows you to find items with a name or content type matching the entered text in the current [workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces).
The listing displays only items in the current workspace. If you have access to multiple workspaces, you can switch between them using the workspace switcher located at the top left of the Content Hub interface.
If you select a [folder](/documentation/business-users/content-hub/content-hub-folders) in the tree, the listing only displays items located directly in the given folder. You can use the search function to find items, including subfolders.
You can also **Filter** the list of content items to display only selected content types, items with specific statuses, items containing specific tags, or items with authentication requirements.
[![Filtering items in the Content hub application](/docsassets/documentation/content-items/CH_filter.png)](/docsassets/documentation/content-items/CH_filter.png)
## Create content items
  1. Select **New content item**.  
[![Creating a new content item](/docsassets/documentation/content-items/CH_create_item.png)](/docsassets/documentation/content-items/CH_create_item.png)
    1. Enter a **Content item name** which identifies the content item in the admin UI.
    2. If [content folders](/documentation/business-users/content-hub/content-hub-folders#content-folders) are enabled, select the **Location** for the new content item.
    3. Choose the **Content type** of the new content item.  
[![Choosing the content item name, location and type](/docsassets/documentation/content-items/ContentItemCreate.png)](/docsassets/documentation/content-items/ContentItemCreate.png)
  2. Select **Continue**.
  3. Enter the values required by the selected content type.
  4. **Save** the new content item, which creates a draft version of the item in the current [workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces).


**Directly publish items**
You can directly **Publish** new items to make them available for your audience (either immediately or at a scheduled time). See [Publish content items](#publish-content-items) to learn more.
If the item has fields [linking](#link-content-items) to other reusable content items, the cascade publishing dialog opens when publishing. You can view all linked content items you have access to, that are currently not published, and select them for publishing together with the main item.
The content item is now created.
## Clone content items
Cloning a content item allows you to create a new item with the same data. If the item has an [asset](/documentation/business-users/content-hub/content-item-assets) (i.e., a file that is uploaded to the item), a new independent copy of the asset is created for the cloned item. [Security settings](/documentation/business-users/content-hub/content-items#secure-content-items) of the item are not copied.
You can clone all [language variants](#translate-content-items) of an item or just a selected language variant.
To clone a content item:
  1. Open the **Content hub** application.
  2. Locate the content item you want to clone.
     * Use the search bar, filter or [folders](/documentation/business-users/content-hub/content-hub-folders) to [find content items](#find-content-items).
  3. Select the **Clone** ([![Clone action](/docsassets/documentation/content-items/CH_clone.png)](/docsassets/documentation/content-items/CH_clone.png)
  4. Enter the content item name. The default name is _(Clone) {original name}_.
  5. If you want to clone only the current language variant of the item, clear the **Clone all language variants** checkbox. Otherwise, all language variants are cloned by default.
  6. Select **Clone**.


You can [find](#find-content-items) the new item under the name you selected. Language variants other than the one selected during the cloning are automatically named using the pattern _(Clone) {original name}_. If you cloned an item located inside a [content folder](/documentation/business-users/content-hub/content-hub-folders#content-folders), you can find it under **All content items**.
The new item is created in the _Draft_ step, where you can [edit](#edit-content-items) it. The item needs to be [published](#publish-content-items) before it can be delivered to your audience.
## Edit content items
  1. Select the content item you want to edit. 
     * Use the search bar, filter or [folders](/documentation/business-users/content-hub/content-hub-folders) to [find content items](#find-content-items).
     * If you have multiple languages set up, select the language in which you want to edit the content item from the language switcher.
  2. Make sure the content item is in the _Draft_ status or another [custom workflow step](/documentation/developers-and-admins/configuration/workflows). 
     * When a content item is _Published_ or _Scheduled_ , select **Edit content item** to transition it to the _Draft_ step and enable editing and publishing of the item.  
[![Edit a Published or Scheduled content item](/docsassets/documentation/content-items/CH_edit_item.png)](/docsassets/documentation/content-items/CH_edit_item.png)
     * When a content item is _Unpublished_ , select **Create new version** to move the item to the _Draft_ step.  
[![Create new version of a content item](/docsassets/documentation/content-items/CH_create_new_version.png)](/docsassets/documentation/content-items/CH_create_new_version.png)
  3. Edit the fields of the content item.
  4. **Save** the updated content.


## Move content items
**Possible side effects of moving content items**
Moving content items may affect what you and other editors are [allowed to do with them](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management).
Consult with your administrator if you are unsure about how moving content items may affect other editors. Moving items to a workspace with different permissions could result in other editors losing access to or being unable to work with the moved items.
You can move content items into a [content folder](/documentation/business-users/content-hub/content-hub-folders#content-folders) or a different [workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces). The action is available both for individual items in the list, and as a mass action after selecting multiple items.
To move content items:
  1. Open the **Content hub** application.
  2. Select the content items you want to move. 
     * Use the search bar, filter or [folders](/documentation/business-users/content-hub/content-hub-folders) to [find content items](#find-content-items).
  3. Select the **Move** (  
[![Move content items](/docsassets/documentation/content-items/move_content_items.png)](/docsassets/documentation/content-items/move_content_items.png)
  4. Select the workspace or content folder where you want to move the items.
  5. **Move** the content items.


The content items are now moved to the new location.
## Link content items
Content items may support linking other items to create nested hierarchies. For example, a content item representing a medical practitioners office can link to other content items representing employed doctors, patient testimonials, and so on. These relationships are determined by the content model used by your project.
  1. [Edit](#edit-content-items) a content item that supports referencing other content items.
  2. You can choose existing content items to link via **Select existing** in any workspace you have access to, or create a completely new content item using **Create new**.  
[![Link content items to a content item](/docsassets/documentation/content-items/CH_page_link.png)](/docsassets/documentation/content-items/CH_page_link.png)
  3. **Save** the changes.


The content items are now linked to the edited item. Each linked item also respects its status – it may not be immediately active upon being linked. Linked items can be published together with the main item using [Cascade publishing](#cascade-publishing). To learn how the lifecycle influences linked items, see [Content items lifecycle](#content-items-lifecycle).
## Add tags to content items
Depending on the configuration of your project, content items may support [taxonomies](/documentation/developers-and-admins/configuration/taxonomies) and tags.
  1. [Edit](#edit-content-items) a content item that has a field with the tag selector.
  2. **Select tags** that describe the content item.  
[![Tag selector](/docsassets/documentation/content-items/tag_selector.png)](/docsassets/documentation/content-items/tag_selector.png)
  3. **Save** the changes.


The selected tags are now stored with the content item. Developers can use the information to [display content based on tags](/documentation/developers-and-admins/configuration/taxonomies#retrieve-content-items-with-specified-tags).
## View content item details
Switch to the **Properties** tab on the left to display various information about the selected content item, such as its name, content type, and culture.
[![Properties tab](/docsassets/documentation/content-items/properties_tab.png)](/docsassets/documentation/content-items/properties_tab.png)
## Track usage of content items
Even with thousands of content items in your project, the Content reuse locator feature helps you monitor where each item is [linked](#link-content-items) and which channels display it. Understanding a content item’s usage is valuable when assessing the potential impact of edits or deletions. For instance, you can verify whether a content item you plan to delete appears in published content and whether removing it might disrupt that content.
The chain of usage starts with items that directly reference your content item. These items, in turn, may be used in other items (found under _Related items_), and this pattern continues. Unfolding these chains of usage creates a tree-like structure, illustrating how your content item is referenced across different levels.
  1. Open the **Content hub** application.
  2. Select the content item you want to track. 
     * Use the search bar, filter or [folders](/documentation/business-users/content-hub/content-hub-folders) to [find content items](#find-content-items).
  3. Navigate to the **Used in** tab to view where the selected content item is used. 
     * The tab displays usages in content items and also in website channel pages.


[![Content hub usage tab](/docsassets/documentation/content-items/CH_Usage.png)](/docsassets/documentation/content-items/CH_Usage.png)
**Tracking limitations**
The system does not track or display usage of content items through [smart folders](/documentation/business-users/content-hub/content-hub-folders#smart-folders). If you use smart folders to dynamically link content items or deliver content to channels, we recommend manually checking the content of such folders before you delete or significantly edit content items that could be related.
The usage of content items in [notifications](/documentation/developers-and-admins/configuration/notifications) is also not tracked by the system. You should manually check whether a content item is used in notification emails before making any significant edits or deletions.
Similarly, usage is not tracked for already sent emails, as changes to content items cannot affect emails that have already been delivered.
The system also does not track usage in custom fields added to [extendable system object types](/documentation/developers-and-admins/customization/object-types/extend-system-object-types) or in custom classes. If you’re managing content linked this way, it’s important to manually confirm where it’s used before performing changes.
## Publish content items
Content items must be published before they can be delivered to your audience through various types of channels. For example, a website channel’s pages only display published content on the live site.
You can publish items under the following conditions:
  * If the item’s content type is under [workflow](/documentation/developers-and-admins/configuration/workflows), the item must be in the last step of the workflow, or you need to belong to a role with full control for the given workflow (this allows you to publish items from any step).
  * For items that are not under workflow, the item must be in the _Draft_ step.


To publish a content item:
  1. Open the **Content hub** application.
  2. Select a content item that has recently been edited and is in the _Draft_ status, or a [workflow step](/documentation/developers-and-admins/configuration/workflows) from which you are allowed to publish the item. 
     * Use the search bar, filter or folders to [find content items](#find-content-items).
  3. **Publish** the content item. For items under workflow, select **Change workflow step** and choose the **Schedule / Publish** step.
  4. Choose when you want to publish the item: 
     * **Publish now**
     * **Schedule publish** – select a date and time in the future. The system will automatically publish the item at the given time (see [Scheduled publishing](#scheduled-publishing)).
  5. Select **Confirm**. 
     * If the item has fields [linking](#link-content-items) to other reusable content items or pages, the [cascade publishing](#cascade-publishing) dialog opens.


Once published, the item is available for use within individual channels. When you edit a published content item, a new draft version is created. The item’s last _Published_ version remains public.
### Scheduled publishing
When publishing content items, you can choose to postpone the publish action to a later time. Select the **Schedule publish** option while publishing, and then pick a date and time when you want the system to automatically publish the item.
[![Scheduling the time when an item will be published](/docsassets/documentation/content-items/CH_schedule_publish.png)](/docsassets/documentation/content-items/CH_schedule_publish.png)
Once an item is _Scheduled_ , it can no longer be edited (like a _Published_ item). However, the item is not available for your audience until it actually becomes published.
If you need to edit a scheduled item, you have the following options:
  * Select the **Edit content item** action – cancels the scheduled publish and moves the item to the first step in the item’s workflow cycle (_Draft_).
  * Expand the **Edit content item** menu and select **Cancel scheduled publish** – cancels the scheduled publish and moves the item to the final step in the item’s workflow cycle (_Draft_ for items without workflow).


**Scheduled publish timing**
We cannot guarantee that items will be published exactly at the scheduled time. The system checks if there are items scheduled to be published once per minute. If there’s a significant volume of content scheduled to be published at the same time, there may be a delay before all items are published (up to several minutes when publishing many thousands of items).
During the publishing process, reusable content items are always published **before** [pages](/documentation/business-users/website-content) to ensure that content is displayed consistently.
### Mass publishing
To publish multiple content items at once:
  1. Open the **Content hub** application.
  2. Select the content items you want to publish using the checkboxes on the left side of the list.
  3. Select **Publish** under the search bar.


The **Publish selected content items?** confirmation dialog opens. Items can only be published if they fulfill the following requirements:
  * Items under [workflow](/documentation/developers-and-admins/configuration/workflows) must be in the last step of the workflow, or you need to belong to a role with full control for the given workflow (this allows you to publish items from any step). Items without workflow must be in the _Draft_ step.
  * You must be allowed to publish the item under the item’s workflow restrictions for roles.
  * The item must be translated in the currently selected [language](/documentation/developers-and-admins/configuration/languages).


If any of the selected items do **not** fulfill the requirements for publishing, they are listed in the dialog. You can directly open (
[![Mass publish confirmation dialog](/docsassets/documentation/content-items/CH_mass_publish_confirm.png)](/docsassets/documentation/content-items/CH_mass_publish_confirm.png)
To confirm the mass publish, select **Publish** or **Continue** (appears if [cascade publishing](#cascade-publishing) of linked items is possible for any of the published items).
### Cascade publishing
When publishing (or scheduling the publish for) content items with fields [linking](#link-content-items) to other reusable content items or pages, the cascade publishing dialog opens. Here you can view all items that are associated with the item you’re about to publish, but are currently not published. If you are publishing multiple items using [mass publishing](#mass-publishing), the dialog displays linked items for all published items (in this case, the main published items are not listed).
You can select [linked items](#link-content-items) to be published together with the main items. Linked items that currently do not fulfill the requirements for publishing are displayed, but cannot be selected. Leaving linked items unpublished may lead to missing data or other issues in the content of the published items.
[![Cascade publishing](/docsassets/documentation/content-items/Cascade_Publishing.png)](/docsassets/documentation/content-items/Cascade_Publishing.png)
**Cascade scheduled publishing**
If you are [scheduling](#scheduled-publishing) an item to be published, any linked items that you select in the cascade publishing dialog are scheduled to be published at the same time as the main item. This overwrites the publish time for any linked items that are already scheduled to be published.
Linked items that are scheduled to be [unpublished](#unpublish-content-items) at a time before the new scheduled publish date **cannot be selected** in the cascade publishing dialog.
Cascade publishing is available when publishing all types of content, including: reusable content items in _Content hub_ , [pages](/documentation/business-users/website-content), [emails](/documentation/business-users/digital-marketing/emails), and [headless items](/documentation/business-users/headless-content).
Together with the main items, cascade publishing allows you to publish reusable content items and [pages](/documentation/business-users/website-content) linked in the following ways:
  * through the fields of an item, page or email in the **Content** view of the respective application
  * recursively through the fields of another linked item
  * linked on pages through [Page Builder widget](/documentation/business-users/website-content/widgets-and-page-builder) properties using the _Combined content selector_ form component


Cascade publishing does **not** support publishing of:
  * related pages linked through fields with the _Pages_ data type
  * headless items linked through fields with the _Headless items_ data type
  * pages linked through Page Builder widget properties using the _Page selector_ form component
  * pages or content item assets inserted or linked in rich text editor fields


## Revert content items to published status
After creating a new version of a content item, the item goes through a new editing cycle with the _Draft_ step and optionally other [custom workflow steps](/documentation/developers-and-admins/configuration/workflows).
At any time before the new version is published, you can revert the item back to the last _Published_ status. All changes made in any workflow step will be lost.
  1. Open the **Content hub** application.
  2. Select a content item that has been edited. 
     * Use the search bar, filter or folders to [find content items](#find-content-items).
  3. Expand the **Publish** or **Change workflow step** menu.
  4. Select **Revert to published** to revert the changes. [![Reverting a content item to its published status](/docsassets/documentation/content-items/CH_revert_to_published.png)](/docsassets/documentation/content-items/CH_revert_to_published.png)


The item is now reverted to its _Published_ status.
## Translate content items
Before you can translate individual content items in your project into other languages, you need to set those up in the **Languages** application. For detailed info on how to configure multiple languages in Xperience see [Languages](/documentation/developers-and-admins/configuration/languages).
Afterwards you can start to translate and create language variants of your items:
  1. Open the **Content hub** application.
  2. In the header bar, open the language switcher.  
[![Content language selector](/docsassets/documentation/content-items/header_bar_language_switcher.png)](/docsassets/documentation/content-items/header_bar_language_switcher.png)
The language switcher is available only if two or more languages are set up.
  3. Select the language to translate content into.
     * **Tip** : You can see what languages a content item is translated into in the _Languages_ column.
  4. Select the content item to translate.
Untranslated content items are visually marked by the _Not translated_ (
  5. Enter the translated name of the content item. You can now choose one of the following options:
     * **Copy content from another language** is the preselected option. By default, the closest language in the [fallback chain](/documentation/developers-and-admins/configuration/languages) is preselected. You can choose a different language from the drop-down list. This option lets you prefill the content item fields with the content available in the chosen language. If a linked item does not have a translated or published version, a warning will be displayed, and a fallback language variant of the item will be used.
     * **Create empty content item** allows you to create an empty content item with blank fields. Linked items need to be re-linked, and files need to be re-uploaded into the new language variant of the translated content item.
     * **Translate using AIRA** see [Translate using AIRA](#translate-using-aira) for more information.  
[![Translate content item](/docsassets/documentation/content-items/translate_content_item.png)](/docsassets/documentation/content-items/translate_content_item.png)
  6. Select **Continue**.
  7. Enter the translated values of the content item’s fields.
  8. **Save** the translated content item.


You have now successfully translated the content item. Note that the particular language variant is now only a _Draft_ and so won’t be accessible on the live site until you [publish](#publish-content-items) it. Until you do so, the language variant in the fallback language will be displayed.
### Translate using AIRA
**AIRA license requirements**
[AIRA](/documentation/business-users/aira) features require the Xperience by Kentico **Advanced** license tier.
**Preview feature**
AIRA is currently in preview mode. Expect AIRA features to be updated and modified in upcoming versions, including breaking changes.
Feel free to try out the AIRA features, for example using the sample _Dancing Goat_ [project template](/documentation/developers-and-admins/installation) or other demo sites. You can share your feedback directly with the Kentico [Product team](https://roadmap.kentico.com/).
When you choose **Translate using AIRA** and start the translation process, the name and content of your content item will be translated using [AIRA](/documentation/business-users/aira).
The translation workflow changes depending on the number of items submitted for translation. For a single item, AIRA creates a new language variant of the content in the _Draft_ state and immediately takes you to it.
However, when translating multiple content items at once (for example, when translating content items along with their linked content), your content is sent to a _Translation queue_. This allows the translation to be processed in the background, freeing you up to continue your work. You can check on its progress in the _Translation queue_ application. Once the translation of your content finishes, the related items are removed from the _Translation queue_ application.
The behavior of the translation can be [configured](/documentation/developers-and-admins/configuration/aira-configuration#configure-aira-translations), allowing you to specify, for example, formatting rules or brand terminology.
Existing text links to the translated content are not redirected to the new variant or translated automatically and need to be manually reviewed to ensure they lead to the correct content.
#### Mass translating
To translate multiple content items at once using [AIRA](/documentation/business-users/aira):
  1. Open the **Content hub** application.
  2. Select the content items you want to translate using the checkboxes on the left side of the list.
  3. Select **Translate using AIRA** under the search bar.
  4. Select which of the linked items you want to include in the translation.
  5. **Translate item with selected items**.


You have successfully sent your items to be translated. The items will be placed in the “Translation queue” to be processed asynchronously. You can continue with your work or check the progress of the translation in the _Translation queue_ application.
## Secure content items
Secured content items require that the consumer (e.g., a site visitor) is [signed in](/documentation/business-users/members) before the content item is displayed.
To secure content items:
  1. Open the **Content hub** application.
  2. Select the content item you want to secure. 
     * Use the search bar, filter or folders to [find content items](#find-content-items).
  3. Switch to the **Properties** tab.
  4. Set the content item’s security configuration. 
     * **Requires authentication** – only visitors signed in to the website can access the item.  
[![Content item security settings](/docsassets/documentation/content-items/ContentItemSecuritySettings.jpg)](/docsassets/documentation/content-items/ContentItemSecuritySettings.jpg)
  5. Select **Save**.


Secured content items show a lock icon when listed in various places in the Xperience admin UI.
Note that secured content items are only available for [website channel pages](/documentation/business-users/website-content), where the system can identify which users are authenticated. Secured items are **not included** in the content of [emails](/documentation/business-users/digital-marketing/emails) or [headless items](/documentation/business-users/headless-content) (even if selected in the fields of such items).
See [Secure pages](/documentation/business-users/website-content/secure-pages) to learn how to secure individual pages or entire sections of website channels.
## Unpublish content items
An unpublished content item is not available for use by any channels, but you can still access it from the administration.
To unpublish a content item:
  1. Open the **Content hub** application.
  2. Select a content item you want to unpublish. 
     * You can only unpublish content items that are already [published](#publish-content-items) or [scheduled to be published](#scheduled-publishing) in the future.
     * Use the search bar, filter or [folders](/documentation/business-users/content-hub/content-hub-folders) to [find content items](#find-content-items).
  3. Expand the **Edit content item** menu and select **Unpublish options**.  
[![Unpublish a content item](/docsassets/documentation/content-items/CH_unpublish.png)](/docsassets/documentation/content-items/CH_unpublish.png)
  4. Choose when you want to unpublish the item: 
     * **Unpublish now**
     * **Schedule unpublish** – select a date and time in the future. If the item is scheduled to be published, the unpublish time must always be set _after the publish time_. The system will automatically unpublish the item at the given time.
  5. Select **Confirm**.


Once an item is _Unpublished_ , it is no longer available.
To [edit](#edit-content-items) or re-publish an unpublished content item, select **Create new version** for the content item. This creates a new version of the item in the _Draft_ step.
### Change scheduled unpublish options
If you wish to cancel scheduled unpublishing for an item, expand the item’s **Edit content item** menu and select **Cancel scheduled unpublish**. To change the unpublish date and time, use the same approach to cancel the scheduled unpublish and then schedule it again.
For items that have a scheduled unpublish time while they are still [scheduled for publishing](#scheduled-publishing), canceling the scheduled publish (or editing the item) also **automatically cancels the scheduled unpublish**.
## Restore previous versions of content items
See [Content versioning](/documentation/business-users/content-versioning#restore-previously-published-versions) for more information. You can restore previously published versions of content items only when versioning is [configured](/documentation/developers-and-admins/configuration/content-versioning-configuration).
## Delete content items
In the current version of Xperience by Kentico, deleted items can be [restored](/documentation/business-users/recycle_bin). If you’re using a version older than 30.12.0, deleting a content item is non-reversible and the item cannot be restored.
  1. Open the **Content hub** application.
  2. Locate the content item you want to delete. 
     * Use the search bar, filter or [folders](/documentation/business-users/content-hub/content-hub-folders) to [find content items](#find-content-items).
  3. Select **Move to recycle bin** (
     * If the deleted content item is used in other content, you may want to [check the usage](#track-usage-of-content-items) of the item and edit all content where it is used.
     * **Tip** : You can quickly see whether a content item is used by checking the **In use** column of the content item listing.


[![Delete content items](/docsassets/documentation/content-items/ContentHubDelete.png)](/docsassets/documentation/content-items/ContentHubDelete.png)
The content item is now deleted. You can find it in the recycle bin and [restore](/documentation/business-users/recycle_bin#restore-deleted-items) it later.
To delete multiple items at once, select the items in the list and use the **Delete** button under the search bar.
## Restore deleted content items
The current version of Xperience allows you to restore deleted content items from the recycle bin. See [Restore deleted items](/documentation/business-users/recycle_bin#restore-deleted-items) for more information. If you’re using a version older than 30.12.0, recovering deleted content is not possible.
## Synchronize content items
Xperience allows you to easily transfer new and updated content items from one instance of Xperience by Kentico to another. For detailed information and instructions, see [Content sync](/documentation/business-users/content-sync).
## Content items lifecycle
Content items can transition through the following statuses:
  * **Draft (Initial)** / **Draft (New version)**
  * [Custom workflow step](/documentation/developers-and-admins/configuration/workflows) (the item is being edited, “between” the _Draft_ and _Published_ status)
  * **Scheduled** (will be automatically published at a selected date and time in the future)
  * **Published**
  * **Unpublished**
  * **Not translated**


Each status changes the behavior of the item in the system.
Generally, items in the **Draft (Initial)** status are only shown in content previews, e.g., in page previews of website channels or email previews. In live content, only **Published** content items are shown. When you edit a _Published_ item and move it to **Draft (New version)** , the published content remains the active (main) version shown live until replaced by the new draft. **Unpublished** items can only be found in the Content hub application. Finally, **Not translated** items do not exist in the currently selected language. See the following diagram:
[![Content availability for individual statuses](/docsassets/documentation/content-items/WorkflowStatesSummary.png)](/docsassets/documentation/content-items/WorkflowStatesSummary.png)
The same general behavior also applies to [linked content items](#link-content-items). A content item in the **Draft** status linked to a content item in the **Published** status will only show up in content previews (not in live content) until it transitions to **Published**.
The following flowchart demonstrates available status transitions:
[![Status transitions flowchart](/docsassets/documentation/content-items/final_workflow.png)](/docsassets/documentation/content-items/final_workflow.png)
### Advanced scenarios
This section looks at a couple of example scenarios and explains how they can be influenced by statuses.
  * [Mass translating](#mass-translating)
  * [Content item with linked items](#content-item-with-linked-items)
  * [Creating new drafts of published (linked) content items](#creating-new-drafts-of-published-linked-content-items)
  * [Content item assets](#content-item-assets)
  * [Publishing a content item containing non-published content items](#publishing-a-content-item-containing-non-published-content-items)
  * [Publishing a content item containing items with a new draft version](#publishing-a-content-item-containing-items-with-a-new-draft-version)


#### Content item with linked items
**Scenario:** I have a content item with multiple [linked content items](#link-content-items). How does the status of the linked items impact my main item?
The status is evaluated independently for each content item. Linked content items that are in the **Draft (Initial)** status will not be shown as part of the main content item. For example, an FAQ section will be missing all linked “FAQ” content items that are in the _Draft (Initial)_ status.
See the following diagram for an illustration:
[![Linked content items lifecycle behavior](/docsassets/documentation/content-items/LinkedItemWorkflow.png)](/docsassets/documentation/content-items/LinkedItemWorkflow.png)
Linked item 2 (Draft new version) contains a published version that the system uses instead. However, _Linked item 3_ has never been published before and therefore has no version that the system can use for live content.
* * *
#### Creating new drafts of published (linked) content items
**Scenario:** I have a published content item (that may or may not be linked by other items) and create a new draft version of it. How does this affect the published data? 
Creating new drafts of published items doesn’t interfere with existing public items at all. The draft version is stored in the background and when eventually promoted, silently replaces the current published version.
* * *
#### Content item assets
**Scenario:** I have [content item assets](/documentation/business-users/content-hub/content-item-assets) linked from a content item. How does the status of the asset affect the integrity of the main content item?
Same as with regular content items, the status of content item assets is evaluated independently. **Initial draft versions** (i.e., newly created, never before published) will not be shown in live content until **Published**. **Draft** versions of published assets only replace the current public version when the entire item transitions to **Published**. **Unpublished** assets are not shown in the content of other content items.
* * *
#### Publishing a content item containing non-published content items
**Scenario:** I have a draft content item A which includes another draft content item B. How does the draft status of the included content item B affect the publishing of content item A?
When publishing a content item which links to content items that are not published, all of the content items can be published at once using [cascade publishing](#cascade-publishing).
* * *
#### Publishing a content item containing items with a new draft version
**Scenario:** I have a draft of an item that includes another [linked item](#link-content-items). Even though there’s a new version of the linked item, I want to publish the first item with the older (currently published) version of the linked item. How does the draft status of the linked item affect publishing of the first item?
When trying to publish a content item which includes content items with a new draft version, you can select which content items to publish along with it. You can choose not to publish the linked item, which means the currently published version of the item will be used, until the new version is published.
![]()
[]()[]()
