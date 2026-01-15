# ContentRetriever API
  * [ Copy page link ](documentation/developers-and-admins/api/content-item-api/content-retriever-api#) | [Get HelpService ID](documentation/developers-and-admins/api/content-item-api/content-retriever-api#)
Core MVC 5


[✖](documentation/developers-and-admins/api/content-item-api/content-retriever-api# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/api/content-item-api/content-retriever-api#)
`IContentRetriever` provides a unified API to fetch various types of content managed by Xperience from within web applications. It’s intended as the primary service for the retrieval of:
  * Pages
  * Reusable content items
  * Content sharing [reusable field schemas](documentation/developers-and-admins/development/content-types/reusable-field-schemas)


The simplest way to use `IContentRetriever` is directly calling each method with the default settings, which are optimized for most common scenarios:
C#
**Retrieve the current page**
Copy
```
// Retrieve the current page
var currentPage = await contentRetriever.RetrieveCurrentPage<HomePage>();
```

C#
**Retrieve pages of a specific content type**
Copy
```
// Retrieve all pages of a specific content type
var articles = await contentRetriever.RetrievePages<ArticlePage>();
```

C#
**Retrieve reusable content items**
Copy
```
// Retrieve content items by content type
var testimonials = await contentRetriever.RetrieveContent<TestimonialItem>();
```

Functionally, the API serves as an abstraction layer over [content query](documentation/developers-and-admins/api/content-item-api/content-item-query-api), where it handles common retrieval scenarios by providing default, recommended configuration for various aspects like [query construction](documentation/developers-and-admins/api/content-item-api/content-item-query-api#build-queries), [language fallback](documentation/developers-and-admins/configuration/languages#language-fallbacks) handling, and [linked item retrieval](documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-linked-content-items). However, it also provides developers the option to override these defaults by exposing parts of the retrieval lifecycle (e.g., query construction, model mapping) via function delegates.
This page introduces concepts shared by all methods available on the service, together with relevant examples. For a complete method reference, see [Reference - ContentRetriever API](documentation/developers-and-admins/api/content-item-api/reference-content-retriever-api).
## Shared aspects of the API
### Configurable retrieval behavior
`IContentRetriever` method overloads accept an optional parameter object, named according to the retrieval scenario (e.g., [RetrieveCurrentPageParameters](documentation/developers-and-admins/api/content-item-api/reference-content-retriever-api#retrieve-the-current-page), [RetrievePagesParameters](documentation/developers-and-admins/api/content-item-api/reference-content-retriever-api#retrieve-pages-of-a-single-content-type) ). These objects allow you to configure the high-level behavior and scope of the content retrieval operation. They primarily control how and under what context data is fetched.
These parameters objects define behavior such as:
  * The desired [content language](documentation/developers-and-admins/configuration/languages) (`LanguageName`, `UseLanguageFallbacks`).
  * Whether to retrieve preview or live content (`IsForPreview`).
  * The depth of linked content to include (`LinkedItemsMaxLevel`).
  * Whether to include content items requiring [authentication](documentation/business-users/website-content/secure-pages) (`IncludeSecuredItems`).
  * Scope limitations like specific [content tree paths](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#filter-pages-based-on-content-tree-structure) (`PathMatch`) or [workspaces](documentation/developers-and-admins/configuration/users/role-management/workspaces) (`WorkspaceNames`).


When you call a retriever method without providing a specific parameters object – often by using the simpler extension method overloads – or if you provide a default instance (`new RetrieveCurrentPageParameters()` for example), the API uses sensible defaults.
These defaults are designed for the most common use cases, typically relying on the current request’s context (e.g., using the detected preferred language, respecting the current preview mode status) and opting for safer, less resource-intensive settings (like `LinkedItemsMaxLevel = 0` and `IncludeSecuredItems = false`). This results in concise and performant queries.
The flexibility of this approach is in overriding the defaults when instantiating these objects. This allows you to tailor the retrieval to specific requirements. For example:
  * You might need to display content in a specific language regardless of the user’s preference: 
```
new RetrievePagesParameters { LanguageName = "spanish" }
```

  * You might need to fetch related items linked from a main article: 
```
new RetrieveCurrentPageParameters { LinkedItemsMaxLevel = 1 }
```

  * You might need to ensure you’re always getting the latest, unpublished content: 
```
new RetrieveContentParameters { IsForPreview = true }
```

  * You might need to retrieve pages only within a specific section of the site: 
```
new RetrievePagesParameters { PathMatch = PathMatch.Children("/products") }
```



By using these parameters objects, you gain explicit control over the retrieval context and scope, ensuring the retriever fetches the right content based on your application’s requirements, rather than just relying on provided defaults.
All `*Parameters` objects also include a `Default` property. This property provides a default instance of the specific parameter type when no custom configuration is needed. Useful when a configuration action is syntactically required but no additional configuration is necessary (for instance, when using certain advanced overloads of the available methods).
### Optional query configuration
Certain overloads of `IContentRetriever` methods accept an `additionalQueryConfiguration` parameter. This parameter allows you to directly configure the underlying [content query builder](documentation/developers-and-admins/api/content-item-api/content-item-query-api#build-queries) instance, providing advanced control over the database query construction beyond what is possible using the [retrieval parameters](documentation/developers-and-admins/api/content-item-api/content-retriever-api#configurable-retrieval-behavior) described above.
You can use this to apply various query modifications, such as:
  * **Filtering** – limiting results using [Where](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#where) conditions.
  * **Sorting** – defining [OrderBy](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#orderby) clauses.
  * **Projection** – specifying exact database columns to retrieve using [Columns](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#columns).
  * **Paging** – limits results using: 
    * [TopN](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#topn) – returns only the first `N` records according to the specified order. Cannot be combined with `Offset`.
    * [Offset](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#offset) – skips the first `offset` records and then retrieves the next `fetch` records. This is the recommended method for implementing paging and requires an `OrderBy` clause to ensure consistent results. Cannot be combined with `TopN`.
  * **Linked items configuration** – options for retrieving [linked items](documentation/business-users/content-hub/content-items#link-content-items) via [WithLinkedItems](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#withlinkeditems). Linked item depth is reflected from the corresponding [method configuration object](documentation/developers-and-admins/api/content-item-api/content-retriever-api#configurable-retrieval-behavior).
  * **Method-specific options** – certain configuration options are unique to specific methods. For example, `UrlPathColumns` for page queries or [InSmartFolder](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#insmartfolder) for content item queries.


Query configuration objects are named according to the corresponding retrieval method (e.g., `RetrieveCurrentPageQueryParameters`, `RetrievePagesQueryParameters`, `RetrieveContentQueryParameters`). All `*QueryParameters` objects also include a `Default` property. This property provides a default instance of the specific parameter type when no custom configuration is needed. Useful when a configuration action is syntactically required but no additional configuration is necessary (for instance, when using certain advanced overloads of the available methods).
### URL retrieval behavior
When retrieving pages using `IContentRetriever`, you have control over how page URLs are generated, particularly for multilingual sites and when retrieving data explicitly to construct URLs. This is managed through the [additionalQueryConfiguration delegate](documentation/developers-and-admins/api/content-item-api/content-retriever-api#optional-query-configuration), which allows you to customize the underlying query.
#### Controlling URL language with fallbacks
By default, `IContentRetriever` methods configure their internal queries to use [UrlLanguageBehavior.UseRequestedLanguage](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls#url-language-behavior). When a page uses content from a fallback language, the URL is still generated for the originally requested language.
Use `SetUrlLanguageBehavior(UrlLanguageBehavior.UseFallbackLanguage)` to generate URLs that match the actual language of the retrieved content instead of the requested language.
C#
**Configure query to use fallback language for URLs**
Copy
```
var pages = await contentRetriever.RetrievePages<ArticlePage>(
    new RetrievePagesParameters { LanguageName = "es" },
    query => query.SetUrlLanguageBehavior(UrlLanguageBehavior.UseFallbackLanguage),
    new RetrievalCacheSettings(cacheItemNameSuffix:
        $"{nameof(RetrievePagesQueryParameters.SetUrlLanguageBehavior)}|UseFallbackLanguage")
);

foreach (var page in pages) {
  // If content fell back to English, URL will be
  // '/en/my-page' instead of '/es/my-page'
  var url = page.GetUrl();
}
 


```

#### Optimizing retrieved data for URL generation
To improve query performance when your primary need is page URLs rather than full page content, use the `UrlPathColumns()` method in the `additionalQueryConfiguration`.
This method ensures that the query only fetches the minimal set of data columns required to construct page URLs. This is especially beneficial when you intend to use the URLs for navigation menus, sitemaps, or other linking purposes without loading the entire page object.
C#
**Retrieve pages optimized for URL generation**
Copy
```
var pagesWithUrlsOnly = await contentRetriever.RetrievePages<ArticlePage>(
    RetrievePagesParameters.Default,
    // Retrieves the title and all columns required for URL resolution
    query => query
              .Columns("ArticleTitle")
              .UrlPathColumns(),
    new RetrievalCacheSettings(cacheItemNameSuffix:
        $"{nameof(RetrievePagesQueryParameters.Columns)}|ArticleTitle|{nameof(RetrievePagesQueryParameters.UrlPathColumns)}")
);
 
 
 


```

Using `UrlPathColumns()` helps ensure that [GetUrl()](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls#using-the-geturl-extension-method) can operate on the retrieved objects without further database calls, as the necessary data is already pre-fetched.
### Implicit result caching
The `IContentRetriever` API supports implicit result [caching](documentation/developers-and-admins/development/caching/data-caching). By default, caching is enabled for all retrieval methods unless explicitly disabled using `RetrievalCacheSettings.CacheDisabled`.
You can configure [caching behavior](documentation/developers-and-admins/development/caching/data-caching#cache-reusable-content-items-and-pages) using the `RetrievalCacheSettings` parameter. This parameter allows you to:
  * **Enable or disable caching** – Use `RetrievalCacheSettings.CacheDisabled` to disable caching for specific retrieval operations.
  * **Set cache item name suffix** – If you [modify the default query](documentation/developers-and-admins/api/content-item-api/content-retriever-api#optional-query-configuration) used when retrieving data, you must ensure a unique cache item name for the data via a suffix appended to the cache key. See [Ensure unique cache keys](documentation/developers-and-admins/api/content-item-api/content-retriever-api#ensure-unique-cache-keys).
  * **Set cache expiration** – Specify the cache expiration time using the `CacheExpiration` property.
  * **Use sliding expiration** – Enable sliding expiration by setting `UseSlidingExpiration` to `true`.
  * **Add additional cache dependencies** – Use the `AdditionalCacheDependencies` property to define custom dependencies for cache invalidation.


#### Default cache settings
  * The default cache expiration time is set to **10** minutes. This value is used when no specific expiration is provided in `RetrievalCacheSettings`. You can set a default expiration time globally for all requests made via the content retriever API using `ContentRetrieverCacheOptions`. See [Set global cache expiration time](documentation/developers-and-admins/api/content-item-api/content-retriever-api#set-global-cache-expiration-time).
  * Sliding expiration is **disabled** by default, meaning the cache duration is fixed and doesn’t get extended when the cached item is accessed.


#### Caching with custom configuration
C#
**Use custom caching configurations**
Copy
```
// Configure cache settings with a 30-minute expiration and sliding expiration enabled.
var cacheSettings = new RetrievalCacheSettings(
    // Add a unique suffix to identify the cached data.
    cacheItemNameSuffix: $"{nameof(ContentTypesQueryParameters.OfContentType)}|ArticleGenerated",
    // Cache entries will expire 30 minutes after their last access.
    cacheExpiration: TimeSpan.FromMinutes(30),
    // Resets the expiration timer on each access to keep frequently accessed items in the cache.
    useSlidingExpiration: true);

var result = await contentRetriever.RetrieveCurrentPage<ArticleGenerated>(
    RetrieveCurrentPageParameters.Default,
    RetrieveCurrentPageQueryParameters.Default,
    cacheSettings
);
 
 
 
 
 
 



 


```

#### Disable result caching
C#
**Disable caching for queries**
Copy
```
var result = await contentRetriever.RetrieveCurrentPage<ArticleGenerated>(
    RetrieveCurrentPageParameters.Default,
    RetrieveCurrentPageQueryParameters.Default,
    // Disables result caching for the query
    RetrievalCacheSettings.CacheDisabled
);
 


```

#### Ensure unique cache keys
When you use the `additionalQueryConfiguration` parameter to customize the query, you must provide a `cacheItemNameSuffix` in `RetrievalCacheSettings`. This suffix ensures that the cache key is unique for your specific query variation, as the system **does not** reflect the additional query configuration in the final constructed cache item name.
A recommended pattern for the suffix is to combine the name of the query configuration object with a string representation of its parameters. For example:
C#
**Ensure unique cache item names for parameterized queries**
Copy
```
var cacheSettings = new RetrievalCacheSettings(
    // Constructs a unique suffix by combining the string representation of the parameter values.
    // This ensures that different query configurations result in different cache keys.
    cacheItemNameSuffix: $"{nameof(RetrievePagesQueryParameters.TopN)}|3"
);

var result = await contentRetriever.RetrieveCurrentPage<ArticleGenerated>(
    RetrieveCurrentPageParameters.Default,
    a => a.TopN(3),
    cacheSettings
);
 



 


```

This practice helps prevent cache collisions when different query configurations might otherwise produce the same default cache key. Your strategy for generating suffixes should ensure consistent outputs varied only by the provided parameterization. This way, you leverage the cache item name generated internally by the method to its maximum potential and minimize the possibility of caching identical results multiple times under different keys.
In requests that don’t modify the retrieval query in any way, the suffix can be defined as an empty string.
#### Set global cache expiration time
Results returned by the API are by default cached for 10 minutes, unless overridden directly at the point of the retrieval operation by a specific instance of `RetrievalCacheSettings`. You can set this default caching period using `ContentRetrieverCacheOptions` and the [options pattern](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options).
C#
**Program.cs - Set global cache expiration time**
Copy
```
// Sets the global default cache expiration for IContentRetriever instances.
// This value is used if no specific RetrievalCacheSettings.CacheExpiration is provided
// at the point of a retrieval operation, allowing for both global defaults and granular overrides.
builder.Services.Configure<ContentRetrieverCacheOptions>(options =>
{
    // Sets the default cache duration to 30 minutes.
    // Individual IContentRetriever calls can override this via RetrievalCacheSettings.
    options.DefaultCacheExpiration = TimeSpan.FromMinutes(30);
});
 


```

#### Gather generated cache dependencies
The `IContentRetriever` API automatically collects generated [cache dependencies](documentation/developers-and-admins/development/caching/cache-dependencies) for all retrieved items. This is achieved using the `CacheDependencyCollector` class, which operates in a scoped context. You can access the collected dependencies by creating an instance of `CacheDependencyCollector` and calling its `GetCacheDependency()` method.
This is particularly useful when you have a broader caching strategy that needs to incorporate the dependencies of data fetched by multiple content retriever calls. For example, you can disable caching for individual requests, collect cache dependencies generated for the retrieved data, and use them as dependencies in a broader cached section.
C#
**Gather cache dependencies using CacheDependencyCollector**
Copy
```
using Kentico.Content.Web.Mvc;
using CMS.Helpers;

// Initializes a new collector scope
using (var dependencyCollector = new CacheDependencyCollector())
{
    var cachedData = await progressiveCache.LoadAsync(async (cs) =>
    {
        var page = await contentRetriever.RetrieveCurrentPage<Home>(
            RetrieveCurrentPageParameters.Default,
            RetrieveCurrentPageQueryParameters.Default,
            // Cache disabled for the request
            cacheSettings: RetrievalCacheSettings.CacheDisabled
        );

        var relatedArticles = await contentRetriever.RetrievePages<ArticlePage>(
            RetrievePagesParameters.Default,
            RetrievePagesQueryParameters.Default,
            // Cache disabled for the request
            cacheSettings: RetrievalCacheSettings.CacheDisabled
        );

        // Get the combined dependencies from all IContentRetriever calls
        // within the CacheDependencyCollector's scope up until this point.
        // The current set of dependencies continues to accumulate
        // until the parent scope gets disposed.
        cs.CacheDependency = dependencyCollector.GetCacheDependency();

        // ...
    }, cacheSettings);
}
 



 



 



 


```

The collector also supports nested scopes. If you create multiple `CacheDependencyCollector` instances in a nested fashion (e.g., within a method that calls another method, both using a collector), dependencies from inner scopes are automatically propagated to outer scopes upon disposal of the inner collector.
The `CacheDependencyCollector.AddCacheDependency(CMSCacheDependency dependency)` method can also be used to manually add dependencies to the current scope if needed. Note that the method must be called from inside an initialized collector scope – the call otherwise returns an exception.
### Custom model mapping
The `IContentRetriever` API provides flexible model mapping capabilities that support both registered content types and custom model classes. The underlying model mapping system uses the following priority:
  1. **Registered content types** – If a model type is registered for the content type via [RegisterContentTypeMappingAttribute](documentation/developers-and-admins/api/generate-code-files-for-system-objects#registercontenttypemapping-attribute) and your target model type (provided via the generic method parameter) is assignable from the registered type, the registered type is used for mapping.
  2. **Direct model mapping** – If no registered type is found or it’s not assignable, your specified model type is used directly for mapping. The model must be a non-abstract class, struct, or record with a parameterless constructor. The API attempts automatic mapping to your specified model, using case-insensitive property matching with the columns retrieved by the query. This flexibility allows you to use any custom, simplified models containing only the properties you need.


[![Content retriever default model mapping flow](docsassets/documentation/content-retriever-api/ContentRetrieverDefaultMapping.png)](https://docs.kentico.com/docsassets/documentation/content-retriever-api/ContentRetrieverDefaultMapping.png)
#### Custom mapping with configureModel delegate
Additionally, `IContentRetriever` method overloads include an optional `configureModel` parameter, which allows you to inject custom logic into the model mapping pipeline for even more advanced scenarios.
[![Content retriever model mapping flow with ‘configureModel’](docsassets/documentation/content-retriever-api/ContentRetrieverCustomMappingFlow.png)](https://docs.kentico.com/docsassets/documentation/content-retriever-api/ContentRetrieverCustomMappingFlow.png)
The parameter has the following signature:
```
Func<IContentQueryDataContainer, TInput, Task<TResult>> configureModel;
```

This parameter gives you control over how the retrieved data is mapped to your resulting model class.
  * `IContentQueryDataContainer` – contains the database row (page, content item) being mapped. You can access column values via `GetValue<TValue>(string columnName)`.
  * `TInput` – the intermediate mapped object. This could be either a registered content type model or your custom model type, depending on the mapping priority. For example, when calling:
```
contentRetriever.RetrievePages<ArticleGenerated>()
```

`TInput` contains an instance of `ArticleGenerated` for each mapped item from the result set. You can use this intermediate representation to fetch the data, run additional transformations (combining fields, or enriching the model with external data) and any other custom logic.
  * `TResult` – the final type you want to return from the content retrieval operation. This is the type of object that your `configureModel` delegate creates and returns.


See the [Run queries and map the result](documentation/developers-and-admins/api/content-item-api/content-item-query-api#run-queries-and-map-the-result) documentation to learn about the content item query model mapping lifecycle. `IContentRetriever` methods simply expose certain parts of the lifecycle.
#### Direct model mapping without registration
You can use any custom model class directly without needing to register it using `RegisterContentTypeMappingAttribute`. If you leave `configureModel` as `null`, the content retriever attempts automatic mapping to your specified model type, using case-insensitive property matching with the columns retrieved by the query. This works for both [generated model classes](documentation/developers-and-admins/api/generate-code-files-for-system-objects) and custom classes that match the underlying content type structure.
C#
**Direct mapping to a custom model class**
Copy
```
// Custom model class without content type registration
public class SimpleArticle
{
    public string Title { get; set; }
    public string Summary { get; set; }
    public DateTime PublishedDate { get; set; }
}

var contentTypes = new[] { ArticlePage.CONTENT_TYPE_NAME, BlogPage.CONTENT_TYPE_NAME };

// Direct mapping to custom model 'SimpleArticle' that does not need to be registered
var articles = await contentRetriever.RetrievePagesOfContentTypes<SimpleArticle>(contentTypes);
```

C#
**Direct mapping to a generated class**
Copy
```
// 'ArticleGenerated' is a model class generated by Xperience
// Works the same way as before - uses registered mapping if available
var articles = await contentRetriever.RetrievePages<ArticleGenerated>();
```

#### Advanced mapping with configureModel delegate
For more complex scenarios, you can provide a custom mapping function using the `configureModel` delegate. This function receives the raw data for each retrieved item and the intermediate mapped object, allowing you to implement custom transformation logic.
C#
**Advanced mapping with configureModel delegate**
Copy
```
using Kentico.Content.Web.Mvc;

// Custom view model for complex transformations
public class CustomArticleViewModel
{
    public string PageTitle { get; set; }
    public string PageAdminTitle { get; set; }
    public string FormattedDate { get; set; }
}

// Using configureModel for complex transformations
// TInput could be either a registered model or custom model based on mapping priority
var customViewModels =
    await contentRetriever.RetrievePages<SimpleArticle, CustomArticleViewModel>(
        new RetrievePagesParameters(),
        null, // No query modifications
        RetrievalCacheSettings.CacheDisabled,

        // configureModel delegate for custom transformations
        async (container, mappedResult) =>
        {
            // 'mappedResult' is the intermediate SimpleArticle object
            // 'container' provides access to raw database data
            return new CustomArticleViewModel
            {
                PageTitle = mappedResult.Title ?? "Default Title",
                PageAdminTitle = container.GetValue<string>("WebPageItemName"),
                FormattedDate = mappedResult.PublishedDate.ToString("MMM dd, yyyy")
            };
        }
);
```

See [Usage scenarios](documentation/developers-and-admins/api/content-item-api/content-retriever-api#usage-scenarios) for common use cases and recommendations for when to use either approach.
This mechanism offers flexibility, allowing simple mapping for standard cases and complex, custom transformations for advanced scenarios.
#### Usage scenarios
##### Direct mapping to custom models
  * _Use case:_ Using custom model classes without requiring content type registration. Create simplified models with only the properties you need, or use models that don’t exactly match the content type structure.
  * _Approach:_ Use methods like `contentRetriever.RetrieveCurrentPage<YourCustomClass>()` directly. The model must be a non-abstract class with a parameterless constructor.


##### Simple direct mapping to registered models
  * _Use case:_ Retrieving content directly into [generated model classes](documentation/developers-and-admins/api/generate-code-files-for-system-objects) or other registered content type models when your class structure matches the content type fields.
  * _Approach:_ Use methods like `contentRetriever.RetrieveCurrentPage<YourRegisteredClass>()`.


##### Small adjustments or conformance to shared models (Minimal custom mapping)
  * _Use case:_ Making small changes like formatting dates, providing default values, renaming properties for view models, or when the intermediate mapped object needs slight modifications.
  * _Approach:_ Use the `configureModel` delegate with simple transformations or object-to-object mapping.


##### Complex transformations and custom view models (Extensive custom mapping)
  * _Use case:_ Transforming fetched content into view models with significantly different structures, combining multiple source fields, performing calculations or conditional logic, or accessing data not directly available through standard mapping.
  * _Approach:_ Leverage the `configureModel` delegate extensively to build your target C# object with full control over the transformation process.


### Using shared types for bulk content retrieval
[Generated model classes](documentation/developers-and-admins/api/generate-code-files-for-system-objects#icontentitemfieldssource-interfaces) implement shared interfaces that provide access to system properties:
  * [Page content type model classes](documentation/developers-and-admins/api/generate-code-files-for-system-objects) implement `IWebPageFieldsSource`
  * [Reusable content type model classes](documentation/developers-and-admins/api/generate-code-files-for-system-objects) implement `IContentItemFieldsSource`


However, the way `IContentRetriever` methods work with these interfaces depends on how content types are identified and resolved during retrieval operations.
The following `IContentRetriever` methods:
  * `RetrievePages`
  * `RetrievePagesByGuids`
  * `RetrieveContent`
  * `RetrieveContentByGuids`


determine the target content type from the provided generic. This identification happens through the [RegisterContentTypeMappingAttribute](documentation/developers-and-admins/api/generate-code-files-for-system-objects#registercontenttypemapping-attribute) present on [generated model classes](documentation/developers-and-admins/api/generate-code-files-for-system-objects).
Since `IWebPageFieldsSource` and `IContentItemFieldsSource` are shared interfaces without specific content type mappings, using them directly as generic type parameters is not supported by these methods. The system cannot determine which specific content type to retrieve:
C#
**Unsupported approaches**
Copy
```
// These approaches are not supported - no content type can be determined
var pages = await contentRetriever.RetrievePages<IWebPageFieldsSource>();
var content = await contentRetriever.RetrieveContent<IContentItemFieldsSource>();
```

#### Multi-content type page retrieval with RetrieveAllPages
When you need to retrieve pages across multiple content types without knowing the specific types at compile time, use the `RetrieveAllPages` method. This method retrieves all web pages that match the specified [parameterization](documentation/developers-and-admins/api/content-item-api/reference-content-retriever-api#retrieve-all-pages):
C#
**Retrieve all page content types**
Copy
```
// Retrieves all page content types matching the specified criteria
IEnumerable<IWebPageFieldsSource> allPages =
    await contentRetriever.RetrieveAllPages<IWebPageFieldsSource>(
        new RetrieveAllPagesParameters
        {
            LanguageName = "en-US",
            PathMatch = PathMatch.Children("/articles")
        }
);
 


```

#### Retrieving pages by GUIDs across content types
When you need to retrieve all pages of any content type by specific GUIDs, use the `RetrieveAllPages` method with [additional query configuration](documentation/developers-and-admins/api/content-item-api/content-retriever-api#optional-query-configuration):
C#
**Retrieve pages by GUIDs across all content types**
Copy
```
// A collection of page GUIDs (typically obtained from Page selector)
var webPageItemGuids = new[] { guid1, guid2, guid3 };

var pagesByGuids = await contentRetriever.RetrieveAllPages<IWebPageFieldsSource>(
    RetrieveAllPagesParameters.Default,
    query => query
        .Where(where => where.WhereIn(
            nameof(IWebPageFieldsSource.SystemFields.WebPageItemGUID),
            webPageItemGuids)),
    new RetrievalCacheSettings($"{nameof(RetrieveAllPagesQueryParameters.Where)}|ByGUIDs")
);
 
 
 


```

#### Limitations for reusable content items
The `RetrieveAllPages` approach is only available for page content types. For reusable content items, you must use one of the following approaches:
  * Specify exact content types you want to retrieve.
  * Use [reusable field schemas](documentation/developers-and-admins/development/content-types/reusable-field-schemas) to group related content types that share common properties.


You can cast results to the shared `IContentItemFieldsSource` interface when retrieving across multiple reusable field schemas or content types:
C#
**Reusable content item retrieval requires specific content types**
Copy
```
// Retrieve a specific reusable content type
var testimonials = await contentRetriever.RetrieveContent<TestimonialItem>();

// Retrieve multiple specific content types using reusable field schema
var schemaNames = new[] { IMetadataFields.REUSABLE_FIELD_SCHEMA_NAME };
IEnumerable<IMetadataFields> itemsWithSchema =
  await contentRetriever.RetrieveContentOfReusableSchemas<IMetadataFields>(schemaNames);

// Retrieve across multiple reusable schemas and cast to shared interface
var schemaNames = new[] {
    IMetadataFields.REUSABLE_FIELD_SCHEMA_NAME,
    ISeoFields.REUSABLE_FIELD_SCHEMA_NAME
};
IEnumerable<IContentItemFieldsSource> itemsWithSchemas =
  await contentRetriever.RetrieveContentOfReusableSchemas<IContentItemFieldsSource>(schemaNames);
```

## Method reference
See [Reference - ContentRetriever API](documentation/developers-and-admins/api/content-item-api/reference-content-retriever-api) for a reference of all methods available on `IContentRetriever`.