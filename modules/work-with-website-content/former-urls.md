---
source: https://docs.kentico.com/modules/work-with-website-content/former-urls
scrape_date: 2026-01-22
---

Module: Work with website content
17 of 20 Pages
# Manage former URLs
The system can automatically keep track of page URL changes by storing old URLs and handling redirects to the current URLs. You can see and manage these redirects in the **URLs** application on the **Former URLs** tab. Redirects created by unpublishing or deleting pages are stored and handled by default. You can additionally configure the system to create former URLs when a URL is edited and to redirect to the current URLs.
When a redirect target of a former URL is changed, all redirects that target the corresponding page are also automatically adjusted.
### Automatically store edited URLs as former URLs
To configure the system to automatically store edited URLs as former URLs:
  1. Open the **Channel management** application.
  2. Select the website channel for which you want to configure Former URLs to store and handle edited URLs.
  3. Open **Channel settings**.
  4. Select the **Store edited URLs as former URLs** setting.
  5. **Save** the changes.


Former URLs are now created when URLs are edited in the given website channel. Every time a former URL is requested, the system returns a “301 Moved Permanently” response and redirects to the new URL of the page.
If the setting is disabled later, former URLs created up to that point will continue redirecting visitors to current URLs unless the former URLs are [removed](#remove-former-urls-created-by-editing-urls).
To access a list of all stored old URLs for the website, together with the current URL paths to which they are redirected, open the **Former URLs** tab in the **URLs** application.
[![Former URLs tab](/docsassets/documentation/manage-page-urls/former-URLs.png)](/docsassets/documentation/manage-page-urls/former-URLs.png)
### Former URLs scenarios
Former URLs are created when you unpublish or delete a page and also create a redirect from the page. Additionally, you can configure the system to create former URLs when modifying page URLs.
Former URLs are always created when: |  If [configured](#automatically-store-edited-urls-as-former-urls) to store edited URLs, former URLs are created when:  
---|---  
  * Unpublishing a page and creating a redirect to another page. 
    * If a page is scheduled to be unpublished, the former URL is created when the unpublishing occurs.
  * Deleting a page and creating a redirect to another page.

| 
  * Changing a page’s URL by: 
    * Editing the URL slug for the given page or one of its parent pages. 
      * The former URL is created only after you publish the page with the new URL.
    * Moving the page across the content tree. 
      * In this case the former URL is created immediately.
    * Editing or deleting a page’s vanity URL. 
      * The former URL is created only after you publish the page with the new URL.

  
Former URLs are **NOT** created when:
  * Deleting a page without creating a redirect. 
    * [Deleting a page](/documentation/business-users/website-content/delete-pages) without creating a redirect **permanently destroys** **and removes** all former URLs pointing to that page.
  * Unpublishing a page and choosing to serve content in the fallback language.
  * Unpublishing a page without creating a redirect.
  * Administrators perform certain global changes, for example changing the domain of a website channel.
  * Changing a [language](/documentation/developers-and-admins/configuration/languages) code name.
  * Changing the _Primary language_ setting for a website channel.


Changing the language code name or the primary language setting breaks all former URLs for the corresponding language variants of pages.
### Remove former URLs created by editing URLs
This action is available only for former URLs [created](#former-urls-scenarios) by editing a page’s URL or by deleting a page. Their remove button is active (
To remove a former URL:
  1. Open the **URLs** application.
  2. Go to the **Former URLs** tab.
  3. Remove (


The former URL is now permanently removed.
[ Previous page ](/modules/work-with-website-content/prevent-url-collision)
17 of 20
[ Mark complete and continue ](/modules/work-with-website-content/securing-page-introduction)
![]()
[]()[]()
