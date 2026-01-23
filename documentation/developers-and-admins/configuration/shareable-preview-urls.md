---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/shareable-preview-urls
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * Shareable preview URLs 


# Shareable preview URLs
_Shareable preview URLs_ let editors share the page previews with contributors who can’t access the administration, allowing the external contributors to see the changes that are not yet visible on the live site. Users need the _Read_ [permission](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management) to create a shareable preview URL.
Anybody with the link can access the shareable preview. If your project uses Google Analytics or similar tracking tools, we recommend [excluding](#exclude-shareable-preview-from-analytics) the shareable URLs from tracking to avoid unintentional leaks of the URLs to search engines.
The feature is not available for pages excluded from [content tree-based routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing) and for pages with a [custom authorization scheme](/documentation/developers-and-admins/development/registration-and-authentication#mvc-authorization-flows-and-preview-mode).
The shareable preview URLs are created under the domain where the project is running. If your project limits access to certain environments (_QA_ , _Staging_) and editors generate the URLs from these environments, make sure that the external reviewers can access the shareable preview URLs.
## Expiration
Shareable preview URLs expire after a certain amount of time. The intended workflow is that editors create these URLs for pages in the _Draft_ step and the system automatically invalidates the corresponding URLs when the page is [published](/documentation/business-users/website-content/edit-and-publish-pages#publish-pages). As a safeguard, the URLs also automatically expire after a [configurable](#change-automatic-expiration) expiration period, even if the page hasn’t been published yet.
For completeness, a shareable preview URL is invalidated when any of these conditions is met:
  * The [configured](#change-automatic-expiration) automatic expiration period elapses.
  * A user [stops sharing](/documentation/business-users/website-content#stop-sharing-the-preview-of-a-page) the preview and therefore invalidates all associated shareable preview URLs.
  * The page is published or unpublished.
  * A **new version** of the page is created, for example, by: 
    * [reverting](/documentation/business-users/website-content/edit-and-publish-pages#revert-changes) to the currently published version.
    * [restoring](/documentation/business-users/content-versioning) a previously published version.
    * creating a new version for an [unpublished](/documentation/business-users/website-content/edit-and-publish-pages#unpublish-pages) page.
    * editing a [published page](/documentation/business-users/website-content/edit-and-publish-pages#publish-pages).
  * The page is [deleted](/documentation/business-users/website-content/delete-pages).


When users continue to make edits on a page in the _Draft_ status or a custom workflow step, the URL stays valid and the changes are reflected on the preview if saved. [Scheduling](/documentation/business-users/website-content/edit-and-publish-pages#scheduled-publishing) a page for publishing does not immediately invalidate its shareable preview URLs and they are disabled only when the page is published.
To invalidate all shareable preview URLs in the system, [disable](#disable-shareable-preview) the feature in the **Settings** application.
### URL changes
Shareable preview URLs rely on the page’s [canonical URL](/documentation/business-users/website-content/manage-page-urls). If the page’s canonical URL changes, the preview link stops working. Shareable URLs generated after the change work normally unless further modified. Note that the canonical URL may change when the page is [moved](/documentation/business-users/website-content/edit-and-publish-pages#move-pages) or a parent page is [renamed](/documentation/business-users/website-content/edit-and-publish-pages#rename-pages).
### Change automatic expiration
By default, shareable preview URLs automatically expire after 7 days if not [invalidated earlier](#expiration). To change this setting:
  1. In the **Settings** application, go to **Content** → **URLs and SEO**.
  2. Set the desired **Expiration time** under **Shareable preview URLs**. 
     * You can set ‘0’ to prevent the default automatic expiration, however the URLs can still expire due to [other reasons](#expiration).
  3. **Save** the changes.


From now on, the set automatic expiration applies to newly created shareable URLs. Existing links expire based on the setting that was in place when they were generated.
## Disable shareable preview
By default, shareable preview URLs are enabled. You can disable the feature, which also invalidates all existing shareable URLs in the system.
To disable the shareable preview URLs:
  1. In the **Settings** application, go to **Content** → **URLs and SEO**.
  2. Clear the **Allow shareable preview URLs for pages** checkbox.
  3. **Save** the changes.


## Verify if a page has an active shareable preview URL
To find out if a page preview has any active shareable preview URLs:
  1. Locate the selected page in a website channel application.
  2. Go to the **URLs** tab.
  3. Under **Shareable preview URL** , verify whether the page is shared or not.


## Exclude shareable preview from analytics
If you’re using Google Analytics or other similar tools, we recommend excluding the shareable preview URLs from tracking to prevent unintentional leaking of the URLs to search engines.
You can achieve this, for example, by not including the tracking scripts in the _Views_ for pages in preview mode (impacts both the internal [preview mode](/documentation/business-users/website-content#preview) and the shareable preview):
cshtml
**_ArticleLayout.cshtml**
Copy
```
@using CMS.Websites.Routing
...

@inject IWebsiteChannelContext websiteChannelContext;

...

@if (websiteChannelContext.IsPreview)
{
    // custom logic to prevent the execution of analytics scripts
}
```

If you’re using the [Xperience by Kentico tag manager](https://github.com/Kentico/xperience-by-kentico-tag-manager) integration, you can choose to not include tracking scripts on preview pages, which applies to both the administration and shareable preview.
![]()
[]()[]()
