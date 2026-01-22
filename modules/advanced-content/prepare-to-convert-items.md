---
source: https://docs.kentico.com/modules/advanced-content/prepare-to-convert-items
scrape_date: 2026-01-22
---

Module: Advanced content
6 of 11 Pages
# Prepare to convert flat items to schemas
In earlier examples, we explored a scenario of content model evolution; we integrated a flat-structured _Article_ content type with a newer [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) that spanned multiple content types. We adjusted code to work with the new schema-based types alongside the original _Article_ type.
Let’s continue further with this example, converting articles from the old content type into a schema-based type. We’ll also update pages that referenced the old articles to point to the new schema-based items.
This example covers conversion of data in Xperience by Kentico from one content type to another. Though it may mention an “old” content type and a “new” one, it does not cover upgrading from Kentico Xperience 13 (or older versions) to Xperience by Kentico.
## Consider the scenario
[Earlier in this series](/guides/development/advanced-content/work-with-reusable-field-schemas), we worked with an older flat content type for articles alongside new content types based on a [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas). We updated the `ArticlePageService` to work with the new content types in parallel with the original `Article` content type, and extended the new article types to work with taxonomy - the **expand** step of the [expand and contract approach.](https://community.kentico.com/blog/safely-evolving-a-content-model-with-expand-and-contract).
With old and new article models existing in parallel, our project works, but its content model is unnecessarily bloated. Additionally, editors can still create items of the deprecated `Article` type by mistake.
If we can adjust our article functionality so that it no longer needs the old content type, we can remove it, moving forward with only schema-based article content types. The next step toward this goal is to move the existing `Article` data to one of our new schema-based types, `GeneralArticle`, completing the **contract** step.
For this scenario, imagine we’ve instructed our content editors to make sure that all of the `Article` items are either **published** or **deleted** , along with the pages that display them. The code will be much less complicated if we only need to work with published items.
## Prepare one-time migration code
We are essentially creating a one-off data migration, where we will not need the code anymore after running it, so let’s keep our code files in one place. Create a folder for one-time code in your project, for example _~/OneTimeCode_.
Add a class called `ArticleConverter` to this folder, and configure the following services, which we’ll use throughout this example:
C#
**ArticleConverter.cs**
Copy
```
using CMS.ContentEngine;
using CMS.DataEngine;
using CMS.Membership;
using CMS.Websites.Routing;
using CMS.Workspaces;

namespace TrainingGuides.Web.OneTimeCode;

public class ArticleConverter
{
    private readonly IWebsiteChannelContext websiteChannelContext;
    private readonly IContentQueryExecutor contentQueryExecutor;
    private readonly IInfoProvider<WorkspaceInfo> workspaceInfoProvider;
    private readonly IInfoProvider<ContentLanguageInfo> contentLanguageInfoProvider;

    private readonly IWebPageManager webPageManager;
    private readonly IContentItemManager contentItemManager;

    private const string ADMINISTRATOR_USERNAME = "administrator";
    private const string DEFAULT_WORKSPACE_NAME = "KenticoDefault";

    public ArticleConverter(
        IInfoProvider<UserInfo> userInfoProvider,
        IContentItemManagerFactory contentItemManagerFactory,
        IWebsiteChannelContext websiteChannelContext,
        IWebPageManagerFactory webPageManagerFactory,
        IContentQueryExecutor contentQueryExecutor,
        IInfoProvider<WorkspaceInfo> workspaceInfoProvider,
        IInfoProvider<ContentLanguageInfo> contentLanguageInfoProvider)
    {
        this.websiteChannelContext = websiteChannelContext;
        this.contentQueryExecutor = contentQueryExecutor;
        this.workspaceInfoProvider = workspaceInfoProvider;
        this.contentLanguageInfoProvider = contentLanguageInfoProvider;

        var adminUser = userInfoProvider.Get()
            .WhereEquals(nameof(UserInfo.UserName), ADMINISTRATOR_USERNAME)
            .FirstOrDefault() ?? new UserInfo();

        if (adminUser is null)
        {
            throw new Exception("Administrator user not found. Cannot proceed with ArticleConverter initialization.");
        }

        contentItemManager = contentItemManagerFactory.Create(adminUser.UserID);
        webPageManager = webPageManagerFactory.Create(websiteChannelContext.WebsiteChannelID, adminUser.UserID);
    }
...
```

## Define supporting utility code
### Retrieve and format data
Let’s start out with some utility methods to retrieve information from the database, and reformat data.
In this example, we want to work with data in all languages, so we’ll use `ContentQueryBuilder` without specifying a language to retrieve both `Article` objects (the old content type), and `ArticlePage` objects (the pages linking to old articles).
We’ll also add some utilities for retrieving and working with the names of various object types, which we’ll use throughout this example.
C#
**ArticleConverter.cs**
Copy
```
...
// Retrieve reusable articles of the provided type
// We'll use this to get the old articles for migration, then later to retrieve new articles
private async Task<IEnumerable<T>> RetrieveArticles<T>(
    Func<ContentTypeQueryParameters, ContentTypeQueryParameters> queryFilter,
    string contentTypeName)
{
    var builder = new ContentItemQueryBuilder()
        .ForContentType(
            contentTypeName,
            // Apply the query filtering function provided in the options
            config => queryFilter(config)
                .WithLinkedItems(3)
        );

    var queryExecutorOptions = new ContentQueryExecutionOptions
    {
        // Retrieve only published data
        ForPreview = false
    };

    var items = await contentQueryExecutor.GetMappedResult<T>(builder, queryExecutorOptions);

    return items;
}

// Retrieve article pages that link a specific old article
// We'll use this when we update the pages that reference old articles.
private async Task<IEnumerable<ArticlePage>> RetrieveArticlePagesLinkingArticle(int oldArticleId)
{
    var builder = new ContentItemQueryBuilder()
        .ForContentType(
            ArticlePage.CONTENT_TYPE_NAME,
            config => config
                // Specify pages that link the old article via the ArticlePageContent field
                .Linking(nameof(ArticlePage.ArticlePageContent), [oldArticleId])
                // Specify the web channel based on the current context
                // Using the context is not necessary, especially if you want to change which web channel you are working with,
                // or run this code from somewhere context is not available.
                .ForWebsite(websiteChannelContext.WebsiteChannelName)
                // Retrieve items linked by article pages, and items linked by those items, in case we decide to work with that data.
                .WithLinkedItems(2)
        );

    var queryExecutorOptions = new ContentQueryExecutionOptions
    {
        // Retrieve only published data
        ForPreview = false
    };

    var items = await contentQueryExecutor.GetMappedResult<ArticlePage>(builder, queryExecutorOptions);

    return items;
}

// Retrieve the name of a language based on its ID
private string GetContentLanguageName(int contentLanguageId)
{
    var contentLanguage = contentLanguageInfoProvider.Get(contentLanguageId);
    return contentLanguage.ContentLanguageName;
}

// Retrieve the name of a workspace in which the provided content item lives
private async Task<string> GetWorkspaceName(int contentItemID)
{
    var metadata = await contentItemManager.GetContentItemMetadata(contentItemID);

    string result = await workspaceInfoProvider.Get()
        .WhereEquals(nameof(WorkspaceInfo.WorkspaceID), metadata.WorkspaceId)
        .AsSingleColumn(nameof(WorkspaceInfo.WorkspaceName))
        .GetScalarResultAsync<string>();

    return result ?? DEFAULT_WORKSPACE_NAME;
}

// Construct a code name for a new schema-based article based on an old article
private string GetNewReusableArticleName(Article oldArticle) => $"{oldArticle.SystemFields.ContentItemName}-schema";

// Retrieve the display name of an article in a specific language
private async Task<string> GetArticleDisplayName(Article oldArticle, string languageName, ConversionAttempt attempt)
{
    string displayName;

    try
    {
        displayName = (await contentItemManager.GetContentItemLanguageMetadata(oldArticle.SystemFields.ContentItemID, languageName)).DisplayName;
    }
    catch (Exception ex)
    {
        displayName = oldArticle.ArticleTitle;
        string newMessage = $" Failed to retrieve display name for [{oldArticle.ArticleTitle}] with ID [{oldArticle.SystemFields.ContentItemID}] in language [{languageName}]."
            + (!string.IsNullOrWhiteSpace(ex.Message) ? $" Error: {ex.Message}" : string.Empty);
        attempt.Exceptions.Add(new Exception(newMessage));
    }

    return displayName;
}
...
```

If you wish to create web page items instead of reusable items in the Content hub, use the `IWebPageManager` instead of the `IContentItemManager`.
### Track progress
Now, add a class to keep track of conversion of each specific article. For brevity, we’ll include it in the same file for this example.
Each of these objects will contain information about all language versions of an article, and the language versions of pages that reference them, logging both information and exceptions. A collection of these objects will represent the results of our conversion code overall.
C#
**ArticleConverter.cs**
Copy
```
...
public class ConversionAttempt
{
    public Article OldArticle { get; set; } = new();
    public int? NewArticleContentItemId { get; set; }
    public List<Exception> Exceptions { get; set; }
    public List<string> LogMessages { get; set; }
    public List<string> FinishedLanguagesReusable { get; set; } = [];
    public List<string> FinishedLanguagesPage { get; set; } = [];

    public ConversionAttempt(
        Article oldArticle,
        int? newContentItemPublishedId,
        Exception? exception = null,
        string? logMessage = null)
    {
        OldArticle = oldArticle;
        NewArticleContentItemId = newContentItemPublishedId;
        Exceptions = exception is null ? [] : [exception];
        LogMessages = logMessage is null ? [] : [logMessage];
    }
}
```

[ Previous page ](/modules/advanced-content/filter-content-with-taxonomies)
6 of 11
[ Mark complete and continue ](/modules/advanced-content/convert-items-to-schemas)
![]()
[]()[]()
