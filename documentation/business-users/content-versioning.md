---
source: https://docs.kentico.com/documentation/business-users/content-versioning
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Business users](/documentation/business-users)
  * Content versioning 


# Content versioning
When content versioning is [enabled](/documentation/developers-and-admins/configuration/content-versioning-configuration), the system automatically stores the previously published versions of [reusable content items](/documentation/business-users/content-hub/content-items), [pages](/documentation/business-users/website-content), and [headless items](/documentation/business-users/headless-content). Other types of content, such as forms and emails, are not versioned.
Versioning allows users to:
  * [view previously published versions](#view-version-history) of versioned items
  * [restore previously published versions](#restore-previously-published-versions) of versioned items
  * find out who and when made changes to versioned items
  * view the history of the following actions: 
    * [unpublishing](/documentation/business-users/website-content/edit-and-publish-pages#unpublish-pages)
    * scheduled [publishing](/documentation/business-users/website-content/edit-and-publish-pages#scheduled-publishing) and [unpublishing](/documentation/business-users/website-content/edit-and-publish-pages)
    * canceling of scheduled publishing and unpublishing


## Version history
The version history of an item allows you to [view](#view-version-history) the list of previously published versions and other records. Published versions ( 
Version history includes other records that are created when the item is unpublished ( _Save_ , _Discard_ , or moving to workflow steps other than _Publish_ and _Unpublish_ , are not tracked.
Versions and records are identified by the date and time when they were automatically created. Each version also contains the name of the user who made the changes.
[![Version history](/docsassets/documentation/content-versioning/version-history.png)](/docsassets/documentation/content-versioning/version-history.png)
### Linked content
Linked items are shown and restored in their most recently published version. Fields of an item that reference other content that exists outside of the given item (for example, media files, tags, smart folders) also show the current version of the content, which is used when the item is restored. Note that the current version of the content that is displayed can differ from the version that was used when the selected version of the item was published.
### Assets
[Files](/documentation/business-users/content-hub/content-item-assets) uploaded to content items (for example, photos, videos, or documents) are not versioned. That means that files are always shown and restored in their latest version and previous versions aren’t accessible.
### Page Builder
If a [page](/documentation/business-users/website-content) contains [Page Builder](/documentation/business-users/website-content/widgets-and-page-builder) data, the data is displayed in a separate section and structured according to the sections and widgets used in the Page Builder. The record also includes page template properties at the bottom of this section. Content from inline editors is displayed as plain text.
## Versioning coverage
Versioning has to be [configured](/documentation/developers-and-admins/configuration/content-versioning-configuration) to be applied. The number of versions stored in the system is set during the configuration.
  * **Versioned data**
    * data from the _Content_ tab, except for [assets](#assets)
    * data from _Page Builder_ tab (applies only to pages)
  * **Examples of non-versioned data include** : 
    * [assets](#assets)
    * all [page URLs](/documentation/business-users/website-content/manage-page-urls)
    * positions of pages in the content tree
    * [page permissions](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management)


## View version history
To view version history of a [reusable content item](/documentation/business-users/content-hub/content-items), [page](/documentation/business-users/website-content), or [headless item](/documentation/business-users/headless-content):
  1. Open Content hub or the corresponding channel application where the item is located.
  2. Find and select the item for which you want to view the version history.
  3. Expand the action menu and select **Show version history**.
     * The **Show version history** option is available in any workflow step.


[![action menu](/docsassets/documentation/content-versioning/action_menu.png)](/docsassets/documentation/content-versioning/action_menu.png)
You can now browse the version history. Selecting a published version in the version history allows you to view the data that was used in that version. Assets, linked items, and any content that exists outside of this item are shown in their [current version](#linked-content).
[![Version history](/docsassets/documentation/content-versioning/version-history-highlighted.png)](/docsassets/documentation/content-versioning/version-history-highlighted.png)
## Restore previously published versions
When restoring a version, the item is restored to the _Draft_ step with the data from the selected version. Changes that weren’t published at the time of restoring will be lost.
To restore a version of a [reusable content item](/documentation/business-users/content-hub/content-items), [page](/documentation/business-users/website-content), or [headless item](/documentation/business-users/headless-content):
  1. [Open the version history](#view-version-history) of the item whose previous version you want to restore.
  2. Select the version you want to restore in the version history. 
     * Only published versions can be restored. 
For [linked items](/documentation/business-users/content-hub/content-items#link-content-items) and [assets](/documentation/business-users/content-hub/content-item-assets), the latest published version is used. Other [content](#linked-content) that exists outside of the item is restored in its current version. If the underlying [content type](/documentation/developers-and-admins/development/content-types) was modified since the version you want to restore was published, some fields might be [affected](#modifications-of-content-types-of-versioned-items). To learn more about the versioning coverage, see the [Versioning coverage](#versioning-coverage) section. 
  3. Select **Restore this version**.
  4. Confirm restoration of this version by selecting **Restore version** in the dialog window.


The item is always restored to the _Draft_ step with the data from the selected version. You can edit the item and the restored version is not visible until you publish it.
### Modifications of content types of versioned items
Modifying a [content type](/documentation/developers-and-admins/development/content-types) of a versioned item can influence what happens when restoring a version of an item of the modified content type.
#### Field addition
If a new required field is added to an existing [content type](/documentation/developers-and-admins/development/content-types), and you [restore a version](#restore-previously-published-versions) that didn’t have this field, this field is populated with the most recently used value of this field. That would be either the value from the last published version or from the _Draft_ that is currently edited.
When an optional field is added to the content type, it is left empty in versions that didn’t have this field.
#### Modification of a field data type
When a field’s [data type](/documentation/developers-and-admins/customization/field-editor/data-type-management) is changed, and both data types are compatible (for example, a date and text), the modified fields are filled in when restoring a previous version. If the data types are not compatible (for example, a text field changed to a checkbox), the given field is left empty.
#### Field removal
When a field is removed, the previous version is restored without the deleted field and without the data from this field.
![]()
[]()[]()
