---
source: https://docs.kentico.com/documentation/business-users/headless-content
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Business users](/documentation/business-users)
  * Headless content 


# Headless content
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Headless content items store content that is intended for distribution to channels outside of the system through a headless API ([GraphQL](https://graphql.org/)). This way, you can consolidate your various channels, like mobile apps, web apps, kiosks, etc. into the Xperience ecosystem. When modeling headless content, one headless item usually represents one unit of the presentation layer (page, screen, navigation, etc.) in the channel to which you provide the data. 
Headless items for each channel are managed in respective headless channel applications – by default, the name of the application corresponds to the name of the channel. The application enables you to create, delete or edit the content and properties of headless items. It also provides intuitive headless item lifecycle management.
[![Headless items](/docsassets/documentation/headless-content/headless_items_overview.png)](/docsassets/documentation/headless-content/headless_items_overview.png)
## Create headless items
To create a new headless item:
  1. Open a headless channel application.
  2. Select **New headless item**.  
[![New headless item](/docsassets/documentation/headless-content/new_headless_item.png)](/docsassets/documentation/headless-content/new_headless_item.png)
  3. Enter the headless item name.
  4. Select a content type from the list of available content types.  
[![New headless item - select content type](/docsassets/documentation/headless-content/new_headless_item_select_type.png)](/docsassets/documentation/headless-content/new_headless_item_select_type.png)
  5. Select **Continue**.
  6. Enter the values required by the selected content type. 
     * In many cases, headless items will have fields that allow you to add content by selecting reusable content items (blocks of text, images, etc.). See [Content hub](/documentation/business-users/content-hub) to learn more. When selecting reusable content items, note that secured items with the **Requires authentication** flag enabled are not included in headless content.
  7. **Save** or **Publish** the new headless item. 
     * Saving creates a  _Draft_ of the headless item.
     * Publishing immediately sets the headless item to the  _Published_ status.


The headless item is now created. Keep in mind that only _Published_ items are available through the [headless API](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content).
## Clone headless items
Cloning a headless item allows you to create a new item with the same data. You can clone all [language variants](#translate-headless-items) of an item or just a selected one.
To clone a headless item:
  1. Open a headless channel application.
  2. [Find](#find-headless-items) the item you want to clone.
  3. Select the **Clone** ([![Clone action](/docsassets/documentation/headless-content/headless_item_clone.png)](/docsassets/documentation/headless-content/headless_item_clone.png)
  4. Enter the new headless item name. This name will be used for all language variants. 
     * The default name is _(Clone) {original name}_.
  5. If you want to clone only the current language variant of the item, clear the **Clone all language variants** checkbox. By default, all language variants are cloned.
  6. Select **Clone**.


The new item is created in the _Draft_ step, where you can [edit](#edit-headless-items) it. The item needs to be [published](#publish-headless-items) to be available through the headless API.
## Find headless items
A headless channel application may contain a large number of headless items. The search bar allows you to find headless items with a name or content type matching the entered text.
You can also **Filter** the list of headless items to display only selected content types, items with specific statuses, or items containing specific tags.
[![Headless items filter](/docsassets/documentation/headless-content/headless_items_filter.png)](/docsassets/documentation/headless-content/headless_items_filter.png)
## Edit headless items
To edit a headless item:
  1. Open a headless channel application.
  2. Select the headless item you want to edit. 
     * Use the search bar or filter to [find headless items](#find-headless-items).
     * If you have [multiple languages](/documentation/developers-and-admins/configuration/languages) set up, select the language in which you want to edit the headless item from the language switcher.
  3. Make sure the headless item is in the _Draft_ status or another [custom workflow step](/documentation/developers-and-admins/configuration/workflows). 
     * When a headless item is _Published_ , select **Edit item** to move the headless item to the _Draft_ step.  
[![Edit headless item](/docsassets/documentation/headless-content/headless_item_edit_button.png)](/docsassets/documentation/headless-content/headless_item_edit_button.png)
     * When a headless item is _Unpublished_ , select **Create new version** to move the headless item to the _Draft_ step.  
[![Create a new version of the headless item](/docsassets/documentation/headless-content/headless_item_create_new_version_button.png)](/docsassets/documentation/headless-content/headless_item_create_new_version_button.png)
  4. Edit the fields of the headless item.
  5. **Save** the changes.


The headless item is now edited.
## Preview headless items
As headless content can have different presentations on multiple platforms, it is not possible to include a preview of headless items directly within Xperience. To see how your content will appear on a specific platform, you need to preview the content directly in the target environment.
You can use the **Preview button** to quickly get to your preview environment. The button links to a single URL, as it is not currently possible to assign a specific URL for each headless item separately.
However, before you can make use of the Preview button, you need to ask your administrators to set up a **[Preview URL](/documentation/developers-and-admins/configuration/headless-channel-management)** in the channel settings. The button is present only once the Preview URL is set up. 
## Revert changes made to a headless item
After creating a new version of a headless item, the item goes through a new editing cycle with the _Draft_ step and optionally other [custom workflow steps](/documentation/developers-and-admins/configuration/workflows).
At any time before the new version is published, you can revert the item back to the last _Published_ status. All changes made in any workflow step will be lost.
  1. Open a headless channel application.
  2. Select the headless item whose changes you want to revert. 
     * Use the search bar or filter to [find headless items](#find-headless-items).
  3. Expand the **Publish** or **Change workflow step** menu.
  4. Select **Revert to published** to revert the changes.


[![Reverting a headless item to its published status](/docsassets/documentation/headless-content/headless_item_revert_to_published.png)](/docsassets/documentation/headless-content/headless_item_revert_to_published.png)
The headless item is now reverted to its _Published_ status.
## Publish headless items
Headless items can be published under the following conditions:
  * If the headless item’s content type is under [workflow](/documentation/developers-and-admins/configuration/workflows), the item must be in the last step of the workflow, or you need to belong to a role with full control for the given workflow (this allows you to publish items from any step).
  * For headless items that are not under workflow, the item must be in the _Draft_ step.


In order to publish a headless item:
  1. Open a headless channel application.
  2. Select the headless item you want to publish. 
     * Use the search bar or filter to [find headless items](#find-headless-items).
  3. Make sure the headless item has the _Draft_ status, or is in a [workflow step](/documentation/developers-and-admins/configuration/workflows) from which you are allowed to publish the item. 
     * When a headless item is _Unpublished_ , select **Create new version** to move the headless item to the _Draft_ step.  
[![Create a new version of the headless item](/docsassets/documentation/headless-content/headless_item_create_new_version_button.png)](/docsassets/documentation/headless-content/headless_item_create_new_version_button.png)
  4. **Publish** the headless item. For items under workflow, select **Change workflow step** and choose the **Publish** step. 
     * If the headless item has fields linking to reusable content items or pages, the cascade publishing dialog opens. You can view all linked items that are currently not _Published_. Select any of the listed items to publish them together with the headless item. To learn more about cascade publishing, see [Work with content items](/documentation/business-users/content-hub/content-items#cascade-publishing).
Cascade publishing is only supported for reusable content items (managed in _Content hub_) and pages linked through the _Combined content selector_ component. Headless items or pages linked through fields using the _Headless item selector_ or _Page selector_ components are not included in the cascade publishing dialog.


The headless item is now  _Published_ and available through the headless API.
## Translate headless items
Before you can translate individual headless items in your project into other languages, you need to set those up in the **Languages** application. For detailed info on how to configure multiple languages in Xperience, see [Languages](/documentation/developers-and-admins/configuration/languages).
Afterward, you can start to translate and create language variants of your items: 
  1. Open a headless channel application.
  2. In the header bar, open the language switcher.  
[![Language switcher in header bar](/docsassets/documentation/headless-content/headless_header_bar_language_switcher.png)](/docsassets/documentation/headless-content/headless_header_bar_language_switcher.png)
The language switcher is available only if two or more languages are set up.
  3. Select the language to translate headless items into.
  4. Select the headless item to translate.
     * Use the search bar or filter to [find headless items](#find-headless-items).
Untranslated headless items are visually marked by the  _Not translated_ (
  5. Enter the translated name of the headless item. You can now choose one of the following options:
     * **Copy content from another language** is the preselected option. By default, the closest language in the [fallback chain](/documentation/developers-and-admins/configuration/languages) is preselected. You can choose a different language from the drop-down list. This option lets you prefill the headless item fields with the content available in the chosen language. If a linked item does not have a translated or published version, a warning will be displayed, and a fallback language variant of the item will be used.
     * **Create empty headless item** allows you to create an empty headless item with blank fields. Linked items need to be re-linked, and files need to be re-uploaded into the new language variant of the translated headless item.  
[![Translating headless item](/docsassets/documentation/headless-content/headless_item_copy_from_existing.png)](/docsassets/documentation/headless-content/headless_item_copy_from_existing.png)
  6. Select **Continue**.
  7. Enter the translated values of the headless item’s fields.
  8. **Save** the translated content item.


You have now successfully translated the headless item. Note that the new language variant is now only a  _Draft_ until you [publish](#publish-headless-items) it.
## Unpublish headless items
Unpublished headless items are not available through the headless API, but you can still access them from the administration.
To unpublish a headless item:
  1. Open a headless channel application.
  2. Select a headless item you want to unpublish. 
     * You can only unpublish headless items that are already _Published_. See how to [publish headless items](#publish-headless-items).
     * Use the search bar or filter to [find headless items](#find-headless-items).
  3. **Unpublish** the headless item.
  4. Expand the **Edit item** menu and select **Unpublish**.  
[![Unpublishing a headless item](/docsassets/documentation/headless-content/headless_item_unpublish.png)](/docsassets/documentation/headless-content/headless_item_unpublish.png)


The headless item is now _Unpublished_.
To edit or re-publish an unpublished headless item, use the **Create new version** action for the headless item. This creates a new version of the item in the  _Draft_ step.
## Restore previous versions of headless items
See [Content versioning](/documentation/business-users/content-versioning#restore-previously-published-versions) for more information. You can restore previously published versions of headless items only when versioning is [configured](/documentation/developers-and-admins/configuration/content-versioning-configuration).
## Delete headless items
In the current version of Xperience by Kentico, deleted headless items can be [restored](/documentation/business-users/recycle_bin). If you’re using a version older than 30.12.0, deleting a headless item is a non-reversible action.
To delete a headless item:
  1. Open a headless channel application.
  2. Locate the headless item you want to delete.
     * Use the search bar or filter to [find headless items](#find-headless-items).
  3. Select **Move to recycle bin** (


[![Delete headless items](/docsassets/documentation/headless-content/headless_item_delete.png)](/docsassets/documentation/headless-content/headless_item_delete.png)
You have successfully deleted the headless item. You can find it in the recycle bin and [restore](/documentation/business-users/recycle_bin#restore-deleted-items) it later.
If you have translated the headless item into multiple languages, this process deletes only the current language variant of the item. In order to delete the headless item completely, you need to delete its variants in all languages. Deleting the last language variant of the headless item deletes it permanently from the system. 
## Restore deleted headless items
The current version of Xperience by Kentico allows you to restore deleted headless items from the recycle bin. See [Restore deleted items](/documentation/business-users/recycle_bin#restore-deleted-items) for more information. If you’re using a version older than 30.12.0, recovering deleted content is not possible.
## Headless items lifecycle
Headless items can have one of the following statuses:
  * **Draft (Initial)** – new headless items that have not been published yet. 
    * Can only be accessed through the headless API when using an [API key](/documentation/developers-and-admins/configuration/headless-channel-management#manage-api-keys) with the _Headless content - Draft_ access type.
  * **Published** – headless items that are ready for use. 
    * You can query published items using the headless API.
  * **Draft (New version)** – headless items that have already been published, but a new version is being edited. 
    * Can only be accessed through the headless API when using an [API key](/documentation/developers-and-admins/configuration/headless-channel-management#manage-api-keys) with the _Headless content - Draft_ access type.
    * If queried with an API key using the _Headless content - Published_ access type, the previously published version is returned.
  * [Custom workflow step](/documentation/developers-and-admins/configuration/workflows) (the item is being edited, “between” the _Draft_ and _Published_ status) 
    * The item is accessible via the headless API as described for the _Draft_ status.
  * **Unpublished** – headless items that were previously published, but are no longer available through the headless API.
  * **Not translated** – headless items that are not yet translated into the currently selected language. 
    * If [language fallbacks](/documentation/developers-and-admins/configuration/languages) are set up, the headless items are accessible through the headless API in their fallback variants.


The following diagram illustrates the default headless items lifecycle in a simplified way:
[![Lifecycle transitions of headless items](/docsassets/documentation/headless-content/final_workflow.png)](/docsassets/documentation/headless-content/final_workflow.png)
![]()
[]()[]()
