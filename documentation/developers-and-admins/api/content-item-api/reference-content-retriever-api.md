---
source: https://docs.kentico.com/documentation/developers-and-admins/api/content-item-api/reference-content-retriever-api
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [API](/documentation/developers-and-admins/api)
  * [Content API](/documentation/developers-and-admins/api/content-item-api)
  * Reference - ContentRetriever API 


# Reference - ContentRetriever API
## Page queries
### Retrieve the current page
**Method:** `RetrieveCurrentPage<TResult>(...)`
Retrieves the page requested by the current HTTP request. The method maps the retrieved data to the specified `TResult` model.
C#
**Basic usage**
Copy
```
// Assuming 'ArticlePage' is a generated model class
// For instance, assume the request is handled by 'ArticlesController'
// so 'TResult' always matches the retrieved schema and is mapped correctly.
ArticlePage currentPage =
  await contentRetriever.RetrieveCurrentPage<ArticlePage>();

// If the current page can be of multiple types, you can use 'IWebPageFieldsSource'
// or different suitable base class together with pattern matching.
// Suitable model classes are paired with content types using
// 'RegisterContentTypeMappingAttribute' (automatically added to generated classes)
//
// Gets the current page using a shared interface
var currentPageData =
  await contentRetriever.RetrieveCurrentPage<IWebPageFieldsSource>();

// Uses pattern matching to handle the specific type
switch (currentPageData)
{
  case ArticlePage article:
    // Handles specific logic for ArticlePage
    Console.WriteLine($"Displaying Article: {article.Title}");
    // Renders article view...
    break;

  case LandingPage landing:
    // Handles specific logic for LandingPage
    Console.WriteLine($"Displaying Landing Page: {landing.Headline}");
    // Renders landing page view...
    break;

  case null:
    // Handles case where the page is not found or not retrievable
    Console.WriteLine("Current page not found.");
    // Renders 404 view...
    break;

  default:
    // Handles any other unexpected page types
    // Renders a default view...
    break;
}
```

C#
**With parameters**
Copy
```
// Overrides method defaults with custom parameters
var parameters = new RetrieveCurrentPageParameters
{
    // Forces a language variant
    LanguageName = "spanish",
    // Includes directly linked items
    LinkedItemsMaxLevel = 1,
    // Forces preview mode data
    IsForPreview = true
};

ArticlePage page = await contentRetriever.RetrieveCurrentPage<ArticlePage>(parameters);
```

C#
**All configuration options**
Copy
```
// Disables caching
var cacheSettings = RetrievalCacheSettings.CacheDisabled;

ArticlePage page = await contentRetriever.RetrieveCurrentPage<ArticlePage>(
    // Uses the default retrieval params
    new RetrieveCurrentPageParameters(),
    // Only gets the ArticleTitle property
    query => query.Columns(nameof(ArticlePage.ArticleTitle)),
    cacheSettings,
    // Explicitly uses default mapping
    configureModel: null
);
```

