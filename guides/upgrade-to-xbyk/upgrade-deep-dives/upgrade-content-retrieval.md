---
source: https://docs.kentico.com/guides/upgrade-to-xbyk/upgrade-deep-dives/upgrade-content-retrieval
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Upgrade to Xperience by Kentico](/guides/upgrade-to-xbyk)
  * [Upgrade deep dives for developers](/guides/upgrade-to-xbyk/upgrade-deep-dives)
  * Upgrade your content retrieval code 


# Upgrade your content retrieval code
If you’ve followed along with our [upgrade walkthrough](/guides/upgrade-to-xbyk/upgrade-walkthrough), you’ve already seen several examples of converting `IPageRetriever` code from Kentico Xperience 13 (KX13) to Xperience by Kentico (XbyK) APIs.
While `IPageRetriever` was the recommended approach for live sites in KX13, many upgraded KX13 projects still use other methods for retrieving content, such as `DocumentHelper` and `TreeProvider`. These options are all built on `DocumentQuery` and `MultiDocumentQuery`, and share similar extension methods for parameterization.
We’ve put together some practical examples, inspired by real technical support cases and educational scenarios, to explore how to replicate these KX13 page retrieval scenarios using the [ContentRetriever](/documentation/developers-and-admins/api/content-item-api/content-retriever-api) and [Content item query](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) APIs.
## Filter columns
Let’s start out with a sample inspired by the [KX13 documentation](/13/developing-websites/form-builder-development/developing-form-components/using-a-dynamic-data-source-with-selector-components).
This example uses `DocumentHelper`, which by default selects the most recently-edited versions of pages under workflow. This made it ideal for working with preview data in older Kentico versions, but it could also work with published data using the `PublishedVersion` extension method.
Let’s take a look at the KX13 code:
C#
**KX13**
Copy
```
// Get Articles, selecting the latest edited version (DocumentHelper)
var articles = DocumentHelper.GetDocuments("DancingGoatCore.Article")
    // Filter the results to include children of the "Articles" page
    .Path("/Articles/", PathTypeEnum.Children)
    // Only retrieve the specified columns
    .Columns("DocumentName", "DocumentGUID")
    // Specify which site to query pages from
    .OnSite("DancingGoatCore")
    // Specify the culture (language) version to look for
    .Culture("en-us")
    // Execute query and get results in enumerable format
    .ToList();
```

We can achieve the same result in XbyK using `IContentRetriever`.
Let’s assume we’ve migrated KX13 pages to a [website channel](/documentation/developers-and-admins/configuration/website-channel-management) in XbyK, rather than converting them to reusable items in the [Content hub](/documentation/business-users/content-hub). We’ll also assume that `contentRetriever` is an instance of `IContentRetriever` supplied through dependency injection.
C#
**XbyK**
Copy
```
var parameters = new RetrievePagesParameters
{
    // Specify the culture language version to look for
    LanguageName = "en-US",
    // Ensure the latest edited version of pages under workflow
    IsForPreview = true,
    // Filter the results to include children of the "Articles" page
    PathMatch = PathMatch.Children("/Articles/"),
    // Specify which site to query web page items from
    ChannelName = "DancingGoatCore"
};

// Get Articles in enumerable format
var articles = await contentRetriever.RetrievePages<Article>(
    parameters: parameters,
    additionalQueryConfiguration: query => query
        // Only retrieve the specified columns
        .Columns(nameof(Article.DocumentName), nameof(Article.SystemFields.ContentItemGUID)),
    cacheSettings: RetrievalCacheSettings.CacheDisabled
);
```

**Optional parameters**
Many of the properties specified in `RetrievePagesParameters`, such as `ChannelName` and `LanguageName`, will automatically resolve to relevant values based on the request context. The same applies to similar objects throughout this guide.
## Select items from several content types
Next, let’s explore a query that selects pages from multiple content types based on a provided set of IDs.
In these examples, assume that `idsToSelect` is an enumerable set of IDs provided by some API.
C#
**KX13**
Copy
```
// Get published versions of pages (TreeProvider)
TreeProvider tree = new TreeProvider(MembershipContext.AuthenticatedUser);
// Select pages
var pages = Tree.SelectNodes()
    // Choose only pages whose IDs are in the provided set
    .WhereIn("DocumentID", idsToSelect)
    // Specify the culture (language) version to look for
    .Culture("en-us")
    // Specify which site to query pages from
    .OnSite("DancingGoatCore")
    // Execute query to get results in enumerable format
    .ToList();
```

