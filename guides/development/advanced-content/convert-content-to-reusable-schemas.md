---
source: https://docs.kentico.com/guides/development/advanced-content/convert-content-to-reusable-schemas
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Advanced content](/guides/development/advanced-content)
  * Convert existing content to reusable field schemas 


# Convert existing content to reusable field schemas
In earlier examples, we explored a scenario of content model evolution; we integrated a flat-structured _Article_ content type with a newer [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) that spanned multiple content types. We adjusted code to work with the new schema-based types alongside the original _Article_ type.
Let’s continue further with this example, converting articles from the old content type into a schema-based type. We’ll also update pages that referenced the old articles to point to the new schema-based items.
This example covers conversion of data in Xperience by Kentico from one content type to another. Though it may mention an “old” content type and a “new” one, it does not cover upgrading from Kentico Xperience 13 (or older versions) to Xperience by Kentico.
## Before you start
This guide is part of a series exploring the remodeling of articles. If you’d like to follow along from the beginning, we recommend visiting the [Advanced content training module](/modules/advanced-content), where you can track your progress through the example. If you prefer to continue with the longer-form guide format, start with with [Work with reusable field schemas](/guides/development/advanced-content/work-with-reusable-field-schemas).
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](/documentation/changelog) or higher.
Some features covered in the Training guides may not work in older versions. 


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository. In this case, you can run the one-time code by running the project and visiting the _/ConvertArticles_ path.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with this series of guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
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

## Create article conversion functionality
For the primary functionality of this article, we’ll
### Set a target
Let’s start out with the top-level method that orchestrates the conversion. We’ll include calls to methods we haven’t implemented yet to help set our direction.
First we need to retrieve the existing articles of the old _Article_ content type. Then we can transfer their data to new reusable items of the schema-based _General article_ type.
Since the existing _Article_ items are published (visible to visitors by default), we’ll need to publish their replacements as well.
If the old published items are scheduled to be unpublished on a certain date, we should also transfer that scheduling to the new items.
Finally, we can find references to old _Article_ items and point them to the new _General article_ items instead - both in pages and other reusable items.
The method should return a collection of `ConversionAttempt` objects, containing logging information about the process.
The method should have the following structure:
C#
**ArticleConverter.cs**
Copy
```
...
/// <summary>
/// Main method to convert old Article items to new GeneralArticle items and update references in ArticlePage items and GeneralArticle.RelatedArticles.
/// </summary>
/// <returns>Collection of <see cref="ConversionAttempt"/> objects representing the results for each item.</returns>
public async Task<List<ConversionAttempt>> Convert()
{
    var oldArticles = await RetrieveArticles<Article>(para => para, Article.CONTENT_TYPE_NAME);

    // Create new schema-based GeneralArticle items from old Article items
    var reusableAttempts = await ConvertReusableArticles(oldArticles);

    // Update pages that reference old Article items to reference new GeneralArticle items
    var updatedAttempts = await UpdatePageReferences(reusableAttempts);

    // Update related articles in newly created GeneralArticle items to reference other new GeneralArticle items
    return await UpdateRelatedArticlesInNewArticles(updatedAttempts);
}
...
```

