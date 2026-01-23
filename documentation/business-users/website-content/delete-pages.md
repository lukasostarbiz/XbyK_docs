---
source: https://docs.kentico.com/documentation/business-users/website-content/delete-pages
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Business users](/documentation/business-users)
  * [Website content](/documentation/business-users/website-content)
  * Delete pages 


# Delete pages
In the current version of Xperience by Kentico, deleted pages can be [restored](/documentation/business-users/recycle_bin). If you’re using a version older than 30.12.0, deleting a page is a non-reversible action.
To delete a page:
  1. Open a website channel application.
  2. Locate the page or section you want to delete in the content tree.
  3. Open the **More actions** (**Move to recycle bin** to open the deletion dialog. 
     * If the deleted page is used in other content, you may want to [check the usage](/documentation/business-users/website-content/track-page-usages) of the page and edit all content where it is used.
[![Delete a page](/docsassets/documentation/delete-pages/delete_page.png)](/docsassets/documentation/delete-pages/delete_page.png)
  4. Choose where to redirect visitors when they try to reach the deleted page. Your options depend on the page’s language variants. 
     * If a page variant in a fallback language is available, you need to select one of the two options below. 
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
       2. Leave the page without a redirect. Visitors will receive a [404 error](https://en.wikipedia.org/wiki/HTTP_404) when they try to reach the deleted page.
If you decide not to create a redirect from the deleted page, it cannot be added later in the admin UI. See [Redirects from deleted pages](#redirects-from-deleted-pages) for other options. If you delete a page targeted by redirects, the redirect configuration of the deleted page can affect redirects from other pages. See section [Unpublishing or deleting pages targeted by redirects](/documentation/business-users/website-content/edit-and-publish-pages#unpublishing-or-deleting-pages-targeted-by-redirects) for details. 
  5. Check the pages you are about to delete and confirm the dialog to delete the pages.


You have successfully deleted the page. Visitors trying to access the page are redirected according to the options you chose. You can find the page in the recycle bin and [restore](/documentation/business-users/recycle_bin#restore-deleted-items) it later.
If you have translated the page into multiple languages, this process deletes only the current language variant of the page. In order to delete the page completely, you need to delete its variants in all languages. Deleting the last language variant of the page deletes it permanently from the content tree.
## Redirects from deleted pages
Using redirects from deleted pages can prevent visitors from getting a 404 error when they try to access deleted pages. You can edit and delete redirects in the [URLs](/documentation/business-users/website-content/manage-page-urls#manage-redirects-from-deleted-pages) application on the **Former URLs** tab. If a redirect from a deleted page was never created or has been deleted, it cannot be added in the admin UI. However, the redirect can be added via [code](/documentation/developers-and-admins/development/routing/custom-redirects), for example by developers. Visitors who try to access a deleted page without a redirect will be served content in a fallback language if available. Otherwise, they receive the 404 error.
If a page’s language variant is deleted with a redirect and the page is later translated again into the same language, the redirect is deleted.
For more information about redirects, see [Edit and publish pages](/documentation/business-users/website-content/edit-and-publish-pages#redirects-from-unpublished-and-deleted-pages).
## Deleting a page with child pages
Deleting a page that has only one language variant also deletes its child pages. When deleting such a page, all pages that are going be deleted can be seen in a listing in the sidebar dialogue. The listing shows all language variants that are about to be deleted. You can see existing redirects from pages that are [unpublished](/documentation/business-users/website-content/edit-and-publish-pages#unpublish-pages) or scheduled to be unpublished ( 
You can open ([unpublishing](/documentation/business-users/website-content/edit-and-publish-pages#unpublish-pages) or deleting the page, or to [adjust](/documentation/business-users/website-content/manage-page-urls#manage-redirects-from-unpublished-pages) an existing redirect if the page is already unpublished.
We recommend deleting pages individually to avoid mistakes.
[![Delete a page with child pages](/docsassets/documentation/delete-pages/mass_delete.png)](/docsassets/documentation/delete-pages/mass_delete.png)
## Restore deleted pages
The current version of Xperience by Kentico allows you to restore deleted pages from the recycle bin. See [Restore deleted items](/documentation/business-users/recycle_bin#restore-deleted-items) for more information. If you’re using a version older than 30.12.0, recovering deleted content is not possible.
![]()
[]()[]()