Let’s look at two ways to achieve this in XbyK. First, we’ll use another `IContentRetriever` instance (`contentRetriever`) to select from a list of known content types. The resulting `IWebPageFieldsSource` objects contain valuable information, such as the tree path and URL of the resulting web pages. You can cast these objects into your strongly-typed generated models as needed.
C#
**XbyK - ContentRetriever**
Copy
```
// Identifiers of all content types we want to query
string[] productContentTypes = {
    DancingGoatCore.Brewer.CONTENT_TYPE_NAME,
    DancingGoatCore.Coffee.CONTENT_TYPE_NAME,
    DancingGoatCore.ElectricGrinder.CONTENT_TYPE_NAME,
    DancingGoatCore.FilterPack.CONTENT_TYPE_NAME,
    DancingGoatCore.ManualGrinder.CONTENT_TYPE_NAME,
    DancingGoatCore.Tableware.CONTENT_TYPE_NAME,
    };

var parameters = new RetrievePagesOfContentTypesParameters
{
    // Specify the language version to look for
    LanguageName = "en-US",
    // Specify which channel to query pages from
    ChannelName = "DancingGoatCore"
};

// Retrieve pages in enumerable format
var pages = await contentRetriever.RetrievePagesOfContentTypes<IWebPageFieldsSource>(
    // Specify the list of content types to query
    contentTypeNames: productContentTypes,
    parameters: parameters,
    additionalQueryConfiguration: query => query
    // Choose only pages whose IDs are in the provided set
    .Where(where => where.WhereIn(nameof(IWebPageFieldsSource.SystemFields.WebPageItemID), idsToSelect)),
    // Disable caching for demo purposes
    cacheSettings: RetrievalCacheSettings.CacheDisabled
);
```

If you don’t want to provide a specific list of content types to query, you can use the `ContentItemQueryBuilder` instead.
For this example, `contentQueryExecutor` is an instance of `IContentQueryExecutor`, populated with dependency injection.
C#
**XbyK - ContentItemQueryBuilder**
Copy
```
var builder = new ContentItemQueryBuilder()
    // Retrieve multiple content types, no list necessary
    .ForContentTypes(query => query
        .WithLinkedItems(1, options => options.IncludeWebPageData(true))
        // Specify the channel
        .ForWebsite("DancingGoatCore"))
    .Parameters(query => query
        // Choose only pages whose IDs are in the provided set
        .Where(where => where.WhereIn(nameof(IWebPageFieldsSource.SystemFields.WebPageItemID), idsToSelect)))
    // specify language
    .InLanguage("en-US");

var queryExecutorOptions = new ContentQueryExecutionOptions
{
    // Get published version of web page items
    ForPreview = false
};

// Retrieve pages in enumerable
var pages = await contentQueryExecutor.GetMappedResult<IWebPageFieldsSource>(builder, queryExecutorOptions);  format
```

