---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/recycle-bin-management
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * Recycle bin management 


# Recycle bin management
Recycle bin preserves deleted [pages](/documentation/business-users/website-content), [content items](/documentation/business-users/content-hub/content-items), [emails](/documentation/business-users/digital-marketing/emails) and [headless items](/documentation/business-users/headless-content), allowing users to recover content they accidentally deleted.
## Adjust the retention policy
The system permanently deletes items in the recycle bin after a period of time specified by the retention policy to prevent content from accumulating. By default, deleted items are kept for 30 days.
To adjust the policy:
  1. Open the **Settings** application.
  2. Go to the **Content → Recycle bin** category.
  3. Set the number of days after which an item in the recycle bin will be permanently deleted.
  4. **Save** the changes.


**Choosing the policy**
When choosing the retention policy, balance the needs of the users and the available storage space. Note that [assets](/documentation/business-users/content-hub/content-item-assets), such as pictures or videos, uploaded to content items remain on the file system until the item is deleted permanently.
Setting the policy limit too low can lead to the loss of content that users want to restore, while setting it too high can result in consuming too much storage space if the items in the bin include large assets.
## Permissions
Users need to be granted the _View_ permission to access the recycle bin application and work with the items in the recycle bin. Each user with a _View_ permission can perform these actions for the items they deleted:
  * [restore](/documentation/business-users/recycle_bin#restore-deleted-items) an item
  * [permanently delete](/documentation/business-users/recycle_bin#permanently-delete-items-from-recycle-bin) an item


The administrator can work with items deleted by any user.
![]()
[]()[]()
