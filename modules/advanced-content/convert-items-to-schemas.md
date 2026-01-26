---
source: https://docs.kentico.com/modules/advanced-content/convert-items-to-schemas
scrape_date: 2026-01-26
---

Module: Advanced content
7 of 11 Pages
# Convert flat items to schemas
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

[ Previous page ](/modules/advanced-content/prepare-to-convert-items)
7 of 11
[ Mark complete and continue ](/modules/advanced-content/update-references)
![]()
[]()[]()
