---
source: https://docs.kentico.com/documentation/business-users/content-hub/content-hub-folders
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Business users](/documentation/business-users)
  * [Content hub](/documentation/business-users/content-hub)
  * Content hub folders 


# Content hub folders
Content hub allows you to create folders that help organize and efficiently navigate through [content items](/documentation/business-users/content-hub/content-items). The following types of folders are available:
  * [Smart folders](#smart-folders)
  * [Content folders](#content-folders)


## Smart folders
Smart folders provide a dynamic way to organize and filter [content items](/documentation/business-users/content-hub/content-items). Each smart folder is based on a filtering condition, such as “items modified in the last 7 days”, “items with the _Draft_ status”, “items with the _Acme_ tag”, etc. The condition is evaluated dynamically, so items move in and out of smart folders as their fields and metadata change. A single content item can belong into any number of smart folders.
Smart folders can be used to organize content items in the administration interface, and also to dynamically control [content delivery](#enable-content-delivery-for-smart-folders) and filter content. Out-of-the-box, Xperience allows dynamic content delivery via smart folders for website channels (headless and email channels are currently not supported).
### Create smart folders
To create a smart folder:
  1. Select the **Create new smart folder** (_Smart folders_ section in the left-side panel.  
[![Creating a new smart folder](/docsassets/documentation/content-hub-folders/CH_create_new_smart_folder.png)](/docsassets/documentation/content-hub-folders/CH_create_new_smart_folder.png)
  2. Choose whether to **Enable dynamic content delivery** for the smart folder.
     * See [Enable content delivery for smart folders](#enable-content-delivery-for-smart-folders) for detailed information.
     * If you’re not sure, you can enable content delivery at any point after you create the folder.
  3. Set up the folder’s filter conditions.
  4. Select **Continue**.
  5. Enter a **Smart folder name**.
  6. Select **Save**.


Alternatively, you can **Save** any [filter](/documentation/business-users/content-hub/content-items#find-content-items) applied to the content hub listing as a new smart folder.
[![Saving a filter as a new smart folder](/docsassets/documentation/content-hub-folders/CH_save_filter_as_smart_folder.png)](/docsassets/documentation/content-hub-folders/CH_save_filter_as_smart_folder.png)
Note that the smart folder only saves the **Filter** conditions. Search inputs are not reflected by the new smart folder.
When you select a smart folder in the left-side panel, the listing displays all content items that currently match the folder’s filter conditions. In addition to the Content hub application, smart folders are also available in all content item selectors throughout the system.
### Clone smart folders
When creating new smart folders, you can save time by reusing the filter conditions of an existing folder.
  1. Expand the **Menu actions** (
  2. Select **Clone**.
  3. Choose whether to [Enable dynamic content delivery](#enable-content-delivery-for-smart-folders) for the new folder.
  4. Select **Clone** in the confirmation dialog.


You can then adjust the new folder’s condition as required.
### Edit smart folders
You can adjust a smart folder’s filter conditions at any time:
  1. Select the smart folder in the left-side panel.
  2. Select the **Filter** button or any filter summary item displayed in the **Applied filters** section above the listing.
  3. Change the filter conditions as required.
  4. Select **Apply**.
  5. Check the results in the listing and select **Save changes**.


[![Saving adjusted filter conditions for a smart folder](/docsassets/documentation/content-hub-folders/CH_save_changes_smart_folder.png)](/docsassets/documentation/content-hub-folders/CH_save_changes_smart_folder.png)
To change a folder’s name, expand the **Menu actions** (**Rename**.
### Enable content delivery for smart folders
Dynamic content delivery allows developers to retrieve content items matching a smart folder’s filter criteria. This gives you the power to control which items are delivered by editing the filter conditions of smart folders directly in the _Content hub_ UI, without needing to adjust code. Out-of-the-box, Xperience allows smart folder content delivery for website channels (headless and email channels are currently not supported).
Note that smart folders with dynamic content delivery have the following limitations on their filter conditions:
  * **Content type** – most content delivery scenarios will be limited to a specific content type or content types that share a [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas). This means that the final content delivered through the smart folder may not include items from all selected content types. Consult your project’s developers if you are not sure.
  * **Status** , **Requires authentication** , **Last modified** – these filter options are not supported for smart folders with dynamic content delivery. Retrieval of content based on published status or authentication requirements is controlled by developers, and the _Last modified_ value is not intended for public use outside of the administration interface.


The **order** and **maximum number** of retrieved items is controlled by developers, and you cannot configure these parameters through a smart folder’s filter conditions or other settings.
To enable dynamic content delivery for a smart folder:
  * When [creating new smart folders](#create-smart-folders), select the **Enable dynamic content delivery** option at the top of the filter conditions.
  * For existing folders, expand the **Menu actions** (**Enable dynamic content delivery**.


Be careful when [editing](/documentation/business-users/content-hub/content-items#edit-content-items) or [deleting](/documentation/business-users/content-hub/content-items#delete-content-items) items that are included in a smart folder with dynamic content delivery. Changes may affect how the content is displayed in your website channels.
You cannot disable content delivery for a smart folder after you choose to enable it. If necessary, you can clone the folder to create a new copy without dynamic content delivery – expand the **Menu actions** (**Clone**.
### Select smart folders in content
When [editing](/documentation/business-users/content-hub/content-items) content, you may encounter fields that allow you to select a smart folder (depending on your project’s content model). For example, you could find smart folder selectors among the fields of content items and [pages](/documentation/business-users/website-content), or in the Page Builder when configuring the properties of a [widget](/documentation/business-users/website-content/widgets-and-page-builder).
[![Selecting a smart folder](/docsassets/documentation/content-hub-folders/CH_smart_folder_selector.png)](/docsassets/documentation/content-hub-folders/CH_smart_folder_selector.png)
After you select a folder, developers can then use the field to [retrieve content items from the selected smart folder](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-content-items-from-smart-folders) and display or otherwise use them in the related channel.
## Content folders
Content folders may be disabled or enabled for your project. If the folders are not available and you wish to use them, contact your administrators or see the [Content hub configuration](/documentation/developers-and-admins/configuration/content-hub-configuration#enable-content-hub-folders) documentation.
You can use a hierarchy of content folders to catalog [content items](/documentation/business-users/content-hub/content-items). Folders can greatly help you organize and efficiently navigate through content items in the administration interface, particularly when your project contains a large number of items.
Unlike with [smart folders](#smart-folders), content items are placed into content folders manually, and each item can only be located in one content folder.
The purpose of content folders is only to help organize content items in the administration, and they **cannot be used to control content delivery**.
[![Overview of Content folder actions](/docsassets/documentation/content-hub-folders/CH_folder_actions.png)](/docsassets/documentation/content-hub-folders/CH_folder_actions.png)
  * To add a content folder, use the **Create new folder** (
    * The system supports up to 20 levels of subfolders.
  * Expand the **Menu actions** (
    * [workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces) (together with all subfolders and contained items)
    * _Properties_ (identifiers, created and last modified timestamps, other metadata)
  * If you select a folder in the tree, the listing on the right only displays items located directly in the given folder. You can use the search function to [find](/documentation/business-users/content-hub/content-items#find-content-items) items, including subfolders.
  * To access the full list of all content items in the current workspace (from all folders), select **All content items** above the folder tree.
  * When [creating content items](/documentation/business-users/content-hub/content-items#create-content-items), you can choose the folder **Location** for each new item.
  * You can [move items](/documentation/business-users/content-hub/content-items#move-content-items) between content folders or workspaces at any time using the **Move** (


![]()
[]()[]()