The `ContentItemQueryBuilder` sample here includes a hard-coded language for the sake of illustration.
If you need to [resolve the current visitor’s preferred language dynamically](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#access-current-preferred-language), you can inject an instance of `IPreferredLanguageRetriever`, and call the `Get()` method.
This happens automatically if you do not supply a language name to `IContentRetriever`.
## Use a complex sorting condition
Now let’s look at another scenario, based on a support case we received, where products of a certain brand are prioritized.
This KX13 query selects brewers, promoting Hario brewers to the top of the list and sorting the remaining brewers alphabetically.
C#
**KX13**
Copy
```
// Retrieve brewers, selecting the latest edited version (DocumentHelper)
var brewers = DocumentHelper.GetDocuments("DancingGoatCore.Brewer")
    // Specify which site to query pages from
    .OnSite("DancingGoatCore")
    // Specify the culture (language) version to look for
    .Culture("en-us")
    // Define a custom sorting order
    .OrderBy("CASE WHEN DocumentName LIKE 'Hario%' THEN 0 ELSE 1 END, DocumentName")
    // Execute query to get results in enumerable format
    .ToList();
```

In XbyK, you can use the same T-SQL syntax in the `string[]` overload of the `OrderBy` extension method for `RetrievePagesQueryParameters`.
C#
**XbyK**
Copy
```
var parameters = new RetrievePagesParameters
{
    // Specify the language version to look for
    LanguageName = "en-US",
    // Ensure the latest edited version of pages under workflow
    IsForPreview = true,
    // Specify which channel to query pages from
    ChannelName = "DancingGoatCore"
};

// Retrieve brewers
var brewers = await contentRetriever.RetrievePages<Brewer>(
    parameters: parameters,
    additionalQueryConfiguration: query => query
        // Define a custom sorting order
        .OrderBy("CASE WHEN ContentItemName LIKE 'Hario%' THEN 0 ELSE 1 END, ContentItemName"),
    cacheSettings: RetrievalCacheSettings.CacheDisabled
);
```

## Use a subquery
In the previous example, we retrieved items based on a provided collection of IDs. This time, let’s cover a similar scenario, but we’ll dynamically resolve that list based on another query.
Here is the KX13 code, inspired by a support case, which searches for _Twitter post_ objects referencing articles that are not published:
C#
**KX13**
Copy
```
// Retrieve twitter posts
var posts = TwitterPostInfoProvider.GetTwitterPostInfoByAccountId(twitterAccount.TwitterAccountID)
    // Find posts that are not yet published to Twitter
    .Where(tp => !tp.IsPublished)
    // Only retrieve posts that reference pages from the subquery
    .WhereIn("TwitterPostDocumentGUID", DocumentHelper
        // Retrieve articles
        .GetDocuments("DancingGoatCore.Article")
            // Specify the ID column used to filter the parent query
            .Column("DocumentGUID")
            // Specify the culture (language) version to look for
            .Culture("en-us")
            // Specify which site to query pages from
            .OnSite("DancingGoatCore"))
    // Order by date scheduled for publishing
    .OrderBy(tp => tp.TwitterPostScheduledPublishDateTime)
    // Execute query to get results in enumerable format
    .ToList();
```

Moving on to XbyK, there is no built-in Twitter feature. Let’s imagine we’ve recreated this functionality using the Content hub, with a `SocialMediaPush` content type. Now, we promote content from `SocialMediaPush` items to multiple social media platforms via a third-party service, instead of just posting to Twitter.
The third-party publishing service posts to a custom endpoint after a successful push to all platforms, which then updates `SocialMediaPushIsPublished` to `true`.
The resulting XbyK query uses the same `IContentRetriever` object to make two queries:
  * One retrieves reusable items from the Content hub using `RetrieveContentParameters`.
  * The other retrieves articles from a website channel using `RetrievePagesParameters`.


C#
**XbyK**
Copy
```
var parameters = new RetrieveContentParameters
{
    // Specify the language version to look for
    LanguageName = "en-US",
    // Include non-published content items
    IsForPreview = true,
};

var subQueryParameters = new RetrievePagesParameters
{
    // Specify the language version to look for
    LanguageName = "en-US",
    // Specify which channel to query pages from
    ChannelName = "DancingGoatCore",
    // Include non-published content items
    IsForPreview = true,
};

// Subquery
var articles = await contentRetriever
    // Retrieve articles
    .RetrievePages<Article>(
        parameters: subQueryParameters,
        additionalQueryConfiguration: subQuery => subQuery
            // Specify the ID column used to filter the parent query
            .Columns(nameof(Article.SystemFields.ContentItemID)),
        cacheSettings: RetrievalCacheSettings.CacheDisabled);

// Primary query
var posts = await contentRetriever.RetrieveContent<SocialMediaPush>(
        parameters: parameters,
        additionalQueryConfiguration: query => query
            // Find posts that are not yet published to social media
            .Where(where => where.WhereFalse(nameof(SocialMediaPush.SocialMediaPushIsPublished)))
            // Only retrieve posts that reference pages from the articles subquery
            .Linking(nameof(SocialMediaPush.SocialMediaPushPage), articles.Select(article => article.SystemFields.ContentItemID))
            // Order by date scheduled for publishing
            .OrderBy(nameof(SocialMediaPush.SocialMediaPushScheduledPublishDateTime)),
        cacheSettings: RetrievalCacheSettings.CacheDisabled
    );
```

We’ve broken the query, subquery, and parameters for each into separate blocks for readability, but they are nestable if your code style conventions prefer to avoid this kind of separation.
## Upgrade a repository
For our last example, let’s look at something more reusable, using methods from the `BasePageRepository` in the Kentico Xperience 13 [training site](https://github.com/Kentico/xperience-training-13/blob/master/XperienceAdapter/Repositories/BasePageRepository.cs).
This sample contains two methods:
  * `GetPagesInCurrentCultureAsync`, which retrieves pages in the provided culture, allowing for configuration through functions and mappers
  * `GetAllAsync`, which utilizes `GetPagesInCurrentCultureAsync` to retrieve all pages of the repository’s specified type


C#
**KX13**
Copy
```
public virtual async Task<IEnumerable<TPageDto>> GetAllAsync(CancellationToken? cancellationToken = default) =>
    await GetPagesInCurrentCultureAsync(
        cancellationToken,
        buildCacheAction: cache => cache
            // Set cache key
            .Key($"{nameof(BasePageRepository<TPageDto, TPage>)}|{typeof(TPage).Name}")
            // Set cache expiration to 30 seconds
            .Expiration(TimeSpan.FromSeconds(30)));

public virtual async Task<IEnumerable<TPageDto>> GetPagesInCurrentCultureAsync(
    CancellationToken? cancellationToken = default,
    Action<DocumentQuery<TPage>>? filter = default,
    Func<TPage, TPageDto, TPageDto>? additionalMapper = default,
    Action<IPageCacheBuilder<TPage>>? buildCacheAction = default,
    bool includeAttachments = default)
{
    //Current culture and site are used by default with IPageRetriever
    var result = await _repositoryServices.PageRetriever.RetrieveAsync(query =>
    {
        // Filter columns
        query.Columns(DefaultDtoFactory().SourceColumns);
        // Apply additional filtering if provided
        filter?.Invoke(query);
    },
    buildCacheAction,
    cancellationToken);

    return MapPages(result, additionalMapper, includeAttachments);
}
```

Since these KX13 methods use `IPageRetriever`, converting them to use `IContentRetriever` is a bit more straightforward.
However, there are still some notable changes, such as:
  * The format of cache settings, now a `RetrievalCacheSettings` object instead of an `Action`
  * The definition of the cache item’s suffix, rather than its entire name
  * The use of a separate parameters object
  * The replacement of [page attachments](/13/managing-website-content/working-with-files/page-attachments) with reusable content items
  * The existence of a linked items depth parameter in Xperience by Kentico


C#
**XbyK**
Copy
```
public virtual async Task<IEnumerable<TPageDto>> GetAllAsync(CancellationToken? cancellationToken = default) =>
    await GetPagesInCurrentCultureAsync(
        cancellationToken,
        cacheSettings: new RetrievalCacheSettings(
            // Set cache key suffix
            cacheItemNameSuffix: $"{nameof(BasePageRepository<TPageDto, TPage>)}|{typeof(TPage).Name}",
            // Set cache expiration to 30 seconds
            cacheExpiration: TimeSpan.FromSeconds(30),
            useSlidingExpiration: true,
            additionalCacheDependencies: null
        )
    );

public virtual async Task<IEnumerable<TPageDto>> GetPagesInCurrentCultureAsync(
    CancellationToken? cancellationToken = default,
    Action<RetrievePagesQueryParameters>? filter = null,
    Func<TPage, TPageDto, TPageDto>? additionalMapper = default,
    RetrievalCacheSettings? cacheSettings = default,
    int depth = 1)
{
    // Current culture and channel are used by default in IContentRetriever
    var result = await contentRetriever.RetrievePages<TPage>(
        parameters: new RetrievePagesParameters
        {
            // Set the depth of linked items to retrieve
            LinkedItemsMaxLevel = depth
        },
        additionalQueryConfiguration: query =>
        {
            // Apply additional filtering if provided (queryConfiguration)
            filter?.Invoke(query);
            // Filter columns
            query.Columns(DefaultDtoFactory().SourceColumns);
        },
        cacheSettings: cacheSettings ?? RetrievalCacheSettings.CacheDisabled);

    return MapPages(result, additionalMapper, depth);
}
```

**MapPages method**
The `MapPages` method, which converts a `TPage` object into a `TPageDto` object, is beyond the scope of this example.
Because the migration tool converts attached files to reusable content items, we assume the new `MapPages` implementation uses the `depth` parameter to decide whether it should work with related files, instead of the `includeAttachments` boolean parameter.
## What’s next?
If you haven’t already, we recommend checking out our [upgrade walkthrough](/guides/upgrade-to-xbyk/upgrade-walkthrough), to see content retrieval migration in context of the whole upgrade process, including more caching examples. You can find more advanced upgrade and data migration use cases in our other [Upgrade deep dive guides](/guides/upgrade-to-xbyk/upgrade-deep-dives).
If you’ve encountered any pain points during your upgrade to Xperience by Kentico that additional training materials could help with, or you’ve found any issues in our existing materials, please use the **Send us feedback** button at the bottom of the page to let us know!
![]()
[]()[]()