If you have a large number of items that leads to timeouts or memory issues, you can use the `Offset` and `OrderBy` extension methods on the `queryFilter` parameter to [process items in batches](https://docs.kentico.com/documentation/developers-and-admins/api/content-item-api/reference-content-item-query#offset).
### Convert reusable items
Now let’s convert our reusable items from the original `Article` content type to the new schema-based `GeneralArticle` type.
#### Create items and language versions in the new type
First, let’s add a method that creates a `GeneralArticle` object based on a provided `Article`, returning the `ContentItemID` of the newly created item.
Use the `IContentItemManager`, and log the success or any errors that might occur to a `ConversionAttempt` object.
C#
**ArticleConverter.cs**
Copy
```
...
private async Task<int> CreateNewReusableArticle(Article oldArticle, string languageName, ConversionAttempt attempt)
{
    // Specify the content type and generic content item properties
    var createParams =
        new CreateContentItemParameters(
            contentTypeName: GeneralArticle.CONTENT_TYPE_NAME,
            name: GetNewReusableArticleName(oldArticle),
            displayName: await GetArticleDisplayName(oldArticle, languageName, attempt),
            languageName: languageName,
            workspaceName: await GetWorkspaceName(oldArticle.SystemFields.ContentItemID)
            );

    // Assemble the field values for a new GeneralArticle item
    var contentItemData = GetContentItemData(oldArticle);

    int newId;

    try
    {
        // Create the new item, log any exceptions that occur
        newId = await contentItemManager.Create(createParams, contentItemData);

        if (newId > 0)
        {
            attempt.LogMessages.Add($"Created new article for [{oldArticle.ArticleTitle}] with new ID [{newId}] in language [{languageName}].");
            attempt.FinishedLanguagesReusable.Add(languageName);
        }
        else
        {
            // Log an exception if the ID is not valid
            throw new Exception($"Invalid ID value [{newId}].");
        }
    }
    catch (Exception ex)
    {
        newId = -1; // Indicate failure

        // Combine exception message with our own, if applicable
        string newMessage = $" Failed to create new article for [{oldArticle.ArticleTitle}] with ID [{oldArticle.SystemFields.ContentItemID}] in language [{languageName}]."
            + (!string.IsNullOrWhiteSpace(ex.Message) ? $" Error: {ex.Message}" : string.Empty);
        attempt.Exceptions.Add(new Exception(newMessage));
    }

    return newId;
}

// Assemble content item data for a new GeneralArticle item based on the provided Article item
private ContentItemData GetContentItemData(Article oldArticle) =>
    new(new Dictionary<string, object> {
        { nameof(GeneralArticle.ArticleSchemaTitle), oldArticle.ArticleTitle },
        { nameof(GeneralArticle.ArticleSchemaSummary), oldArticle.ArticleSummary },
        { nameof(GeneralArticle.ArticleSchemaTeaser), new List<ContentItemReference>(){
                new(){ Identifier = oldArticle.ArticleTeaser.FirstOrDefault()?.SystemFields.ContentItemGUID ?? Guid.Empty }
        }},
        { nameof(GeneralArticle.ArticleSchemaText), oldArticle.ArticleText },
        { nameof(GeneralArticle.ArticleSchemaRelatedArticles), oldArticle
            .ArticleRelatedArticles.Select(oldRef => new ContentItemReference()
            {
                Identifier = oldRef.SystemFields.ContentItemGUID
            })
            .ToList()
        }
    });
...
```

Add another method that creates a new language version of an existing `GeneralArticle` item.
C#
**ArticleConverter.cs**
Copy
```
...
private async Task AddLanguageVersionOfReusableArticle(Article oldArticle, int publishableID, string languageName, ConversionAttempt attempt)
{
    // Specify namespace to distinguish CreateLanguageVariantParameters from CMS.Websites version
    var languageVariantParams = new CMS.ContentEngine.CreateLanguageVariantParameters(
        publishableID,
        oldArticle.ArticleTitle,
        languageName);

    // Assemble the field values for a new language version
    var contentItemData = GetContentItemData(oldArticle);

    try
    {
        if (await contentItemManager.TryCreateLanguageVariant(languageVariantParams, contentItemData))
        {
            // Log success
            attempt.LogMessages.Add($"Added language version [{languageName}] for article [{oldArticle.ArticleTitle}] with ID [{publishableID}].");
            attempt.FinishedLanguagesReusable.Add(languageName);
        }
        else
        {
            // Log an error if TryCreateLanguageVariant returns false without throwing an exception
            throw new Exception();
        }
    }
    catch (Exception ex)
    {
        // Combine exception message with our own, if applicable
        string newMessage = $" Failed to add language version [{languageName}] for article [{oldArticle.ArticleTitle}] with ID [{publishableID}]."
            + (!string.IsNullOrWhiteSpace(ex.Message) ? $" Error: {ex.Message}" : string.Empty);
        attempt.Exceptions.Add(new Exception(newMessage));
    }
}
...
```

#### Handle publishing and scheduling
Now, let’s move on to methods that allow you to publish the reusable items and, if applicable, schedule them for unpublishing. You can use the `IContentItemManager` for this functionality as well.
C#
**ArticleConverter.cs**
Copy
```
...
private async Task PublishReusableArticle(Article oldArticle, int publishableID, string languageName, ConversionAttempt attempt)
{
    try
    {
        // Try to publish the new article
        if (await contentItemManager.TryPublish(publishableID, languageName))
        {
            // Check if the old article was scheduled for unpublish
            var unpublishDate = await GetReusableUnpublishTimeIfScheduled(oldArticle, languageName, attempt);

            // Schedule for unpublish if applicable
            await TryScheduleReusableItem(unpublishDate, oldArticle, publishableID, languageName, attempt);
        }
        else
        {
            // Log an error if TryPublish returns false without throwing an exception
            throw new Exception();
        }
    }
    catch (Exception ex)
    {
        // Combine exception message with our own, if applicable
        string newMessage = $" Failed to publish article [{oldArticle.ArticleTitle}] with ID [{publishableID}] in language [{languageName}]."
            + (!string.IsNullOrWhiteSpace(ex.Message) ? $" Error: {ex.Message}" : string.Empty);
        attempt.Exceptions.Add(new Exception(newMessage));
    }
}

private async Task TryScheduleReusableItem(DateTime? unpublishDate, IContentItemFieldsSource oldItem, int newItemId, string languageName, ConversionAttempt attempt)
{
    try
    {
        // Check if the unpublish date is scheduled
        if (unpublishDate is DateTime unpDate && unpDate > DateTime.MinValue)
        {
            // Schedule unpublish
            await contentItemManager.ScheduleUnpublish(newItemId, languageName, unpDate);
            attempt.LogMessages.Add($"Scheduled unpublish for item [{oldItem.SystemFields.ContentItemName}] with ID [{newItemId}] in language [{languageName}] at [{unpublishDate}].");
        }
    }
    catch (Exception ex)
    {
        // Combine exception message with our own, if applicable
        string newMessage = $" Failed to schedule unpublish for reusable article [{oldItem.SystemFields.ContentItemName}] with ID [{newItemId}] in language [{languageName}]."
            + (!string.IsNullOrWhiteSpace(ex.Message) ? $" Error: {ex.Message}" : string.Empty);
        attempt.Exceptions.Add(new Exception(newMessage));
    }
}

private async Task<DateTime?> GetReusableUnpublishTimeIfScheduled(IContentItemFieldsSource item, string languageName, ConversionAttempt conversionAttempt)
{
    try
    {
        // Check if the unpublish date is scheduled
        if (await contentItemManager.IsUnpublishScheduled(item.SystemFields.ContentItemID, languageName))
        {
            // If the manager says the item is scheduled for unpublish but there's no date, use the maximum possible date, keeping the item published
            return (await contentItemManager.GetContentItemLanguageMetadata(item.SystemFields.ContentItemID, languageName)).ScheduledUnpublishWhen;
        }
    }
    catch (Exception e)
    {
        string newMessage = $" Failed to check or retrieve unpublish date for item [{item.SystemFields.ContentItemName}] with ID [{item.SystemFields.ContentItemID}] in language [{languageName}]."
            + (!string.IsNullOrWhiteSpace(e.Message) ? $" Error: {e.Message}" : string.Empty);
        conversionAttempt.Exceptions.Add(new Exception(newMessage));
    }

    return null;
}
...
```

#### Coordinate reusable article conversion
With item creation, language handling, and publishing in place, we have all the parts we need to build our overarching conversion functionality for reusable items.
Take care to avoid making multiple `ConversionAttempt` objects for the same item. It’s okay if the `OldItem` property does not match the item you are working with at a given time.
C#
**ArticleConverter.cs**
Copy
```
...
private async Task<List<ConversionAttempt>> ConvertReusableArticles(IEnumerable<Article> oldReusableArticles)
{
    List<ConversionAttempt> result = [];

    int schemaArticleId;

    foreach (var publishedOldArticle in oldReusableArticles)
    {
        ConversionAttempt currentAttempt;

        // Find existing attempts for the same item.
        // We are working with multiple language versions here, so there may be multiple entries for items with the same ContentItemID.
        var previousAttemptsForSameItem = result.Where(attempt => attempt.OldArticle.SystemFields.ContentItemID == publishedOldArticle.SystemFields.ContentItemID);

        // Filter previous attempts with valid IDs
        var previousAttemptsForSameItemWithValidIds = previousAttemptsForSameItem.Where(attempt => attempt.NewArticleContentItemId is not null and > 0);

        string languageName = GetContentLanguageName(publishedOldArticle.SystemFields.ContentItemCommonDataContentLanguageID);

        // If there are no previous attempts that contain a valid ID
        if (!previousAttemptsForSameItemWithValidIds.Any())
        {
            // If there is already an attempt for the same old article, but it does not have a new article ID greater than 0, this will reuse it.
            currentAttempt = previousAttemptsForSameItem.FirstOrDefault()
                ?? new ConversionAttempt(publishedOldArticle, null);

            // Create a new article
            schemaArticleId = await CreateNewReusableArticle(publishedOldArticle, languageName, currentAttempt);

            // Log the new article ID in the attempt
            currentAttempt.NewArticleContentItemId = schemaArticleId > 0 ? schemaArticleId : null;

            result.Add(currentAttempt);
        }
        else
        {
            // This should not be null, thanks to the condition of the if statement.
            currentAttempt = previousAttemptsForSameItemWithValidIds.FirstOrDefault() ?? new ConversionAttempt(publishedOldArticle, null);

            // Get the new article ID from the current attempt. This should not be null or 0 thanks to the condition of the if statement.
            schemaArticleId = currentAttempt.NewArticleContentItemId ?? 0;

            // If a version in this language already exists, skip creating a new one.
            if (previousAttemptsForSameItem.Any(attempt => attempt.FinishedLanguagesReusable.Contains(languageName)))
            {
                // Log an exception, as there is a logical error leading us to process the same language version multiple times.
                currentAttempt.Exceptions.Add(new Exception($"Skipped creating new language version for [{publishedOldArticle.ArticleTitle}] with ID [{publishedOldArticle.SystemFields.ContentItemID}] in language [{languageName}] because a version in this language already exists."));
            }
            else
            {
                // If no version exists in this language, add a new language version.
                await AddLanguageVersionOfReusableArticle(publishedOldArticle, schemaArticleId, languageName, currentAttempt);
            }
        }

        await PublishReusableArticle(publishedOldArticle, schemaArticleId, languageName, currentAttempt);

        // Ensure the current attempt is in the result list.
        if (!result.Contains(currentAttempt))
            result.Add(currentAttempt);
    }
    return result;
}
...
```

### Update page references
With the code we’ve authored so far, we have the tools to make schema-based `GeneralArticle` items from each of our `Article` items in Content hub.
However, the _Training guides_ project uses `ArticlePage` items in a web channel to display content from the reusable `Article` items, referencing them via the `ArticlePageContent` field. Let’s remove these references and replace them with references to the new `GeneralArticle` items.
#### Get new page data for the reference
In the _Training guides_ solution, `ArticlePage` items use the **Combined content selector** to select reusable articles from the Content hub, using a `ContentItemReference` object to represent each relationship.
`ContentItemReference` requires the `ContentItemGuid` of the item it is referencing, but we only have a numerical ID for each schema-based `GeneralArticle` that we created. Let’s write a method to retrieve the appropriate GUID for the reference, using our `RetrieveArticles<T>` method from earlier.
C#
**ArticleConverter.cs**
Copy
```
...
// Return the GUID for the schema article with the provided ID
private async Task<Guid?> GetNewArticleItemGuid(int? articleItemId)
{
    if (articleItemId is null)
    {
        return null;
    }

    var articleQuery = await RetrieveArticles<GeneralArticle>(
        query => query
            .Where(where => where.WhereEquals(nameof(Article.SystemFields.ContentItemID), articleItemId))
            .TopN(1),
        GeneralArticle.CONTENT_TYPE_NAME);

    var article = articleQuery.FirstOrDefault();

    if (article is not null)
    {
        return article.SystemFields.ContentItemGUID;
    }
    else
    {
        return null;
    }
}
...
```

#### Update and publish a draft
Even in code, updating the content of a published page requires that we edit and publish a draft. Let’s add methods to update and publish a provided draft.
**Scheduled unpublish**
Earlier in this example, we scheduled our `GeneralArticle` items for unpublish based on their corresponding `Article` items.
This time, we need to preserve the scheduled unpublish date of the item we’re updating _before creating a draft_ , because the date will be lost at that point.
C#
**ArticleConverter.cs**
Copy
```
...
private async Task<bool> UpdatePageDraft(ArticlePage page, string languageName, ConversionAttempt conversionAttempt, DateTime? unpublishDate)
{
    // Get the GUID of the new schema-based article from the conversion attempt
    var schemaArticleGuid = await GetNewArticleItemGuid(conversionAttempt.NewArticleContentItemId);

    if (schemaArticleGuid is Guid newItemGuid)
    {
        // Update the reference based on the conversion attempt
        var contentItemData = new ContentItemData(new Dictionary<string, object>{
            // Clear out the old references to avoid confusion
            { nameof(ArticlePage.ArticlePageContent), new List<ContentItemReference>()},
            // Add the new reference
            { nameof(ArticlePage.ArticlePageArticleContent), new List<ContentItemReference>(){
                new(){ Identifier = newItemGuid }
            }},
            {nameof(ArticlePage.ArticlePagePublishDate), page.ArticlePagePublishDate}
        });

        try
        {
            if (await webPageManager.TryUpdateDraft(page.SystemFields.WebPageItemID, languageName, new UpdateDraftData(contentItemData)))
            {
                if (await webPageManager.TryPublish(page.SystemFields.WebPageItemID, languageName))
                {
                    // Successfully published
                    conversionAttempt.LogMessages.Add($"The draft of page [{page.SystemFields.WebPageItemTreePath}] with ID [{page.SystemFields.ContentItemID}] in language [{languageName}] was updated with a new reference and published successfully.");

                    await TrySchedulePage(unpublishDate, page, languageName, conversionAttempt);
                    return true;
                }
                // Update draft succeeded, but publish failed
                conversionAttempt.Exceptions.Add(new Exception($"The draft of page [{page.SystemFields.WebPageItemTreePath}] with ID [{page.SystemFields.ContentItemID}] in language [{languageName}] was updated with a new reference, but it failed to publish."));
                return false;
            }
        }
        catch (Exception ex)
        {
            // Update draft threw an exception
            conversionAttempt.Exceptions.Add(new Exception($"Updating the draft of page [{page.SystemFields.WebPageItemTreePath}] with ID [{page.SystemFields.ContentItemID}] in language [{languageName}] failed with an exception: {ex.Message}"));
            return false;
        }
        // Update draft failed
        conversionAttempt.Exceptions.Add(new Exception($"Draft of page [{page.SystemFields.WebPageItemTreePath}] with ID [{page.SystemFields.ContentItemID}] in language [{languageName}] could not be updated with the new reference."));
        return false;
    }
    else
    {
        // Failed to retrieve content item GUID based on ID - can't find the new article
        conversionAttempt.Exceptions.Add(new Exception($"The new article with ID [{conversionAttempt.NewArticleContentItemId}] could not be found in language [{languageName}] to update the page reference."));
        return false;
    }
}

private async Task TrySchedulePage(DateTime? unpublishDate, ArticlePage page, string languageName, ConversionAttempt conversionAttempt)
{
    try
    {
        // Check if the unpublish date is scheduled
        if (unpublishDate is DateTime unpDate && unpDate > DateTime.MinValue)
        {
            // Schedule unpublish
            await webPageManager.ScheduleUnpublish(page.SystemFields.WebPageItemID, languageName, unpDate);
            conversionAttempt.LogMessages.Add($"Scheduled unpublish for page [{page.SystemFields.WebPageItemTreePath}] with ID [{page.SystemFields.ContentItemID}] in language [{languageName}] at [{unpublishDate}].");
        }
    }
    catch (Exception e)
    {
        string newMessage = $" Failed to schedule unpublish for page [{page.SystemFields.WebPageItemTreePath}] with ID [{page.SystemFields.ContentItemID}] in language [{languageName}]."
            + (!string.IsNullOrWhiteSpace(e.Message) ? $" Error: {e.Message}" : string.Empty);
        conversionAttempt.Exceptions.Add(new Exception(newMessage));
    }
}


...
```

#### Manage page drafts for conversion
With our code for updating and publishing a page draft in place, we need to provide the draft for updating, along with an unpublish date if applicable.
Let’s use the `IWebPageManager` to retrieve the unpublish date of the provided page, then try to create a draft.
We’ll log an error if draft creation failed, but still try to process the page in case an existing draft caused the failure.
C#
**ArticleConverter.cs**
Copy
```
...
private async Task CreateAndOrUpdatePageDraft(ArticlePage page, string languageName, ConversionAttempt conversionAttempt)
{
    // Before we create a new draft, check if the existing page has a scheduled unpublish date.
    var unpublishDate = await GetPageUnpublishTimeIfScheduled(page, languageName, conversionAttempt);

    // Create a new draft with the same properties, or update the existing draft if it exists
    // This may be false if the published page already has a draft, in which case we will try to update the existing draft.
    bool newCreated = false;

    try
    {
        newCreated = await webPageManager.TryCreateDraft(page.SystemFields.WebPageItemID, languageName);
    }
    catch (Exception ex)
    {
        // Exception thrown when creating new draft
        string newMessage = $" Failed to create a new draft for the page [{page.SystemFields.WebPageItemTreePath}] with ID [{page.SystemFields.ContentItemID}] in language [{languageName}]."
            + (!string.IsNullOrWhiteSpace(ex.Message) ? $" Error: {ex.Message}" : string.Empty);
        conversionAttempt.Exceptions.Add(new Exception(newMessage));
    }

    // Update draft with new reference
    bool updated = await UpdatePageDraft(page, languageName, conversionAttempt, unpublishDate);

    // If we could not create a new draft or update an existing draft, log an exception
    if (!(updated || newCreated))
    {
        conversionAttempt.Exceptions.Add(new Exception($"A new draft could not be created for the page [{page.SystemFields.WebPageItemTreePath}] with ID [{page.SystemFields.ContentItemID}] in language [{languageName}], and an existing draft could not be updated with a new reference."));
    }
}

private async Task<DateTime?> GetPageUnpublishTimeIfScheduled(ArticlePage page, string languageName, ConversionAttempt conversionAttempt)
{
    try
    {
        // Check if the unpublish date is scheduled
        if (await webPageManager.IsUnpublishScheduled(page.SystemFields.WebPageItemID, languageName))
        {
            // If the manager says the item is scheduled for unpublish but there's no date, use the maximum possible date, keeping the item published
            return (await webPageManager.GetContentItemLanguageMetadata(page.SystemFields.WebPageItemID, languageName)).ScheduledUnpublishWhen;
        }
    }
    catch (Exception e)
    {
        string newMessage = $" Failed to check or retrieve unpublish date for page [{page.SystemFields.WebPageItemTreePath}] with ID [{page.SystemFields.ContentItemID}] in language [{languageName}]."
            + (!string.IsNullOrWhiteSpace(e.Message) ? $" Error: {e.Message}" : string.Empty);
        conversionAttempt.Exceptions.Add(new Exception(newMessage));
    }

    return null;
}
...
```

#### Coordinate updates to pages
Now that we have the code to handle individual pages, let’s manage the updates to all pages that reference the now-converted reusable `Article` items.
The methods we’ve already created will do the heavy lifting, so this part is fairly straightforward: we iterate through conversion attempts and call the appropriate methods for each page.
C#
**ArticleConverter.cs**
Copy
```
...
private async Task<List<ConversionAttempt>> UpdatePageReferences(List<ConversionAttempt> conversionAttempts)
{
    // Focus on attempts with a valid GeneralArticle ID
    foreach (var conversionAttempt in conversionAttempts.Where(attempt => attempt.NewArticleContentItemId is not null and > 0))
    {
        // Retrieve all published pages linking to the old article. This includes all languages, as the content item ID is shared between languages.
        var publishedPages = await RetrieveArticlePagesLinkingArticle(conversionAttempt.OldArticle.SystemFields.ContentItemID);

        // Iterate through the language versions of the page
        foreach (var page in publishedPages)
        {
            // Get the language name from the ID
            string languageName = GetContentLanguageName(page.SystemFields.ContentItemCommonDataContentLanguageID);

            // Try to create a new draft and update the reference. Use the existing draft if it exists.
            await CreateAndOrUpdatePageDraft(page, languageName, conversionAttempt);
        }
    }
    return conversionAttempts;
}
...
```

### Update reusable item references
In our example, `ArticlePage` items are not the only objects that reference old `Article` items.
`Article` also contains the field `ArticleRelatedArticles`, and we copied its value into `ArticleSchemaRelatedArticles` [earlier](#create-items-and-language-versions-in-the-new-type).
This means some of our new schema-based articles could still reference old `Article` items. We can ignore references between old articles, since we will delete all of them later.
You can optionally update these references before or after page references. We chose to do it later since the scenario is slightly more complicated, in that we need to conditionally decide which references to update (those that point to `Article` items) and which references to preserve (any other content types).
#### Build a reference list
For each content item, we’ll need to build a new list of references for its `ArticleSchemaRelatedArticles` field.
As part of this process, we’ll need to find the GUID of the new `GeneralArticle` that corresponds to the old `Article`. Thankfully, we created the `GetNewReusableArticleName` method to determine the code name of each new `GeneralArticle` [earlier](#retrieve-and-format-data), which we can use to look up the schema-based article in a new `GetNewArticleItemGuid` overload.
C#
**ArticleConverter.cs**
Copy
```
...
// Conditionally update ContentItemReference list for related articles
private async Task<List<ContentItemReference>> BuildUpdatedRelatedArticlesList(GeneralArticle article, string languageName, ConversionAttempt conversionAttempt)
{
    List<ContentItemReference> relatedArticles = [];

    foreach (var relatedArticle in article.ArticleSchemaRelatedArticles)
    {
        // Only update references to old Article items
        if (relatedArticle is Article oldArticle)
        {
            // Find the GUID of the schema-based article that corresponds to the old article
            var newArticleGuid = await GetNewArticleItemGuid(oldArticle);

            if (newArticleGuid is Guid newGuid)
            {
                // Update the reference if the GUID is valid
                relatedArticles.Add(new ContentItemReference() { Identifier = newGuid });
            }
            else
            {
                // Log an exception if the new GUID could not be found
                conversionAttempt.Exceptions.Add(new Exception($" Failed to find new article for related article [{oldArticle.ArticleTitle}] with ID [{oldArticle.SystemFields.ContentItemID}] in language [{languageName}]."));
            }
        }
        else
        {
            // If the related article is not of type Article, keep the existing reference
            relatedArticles.Add(new ContentItemReference() { Identifier = relatedArticle.SystemFields.ContentItemGUID });
        }
    }

    return relatedArticles;
}

// Get the GUID for the GeneralArticle corresponding to the provided Article
private async Task<Guid?> GetNewArticleItemGuid(Article oldArticle)
{
    // Use the same method that generated the codename of the new article.
    string newArticleCodeName = GetNewReusableArticleName(oldArticle);

    // Retrieve the new article based on the generated codename
    var newArticle = (await RetrieveArticles<GeneralArticle>(
        query => query
            .Where(where => where.WhereEquals(nameof(Article.SystemFields.ContentItemName), newArticleCodeName))
            .TopN(1),
        GeneralArticle.CONTENT_TYPE_NAME)).FirstOrDefault();

    // Return the GUID of the new article, or null if not found
    return newArticle?.SystemFields.ContentItemGUID;
}
...
```

#### Update and publish new related articles references
To update a draft with new related articles, we can reuse logic from [when we updated pages](#manage-page-drafts-for-conversion), replacing `IWebPageManager` with `IContentItemManager`.
C#
**ArticleConverter.cs**
Copy
```
...
private async Task CreateOrUpdateItemDraftRelatedArticles(GeneralArticle article, string languageName, ConversionAttempt conversionAttempt)
{
    // Before we create a new draft, check if the existing article has a scheduled unpublish date
    var unpublishDate = await GetReusableUnpublishTimeIfScheduled(article, languageName, conversionAttempt);

    // Create a list of ContentItemReference objects for the updated related articles
    var relatedArticles = await BuildUpdatedRelatedArticlesList(article, languageName, conversionAttempt);

    // Assemble updated ContentItemData
    var contentItemData = new ContentItemData(new Dictionary<string, object> {
        {nameof(GeneralArticle.ArticleSchemaTitle), article.ArticleSchemaTitle},
        {nameof(GeneralArticle.ArticleSchemaSummary), article.ArticleSchemaSummary},
        {nameof(GeneralArticle.ArticleSchemaTeaser), new List<ContentItemReference>(){
                new(){ Identifier = article.ArticleSchemaTeaser.FirstOrDefault()?.SystemFields.ContentItemGUID ?? Guid.Empty }
        }},
        {nameof(GeneralArticle.ArticleSchemaText), article.ArticleSchemaText},
        {nameof(GeneralArticle.ArticleSchemaRelatedArticles), relatedArticles}
    });

    bool newCreated = false;

    try
    {
        newCreated = await contentItemManager.TryCreateDraft(article.SystemFields.ContentItemID, languageName);
    }
    catch (Exception ex)
    {
        string newMessage = $" Failed to create draft for schema article [{article.ArticleSchemaTitle}] with ID [{article.SystemFields.ContentItemID}] in language [{languageName}] to update its related articles."
            + (!string.IsNullOrWhiteSpace(ex.Message) ? $" Error: {ex.Message}" : string.Empty);
        conversionAttempt.Exceptions.Add(new Exception(newMessage));
    }

    // Update draft with new reference
    bool updated = await UpdateItemDraftRelatedArticles(article, contentItemData, languageName, conversionAttempt, unpublishDate);

    // If we could not create a new draft or update an existing draft, log an exception
    if (!newCreated && !updated)
    {
        conversionAttempt.Exceptions.Add(new Exception($"Could not create draft or update existing draft for schema article [{article.ArticleSchemaTitle}] with ID [{article.SystemFields.ContentItemID}] in language [{languageName}] to update its related articles."));
    }
}

private async Task<bool> UpdateItemDraftRelatedArticles(GeneralArticle article, ContentItemData contentItemData, string languageName, ConversionAttempt conversionAttempt, DateTime? unpublishDate)
{
    try
    {
        if (await contentItemManager.TryUpdateDraft(article.SystemFields.ContentItemID, languageName, contentItemData))
        {
            if (await contentItemManager.TryPublish(article.SystemFields.ContentItemID, languageName))
            {
                // Success if trying to publish
                conversionAttempt.LogMessages.Add($"The draft of schema article [{article.ArticleSchemaTitle}] with ID [{article.SystemFields.ContentItemID}] in language [{languageName}] was updated with new related articles and published successfully.");

                // Schedule unpublish if applicable
                await TryScheduleReusableItem(unpublishDate, article, article.SystemFields.ContentItemID, languageName, conversionAttempt);

                return true;
            }
            // Update draft succeeded, but publish failed
            conversionAttempt.Exceptions.Add(new Exception($"The draft of reusable article [{article.ArticleSchemaTitle}] with ID [{article.SystemFields.ContentItemID}] in language [{languageName}] was updated with new related articles, but it failed to publish."));
            return false;
        }
    }
    catch (Exception ex)
    {
        // Update draft threw an exception
        conversionAttempt.Exceptions.Add(new Exception($"Updating the draft of schema article [{article.ArticleSchemaTitle}] with ID [{article.SystemFields.ContentItemID}] in language [{languageName}] to update related articles failed with an exception: {ex.Message}"));
        return false;
    }
    // Update draft failed
    conversionAttempt.Exceptions.Add(new Exception($"Draft of schema article [{article.ArticleSchemaTitle}] with ID [{article.SystemFields.ContentItemID}] in language [{languageName}] could not be updated with the new related articles."));
    return false;
}
...
```

#### Coordinate related article updates
With this code in place, we can organize the process of updating _related article_ references.
Let’s take the opportunity to explore a slightly different approach this time, using a single query to retrieve all of our converted `GeneralArticle` items instead of a separate query per `ConversionAttempt`. We can iterate through the resulting items and link them to the corresponding `ConversionAttempt` afterward.
C#
**ArticleConverter.cs**
Copy
```
...
private async Task<List<ConversionAttempt>> UpdateRelatedArticlesInNewArticles(List<ConversionAttempt> conversionAttempts)
{
    // Retrieve all of our newly created schema articles
    var schemaArticles = await RetrieveArticles<GeneralArticle>(
        query => query
            .Where(where => where.WhereIn(nameof(GeneralArticle.SystemFields.ContentItemID), conversionAttempts
                .Where(attempt => attempt.NewArticleContentItemId is not null and > 0)
                .Select(attempt => attempt.NewArticleContentItemId!.Value)))
            .WithLinkedItems(3),
        GeneralArticle.CONTENT_TYPE_NAME);

    // We want to skip this logic for schema articles that aren't related to old articles
    var applicableSchemaArticles = schemaArticles
        .Where(article => article.ArticleSchemaRelatedArticles.Any(relatedArticle => relatedArticle is Article));

    foreach (var schemaArticle in applicableSchemaArticles)
    {
        // Find the corresponding conversion attempt
        var conversionAttempt = conversionAttempts
            .Where(attempt => attempt.NewArticleContentItemId == schemaArticle.SystemFields.ContentItemID)
            .FirstOrDefault();

        if (conversionAttempt is null)
        {
            // If no conversion attempt is found, create a new one, then log an exception in it
            var oldArticle = new Article()
            {
                ArticleTitle = $"Error"
            };
            conversionAttempt = new ConversionAttempt(oldArticle, schemaArticle.SystemFields.ContentItemID);

            conversionAttempt.Exceptions.Add(new Exception($"Could not find conversion attempt for new article [{schemaArticle.ArticleSchemaTitle}] with ID [{schemaArticle.SystemFields.ContentItemID}]"));

            conversionAttempts.Add(conversionAttempt);
        }

        string languageName = GetContentLanguageName(schemaArticle.SystemFields.ContentItemCommonDataContentLanguageID);

        // Update the related articles field of the newly created schema articles, so that they point to new articles instead of old ones
        await CreateOrUpdateItemDraftRelatedArticles(schemaArticle, languageName, conversionAttempt);
    }

    return conversionAttempts;
}
...
```

This completes the `ArticleConverter` class. In the end, your code should look like this: [ArticleConverter.cs](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/OneTimeCode/ArticleConverter.cs)
### Register your code
Assuming we want dependency injection to automatically populate the services required by our code, let’s register it. We can remove its registration after we successfully convert our articles.
Add a transient service to the **ServiceCollectionExtensions.cs** file in the root of the _TrainingGuides.Web_ project.
C#
**~/ServiceCollectionExtensions.cs**
Copy
```
...
using TrainingGuides.Web.OneTimeCode;

namespace TrainingGuides.Web;

public static class ServiceCollectionExtensions
{
    public static void AddTrainingGuidesServices(this IServiceCollection services)
    {
        ...
        // Since the ArticleConverter is one-time code, meant to be deleted after its first use, we won't bother with an interface.
        services.AddTransient<ArticleConverter>();
        ...
    }
    ...
}
 


```

## Execute the conversion
With our code to convert articles in place, we need a way to trigger execution.
In this example, we’ll show a proof-of-concept for a simple controller that executes the code when you visit the provided URL.
In real-world scenarios, we only recommend this approach if you have copied your production database to a secure local machine for remodeling, and plan to re-deploy after making the changes.
If you plan to run this kind of code in any kind of public-facing environment, we recommend a more secure approach. For example:
  * Create a controller with a secure POST endpoint, and only execute the code upon successful validation of a secret key in the request body. This way, only people who know the secret key and the endpoint can trigger the code.
  * Create a [custom UI page](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) in the Xperience admin UI that calls the code in its `ConfigurePage` method, or upon a [button click](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-commands). Then, only people with access to the Xperience admin UI can trigger the code. You can also apply [permissions](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-permission-checks) to further control access.
  * Create a [custom scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks) that executes your code. Set the **Task schedule** configuration to _Once_ , so that the system does not automatically run the task, so that users with access to the scheduled tasks application in the Xperience admin UI can manually trigger execution.


Regardless of the approach you take, make sure to create a backup of your database prior to running the conversion, in case something goes wrong.
### Define a controller
Returning to the _~/OneTimeCode_ folder, add a new controller with a single GET action on the path _ConvertArticles_.
Call our `ArticleConverter` code and pass the resulting collection of `ConversionAttempt` objects as the model.
Supply the path to a view which we will implement in the next step.
C#
**ConvertArticlesController.cs**
Copy
```
using Microsoft.AspNetCore.Mvc;

namespace TrainingGuides.Web.OneTimeCode;

public class ConvertArticlesController(ArticleConverter articleConverter) : Controller
{
    [HttpGet("/ConvertArticles")]
    public async Task<IActionResult> Test()
    {
        var attempts = await articleConverter.Convert();
        return View("~/OneTimeCode/ConvertArticlesView.cshtml", attempts);
    }
}
```

Depending on your scenario, you may want to iterate through the results and [log the collected messages and exceptions](/documentation/developers-and-admins/development/logging) using `ILogger`. In this example, we will simply print them on the page for simplicity and formatting.
### Add a view
With the controller in place, we can display the results in a simple view.
C#
**ConvertArticlesView.cshtml**
Copy
```
@using TrainingGuides.Web.OneTimeCode

@model IEnumerable<ConversionAttempt>

@{
    Layout = null;
}

@foreach (ConversionAttempt attempt in Model)
{
    <div style="border-bottom:1px solid black; margin-bottom:5px; padding-bottom:20px; margin-top:5px;">
        <strong>OldArticle:</strong>
        <div>@($"{attempt.OldArticle.ArticleTitle} ({attempt.OldArticle.SystemFields?.ContentItemGUID}) ({attempt.OldArticle.SystemFields?.ContentItemCommonDataContentLanguageID})")</div>
        <ul style="color:grey">
            @foreach (string log in attempt.LogMessages)
            {
                <li>@log</li>
            }
        </ul>
        <ul style="color:red">
            @foreach (var error in attempt.Exceptions)
            {
                <li>@error.Message</li>
            }
        </ul>
    </div>
}
```

### Visit the endpoint
**Make sure you create a backup of your database before executing the code**
Now, you can run the project and trigger the code by visiting the **/ConvertArticles** path in your browser.
[![GIF showing the result of visiting the /ConvertArticles path](/docsassets/guides/convert-content-to-reusable-schemas/ConvertArticles.gif)](/docsassets/guides/convert-content-to-reusable-schemas/ConvertArticles.gif)
Depending on the number of items you are converting, our code may take a while to execute and heavily utilize resources. We recommend running this kind of code during a maintenance window, or during off-peak hours, so performance is less of a concern.
You can see the results in the admin UI:
Your browser does not support the video tag. 
## Clean up
Now our pages point to our new schema-based articles instead of our original `Article` items, but the old items still exist. Moreover, references to the `Article` type exist in several places throughout the solution, like generated classes, widgets, services, and the code we just executed.
### Delete old Article items
If you filter the Content hub listing by the old **Article** content type, you can use the mass actions to delete several items at a time.
Alternately, you can extend our existing code to [Delete the articles](/api/content-management/content-items#delete-content-items) after they are successfully converted.
### Consider references from other content types
Before you delete a content type, we recommend taking inventory of any other types that reference it, which you did not choose to update via code. This serves as a refresher on the other functionality touched by the type.
**Deleting types referenced by other types**
You can delete content types that are still referenced by other types, but the form controls that reference them will display a **DELETED ITEM** message:
[![Screenshot of the message displayed in the UI when a content type references a deleted content type.](/docsassets/guides/convert-content-to-reusable-schemas/deleted-item-ui.png)](/docsassets/guides/convert-content-to-reusable-schemas/deleted-item-ui.png)
The `ClassFormDefinition` column of the `CMS_Class` table stores field and form control configuration for content types, including content types assigned to the _Combined content selector_. Xperience stores this data as an array of content type GUIDs:
XML
**Allowed content types sample (ClassFormDefinition)**
Copy
```
...
<AllowedContentItemTypeIdentifiers>["17a2abf5-c412-4cee-8b6b-e5209bcd3e8c","8afff782-a445-4e6b-a237-821fca0db4fb","da713409-64bd-4bdd-bf21-3ec8294ab1b6"]</AllowedContentItemTypeIdentifiers>
...
```

To find your content type’s GUID, check its row in the CMS_Class table.
SQL
**Find a content type’s GUID**
Copy
```
SELECT ClassDisplayName, ClassName, ClassGuid FROM CMS_Class WHERE ClassName = 'TrainingGuides.Article'
```

Then, you can use the GUID in a query to find other content types that reference it.
SQL
**Find content types that reference another**
Copy
```
SELECT ClassDisplayName, ClassName, ClassFormDefinition FROM CMS_Class
WHERE ClassFormDefinition LIKE '%YOUR_GUID_HERE%'
```

Depending on your SQL Server version, you may be able to use the `REGEXP_LIKE` function for more accurate matching. This is ideal if you want to check for references from specific fields or form controls.
Reusable field schemas are defined in the `CMS.ContentItemCommonData` class. You can determine which fields belong to which schemas by matching the value of their `kxp_schema_identifier` element to the `guid` attribute of a `schema` element.
XML
**ClassFormDefinition of CMS.ContentItemCommonData sample**
Copy
```
...
<schema guid="c3b4896f-ba7c-4b75-9cd4-47afa7489ff1" name="ArticleSchema">
    <properties>
        <fieldcaption>Article schema</fieldcaption>
    </properties>
</schema>
<field allowempty="true" column="ArticleSchemaTitle" columnprecision="0" columnsize="300" columntype="text" enabled="true" guid="7f920142-174c-41cd-8e92-cc40e80aabdb" visible="true">
    <properties>
        <explanationtextashtml>False</explanationtextashtml>
        <fieldcaption>Title</fieldcaption>
        <fielddescriptionashtml>False</fielddescriptionashtml>
        <kxp_schema_identifier>c3b4896f-ba7c-4b75-9cd4-47afa7489ff1</kxp_schema_identifier>
    </properties>
    <settings>
        <controlname>Kentico.Administration.TextInput</controlname>
    </settings>
</field>
...
 



 


```

If you discover a reference you’d like to change to point to new items, as we did with page references and related articles in our code, you can restore a backup and further expand the one-time code.
**Updating references in builder properties**
Updating references from the properties of builder objects like widgets, templates, and sections falls outside the scope of this example.
The process of finding the replacement GUID would be the same as we used while updating references from content types, but you’d need to find those references in the JSON of fields like `ContentItemCommonDataInfo.ContentItemCommonDataVisualBuilderWidgets` and `ContentItemCommonDataInfo.ContentItemCommonDataVisualBuilderTemplateConfiguration`. Working with these fields via API requires use of [API not intended for public use](/documentation/developers-and-admins/customization/stable-customization-guidelines#api-not-intended-for-public-use), from the `CMS.ContentEngine.Internal` namespace.
### Delete the old content type
Once you’re sure everything has been properly migrated to the new content type, you can delete the old one from the listing view in the **Content types** application.
At this point, you’ve completed the transformation of your Article content model. The old content types no longer exist alongside the new ones, and you can move forward with schema-based articles only.
### Get rid of the one-time code
After a successful conversion, you no longer need the `ArticleConverter` code, so you can delete all the files we’ve created throughout this example, and remove the reference to the `ArticleConverter` class from **ServiceCollectionExtensions.cs**.
### Remove the content type from your code
You likely reference the old content type throughout your application, for example, in repositories, facades, services, widgets, etc.
If you navigate to the generated class file for your type (_TrainingGuides.Entities/ReusableContentTypes/Article_ for our example) you can use your IDE to find all references to the type and its members throughout the solution.
Depending on which of our training materials you’ve followed along with and which branch you started with, you’ll find code that utilizes the `Article` type alongside the `IArticleSchema` in several places, including the `ArticlePageService`, `ArticlePageServiceTests` `ArticleEmailWidgetModelMapper`, and `NatureSpotlightEmailService`.
## What’s next?
Congratulations on completing the article remodeling scenario!
To see an example about working with Smart folder data in content delivery, check out [the next advanced content guide](/guides/development/advanced-content/deliver-content-dynamically-with-smart-folders).
If you have ideas you’d like us to cover in future guides, or if you’ve encountered any problems or issues in our existing materials, please click the **Send us feedback** button below to let us know.
![]()
[]()[]()
