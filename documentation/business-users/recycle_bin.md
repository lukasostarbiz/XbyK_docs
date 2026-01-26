---
source: https://docs.kentico.com/documentation/business-users/recycle_bin
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Business users](/documentation/business-users)
  * Recycle bin 


# Recycle bin
The recycle bin feature and restoration of deleted content are available in the current version of Xperience by Kentico. If you’re using a version older than 30.12.0, deleted content cannot be recovered.
The recycle bin allows you to recover content that you accidentally removed. When you delete a [page](/documentation/business-users/website-content), a [content item](/documentation/business-users/content-hub/content-items), an [email](/documentation/business-users/digital-marketing/emails) or a [headless item](/documentation/business-users/headless-content), it is moved to the recycle bin instead of being permanently deleted.
## Restore deleted items
To restore an item from the recycle bin:
  1. Navigate to the **Recycle bin** application.
  2. Select the item you want to restore. 
     * The items in the recycle bin are [permanently deleted](#permanently-delete-items-from-recycle-bin) after a period of time set by your administrator, so some items might no longer be available.
     * You can only see the items deleted by you.
  3. **Restore** (
     * [Contact activities](/documentation/business-users/digital-marketing/emails/track-email-statistics#contact-activities-for-emails) and [statistics](/documentation/business-users/digital-marketing/emails/track-email-statistics) won’t be available for restored emails.
     * When a restored page or content item was linked in other content, those references will also be restored. See [References to restored items](#references-to-restored-items) for more information.
     * References to restored headless items are not restored.
  4. Confirm the dialog to **Restore** the item.


The item is now restored to the initial _Draft_ step in its original location. If a content item was originally in a [folder](/documentation/business-users/content-hub/content-hub-folders#content-folders) that has since been deleted, you can find it under **All content items**.
If you’re not able to restore the item, see the [Restore troubleshooting](#restore-troubleshooting) section.
**Restored items can be used in other content**
Check where restored pages and content items are used before publishing them, for example, on the _Used in_ tab (see [Track page usages](/documentation/business-users/website-content/track-page-usages) and [Track usage of content items](/documentation/business-users/content-hub/content-items#track-usage-of-content-items)). Not doing so can lead to the item appearing in unwanted places or to problems with publishing other content. See [References to restored items](#references-to-restored-items) for more information.
### References to restored items
A [page](/documentation/business-users/website-content) or a [content item](/documentation/business-users/content-hub/content-items) can be used in other content (pages, content items, emails, headless items) before it’s deleted. When the page or the content item is later restored, these references are restored as well. Once the restored item is published, other content can display the restored item.
For example, a home page links to other pages to show a preview of promoted articles. One of these article pages is deleted and since then, its preview is no longer visible on the home page. Later, the deleted article is restored. When it’s published, it appears on the live home page again.
This can create problems, for example if the article is outdated, or if the homepage is now configured to display fewer articles. For this reason, it’s necessary to **check the usages of the restored item before publishing it**. See [Track page usages](/documentation/business-users/website-content/track-page-usages) and [Track usage of content items](/documentation/business-users/content-hub/content-items#track-usage-of-content-items).
Hypertext links to items in the [rich text editor](/documentation/business-users/rich-text-editor) are not restored if they were removed.
References to headless items are not restored.
### Emails
Although [emails](/documentation/business-users/digital-marketing/emails) cannot be used in other pieces of content, they can be selected to be automatically sent when certain actions happen, for example as an [autoresponder email](/documentation/business-users/digital-marketing/forms/create-and-edit-forms) for [form submissions](/documentation/business-users/digital-marketing/forms). These selections are not automatically restored. To use the restored email again, you need to publish it and assign it again in the corresponding application.
Note that [statistics](/documentation/business-users/digital-marketing/emails/track-email-statistics) and [contact activities](/documentation/business-users/digital-marketing/emails/track-email-statistics#contact-activities-for-emails) are not available for restored emails.
## Permanently delete items from recycle bin
If you are sure you no longer need an item in the recycle bin, you can delete it permanently. If you don’t do it yourself, the item will be permanently deleted by the system after a time period set by your administrator.
To permanently delete an item from the recycle bin:
  1. Navigate to the **Recycle bin** application.
  2. Select the item you want to permanently delete.
  3. **Delete** (
  4. Confirm the dialog to **Delete permanently**.


The item is now permanently deleted and cannot be restored.
## Restore troubleshooting
If an item has been deleted for a long time, changes in other content might occur, which can sometimes prevent the item from being restored.
The following table lists the causes and the options you can try to restore the item.
Content items, headless items, pages  
---  
**Cause** |  **Solution**  
Existing variant in the same language |  A new language variant in the same language was created since you deleted the item. The variant in the recycle bin can be restored when the existing one is deleted. Keep in mind that other users might be working on the currently used language variant.  
Pages  
**Cause** |  **Solution**  
Missing parent page |  If the page was created under a parent page, it cannot be restored without the parent. If the parent page is available in the recycle bin, try restoring it first.  
Missing _Create_ permission |  Your permissions changed since you deleted the item. If you no longer have the _Create_ permission for the page’s original location, you cannot restore the page. Contact your administrator to resolve the situation.  
Page scope change |  The [scopes](/documentation/developers-and-admins/development/content-types/limit-the-pages-users-can-create) where pages of certain content types can be created have changed. Contact your administrator.  
URL collision |  An existing page is using the same [URL](/documentation/business-users/website-content/manage-page-urls) as the one you are trying to restore. This issue can be resolved by changing the conflicting URL of the existing page. However, it is not currently possible to view the content of deleted items. Ask your administrator or a developer for assistance.  
In some cases, it is no longer possible to restore the item. Emails cannot be restored if their template (excluding [preset templates](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates#save-emails-as-preset-email-templates)), [recipient list](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-recipient-lists), or an associated [contact group](/documentation/business-users/digital-marketing/contact-groups) were deleted. Content items can no longer be restored when the [workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces) they were removed from was deleted.
![]()
[]()[]()
