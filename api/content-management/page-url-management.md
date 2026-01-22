---
source: https://docs.kentico.com/api/content-management/page-url-management
scrape_date: 2026-01-22
---

  * [Home](/api)
  * [Content management](/api/content-management)
  * Page URL management 


# Page URL management
  
List of examples:
  * [Dependency injection](#dependency-injection)
    * [Initialize required services](#initialize-required-services)
  * [Manage page URLs](#manage-page-urls)
    * [Edit the URL slug](#edit-the-url-slug)
    * [Add a vanity URL](#add-a-vanity-url)
    * [Edit an existing vanity URL](#edit-an-existing-vanity-url)
    * [Remove a vanity URL](#remove-a-vanity-url)
    * [Select the canonical URL of a page](#select-the-canonical-url-of-a-page)
  * [Custom redirects](#custom-redirects)
    * [Create redirects using IWebPageRedirectManager](#create-redirects-using-iwebpageredirectmanager)


## Dependency injection
### Initialize required services
C#
Copy
```
// Initializes all services and provider classes used within
// the API examples on this page using dependency injection
private readonly IWebPageManager webPageManager;
private readonly IWebPageUrlPathDataRetriever webPageUrlPathDataRetriever;
private readonly IWebPageRedirectManager webPageRedirectManager;
private readonly IContentQueryExecutor contentQueryExecutor;

public PageUrlsServices(IContentQueryExecutor contentQueryExecutor,
                    IWebPageManagerFactory webPageManagerFactory,
                    IWebPageUrlPathDataRetriever webPageUrlPathDataRetriever,
                    IUserInfoProvider userInfoProvider,
                    IWebsiteChannelContext websiteChannelContext,
                    IWebPageRedirectManager webPageRedirectManager)
{
    // Gets the user responsible for operations done through webPageManager.
    // The user is used only for auditing purposes and the user's permissions
    // are not checked for any of the operations.
    UserInfo user = userInfoProvider.Get("JimmieWonka");

    // Creates an instance of the manager class facilitating page operations
    this.webPageManager = webPageManagerFactory.Create(websiteChannelContext.WebsiteChannelID, user.UserID);
    this.webPageUrlPathDataRetriever = webPageUrlPathDataRetriever;
    this.webPageRedirectManager = webPageRedirectManager;
    this.contentQueryExecutor = contentQueryExecutor;
}
```

[> Back to list of examples](#toc)
## Manage page URLs
### Edit the URL slug
C#
Copy
```
// This example demonstrates two ways to edit the URL slug of a page. The first method
// retrieves the URL path data of a page, updates the data, and uses the data to create
// a new instance of 'UpdateDraftData'. The second method directly initializes a new
// instance of 'UpdateDraftData' with the new slug as an argument without using the URL
// path data. The first method can be easily expanded to accommodate other operations
// with URL page data.

// Language variant of the page to be updated
string languageName = "en";

// Id of the page to be updated
int pageId = 1;

// Creates a new draft version of the page
await webPageManager.TryCreateDraft(pageId, languageName);

// Retrieves the editable page URL path data. The page identified by the page id has to be in the
// draft step, otherwise an exception is thrown.
WebPageUrlPathData urlPathData = await webPageUrlPathDataRetriever.GetDraftData(pageId,
                                                                         languageName,
                                                                         CancellationToken.None);

// The new URL slug of the page
string newSlug = "wonka-chocolate";

// Updates the URL path data with the new slug. Forbidden characters are replaced according
// to the settings of the system.
urlPathData.EditSystemUrlSlug(newSlug);

// Updates the draft with the edited page URL path data
await webPageManager.TryUpdateDraft(pageId, languageName, new UpdateDraftData(urlPathData));

// Demonstrates the second method of editing the URL slug without using WebPageUrlPathData.
// Useful when you only need to update the URL slug of the page.
string anotherNewSlug = "wonka-sweets";
await webPageManager.TryUpdateDraft(pageId, languageName, new UpdateDraftData(anotherNewSlug));

// Publishes the page to apply the change
await webPageManager.TryPublish(pageId, languageName);
```

[> Back to list of examples](#toc)
### Add a vanity URL
C#
Copy
```
// This example demonstrates how to add a new vanity URL to a page

// Language variant of the page to be updated
string languageName = "en";

// Id of the page to be updated
int pageId = 1;

// Creates a new draft version of the page
await webPageManager.TryCreateDraft(pageId, languageName);

// Retrieves the editable page URL path data. The page identified by the page id
// has to be in the draft step, otherwise an exception is thrown.
WebPageUrlPathData urlPathData = await webPageUrlPathDataRetriever.GetDraftData(pageId,
                                                                         languageName,
                                                                         CancellationToken.None);

// Adds the new vanity URL path to the page's URL path data. Forbidden characters
// are replaced according to the settings of the system.
urlPathData.AddVanityUrlPath("limited-edition");

// Updates the draft with the edited page URL path data
await webPageManager.TryUpdateDraft(pageId, languageName, new UpdateDraftData(urlPathData));

// Publishes the page to apply the change
await webPageManager.TryPublish(pageId, languageName);
```

[> Back to list of examples](#toc)
### Edit an existing vanity URL
C#
Copy
```
// This example demonstrates how to edit an existing vanity URL

// Language variant of the page to be updated
string languageName = "en";

// Id of the page to be updated
int pageId = 1;

// Creates a new draft version of the page
await webPageManager.TryCreateDraft(pageId, languageName);

// Retrieves the editable page URL path data. The page identified by the page id
// has to be in the draft step, otherwise an exception is thrown.
WebPageUrlPathData urlPathData = await webPageUrlPathDataRetriever.GetDraftData(pageId,
                                                                         languageName,
                                                                         CancellationToken.None);
// Existing vanity URL path to be edited
string oldVanityUrlPath = "excellent-chocolate";

// New vanity URL path
string newVanityUrlPath = "brilliant-chocolate";

// Resolves the service that replaces forbidden characters in vanity URLs according to the
// settings of the system.
var vanityUrlPathNormalizer = Service.Resolve<IVanityUrlPathNormalizer>();

// Normalizes the existing vanity path by replacing forbidden characters according
// to the system settings. This step is optional if you know the exact vanity URL path.
// The system automatically replaces forbidden characters when adding a vanity URL,
// which may cause the stored path to differ from the original input.
oldVanityUrlPath = vanityUrlPathNormalizer.Normalize(oldVanityUrlPath);

// Replaces the old vanity URL path with the new one. The forbidden characters in the
// new path are replaced according to the settings of the system.
urlPathData.EditVanityUrlPath(oldVanityUrlPath, newVanityUrlPath);

// Updates the draft with the edited page URL path data
await webPageManager.TryUpdateDraft(pageId, languageName, new UpdateDraftData(urlPathData));

// Publishes the page to apply the change
await webPageManager.TryPublish(pageId, languageName);
```

[> Back to list of examples](#toc)
### Remove a vanity URL
C#
Copy
```
// This example demonstrates how to remove an existing vanity URL

// Language variant of the page to be updated
string languageName = "en";

// Id of the page to be updated
int pageId = 1;

// Creates a new draft version of the page
await webPageManager.TryCreateDraft(pageId, languageName);

// Retrieves the editable page URL path data. The specified page has to be in the draft step,
// otherwise an exception is thrown.
WebPageUrlPathData urlPathData = await webPageUrlPathDataRetriever.GetDraftData(pageId,
                                                                         languageName,
                                                                         CancellationToken.None);

// Existing vanity URL path to be removed
string vanityUrlPath = "excellent-chocolate";

// Resolves the service that replaces forbidden characters in vanity URLs according to the
// settings of the system.
var vanityUrlPathNormalizer = Service.Resolve<IVanityUrlPathNormalizer>();

// Normalizes the path by replacing forbidden characters according to the
// system settings. This step is optional if you know the exact vanity URL path.
// The system automatically replaces forbidden characters when adding a vanity URL,
// which may cause the stored path to differ from the original input.
vanityUrlPath = vanityUrlPathNormalizer.Normalize(vanityUrlPath);

// Removes the specified vanity URL. Forbidden characters are not replaced
// before attempting to remove the URL. The specified path has to match an
// existing vanity URL path.
urlPathData.RemoveVanityUrlPath(vanityUrlPath);

// Updates the draft with the new page URL path data
await webPageManager.TryUpdateDraft(pageId, languageName, new UpdateDraftData(urlPathData));

// Publishes the page to apply the change
await webPageManager.TryPublish(pageId, languageName);
```

[> Back to list of examples](#toc)
### Select the canonical URL of a page
C#
Copy
```
// This example demonstrates how to set a vanity URL as the canonical URL of a page.
// To set the system URL as canonical, use the URL slug of the page and
// 'IWebPageUrlPathSlugNormalizer' if you need to replace forbidden characters.

// Language variant of the page to be updated
string languageName = "en";

// Id of the page to be updated
int pageId = 1;

// Creates a new draft version of the page
await webPageManager.TryCreateDraft(pageId, languageName);

// Retrieves the editable page URL path data. The page has to be in the draft step,
// otherwise an exception is thrown.
WebPageUrlPathData urlPathData = await webPageUrlPathDataRetriever.GetDraftData(pageId,
                                                                         languageName,
                                                                         CancellationToken.None);

// Existing vanity URL path to be set as canonical
string vanityUrlPath = "excellent-chocolate";

// Resolves the service that replaces forbidden characters in vanity URLs according to the
// settings of the system.
var vanityUrlPathNormalizer = Service.Resolve<IVanityUrlPathNormalizer>();

// Normalizes the path by replacing forbidden characters according to the
// system settings. This step is optional if you know the exact vanity URL path.
// The system automatically replaces forbidden characters when adding a vanity URL,
// which may cause the stored path to differ from the original input.
vanityUrlPath = vanityUrlPathNormalizer.Normalize(vanityUrlPath);

// Sets the URL specified by the provided path as canonical. The forbidden
// characters are not replaced, and the path has to represent an existing URL
// of the page.
urlPathData.SetCanonicalUrlPath(vanityUrlPath);

// Updates the draft with the edited page URL path data
await webPageManager.TryUpdateDraft(pageId, languageName, new UpdateDraftData(urlPathData));

// Publishes the page to apply the change
await webPageManager.TryPublish(pageId, languageName);
```

[> Back to list of examples](#toc)
## Custom redirects
### Create redirects using IWebPageRedirectManager
C#
Copy
```
// 'IWebPageRedirectManager' can be used to create redirects from custom URL paths
// during upgrades, migrations, and when updating redirects from deleted pages.

// Gets the id of the page that is going to be targeted by the redirect
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReview";
var builder = new ContentItemQueryBuilder()
    .ForContentType(REVIEW_CONTENT_TYPE, q =>
    {
        q.ForWebsite("WonkaPages")
         .TopN(1)
         .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
    });

var targetPageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    });

// The language variant of the target page
string languageName = "en";

// Id of the website channel where the target page belongs
int websiteChannelId = 1;

// URL path to redirect from, see the following comment for details
string urlPath = "reviews/wonka-chocolate-averageness";

// Creates the redirect. If the urlPath matches any URL of a web page or a former URL,
// the system throws an exception.
await webPageRedirectManager.CreateOrUpdateRedirect(urlPath,
                                                    languageName,
                                                    websiteChannelId,
                                                    targetPageId.First());
```

[> Back to list of examples](#toc)
![]()
[]()[]()
