---
source: https://docs.kentico.com/modules/work-with-website-content/secure-pages
scrape_date: 2026-01-26
---

Module: Work with website content
19 of 20 Pages
# Secure access to pages
This page describes how to restrict access to pages on the live site. For restricting access to pages in the Xperience administration, see [Page permission management](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management).
Pages in the content tree can have their access privileges modified via the **Security** tab.
  1. Open a website channel application.
  2. Select a page in the content tree.
  3. Switch to the **Properties** → **Security** tab.  
[![Page security tab](/docsassets/documentation/secure-pages/SecurityTab.png)](/docsassets/documentation/secure-pages/SecurityTab.png)
  4. Configure the page’s security settings. 
     * **Requires authentication** – only visitors signed in to the website can view the corresponding page.
This setting requires [Registration and authentication](/documentation/developers-and-admins/development/registration-and-authentication) to be implemented on your site. Developers must also [reflect the setting](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#retrieve-page-data) when designing the site’s front end. For example, by displaying a lock icon next to links that lead to such pages and ensuring unauthenticated visitors are redirected appropriately. 
  5. Select **Publish change**. 
     * If securing a page with subpages, you’ll be asked to decide whether the changed settings should apply to all subpages as well.  
[![Secure subpages modal](/docsassets/documentation/secure-pages/secure_subpages_modal.png)](/docsassets/documentation/secure-pages/secure_subpages_modal.png)


You have successfully changed the page’s security configuration. To confirm this you can see a lock icon (
Newly created pages inherit access privileges from their parent page.
[ Previous page ](/modules/work-with-website-content/securing-page-introduction)
19 of 20
[ Mark complete and continue ](/modules/work-with-website-content/final)
![]()
[]()[]()
