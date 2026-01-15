# Pages
  * [ Copy page link ](api/content-management/pages#) | [Get HelpService ID](api/content-management/pages#)
Core MVC 5


[✖](api/content-management/pages# "Close page link panel") [Copy to clipboard](api/content-management/pages#)
  
List of examples:
  * [Dependency injection](api/content-management/pages#dependency-injection)
    * [Initialize required services](api/content-management/pages#initialize-required-services)
  * [Page retrieval](api/content-management/pages#page-retrieval)
    * [Retrieve pages in web applications](api/content-management/pages#retrieve-pages-in-web-applications)
    * [Retrieve pages of a single content type](api/content-management/pages#retrieve-pages-of-a-single-content-type)
    * [Retrieve parent page of known content type](api/content-management/pages#retrieve-parent-page-of-known-content-type)
    * [Retrieve parent page of unknown content type](api/content-management/pages#retrieve-parent-page-of-unknown-content-type)
  * [Page management](api/content-management/pages#page-management)
    * [Create pages](api/content-management/pages#create-pages)
    * [Clone pages](api/content-management/pages#clone-pages)
    * [Create a folder for pages](api/content-management/pages#create-a-folder-for-pages)
    * [Generate code names](api/content-management/pages#generate-code-names)
    * [Create page language variants](api/content-management/pages#create-page-language-variants)
    * [Create page drafts](api/content-management/pages#create-page-drafts)
    * [Update page drafts](api/content-management/pages#update-page-drafts)
    * [Publish pages](api/content-management/pages#publish-pages)
    * [Schedule publishing for pages](api/content-management/pages#schedule-publishing-for-pages)
    * [Cancel scheduled publishing for pages](api/content-management/pages#cancel-scheduled-publishing-for-pages)
    * [Unpublish pages](api/content-management/pages#unpublish-pages)
    * [Schedule unpublishing for pages](api/content-management/pages#schedule-unpublishing-for-pages)
    * [Cancel scheduled unpublishing for pages](api/content-management/pages#cancel-scheduled-unpublishing-for-pages)
    * [Move pages](api/content-management/pages#move-pages)
    * [Secure pages](api/content-management/pages#secure-pages)
    * [Retrieve usages of a page](api/content-management/pages#retrieve-usages-of-a-page)
    * [Delete pages](api/content-management/pages#delete-pages)


## Dependency injection
### Initialize required services
C#
Copy
```
// Initializes all services and provider classes used within
// the API examples on this page using dependency injection
private readonly IWebPageManager webPageManager;
private readonly IContentQueryExecutor contentQueryExecutor;
private readonly IContentQueryModelTypeMapper contentQueryModelTypeMapper;
private readonly IWebPageItemUsageRetriever webPageItemUsageRetriever;

private readonly IContentRetriever contentRetriever;

public PageServices(IContentQueryExecutor contentQueryExecutor,
                    IWebPageManagerFactory webPageManagerFactory,
                    IUserInfoProvider userInfoProvider,
                    IWebsiteChannelContext websiteChannelContext,
                    IContentQueryModelTypeMapper contentQueryModelTypeMapper,
                    IWebPageItemUsageRetriever webPageItemUsageRetriever,
                    IContentRetriever contentRetriever)
{
    // Gets the user responsible for management operations.
    // The user is used only for auditing purposes,
    // e.g., to be shown as the creator in 'Created by' fields.
    // The user's permissions are not checked for any of the operations.
    UserInfo user = userInfoProvider.Get("JimmieWonka");

    // Creates an instance of the manager class facilitating page operations
    this.webPageManager = webPageManagerFactory.Create(websiteChannelContext.WebsiteChannelID, user.UserID);
    this.contentQueryExecutor = contentQueryExecutor;
    this.contentQueryModelTypeMapper = contentQueryModelTypeMapper;
    this.webPageItemUsageRetriever = webPageItemUsageRetriever;
    this.contentRetriever = contentRetriever;
}
```

[> Back to list of examples](api/content-management/pages#toc)
## Page retrieval
### Retrieve pages in web applications
C#
Copy
```
/* IContentRetriever is a service class that provides a high-level abstraction over the Content item query API for retrieving content.
 * The service is intended for content retrieval in the context of Xperience web applications.
 *
 * The service ensures the following default behavior for each query:
 *  -- Uses the current website channel context to determine the website channel for which the content is retrieved
 *  -- Determines whether to retrieve the latest or published versions based on the current request context
 *  -- Retrieves content in the language matching the current request context
 *  -- Takes into account language fallback settings for content that does not exist in the specific language
 *  -- Provides built-in caching with automatic cache dependency collection
 *
 * You can further parameterize the queries using parameter objects and query configuration delegates.
 *
 * See the 'Reference - ContentRetriever API' documentation for a comprehensive overview of the API.
 * For a quick reference, see the following code examples:
 */

// Retrieves the current page from the current site, in the language of the current request
CandyStorePage currentPage = await contentRetriever.RetrieveCurrentPage<CandyStorePage>();

// Retrieves multiple pages of the same type from a specific path with linked items
IEnumerable<CandyStorePage> storePages = await contentRetriever.RetrievePages<CandyStorePage>(
    new RetrievePagesParameters
    {
        PathMatch = PathMatch.Children("/Locations"),
        LinkedItemsMaxLevel = 1
    });

// Retrieves pages of different content types using a shared interface
var contentTypes = new[] { CandyStorePage.CONTENT_TYPE_NAME, ChocolateReviewPage.CONTENT_TYPE_NAME };
IEnumerable<IWebPageFieldsSource> mixedPages = await contentRetriever.RetrievePagesOfContentTypes<IWebPageFieldsSource>(contentTypes);

// Retrieves specific pages by their GUIDs
var pageGuids = new[] { Guid.Parse("c4b1f72e-42d7-41dd-8130-00cd118f5f20") };
IEnumerable<CandyStorePage> specificPages = await contentRetriever.RetrievePagesByGuids<CandyStorePage>(pageGuids);

// Retrieves pages that use specific reusable field schemas
// Assuming 'IPageMetadata' is an interface implemented by types using these schemas
var schemaNames = new[] { "MetadataSchema", "SeoSchema" };
IEnumerable<IPageMetadata> pagesWithSchemas = await contentRetriever.RetrievePagesOfReusableSchemas<IPageMetadata>(schemaNames);

// Retrieves all pages from a specific channel
IEnumerable<IWebPageFieldsSource> channelPages = await contentRetriever.RetrieveAllPages<IWebPageFieldsSource>(
    new RetrieveAllPagesParameters
    {
        ChannelName = "WonkaPages"
    });
```

[> Back to list of examples](api/content-management/pages#toc)
### Retrieve pages of a single content type
C#
Copy
```
// This example demonstrates how to retrieve pages of a single content type
string STORE_CONTENT_TYPE = "Wonka.CandyStorePage";
string languageName = "en";
int maxLevel = 1;

// Gets top 5 rated website channel pages of 'Wonka.CandyStorePage' content type and their linked content items
ContentItemQueryBuilder builder =
        new ContentItemQueryBuilder()
                .ForContentType(STORE_CONTENT_TYPE, subqueryParameters =>
                {
                    subqueryParameters.ForWebsite(
                        websiteChannelName: "WonkaPages",
                        pathMatch: PathMatch.Children("/Locations"))
                    .TopN(5)
                    .WithLinkedItems(maxLevel);
                    // Optional methods for global parametrization of their preceding queries
                }).InLanguage(languageName)
                  .Parameters(subqueryParameters =>
                   subqueryParameters.OrderBy(new OrderByColumn("StoreRating", OrderDirection.Descending)));

// Executes the query specified in 'builder' and binds it to the CandyStorePage class generated for the CandyStorePage content type
IEnumerable<CandyStorePage> candyStorePages =
        await contentQueryExecutor.GetMappedResult<CandyStorePage>(builder);
```

[> Back to list of examples](api/content-management/pages#toc)
### Retrieve parent page of known content type
C#
Copy
```
// This example demonstrates how to retrieve the parent page of a selected page
// in the context of a web application when you know the parent's content type.
// If you need to retrieve a parent page of an unknown type, see the next example.

// Retrieves the selected page (in this case, the current page) whose parent will be retrieved
ChocolateReviewPage selectedPage = await contentRetriever.RetrieveCurrentPage<ChocolateReviewPage>();

// Gets the id of the parent page
// Returns '0' when the page has no parent
int parentId = selectedPage.SystemFields.WebPageItemParentID;

// Retrieves the parent page when the content type is known
// This example assumes that every ChocolateReviewPage has a parent of the CandyStorePage content type
if (parentId != 0)
{
    IEnumerable<CandyStorePage> parentPage = await contentRetriever.RetrievePages<CandyStorePage>(
        new RetrievePagesParameters(),
        query => query.Where(w => w.WhereEquals("WebPageItemID", parentId)),
        new RetrievalCacheSettings($"WebPageItemID|{parentId}"));
}
else
    throw new Exception("Parent page ID is 0, indicating that the selected page has no parent.");
```

[> Back to list of examples](api/content-management/pages#toc)
### Retrieve parent page of unknown content type
C#
Copy
```
// This example demonstrates two ways to retrieve the parent page of a selected
// page in the context of a web application when you don't know the content type
// of the parent. The first method is simpler but results in a larger query. Use
// the second method if you need to optimize the query. If you know the parent's
// content type, you can use the simpler method shown in the previous example.

// Retrieves the selected page (in this case, the current page) whose parent will be retrieved
ChocolateReviewPage selectedPage = await contentRetriever.RetrieveCurrentPage<ChocolateReviewPage>();

// Gets the id of the parent page
// Returns '0' when the page has no parent
int parentId = selectedPage.SystemFields.WebPageItemParentID;

// Retrieves the parent page when its content type is unknown
// This query can be inefficient if your project has a large number of content types for pages
// However, the result is cached, so this might not be an issue depending on the use case
if (parentId != 0)
{
    var parentPage = await contentRetriever.RetrieveAllPages<IWebPageFieldsSource>(
        new RetrieveAllPagesParameters(),
        query => query.Where(w => w.WhereEquals("WebPageItemID", parentId)),
        new RetrievalCacheSettings($"WebPageItemID|{parentId}"));
}
else
    throw new Exception("Parent page ID is 0, indicating that the selected page has no parent.");

// The following section demonstrates an alternative method with better query optimization
// Can be useful for projects with a large number of page content types

// Checks that the parent page exists
if (parentId == 0)
{
    throw new Exception("Parent page ID is 0, indicating that the selected page has no parent.");
}

// Gets the metadata of the parent page
WebPageMetadata parentMetadata = await webPageManager.GetWebPageMetadata(parentId);

// Id of the parent's content type
int parentTypeID = parentMetadata.ContentTypeID;

if (parentTypeID == 0)
{
    throw new Exception("The content type ID is 0, indicating that the parent is a folder");
}
// Content type name of the parent
string contentTypeName = DataClassInfoProvider
        .GetDataClassInfo(parentTypeID).ClassName;

// Retrieves the parent page of an unknown content type in an efficient way
IEnumerable<IWebPageFieldsSource> parentPageOption2 = await contentRetriever.RetrievePagesOfContentTypes<IWebPageFieldsSource>(
        [contentTypeName],
        new RetrievePagesOfContentTypesParameters(),
        query => query.Where(w => w.WhereEquals("WebPageItemID", parentId)),
        new RetrievalCacheSettings($"WebPageItemID|{parentId}"));
```

[> Back to list of examples](api/content-management/pages#toc)
## Page management
### Create pages
C#
Copy
```
/* Web pages consist of presentation data and metadata that drive system behavior.
   When creating new pages, you must provide an instance of 'CreateWebPageParameters' (metadata),
   which contains a 'ContentItemParameters' instance storing 'ContentItemData' (data).
   'ContentItemData' is a dictionary that stores item values in a <fieldName>:<fieldValue> format.
   The dictionary must contain records that correspond to the fields of the page's content type as
   defined in the field editor.
*/

// The code name of the content type on which the page is based
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReviewPage";

// The page display name shown in the admin UI
string pageDisplayName = "Review of the Wonka Chocolate Excellence";

// The initial language in which the page gets created
// Must correspond to a language defined in the 'Languages' application
string languageName = "en";

/* Populates the page's content item data. This example assumes a content type with the following fields:
        -- Data type: Text; Code name: ReviewTitle
        -- Data type: Date; Code name: ReviewPublishDate
        -- Data type: Content items; Code name: ReviewImage
        -- Data type: Long text; Code name: ReviewText
        -- Data type: Content items; Code name: ReviewRelatedReviews
    To find the underlying C# type of other data types, see 'Data type management' in the Kentico documentation.
*/
var itemData = new ContentItemData(new Dictionary<string, object>{
    { "ReviewTitle", "Wonka Chocolate Excellence" },
    { "ReviewPublishDate", DateTime.Now },
    { "ReviewImage", new List<ContentItemReference>()
                { new ContentItemReference()
                        { Identifier = Guid.Parse("c4b1f72e-42d7-41dd-8130-00cd118f5f20")}}},
    { "ReviewText", "<Text of the review>" },
    { "ReviewRelatedReviews", new List<ContentItemReference>()
                { new ContentItemReference()
                        { Identifier = Guid.Parse("c6f453db-dd39-46ac-bdb1-78bdb5b20a9e")}}}
});

// Creates a page metadata object
var contentItemParameters = new ContentItemParameters(REVIEW_CONTENT_TYPE, itemData);
var createPageParameters = new CreateWebPageParameters(pageDisplayName,
                                                       languageName,
                                                       contentItemParameters);

// Creates the page in the database
await webPageManager.Create(createPageParameters);
```

[> Back to list of examples](api/content-management/pages#toc)
### Clone pages
C#
Copy
```
/* When cloning a page, you must provide an instance of 'ClonePageParameters',
   which contains the ID of the page to clone, the language variant to
   clone, the display name, and the URL slug of the new page. If you want to clone
   only the selected language variant of the page, you also need to set the 'cloneAllLanguageVariants'
   parameter to false. Otherwise, the parameter is set to true by default, and all language variants
   are cloned.
 */

// Gets the ID of the source page that you want to clone
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReviewPage";
var builder = new ContentItemQueryBuilder()
                    .ForContentType(REVIEW_CONTENT_TYPE, q =>
                    {
                        q.ForWebsite("WonkaPages")
                         .TopN(1)
                         .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                    });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    },
                    // Retrieves the latest version of the page
                    new ContentQueryExecutionOptions() { ForPreview = true });

// The language variant that you want to clone
// An existing language variant needs to be provided even when cloning all language variants
string languageName = "en";

// The display name of the new page shown in the admin UI
string displayName = "Wonka Chocolate Brilliance";

// The URL slug of the new page
string urlSlug = "wonka-brilliance";

// Creates a parameters object for the cloning operation
var cloneParameters = new ClonePageParameters
(
    pageId.First(),
    languageName,
    displayName,
    urlSlug,
    cloneAllLanguageVariants: true
);

// Creates the new cloned page within the database
await webPageManager.Clone(cloneParameters);
```

[> Back to list of examples](api/content-management/pages#toc)
### Create a folder for pages
C#
Copy
```
// Demonstrates how to create folders for better page organization

// The folder display name shown in the admin UI
string folderDisplayName = "Reviews of chocolate";

// The initial language in which the folder gets created
// Must correspond to a language defined in the 'Languages' application
string languageName = "en";

// Creates a folder metadata object
var createFolderParameters = new CreateFolderParameters(folderDisplayName,
                                                        languageName);

// Creates the folder in the database
await webPageManager.CreateFolder(createFolderParameters);
```

[> Back to list of examples](api/content-management/pages#toc)
### Generate code names
C#
Copy
```
// The 'CreateWePageParameters' constructor in the 'create pages'
// example and 'CreateFolderParameters' in the 'create folders' example above
// use API that automatically generates a unique code name for
// the content item being created.
// You can also transparently generate code names using 'IContentItemCodeNameProvider'.

// An example content item display name
string displayName = "Review of the Wonka Chocolate Excellence";

// 'IContentItemCodeNameProvider.Get' ensures a unique code name from the provided
// string by appending a random suffix in case an identical code name already exists
string codeName = await contentItemCodeNameProvider.Get(displayName);

// Use the 'codeName' value in content item APIs...
```

[> Back to list of examples](api/content-management/pages#toc)
### Create page language variants
C#
Copy
```
/* Page language variants must be based on existing pages.
   When creating new page variants, prepare 'ContentItemData' (data) and provide
   an instance of 'CMS.Websites.CreateLanguageVariantParameters' (metadata).
*/

// Gets the id of the page on which to base the new language variant
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReviewPage";
var builder = new ContentItemQueryBuilder()
                    .ForContentType(REVIEW_CONTENT_TYPE, q =>
                    {
                        q.ForWebsite("WonkaPages")
                         .TopN(1)
                         .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                    });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    },
                    // Retrieves the latest version of the page
                    new ContentQueryExecutionOptions() { ForPreview = true });

// The new language variant display name shown in the admin UI
string variantDisplayName = "Rezension der Wonka Chocolate Excellence";

// The language of the new variant
// Must correspond to a language defined in the 'Languages' application
string languageName = "de";

// Prepares the page's content item data for the new language variant
var itemData = new ContentItemData(new Dictionary<string, object>{
    { "ReviewTitle", "Wonka Schokoladenexzellenz" },
    { "ReviewPublishDate", DateTime.Now },
    { "ReviewImage", new List<ContentItemReference>()
                { new ContentItemReference()
                        { Identifier = Guid.Parse("c4b1f72e-42d7-41dd-8130-00cd118f5f20")}}},
    { "ReviewText", "<Translated text of the review>" },
    { "ReviewRelatedReviews", new List<ContentItemReference>()
                { new ContentItemReference()
                        { Identifier = Guid.Parse("c6f453db-dd39-46ac-bdb1-78bdb5b20a9e")}}}
});

// Prepares the metadata needed for the new language variant
var createLanguageVariantParameters =
            new CMS.Websites.CreateLanguageVariantParameters(pageId.First(),
                                                             languageName,
                                                             variantDisplayName,
                                                             itemData);

// Creates the new language variant in the database
if (await webPageManager.TryCreateLanguageVariant(createLanguageVariantParameters))
{
    Console.WriteLine("Language variant successfully created.");
}
else
    throw new Exception("Something went wrong");
```

[> Back to list of examples](api/content-management/pages#toc)
### Create page drafts
C#
Copy
```
// Demonstrates how to create a new draft version for a page with the 'Published' or 'Unpublished' status

// The language of the page variant for which the draft is created
string languageName = "en";

// Gets the id of the page
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReviewPage";
var builder = new ContentItemQueryBuilder()
                    .ForContentType(REVIEW_CONTENT_TYPE, q =>
                    {
                        q.ForWebsite("WonkaPages")
                         .TopN(1)
                         .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                    });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    });

// Creates a new draft version of the page
if (await webPageManager.TryCreateDraft(pageId.First(), languageName))
{
    Console.WriteLine($"New draft version successfully created.");
}
else
    throw new Exception("Something went wrong.");
```

[> Back to list of examples](api/content-management/pages#toc)
### Update page drafts
C#
Copy
```
// Demonstrates how to update pages

// The language of the page variant that is updated
string languageName = "en";

// Gets the id of the page to update
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReviewPage";
var builder = new ContentItemQueryBuilder()
                    .ForContentType(REVIEW_CONTENT_TYPE, q =>
                    {
                        q.ForWebsite("WonkaPages")
                         .TopN(1)
                         .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                    });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    },
                    // Retrieves the latest version of the page
                    new ContentQueryExecutionOptions() { ForPreview = true });

// Prepares updated content item data for the page
var itemData = new ContentItemData(new Dictionary<string, object>{
    { "ReviewTitle", "Review of the Wonka Chocolate Excellence" },
    { "ReviewPublishDate", DateTime.Now },
    { "ReviewImage", new List<ContentItemReference>()
                { new ContentItemReference()
                        { Identifier = Guid.Parse("c4b1f72e-42d7-41dd-8130-00cd118f5f20")}}},
    { "ReviewText", "<Updated text of the review>" },
    { "ReviewRelatedReviews", new List<ContentItemReference>()
                { new ContentItemReference()
                        { Identifier = Guid.Parse("c6f453db-dd39-46ac-bdb1-78bdb5b20a9e")}}}
});

// Updates the draft with the new item data
if (await webPageManager.TryUpdateDraft(pageId.First(), languageName, new UpdateDraftData(itemData)))
{
    Console.WriteLine($"Draft version successfully updated.");
}
else
    throw new Exception("Something went wrong.");
```

[> Back to list of examples](api/content-management/pages#toc)
### Publish pages
C#
Copy
```
// Demonstrates how to publish pages
// That is, move a page from 'Draft' (or the final step in a workflow) to 'Published'

// The language of the page variant that will be published
string languageName = "en";

// Gets the id of the page to publish
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReviewPage";
var builder = new ContentItemQueryBuilder()
                    .ForContentType(REVIEW_CONTENT_TYPE, q =>
                    {
                        q.ForWebsite("WonkaPages")
                         .TopN(1)
                         .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                    });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    },
                    // Retrieves the latest version of the page
                    new ContentQueryExecutionOptions() { ForPreview = true });

// Publishes the page
// For pages under workflow, 'TryPublish' bypasses all remaining workflow steps and moves the page to the 'Published' status
if (await webPageManager.TryPublish(pageId.First(), languageName))
{
    Console.WriteLine("Page successfully published.");
}
else
    throw new Exception("Something went wrong.");
```

[> Back to list of examples](api/content-management/pages#toc)
### Schedule publishing for pages
C#
Copy
```
// This example demonstrates how to schedule pages to be published at a future date and time

// Prepares a publish date 7 days from the current date and time
var publishDateTime = DateTime.Now.AddDays(7);

// Gets the id of the page to schedule
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReviewPage";
string languageName = "en";

var builder = new ContentItemQueryBuilder()
                    .ForContentType(REVIEW_CONTENT_TYPE, q =>
                    {
                        q.ForWebsite("WonkaPages")
                         .TopN(1)
                         .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                    });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    },
                    // Retrieves the latest version of the page
                    new ContentQueryExecutionOptions() { ForPreview = true });

/* Schedules the page to be published
   - Throws an exception if the page's status is not 'VersionStatus.Draft' or 'VersionStatus.InitialDraft' (this includes custom workflow steps).
   - If the specified date and time is not in the future, the page gets published immediately.
   - For pages  under workflow, 'SchedulePublish' bypasses all remaining workflow steps
     and moves the page to a system step representing the 'Scheduled' status in the UI.
*/
await webPageManager.SchedulePublish(pageId.First(), languageName, publishDateTime);
```

[> Back to list of examples](api/content-management/pages#toc)
### Cancel scheduled publishing for pages
C#
Copy
```
// This example demonstrates how to cancel scheduled publishing of a page

// Gets the id of the page for which publishing will be canceled
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReviewPage";
string languageName = "en";

var builder = new ContentItemQueryBuilder()
                    .ForContentType(REVIEW_CONTENT_TYPE, q =>
                    {
                        q.ForWebsite("WonkaPages")
                         .TopN(1)
                         .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                    });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    },
                    // Retrieves the latest version of the page
                    new ContentQueryExecutionOptions() { ForPreview = true });


// Checks that the page is scheduled to be published
if (await webPageManager.IsPublishScheduled(pageId.First(), languageName))
{
    /* Cancels the scheduled publishing for the page
       - For pages under workflow, 'CancelScheduledPublish' returns the page to the final custom-defined workflow step.
       - If the page is also scheduled to be unpublished, the unpublish is canceled as well.
    */
    await webPageManager.CancelScheduledPublish(pageId.First(), languageName);
}
```

[> Back to list of examples](api/content-management/pages#toc)
### Unpublish pages
C#
Copy
```
// Demonstrates how to unpublish published pages

// The language of the page variant that will be unpublished
string languageName = "en";

// Gets the id of the page to unpublish
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReviewPage";
var builder = new ContentItemQueryBuilder()
                    .ForContentType(REVIEW_CONTENT_TYPE, q =>
                    {
                        q.ForWebsite("WonkaPages")
                         .TopN(1)
                         .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                    });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    });

// Unpublishes the page
if (await webPageManager.TryUnpublish(pageId.First(), languageName))
{
    Console.WriteLine("Page successfully unpublished.");
}
else
    throw new Exception("Something went wrong.");
```

[> Back to list of examples](api/content-management/pages#toc)
### Schedule unpublishing for pages
C#
Copy
```
// This example demonstrates how to schedule pages to be unpublished at a future date and time

// Prepares an unpublish date 7 days from the current date and time
var unpublishDateTime = DateTime.Now.AddDays(7);

// Gets the id of the page to schedule
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReviewPage";
string languageName = "en";

var builder = new ContentItemQueryBuilder()
                    .ForContentType(REVIEW_CONTENT_TYPE, q =>
                    {
                        q.ForWebsite("WonkaPages")
                         .TopN(1)
                         .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                    });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    },
                    // Retrieves the latest version of the page
                    new ContentQueryExecutionOptions() { ForPreview = true });

/* Schedules the page to be unpublished
   - The page must either be published or scheduled to be published. If the page is scheduled to be published,
     the publish date must be before the specified unpublish date. Otherwise the method throws an exception.
   - If the specified unpublish date and time is not in the future, the page gets unpublished immediately.
*/
await webPageManager.ScheduleUnpublish(pageId.First(), languageName, unpublishDateTime);
```

[> Back to list of examples](api/content-management/pages#toc)
### Cancel scheduled unpublishing for pages
C#
Copy
```
// This example demonstrates how to cancel a page's scheduled unpublishing

// Gets the id of the page for which unpublishing will be canceled
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReviewPage";
string languageName = "en";

var builder = new ContentItemQueryBuilder()
                    .ForContentType(REVIEW_CONTENT_TYPE, q =>
                    {
                        q.ForWebsite("WonkaPages")
                         .TopN(1)
                         .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                    });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    },
                    // Retrieves the latest version of the page
                    new ContentQueryExecutionOptions() { ForPreview = true });


// Checks that the page is scheduled to be unpublished
if (await webPageManager.IsUnpublishScheduled(pageId.First(), languageName))
{
    // Cancels the scheduled unpublishing for the page
    await webPageManager.CancelScheduledUnpublish(pageId.First(), languageName);
}
```

[> Back to list of examples](api/content-management/pages#toc)
### Move pages
C#
Copy
```
// Demonstrates how to move pages across the content tree

// Gets the id of the page to be moved
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReviewPage";
var builder = new ContentItemQueryBuilder()
                    .ForContentType(REVIEW_CONTENT_TYPE, q =>
                    {
                        q.ForWebsite("WonkaPages")
                         .TopN(1)
                         .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                    });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    });

// Gets the id of the new parent page under which the desired page will be moved
string PARENT_PAGE_CONTENT_TYPE = "Wonka.ReviewSection";
builder = new ContentItemQueryBuilder()
                .ForContentType(PARENT_PAGE_CONTENT_TYPE, q =>
                {
                    q.ForWebsite("WonkaPages")
                     .TopN(1)
                     .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                });

var parentId = await contentQueryExecutor
                .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                {
                    var pageRowId = rowData.WebPageItemID;
                    return pageRowId;
                });

// Prepares the metadata needed for the page move
var movePageParameters = new MoveWebPageParameters(pageId.First(), parentId.First());

// Moves the page in the content tree
await webPageManager.Move(movePageParameters);
```

[> Back to list of examples](api/content-management/pages#toc)
### Secure pages
C#
Copy
```
// Demonstrates how to change the security settings of page

// Gets the id of the page for which security settings will be updated
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReviewPage";
var builder = new ContentItemQueryBuilder()
                    .ForContentType(REVIEW_CONTENT_TYPE, q =>
                    {
                        q.ForWebsite("WonkaPages")
                         .TopN(1)
                         .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                    });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    });

// The new security settings for the page
bool isSecured = true;

// Changes the security settings for the page
await webPageManager.UpdateSecuritySettings(pageId.First(), isSecured);
```

[> Back to list of examples](api/content-management/pages#toc)
### Retrieve usages of a page
C#
Copy
```
// Demonstrates how to retrieve direct usages of a given page

// Gets the id of the page for which to retrieve usages
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReviewPage";
string languageName = "en";

var builder = new ContentItemQueryBuilder()
                    .ForContentType(REVIEW_CONTENT_TYPE, q =>
                    {
                        q.ForWebsite("WonkaPages")
                         .TopN(1)
                         .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                    });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    });

// Retrieves direct usages of a page specified by its
// pageId and its languageName.
await webPageItemUsageRetriever.Retrieve(pageId.First(), languageName);
```

[> Back to list of examples](api/content-management/pages#toc)
### Delete pages
C#
Copy
```
// Demonstrates how to permanently delete a page

// Language of the page variant to be deleted
string languageName = "en";

// Gets the id of the page to be deleted
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReviewPage";
var builder = new ContentItemQueryBuilder()
                    .ForContentType(REVIEW_CONTENT_TYPE, q =>
                    {
                        q.ForWebsite("WonkaPages")
                         .TopN(1)
                         .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                    });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    });

// Permanently deletes the language variant of the page from the database.
// Set the 'Permanently' parameter to false to retain the page in the recycle bin.
// If this was the last language variant remaining,
// all associated page metadata is also deleted.
await webPageManager.Delete(
                    new DeleteWebPageParameters(pageId.First(), languageName)
                    {
                        Permanently = true,
                    });
```

[> Back to list of examples](api/content-management/pages#toc)