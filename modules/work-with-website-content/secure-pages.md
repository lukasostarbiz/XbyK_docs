# Secure access to pages
  * [ Copy page link ](modules/work-with-website-content/secure-pages#) | [Get HelpService ID](modules/work-with-website-content/secure-pages#)
Core MVC 5


[✖](modules/work-with-website-content/secure-pages# "Close page link panel") [Copy to clipboard](modules/work-with-website-content/secure-pages#)
This page describes how to restrict access to pages on the live site. For restricting access to pages in the Xperience administration, see [Page permission management](documentation/developers-and-admins/configuration/users/role-management/page-permission-management).
Pages in the content tree can have their access privileges modified via the **Security** tab.
  1. Open a website channel application.
  2. Select a page in the content tree.
  3. Switch to the **Properties** → **Security** tab.  
[![Page security tab](docsassets/documentation/secure-pages/SecurityTab.png)](https://docs.kentico.com/docsassets/documentation/secure-pages/SecurityTab.png)
  4. Configure the page’s security settings. 
     * **Requires authentication** – only visitors signed in to the website can view the corresponding page.
This setting requires [Registration and authentication](documentation/developers-and-admins/development/registration-and-authentication) to be implemented on your site. Developers must also [reflect the setting](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#retrieve-page-data) when designing the site’s front end. For example, by displaying a lock icon next to links that lead to such pages and ensuring unauthenticated visitors are redirected appropriately. 
  5. Select **Publish change**. 
     * If securing a page with subpages, you’ll be asked to decide whether the changed settings should apply to all subpages as well.  
[![Secure subpages modal](docsassets/documentation/secure-pages/secure_subpages_modal.png)](https://docs.kentico.com/docsassets/documentation/secure-pages/secure_subpages_modal.png)


You have successfully changed the page’s security configuration. To confirm this you can see a lock icon (
Newly created pages inherit access privileges from their parent page.
[ Previous page ](modules/work-with-website-content/securing-page-introduction)
19 of 20
[ Mark complete and continue ](modules/work-with-website-content/final)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/work-with-website-content/secure-pages)
[](https://docs.kentico.com/modules/work-with-website-content/secure-pages)[](https://docs.kentico.com/modules/work-with-website-content/secure-pages)