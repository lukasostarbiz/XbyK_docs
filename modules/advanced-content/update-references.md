---
source: https://docs.kentico.com/modules/advanced-content/update-references
scrape_date: 2026-01-26
---

Module: Advanced content
8 of 11 Pages
# Update references to converted items
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
`Article` also contains the field `ArticleRelatedArticles`, and we copied its value into `ArticleSchemaRelatedArticles` [earlier](/modules/advanced-content/convert-items-to-schemas).
This means some of our new schema-based articles could still reference old `Article` items. We can ignore references between old articles, since we will delete all of them later.
You can optionally update these references before or after page references. We chose to do it later since the scenario is slightly more complicated, in that we need to conditionally decide which references to update (those that point to `Article` items) and which references to preserve (any other content types).
#### Build a reference list
For each content item, we’ll need to build a new list of references for its `ArticleSchemaRelatedArticles` field.
As part of this process, we’ll need to find the GUID of the new `GeneralArticle` that corresponds to the old `Article`. Thankfully, we created the `GetNewReusableArticleName` method to determine the code name of each new `GeneralArticle` [earlier](/modules/advanced-content/prepare-to-convert-items), which we can use to look up the schema-based article in a new `GetNewArticleItemGuid` overload.
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

[ Previous page ](/modules/advanced-content/convert-items-to-schemas)
8 of 11
[ Mark complete and continue ](/modules/advanced-content/execute-conversion-and-cleanup)
![]()
[]()[]()