Some overloads of `RetrieveCurrentPage` require an instance of `RetrieveCurrentPageParameters`. This object allows you to fine-tune how the current page is retrieved. For available parameters, see the [Page query parameters](#page-query-parameters) table.
### Retrieve pages sharing reusable field schema
Method: `RetrievePagesOfReusableSchemas<TResult>(...)`
Retrieves a collection of pages whose content types use one or more of the specified [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas), mapping them to the `TResult` model. Allows filtering by path, language, and other criteria using the parameters described in the [Page query parameters](#page-query-parameters) table.
C#
**Basic usage**
Copy
```
// Assuming 'IPageMetadata' is an interface implemented by types using these schemas
var schemaNames = new[] { IMetadataFields.REUSABLE_FIELD_SCHEMA_NAME, ISeoFields.REUSABLE_FIELD_SCHEMA_NAME };

// Gets all pages using the specified schemas with default settings
IEnumerable<IPageMetadata> pagesWithSchemas =
  await contentRetriever.RetrievePagesOfReusableSchemas<IPageMetadata>(schemaNames);
```

C#
**With parameters**
Copy
```
var schemaNames = new[] { IMetadataFields.REUSABLE_FIELD_SCHEMA_NAME };

// Gets pages using the 'Metadata' schema under the /products path
var parameters = new RetrievePagesOfReusableSchemasParameters
{
    PathMatch = PathMatch.Children("/products")
};

IEnumerable<IPageMetadata> productPages =
  await contentRetriever.RetrievePagesOfReusableSchemas<IPageMetadata>(schemaNames, parameters);
```

C#
**All configuration options**
Copy
```
var schemaNames = new[] { IMetadataFields.REUSABLE_FIELD_SCHEMA_NAME, ISeoFields.REUSABLE_FIELD_SCHEMA_NAME };

// Disables caching
var cacheSettings = RetrievalCacheSettings.CacheDisabled;

// Gets the top 20 pages using the schemas
IEnumerable<IPageMetadata> pageInfo =
  await contentRetriever.RetrievePagesOfReusableSchemas<IPageMetadata>(
    schemaNames,
    new RetrievePagesOfReusableSchemasParameters(),
    query => query
                // Gets the top 20 results
                .TopN(20),
    cacheSettings,
    // Uses default mapping (columnName <=> propertyName)
    configureModel: null
);
```

For available parameters, see the [Page query parameters](#page-query-parameters) table.
### Retrieve pages of a single content type
Method: `RetrievePages<TSource, TResult>(...)`
Retrieves a collection of pages of a specific content type, represented by the `TResult` generic. Allows filtering by path, language, and other criteria using the parameters described in the [Page query parameters](#page-query-parameters) table.
The provided `TSource` type determines the content type to retrieve. It must be a model class registered using [RegisterContentTypeMapping](/documentation/developers-and-admins/api/generate-code-files-for-system-objects#registercontenttypemapping-attribute) (applied to [generated system classes](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) by default). For this method, `TSource` also serves as the intermediate mapped result provided to the `configureModel` delegate.
C#
**Basic usage**
Copy
```
// Assuming 'NewsArticle' is a generated model class for your news pages
// Gets all news articles using default settings (language, preview context, etc.)
IEnumerable<NewsArticle> newsArticles =
  await contentRetriever.RetrievePages<NewsArticle>();
```

C#
**With parameters**
Copy
```
// Gets news articles under the /news path, including one level of linked items
var parameters = new RetrievePagesParameters
{
    // Gets only children under the specified path
    PathMatch = PathMatch.Children("/news"),
    // Includes directly linked items
    LinkedItemsMaxLevel = 1
};

IEnumerable<NewsArticle> newsArticles =
  await contentRetriever.RetrievePages<NewsArticle>(parameters);
```

C#
**All configuration options**
Copy
```
// Assuming NewsArticleSummary is a simpler model
public class NewsArticleSummary
{
    public string Title { get; set; }
    public string Summary { get; set; }
}

// Gets the top 5 latest news articles under /news, only getting specific columns
var parameters = new RetrievePagesParameters
{
    PathMatch = PathMatch.Children("/news")
};

// Disables caching
var cacheSettings = RetrievalCacheSettings.CacheDisabled;

// NewsArticle is `TSource`, available in configureModel
// NewsArticleSummary is `TResult`, the final type returned by the call
IEnumerable<NewsArticle> latestNews =
  await contentRetriever.RetrievePages<NewsArticle, NewsArticleSummary>(
    parameters,
    query => query
                // Selects specific columns
                .Columns("Headline", "PublicationDate", "Summary")
                // Orders by date
                .OrderByDescending("PublicationDate")
                // Gets the top 5
                .TopN(5),
    cacheSettings,
    configureModel: async (container, source) => new NewsArticleSummary
    {
        // Source is 'NewsArticle'
        Title = $"{source.Headline} - #{source.PublicationDate.ToString("en-US")}",
        Summary = source.Summary
    }
);
```

For available parameters, see the [Page query parameters](#page-query-parameters) table.
### Retrieve pages of multiple content types
Method: `RetrievePagesOfContentTypes<TResult>(...)`
Retrieves a collection of web pages belonging to one of the specified content type code names, mapping them to the `TResult` model. Allows filtering by path, language, and other criteria using the parameters described in the [Page query parameters](#page-query-parameters) table.
When retrieving pages of multiple content types, you can use either `IWebPageFieldsSource` for maximum flexibility or create a custom base class that implements `IWebPageFieldsSource` and includes common properties shared across your content types.
C#
**Basic usage with IWebPageFieldsSource**
Copy
```
var contentTypes = new[] { ArticlePage.CONTENT_TYPE_NAME, BlogPage.CONTENT_TYPE_NAME };

// Gets all articles and blogs using IWebPageFieldsSource
IEnumerable<IWebPageFieldsSource> pages =
  await contentRetriever.RetrievePagesOfContentTypes<IWebPageFieldsSource>(contentTypes);

// Uses pattern matching to handle the specific type
switch (pages.FirstOrDefault())
{
  case ArticlePage article:
    // Specific logic for ArticlePage
    Console.WriteLine($"Displaying Article: {article.ArticleTitle}");
    // Render article view...
    break;

  case BlogPage blog:
    // Specific logic for BlogPage
    Console.WriteLine($"Displaying Blog Page: {blog.BlogTitle}");
    // Render blog page view...
    break;

  default:
    // Handle any other unexpected page types
    // Render a default view...
    break;
}
```

For more complex scenarios involving multiple content types that share common properties, you can create a custom base class. This approach provides strongly-typed access to common properties while maintaining the ability to use pattern matching for handling specific content types.
For pattern matching to work with a custom base class, you must extend the generated model classes to inherit from your base class using the partial class mechanism (since [generated classes are partial](/documentation/developers-and-admins/api/generate-code-files-for-system-objects#customize-generated-classes)).
C#
**Advanced usage with custom base class**
Copy
```
// Example of a custom base class for bulk page retrieval.
// This is a sample implementation that users can create based on their specific requirements when retrieving pages of multiple content types that share common properties.
public class BaseViewModel : IWebPageFieldsSource
{
    // Common custom fields that exist across different page content types
    public string Title { get; set; }
    public DateTime PublishedDate { get; set; }
}

// Since generated model classes are partial, you can extend them to inherit from BaseViewModel.
// This enables pattern matching to work properly with the custom base class.
public partial class ArticlePage : BaseViewModel
{
}

public partial class BlogPage : BaseViewModel
{
}

var contentTypes = new[] { ArticlePage.CONTENT_TYPE_NAME, BlogPage.CONTENT_TYPE_NAME };

// Gets all articles and blogs using the custom base class
// This provides strongly-typed access to common properties while maintaining pattern matching
IEnumerable<BaseViewModel> pages =
  await contentRetriever.RetrievePagesOfContentTypes<BaseViewModel>(contentTypes);

// Uses pattern matching to handle the specific type
// This works because ArticlePage and BlogPage both inherit from BaseViewModel
switch (pages.FirstOrDefault())
{
  case ArticlePage article:
    // Access both common properties from BaseViewModel and specific ArticlePage properties
    Console.WriteLine($"Displaying Article: {article.Title} - Published: {article.PublishedDate}");
    // Render article view...
    break;

  case BlogPage blog:
    // Access both common properties from BaseViewModel and specific BlogPage properties
    Console.WriteLine($"Displaying Blog: {blog.Title} - Published: {blog.PublishedDate}");
    // Render blog page view...
    break;

  default:
    // Handle any other unexpected page types
    // Render a default view...
    break;
}
```

C#
**With parameters**
Copy
```
var contentTypes = new[] { Article.CONTENT_TYPE_NAME, Blog.CONTENT_TYPE_NAME };

// Gets articles and blogs under the /archive path
var parameters = new RetrievePagesOfContentTypesParameters
{
    PathMatch = PathMatch.Children("/archive")
};

IEnumerable<BaseViewModel> archivePages =
  await contentRetriever.RetrievePagesOfContentTypes<BaseViewModel>(contentTypes, parameters);
```

C#
**All configuration options**
Copy
```
var contentTypes = new[] { Article.CONTENT_TYPE_NAME, Blog.CONTENT_TYPE_NAME };

// Gets the first 10 articles or blogs under /archive, ordered by name
var parameters = new RetrievePagesOfContentTypesParameters
{
    PathMatch = PathMatch.Children("/archive")
};

// Disables caching
var cacheSettings = RetrievalCacheSettings.CacheDisabled;

// Assuming BaseViewModel is a suitable base class
IEnumerable<BaseViewModel> links = await contentRetriever.RetrievePagesOfContentTypes<BaseViewModel>(
    contentTypes,
    parameters,
    query => query
                // Orders by name
                .OrderBy("DocumentName")
                // Gets the top 10
                .TopN(10),
    cacheSettings,
    // Uses default mapping
    configureModel: null
);
```

For available parameters, see the [Page query parameters](#page-query-parameters) table.
### Retrieve pages by GUIDs
**Method:** `RetrievePagesByGuids<TResult>(...)`
Retrieves a collection of specific pages identified by their `WebPageItemGUID` values. The method maps the retrieved data to the specified `TResult` model.
This method is useful when you have a list of specific page identifiers, for example obtained using [page selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#page-selector), and need to fetch their data efficiently.
**Combined content selector usage limitations**
The GUIDs returned by [combined content selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) cannot be used with this method because they are incompatible with the page GUIDs this method expects. The combined content selector returns `ContentItemGUID` values inside its `ContentItemReference` return type, while this method expects `WebPageItemGUID` values.
Passing collections of GUIDs from the combined content selector returns an empty result. Use [RetrieveContentByGuids](#retrieve-content-items-by-guids) to work with combined selector data.
C#
**Basic usage**
Copy
```
// Assuming 'ArticlePage' is a generated model class
var specificPageGuids = new[] {
    // Guid values...
};

// Gets the specified pages using default settings
IEnumerable<ArticlePage> specificPages =
  await contentRetriever.RetrievePagesByGuids<ArticlePage>(specificPageGuids);
```

The rest of the usage is identical to [Retrieve pages of a single content type](#retrieve-pages-of-a-single-content-type).
The `RetrievePagesByGuids` method utilizes the `RetrievePagesParameters` object, which is also used by the [Retrieve pages of a single content type](#retrieve-pages-of-a-single-content-type) method. This object allows you to fine-tune how the pages are retrieved. For available parameters, see the [Page query parameters](#page-query-parameters) table.
**Note:** When caching is enabled via `RetrievalCacheSettings`, appropriate [cache dependencies](/documentation/developers-and-admins/development/caching/cache-dependencies#by-idguidcodename) based on the provided `SystemFields.WebPageItemGuid` values are automatically included.
### Retrieve all pages
Method: `RetrieveAllPages<TSource, TResult>(...)`
Retrieves all pages (by default from the current channel), mapping them to the specified `TResult` model. Allows filtering by path, language, and other criteria using the parameters described in the [Page query parameters](#page-query-parameters) table.
C#
**Basic usage**
Copy
```
// Assuming 'BasePageViewModel' is a suitable base class for all pages
// Gets all pages using default settings
IEnumerable<BasePageViewModel> allPages =
  await contentRetriever.RetrieveAllPages<BasePageViewModel>();

// Uses pattern matching to handle the specific type
switch (allPages.FirstOrDefault())
{
  case ArticlePage article:
    // Specific logic for ArticlePage
    Console.WriteLine($"Displaying Article: {article.Title}");
    // Render article view...
    break;

  case LandingPage landing:
    // Specific logic for LandingPage
    Console.WriteLine($"Displaying Landing Page: {landing.Headline}");
    // Render landing page view...
    break;

  case null:
    // Handle case where the page is not found or not retrievable
    Console.WriteLine("Current page not found.");
    // Render 404 view...
    break;

  default:
    // Handle any other unexpected page types
    // Render a default view...
    break;
}
```

C#
**With parameters**
Copy
```
var parameters = new RetrieveAllPagesParameters() {
  ChannelName = "FrenchCuisine"
};

IEnumerable<BasePageViewModel> allLivePages =
  await contentRetriever.RetrieveAllPages<BasePageViewModel>(parameters);
```

C#
**All configuration options**
Copy
```
// Gets all pages under '/Articles', getting only specific columns
var parameters = new RetrieveAllPagesParameters
{
  PathMatch = PathMatch.Section("/Articles")
};

// Disables caching
var cacheSettings = RetrievalCacheSettings.CacheDisabled;

// Assuming Article is a generated model class
IEnumerable<PageTitleModel> pageLinks =
  await contentRetriever.RetrieveAllPages<BasePageViewModel>(
    parameters,
    query => query
                .Columns("PageTitle")
    cacheSettings,
    configureModel: async (container, baseModel) => {
      // Gets only the title and maps to specific model
      return new PageTitleModel() {
        PageTitle = baseModel.PageTitle ?? "Untitled page"
      }
    }
);
```

Some overloads of `RetrieveAllPages` require an instance of `RetrieveAllPagesParameters`. This object allows you to fine-tune how the pages are retrieved. For available parameters, see the [Page query parameters](#page-query-parameters) table.
### Retrieve all pages by GUIDs
**Method:** `RetrieveAllPagesByGuids<TResult>(...)`
Retrieves all pages (of any content type) identified by their `WebPageItemGUID` values. The method maps the retrieved data to the specified `TResult` model.
This method is useful when you need to fetch pages of multiple content types by specific identifiers, for example obtained using [page selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#page-selector), without restricting to a single content type.
**Combined content selector usage limitations**
The GUIDs returned by [combined content selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) cannot be used with this method because they are incompatible with the page GUIDs this method expects. The combined content selector returns `ContentItemGUID` values inside its `ContentItemReference` return type, while this method expects `WebPageItemGUID` values.
Passing collections of GUIDs from the combined content selector returns an empty result. Use [RetrieveContentByGuids](#retrieve-content-items-by-guids) to work with combined selector data.
C#
**Basic usage**
Copy
```
// Assuming you have a collection of page GUIDs from a page selector
var webPageItemGuids = new[] {
    // Guid values...
};

// Gets pages of any content type using default settings
IEnumerable<IWebPageFieldsSource> specificPages =
  await contentRetriever.RetrieveAllPagesByGuids<IWebPageFieldsSource>(webPageItemGuids);
```

C#
**All configuration options**
Copy
```
var webPageItemGuids = new[] {
    // Guid values...
};

var parameters = new RetrieveAllPagesParameters
{
    PathMatch = PathMatch.Children("/products")
};

// Disables caching
var cacheSettings = RetrievalCacheSettings.CacheDisabled;

IEnumerable<IWebPageFieldsSource> pageData =
  await contentRetriever.RetrieveAllPagesByGuids<IWebPageFieldsSource>(
    webPageItemGuids,
    parameters,
    query => query
        // Orders by name
        .OrderBy("DocumentName")
        // Gets the top 10 results
        .TopN(10),
    cacheSettings,
    // Uses default mapping
    configureModel: null
);
```

The `RetrieveAllPagesByGuids` method utilizes the `RetrieveAllPagesParameters` object, which is also used by the [Retrieve all pages](#retrieve-all-pages) method. This object allows you to fine-tune how the pages are retrieved. For available parameters, see the [Page query parameters](#page-query-parameters) table.
**Note:** When caching is enabled via `RetrievalCacheSettings`, appropriate [cache dependencies](/documentation/developers-and-admins/development/caching/cache-dependencies#by-idguidcodename) based on the provided `SystemFields.WebPageItemGuid` values are automatically included.
### Page query parameters
The following table contains all parameters available for page query methods. Each parameter row specifies which methods support that parameter.
Property |  Default value |  Applicable methods |  Description  
---|---|---|---  
ChannelName |  `null` |  `RetrievePagesOfReusableSchemas`, `RetrievePages`, `RetrievePagesOfContentTypes`, `RetrievePagesByGuids`, `RetrieveAllPages` |  Name of the website channel to fetch the pages from. If `null`, the channel from the current request context is used.  
PathMatch |  `null` |  `RetrievePagesOfReusableSchemas`, `RetrievePages`, `RetrievePagesOfContentTypes`, `RetrievePagesByGuids`, `RetrieveAllPages` |  Limits results based on the [content tree path](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#filter-pages-based-on-content-tree-structure) (e.g., children of a path, specific path). If `null`, no path filtering is used.  
IncludeUrlPath |  `true` |  All page query methods |  Specifies if page URL data should be included in the results. This data is necessary when [resolving page URLs](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls) and saves a database roundtrip if included.  
LinkedItemsMaxLevel |  `0` |  All page query methods |  Controls the depth of [linked content items](/documentation/business-users/content-hub/content-items#link-content-items) to retrieve recursively along with the main page. A value of `0` means no linked items are retrieved.  
LanguageName |  `null` |  All page query methods |  Allows you to override the default content [language](/documentation/developers-and-admins/configuration/languages) determined by the [current context](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#access-current-preferred-language). If left `null`, the language retrieved from the current HTTP request is used.  
UseLanguageFallbacks |  `true` |  All page query methods |  Determines if the system should attempt to retrieve content in [fallback languages](/documentation/developers-and-admins/configuration/languages#language-fallbacks) if the content is not available in the primary specified language.  
IncludeSecuredItems |  `false` |  All page query methods |  Specifies whether content items requiring special permissions (e.g., [secured sections](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#page-security-configuration)) should be included in the results.  
IsForPreview |  `null` |  All page query methods |  Allows overriding the preview mode context. If left `null`, the retrieval respects the current website context (whether the request is in preview mode or live). Set to `true` to force preview data or `false` to force live data.  
## Content item queries
### Retrieve content items sharing reusable field schema
Method: `RetrieveContentOfReusableSchemas<TResult>(...)`
Retrieves a collection of reusable content items whose content types use one or more of the specified [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas), mapping them to the `TResult` model. Allows filtering by workspace, language, and other criteria using the parameters described in the [Content item query parameters](#content-item-query-parameters) table.
C#
**Basic usage**
Copy
```
// Assuming 'IContactDetails' is an interface implemented by types using the 'ContactSchema'
var schemaNames = new[] { IContactSchema.REUSABLE_FIELD_SCHEMA_NAME };

// Gets all content items using the 'IContactSchema' generated schema interface
IEnumerable<IContactSchema> contacts =
  await contentRetriever.RetrieveContentOfReusableSchemas<IContactSchema>(schemaNames);
```

C#
**With parameters**
Copy
```
var schemaNames = new[] { IAddressSchema.REUSABLE_FIELD_SCHEMA_NAME,
                          ILocationSchema.REUSABLE_FIELD_SCHEMA_NAME };

// Gets items using Address or Location schemas from the 'Locations' workspace
var parameters = new RetrieveContentOfReusableSchemasParameters
{
    WorkspaceNames = new[] { "Locations" }
};

// Uses 'IContentItemFieldsSource' as the shared type
IEnumerable<IContentItemFieldsSource> locationData =
  await contentRetriever
    .RetrieveContentOfReusableSchemas<IContentItemFieldsSource>(schemaNames, parameters);
```

C#
**All configuration options**
Copy
```
var schemaNames = new[] { IMetadataSchema.REUSABLE_FIELD_SCHEMA_NAME };

// Disables caching
var cacheSettings = RetrievalCacheSettings.CacheDisabled;

// Assuming IMetadata is an inteface generated for 'MetadataSchema'
IEnumerable<IMetadata> oldestMetadataItems =
  await contentRetriever.RetrieveContentOfReusableSchemas<IMetadata>(
    schemaNames,
    parameters,
    query => query
                // Gets the first 5
                .TopN(5),
    cacheSettings,
    // Uses default mapping
    configureModel: null
);
```

For available parameters, see the [Content item query parameters](#content-item-query-parameters) table.
### Retrieve content items of reusable schemas by GUIDs
**Method:** `RetrieveContentOfReusableSchemasByGuids<TResult>(...)`
Retrieves a collection of specific reusable content items whose content types use one or more of the specified [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas), identified by their `ContentItemGUID` values. The method maps the retrieved data to the specified `TResult` model.
This method is useful when you have a list of specific content item identifiers that share common reusable schemas and need to fetch their data efficiently.
C#
**Basic usage**
Copy
```
// Assuming 'IMetadata' is an interface implemented by types using the schema
var schemaNames = new[] { IMetadataFields.REUSABLE_FIELD_SCHEMA_NAME };
var contentItemGuids = new[] {
    // Guid values...
};

// Gets the specified content items using default settings
IEnumerable<IMetadataFields> specificItems =
  await contentRetriever.RetrieveContentOfReusableSchemasByGuids<IMetadataFields>(
    schemaNames,
    contentItemGuids);
```

C#
**All configuration options**
Copy
```
var schemaNames = new[] { IMetadataFields.REUSABLE_FIELD_SCHEMA_NAME };
var contentItemGuids = new[] {
    // Guid values...
};

// Gets specific content items with custom configuration
var parameters = new RetrieveContentOfReusableSchemasParameters
{
    WorkspaceNames = new[] { "MainContent" }
};

// Disables caching
var cacheSettings = RetrievalCacheSettings.CacheDisabled;

IEnumerable<IMetadataFields> contentData =
  await contentRetriever
    .RetrieveContentOfReusableSchemasByGuids<IMetadataFields>(
        schemaNames,
        contentItemGuids,
        parameters,
        query => query
            // Selects specific columns
            .Columns("DisplayName", "ContentItemModifiedWhen")
            // Orders by modified date
            .OrderByDescending("ContentItemModifiedWhen"),
        cacheSettings,
        // Uses default mapping
        configureModel: null
);
```

The `RetrieveContentOfReusableSchemasByGuids` method uses the `RetrieveContentOfReusableSchemasParameters` object. This object allows you to fine-tune how the content items are retrieved. For available parameters, see the [Content item query parameters](#content-item-query-parameters) table.
**Note:** When caching is enabled via `RetrievalCacheSettings`, appropriate cache dependencies based on the provided `contentItemGuids` are automatically included.
### Retrieve content items of a single content type
Method: `RetrieveContent<TSource, TResult>(...)`
Retrieves a collection of content items of a specific [content type](/documentation/developers-and-admins/development/content-types), mapped to the `TResult` model. Allows filtering by workspace, language, and other criteria using the parameters described in the [Content item query parameters](#content-item-query-parameters) table.
The provided `TSource` type determines the content type to retrieve. It must be a model class registered using [RegisterContentTypeMapping](/documentation/developers-and-admins/api/generate-code-files-for-system-objects#registercontenttypemapping-attribute) (applied to [generated system classes](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) by default). For this method, `TSource` also serves as the intermediate mapped result provided to the `configureModel` delegate.
C#
**Basic usage**
Copy
```
// Assuming 'Author' is a generated model class for your author content items
// Gets all authors using default settings (language, preview context, etc.)
IEnumerable<Author> authors =
  await contentRetriever.RetrieveContent<Author>();
```

C#
**With parameters**
Copy
```
// Gets authors from a specific workspace
var parameters = new RetrieveContentParameters
{
    // Specifies relevant workspace
    WorkspaceNames = new[] { "BlogContent" },
};

IEnumerable<Author> blogAuthors =
  await contentRetriever.RetrieveContent<Author>(parameters);
```

C#
**All configuration options**
Copy
```
// Gets the top 5 authors with the most articles from the 'BlogContent' workspace
var parameters = new RetrieveContentParameters
{
     WorkspaceNames = new[] { "BlogContent" }
};

// Disables caching
var cacheSettings = RetrievalCacheSettings.CacheDisabled;

// Assuming AuthorSummary is a simpler model with FullName and ArticleCount
public class AuthorSummary
{
    public string FullName { get; set; }
    public int ArticleCount { get; set; }
}

// 'TSource' set to generated model class 'Author'
// Final result `TResult` transformed and mapped to 'AuthorSummary'
IEnumerable<AuthorSummary> topAuthors = await contentRetriever.RetrieveContent<Author, AuthorSummary>(
    parameters,
    query => query
                // Selects specific columns
                .Columns("FirstName", "LastName", "ArticleCount")
                // Orders by count
                .OrderBy(new OrderByColumn("ArticleCount", OrderDirection.Descending))
                // Gets the top 5
                .TopN(5),
    cacheSettings,
    // Example of custom mapping
    async (container, source) => new AuthorSummary
    {
        // Source is 'Author'
        FullName = $"{source.FirstName} {source.LastName}",
        ArticleCount = source.ArticleCount
    }
);
```

For available parameters, see the [Content item query parameters](#content-item-query-parameters) table.
### Retrieve content items of multiple content types
Method: `RetrieveContentOfContentTypes<TResult>(...)`
Retrieves a collection of reusable content items belonging to the specified content types, mapping them to the `TResult` model. Allows filtering by workspace, language, and other criteria using the parameters described in the [Content item query parameters](#content-item-query-parameters) table.
When retrieving items of multiple content types, you can use either `IContentItemFieldsSource` for maximum flexibility or create a custom base class that implements `IContentItemFieldsSource` and includes common properties shared across your content types.
**Custom base class for content type models**
A custom base class implementing `IContentItemFieldsSource` is suitable for more complex scenarios involving multiple content types that share common properties. This approach provides strongly-typed access to common properties while maintaining the ability to use pattern matching for handling specific content types.
For pattern matching to work with a custom base class, you must extend the generated model classes to inherit from your base class using the partial class mechanism (since [generated classes are partial](/documentation/developers-and-admins/api/generate-code-files-for-system-objects#customize-generated-classes)).
C#
**Basic usage with IContentItemFieldsSource**
Copy
```
var contentTypes = new[] { Author.CONTENT_TYPE_NAME, Article.CONTENT_TYPE_NAME };

// Gets all authors and articles using default settings
// Uses 'IContentItemFieldsSource' as the shared type.
// Suitable model classes are paired with content types using
// 'RegisterContentTypeMappingAttribute' (automatically added to generated classes)
IEnumerable<IContentItemFieldsSource> blogItems =
  await contentRetriever
    .RetrieveContentOfContentTypes<IContentItemFieldsSource>(contentTypes);

foreach (var item in blogItems)
{
  switch (item)
  {
    case Author author:
      // Handles Author specific logic
      Console.WriteLine($"- Found Author: {author.DisplayName}");
      // e.g., DisplayAuthorBio(author);
      break;

    case Article article:
      // Handles Article specific logic
      Console.WriteLine($"- Found Article: {article.DisplayName}");
      // e.g., DisplayArticleSummary(article);
      break;

    default:
      break;
  }
}
```

C#
**With parameters**
Copy
```
var contentTypes = new[] { Author.CONTENT_TYPE_NAME, BookReview.CONTENT_TYPE_NAME };

// Gets authors and book reviews from the 'Reviews' workspace
var parameters = new RetrieveContentOfContentTypesParameters
{
    WorkspaceNames = new[] { "Reviews" }
};

IEnumerable<IContentItemFieldsSource> reviewContent =
  // Can use 'IContentItemFieldsSource' if no common base type
  await contentRetriever
    .RetrieveContentOfContentTypes<IContentItemFieldsSource>(contentTypes, parameters);
```

C#
**All configuration options**
Copy
```
var contentTypes = new[] { Author.CONTENT_TYPE_NAME,
                           Article.CONTENT_TYPE_NAME,
                           Book.CONTENT_TYPE_NAME };

// Gets the 10 most recently modified authors, articles, or books
var parameters = new RetrieveContentOfContentTypesParameters
{
     // Retrieves content from multiple workspaces
     WorkspaceNames = new[] { "MainContent", "BlogContent" }
};

// Disables caching
var cacheSettings = RetrievalCacheSettings.CacheDisabled;

// Assuming IContentInfo is an interface with Name and LastModified properties
IEnumerable<IContentInfo> recentContent =
  await contentRetriever.RetrieveContentOfContentTypes<IContentInfo>(
    contentTypes,
    parameters,
    query => query
                // Selects specific columns
                .Columns("DisplayName", "ContentItemModifiedWhen")
                // Gets the top 10 results
                .TopN(10),
    cacheSettings,
    // Uses default mapping
    configureModel: null
);
```

For available parameters, see the [Content item query parameters](#content-item-query-parameters) table.
### Retrieve content items of multiple content types by GUIDs
**Method:** `RetrieveContentOfContentTypesByGuids<TResult>(...)`
Retrieves a collection of specific reusable content items of multiple content types identified by their `ContentItemGUID` values. The method maps the retrieved data to the specified `TResult` model.
This method is useful when you have a list of specific content item identifiers from multiple content types (e.g., from a [combined content selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector)) and need to fetch their data efficiently.
C#
**Basic usage**
Copy
```
// Assuming you have content item GUIDs from the combined selector
var contentTypes = new[] { Author.CONTENT_TYPE_NAME, Article.CONTENT_TYPE_NAME };
var contentItemGuids = new[] {
    // Guids...
 };

// Gets the specified content items using default settings
IEnumerable<IContentItemFieldsSource> specificItems =
  await contentRetriever
    .RetrieveContentOfContentTypesByGuids<IContentItemFieldsSource>(
        contentTypes,
        contentItemGuids);
```

C#
**All configuration options**
Copy
```
var contentTypes = new[] {
    Author.CONTENT_TYPE_NAME,
    Article.CONTENT_TYPE_NAME
};

var contentItemGuids = new[] {
    // Guids...
};

// Gets specific content items with custom configuration
var parameters = new RetrieveContentOfContentTypesParameters
{
    WorkspaceNames = new[] { "BlogContent" }
};

// Disables caching
var cacheSettings = RetrievalCacheSettings.CacheDisabled;

IEnumerable<IContentItemFieldsSource> contentData =
  await contentRetriever
    .RetrieveContentOfContentTypesByGuids<IContentItemFieldsSource>(
        contentTypes,
        contentItemGuids,
        parameters,
        query => query
                    // Selects specific columns
                    .Columns("DisplayName", "ContentItemModifiedWhen")
                    // Orders by modified date
                    .OrderByDescending("ContentItemModifiedWhen"),
        cacheSettings,
        // Uses default mapping
        configureModel: null
);
```

The `RetrieveContentOfContentTypesByGuids` method uses the `RetrieveContentOfContentTypesParameters` object. This object allows you to fine-tune how the content items are retrieved. For available parameters, see the [Content item query parameters](#content-item-query-parameters) table.
**Note:** When caching is enabled via `RetrievalCacheSettings`, appropriate cache dependencies based on the provided `contentItemGuids` are automatically included.
### Retrieve content items by GUIDs
**Method:** `RetrieveContentByGuids<TResult>(...)`
Retrieves a collection of specific reusable content items identified by their `ContentItemGUID` values. The method maps the retrieved data to the specified `TResult` model.
This method is useful when you have a list of specific content item identifiers (e.g., from a [related items](/documentation/developers-and-admins/customization/field-editor/data-type-management) field) and need to fetch their data efficiently.
C#
**Basic usage when expecting a single type**
Copy
```
// Assuming 'Author' is a generated model class
var specificAuthorGuids = new[] {
    // Guid values...
};

// Gets the specified authors using default settings
IEnumerable<Author> specificAuthors =
  await contentRetriever.RetrieveContentByGuids<Author>(specificAuthorGuids);
```

The rest of the usage is identical to [Retrieve content items of a single content type](#retrieve-content-items-of-a-single-content-type).
The `RetrieveContentByGuids` method uses the `RetrieveContentParameters` object. This object allows you to fine-tune how the content items are retrieved. For available parameters, see the [Content item query parameters](#content-item-query-parameters) table.
**Note:** When caching is enabled via `RetrievalCacheSettings`, appropriate cache dependencies based on the provided `contentItemGuids` are automatically included.
### Content item query parameters
The following table contains all parameters available for content item query methods. Each parameter row specifies which methods support that parameter.
Property |  Default value |  Applicable methods |  Description  
---|---|---|---  
IncludeUrlPath |  `true` |  All content item query methods |  Specifies if page URL data should be included in the results. This data is necessary when [resolving page URLs](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls) and saves a database roundtrip if included.  
WorkspaceNames |  `[]` (Empty _Enumerable_) |  All content item query methods |  Names of the [workspaces](/documentation/developers-and-admins/configuration/users/role-management/workspaces) from which content should be retrieved.  
LinkedItemsMaxLevel |  `0` |  All content item query methods |  Controls the depth of linked content items to retrieve recursively. `0` means no linked items are retrieved.  
LanguageName |  `null` |  All content item query methods |  Allows you to override the default content [language](/documentation/developers-and-admins/configuration/languages) determined by the [current context](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#access-current-preferred-language). If left `null`, the language retrieved from the current HTTP request is used.  
UseLanguageFallbacks |  `true` |  All content item query methods |  Determines if the system should attempt to retrieve content in [fallback languages](/documentation/developers-and-admins/configuration/languages#language-fallbacks) if the content is not available in the primary specified language.  
IncludeSecuredItems |  `false` |  All content item query methods |  Specifies whether content items requiring special permissions (e.g., [secured sections](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#page-security-configuration)) should be included in the results.  
IsForPreview |  `null` |  All content item query methods |  Allows overriding the preview mode context. If left null, the retrieval respects the current website context (whether the request is in preview mode or live). Set to true to force preview data or false to force live data.  
![]()
[]()[]()
