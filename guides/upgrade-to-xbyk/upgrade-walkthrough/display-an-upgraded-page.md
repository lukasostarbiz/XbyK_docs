---
source: https://docs.kentico.com/guides/upgrade-to-xbyk/upgrade-walkthrough/display-an-upgraded-page
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Upgrade to Xperience by Kentico](/guides/upgrade-to-xbyk)
  * [Walk through the upgrade step-by-step](/guides/upgrade-to-xbyk/upgrade-walkthrough)
  * Display an upgraded page with structured data and Page Builder functionality 


# Display an upgraded page with structured data and Page Builder functionality
In this final step, we’ll cover the code adjustments needed to display the _Contacts_ page from the _Kentico Xperience 13_ version of the Dancing Goat in _Xperience by Kentico_ , including the structured _Contact_ and _Cafe_ data it displays, and the widget zone containing the system _Form_ widget.
## Look over the contacts page
To start out, let’s take a look at the _Contacts_ page in KX13, located at the _/Contacts_ path.
[![Contacts page with no form](/docsassets/guides/display-an-upgraded-page/PageKX13.png)](/docsassets/guides/display-an-upgraded-page/PageKX13.png)
It displays the contact information of the Dancing Goat company alongside a form widget. Then below, it shows a list of company cafes and a map.
In order to recreate this page in Xperience by Kentico, we’ll need to handle all of these components.
If you do not have a Google Maps API key, the map won’t render on your page.
If you do have such a key, store it in the _appsettings.json_ file of both your KX13 and XbyK applications.
JSON
**appsettings.json**
Copy
```
...
"GoogleMapsApiKey": "[Your API key here]",
...
```

## Retrieve content items in XbyK
Looking through the code of the Dancing Goat project in KX13, we can see that it uses _repositories_ to centralize the querying code for each page type into its own file, such as `ContactRepository` and `CafeRepository`.
Let’s centralize this further using a _content retrieval service_ that can be shared among repositories that retrieve content items.
### Handle the base logic
In the KX13 Dancing Goat, you can look at the `ContactsController` to see which repository methods the application uses to feed the Contacts page. Here you can see it selects the first and only page of the _Contact_ type.
C#
**ContactRepository.cs (KX13 Dancing Goat)**
Copy
```
...
/// <summary>
/// Returns company's contact information.
/// </summary>
public Contact GetCompanyContact()
{
    return pageRetriever.Retrieve<Contact>(
        query => query
            .TopN(1),
        cache => cache
            .Key($"{nameof(ContactRepository)}|{nameof(GetCompanyContact)}"))
        .FirstOrDefault();
}
...
```

The `ContactsController` also relies on the `CafeRepository` for a query that selects all _Cafe_ pages under a specified path, filtered to only those which are _company cafes_.
C#
**CafeRepository.cs (KX13 Dancing Goat)**
Copy
```
...
/// <summary>
/// Returns an enumerable collection of company cafes ordered by a position in the content tree.
/// </summary>
/// <param name="nodeAliasPath">The node alias path of the articles section in the content tree.</param>
/// <param name="count">The number of cafes to return. Use 0 as value to return all records.</param>
public IEnumerable<Cafe> GetCompanyCafes(string nodeAliasPath, int count = 0)
{
    return pageRetriever.Retrieve<Cafe>(
        query => query
            .Path(nodeAliasPath, PathTypeEnum.Children)
            .TopN(count)
            .WhereTrue("CafeIsCompanyCafe")
            .OrderBy("NodeOrder"),
        cache => cache
            .Key($"{nameof(CafeRepository)}|{nameof(GetCompanyCafes)}|{nodeAliasPath}|{count}")
            // Include path dependency to flush cache when a new child page is created or page order is changed.
            .Dependencies((_, builder) => builder.PagePath(nodeAliasPath, PathTypeEnum.Children).PageOrder()));
}
...
```

We’ll make a service with methods that provide each of these options for a given content type.
#### Create the class
Add a new generic-typed class for retrieving content items in your XbyK solution, for example, in a **~/Services/Shared** folder.
C#
**ContentItemRetrieverService.cs**
Copy
```
using CMS.Helpers;
using CMS.Websites;
using CMS.Websites.Routing;
using Kentico.Content.Web.Mvc;
using Kentico.Content.Web.Mvc.Routing;

namespace DancingGoat.Web.Services.Shared;

public class ContentItemRetrieverService : IContentItemRetrieverService
{
    // Use dependency injection to populate these services
    private readonly IWebsiteChannelContext webSiteChannelContext;
    private readonly IPreferredLanguageRetriever preferredLanguageRetriever;
    private readonly IContentRetriever contentRetriever;

    ...
}
```

#### Query the first web page item of a given type
With these services, we can implement specialized querying scenarios, such as one that retrieves the first item of a specified content type, achieving the _Contact_ scenario mentioned above.
Although it’s not necessary for the _Contacts_ page, retrieving the current page is one of the most common scenarios we see for content retrieval. We’ll include it in the code samples for reference.
C#
**ContentItemRetrieverService.cs**
Copy
```
...
public async Task<T?> RetrieveFirstWebPageOfType<T>(
    int depth = 1,
    Action<RetrievePagesQueryParameters>? queryParameters = null,
    bool includeSecuredItems = true,
    string? languageName = null)
    where T : IWebPageFieldsSource, new()
{
    var parameters = new RetrievePagesParameters
    {
        LinkedItemsMaxLevel = depth,
        LanguageName = languageName ?? preferredLanguageRetriever.Get(),
        IsForPreview = webSiteChannelContext.IsPreview,
        IncludeSecuredItems = includeSecuredItems,
    };

    Action<RetrievePagesQueryParameters> combinedQueryParameters = config =>
    {
        queryParameters?.Invoke(config);
        config.TopN(1);
    };

    var pages = await contentRetriever.RetrievePages<T>(
        parameters: parameters,
        additionalQueryConfiguration: combinedQueryParameters,
        cacheSettings: null);
    return pages.FirstOrDefault();
}

public async Task<T?> RetrieveCurrentPage<T>(
    int depth = 1,
    Action<RetrieveCurrentPageQueryParameters>? queryParameters = null,
    bool includeSecuredItems = true,
    string? languageName = null)
    where T : IWebPageFieldsSource, new()
{
    var parameters = new RetrieveCurrentPageParameters
    {
        LinkedItemsMaxLevel = depth,
        LanguageName = languageName ?? preferredLanguageRetriever.Get(),
        IsForPreview = webSiteChannelContext.IsPreview,
        IncludeSecuredItems = includeSecuredItems
    };

    queryParameters ??= RetrieveCurrentPageQueryParameters.Default;

    return await contentRetriever.RetrieveCurrentPage<T>(
        parameters: parameters,
        additionalQueryConfiguration: queryParameters,
        cacheSettings: null);
}
...
```

#### Find child items under a given path
With the _Company contact_ scenario covered, we can move on to the _Cafes_ requirement.
Add a new method that uses `PathMatch` and conditionally adds additional query parameters.
C#
**ContentItemRetrieverService.cs**
Copy
```
...
public async Task<IEnumerable<T>> RetrieveWebPageChildrenByPath<T>(
    string path,
    int depth = 1,
    bool includeSecuredItems = true,
    Action<RetrievePagesQueryParameters>? queryParameters = null,
    string? languageName = null)
    where T : IWebPageFieldsSource, new()
{
    var parameters = new RetrievePagesParameters
    {
        LinkedItemsMaxLevel = depth,
        LanguageName = languageName ?? preferredLanguageRetriever.Get(),
        IsForPreview = webSiteChannelContext.IsPreview,
        PathMatch = PathMatch.Children(path),
        IncludeSecuredItems = includeSecuredItems
    };

    return await contentRetriever.RetrievePages<T>(
        parameters: parameters,
        additionalQueryConfiguration: queryParameters,
        cacheSettings: null);
}
...
```

### Add caching
So far, we have the basic functionality to retrieve the items, but looking over the page retriever queries from KX13’s Dancing Goat, you can see that there is caching functionality that we’re still missing, for example:
C#
**CafeRepository.cs (KX13 Dancing Goat)**
Copy
```
...
public IEnumerable<Cafe> GetCompanyCafes(string nodeAliasPath, int count = 0)
{
    return pageRetriever.Retrieve<Cafe>(
        ...
        cache => cache
            .Key($"{nameof(CafeRepository)}|{nameof(GetCompanyCafes)}|{nodeAliasPath}|{count}")
            // Include path dependency to flush cache when a new child page is created or page order is changed.
            .Dependencies((_, builder) => builder.PagePath(nodeAliasPath, PathTypeEnum.Children).PageOrder()));
}
...
```

Our `ContentItemRetrieverService` class uses the [ContentRetriever API](/documentation/developers-and-admins/api/content-item-api/content-retriever-api), which has implicit caching functionality.
Taking a look at the [data caching documentation](/documentation/developers-and-admins/development/caching/data-caching) for XbyK, we can see that the formats for caching differ somewhat from the `IPageRetriever` in KX13.
Lets implement this functionality, including reusable utilities we can use across the service.
If you query data using an API other than `ContentRetriever`, or require more advanced caching options, you can use `IProgressiveCache` for data caching. [data caching](/documentation/developers-and-admins/development/caching/data-caching#cache-general-objects)
#### Add caching utility methods
Copy the `IsCacheEnabled` example from the [data caching](/documentation/developers-and-admins/development/caching/data-caching#preview-mode-and-caching) documentation.
C#
**ContentItemRetrieverService.cs**
Copy
```
...
private bool IsCacheEnabled()
{
    return !webSiteChannelContext.IsPreview;
}
...
```

#### Create a class to configure caching in methods
Before we add caching to the data retrieval methods, let’s create a DTO class that we can reuse for configuring caching across the service. We can include a parameter of this new type, rather than typing out the same group of separate parameters each time.
Xperience’s API includes a `RetrievalCacheSettings` class out of box. However, this class’s properties are internal, and can’t be edited after instantiation, which makes combining configurations from multiple sources difficult.
By using our DTO, you can fall back to values from multiple sources when configuring cache, before constructing a `RetrievalCacheSettings` object.
Based on the caching documentation, we can configure **the time interval for which data is cached** , and **whether it uses a sliding expiration window**. Include corresponding properties in the class.
You can also configure the dependencies that specify **when to purge the cache** (dependencies), and **the name of the cache entry**. For these, we can include appropriate defaults in each method, but we should allow callers to specify additional dependencies and a custom name.
C#
**ContentItemRetrieverService.cs**
Copy
```
...
public class ContentItemRetrieverServiceCacheConfig
{
    /// <summary>
    /// The number of minutes the cache should be stored.
    /// </summary>
    public int? CacheExpiration { get; set; } = null;

    /// <summary>
    /// Determines whether the cache should use a sliding expiration, resetting the timer if accessed within the cache duration.
    /// </summary>
    public bool? UseSlidingExpiration { get; set; } = null;

    /// <summary>
    /// Additional cache keys to depend on.
    /// </summary>
    public IEnumerable<string>? ExtraDependencyKeys { get; set; } = null;

    /// <summary>
    /// Custom cache name parts to add to the cache item name suffix.
    /// </summary>
    public IEnumerable<string>? ExtraCacheNameParts { get; set; } = null;
}
```

For the simplicity of this example, we included this new class in the same file as the `ContentItemRetrieverService`.
#### Create a method to construct cache settings
So far, we’ve planned to add a `ContentItemRetrieverServiceCacheConfig` parameter to each of our retrieval methods. We can use this parameter to add extra cache configuration each time we call one of our retrieval methods. Let’s assume that each method will add its own baseline parts to the cache item name, aside from the optional name parts in the `ContentItemRetrieverServiceCacheConfig` object.
Create a method that constructs a `RetrievalCacheSettings` object based two parameters:
  * Cache name parts provided directly by each content retrieval method
  * A `ContentItemRetrieverServiceCacheConfig` object, optionally supplied as a parameter to a specific call of a content retrieval method


C#
**ContentItemRetrieverService.cs**
Copy
```
...
private RetrievalCacheSettings CreateCacheSettings(
    string[] cacheNameParts,
    ContentItemRetrieverServiceCacheConfig? cacheConfig)
{
    return IsCacheEnabled()
        ? new RetrievalCacheSettings(
            cacheItemNameSuffix: cacheNameParts
                .Concat(cacheConfig?.ExtraCacheNameParts?.ToArray() ?? [])
                .Join("|"),
            cacheExpiration: TimeSpan.FromMinutes(cacheConfig?.CacheExpiration ?? 15),
            cacheOnlyResultingItems: false,
            useSlidingExpiration: cacheConfig?.UseSlidingExpiration ?? true,
            additionalCacheDependencies: new CMSCacheDependency { CacheKeys = cacheConfig?.ExtraDependencyKeys?.ToArray() ?? [] }
        )
        : RetrievalCacheSettings.CacheDisabled;
}
...
```

#### Cache your queries
Now you can use this method to create cache settings for your `ContentRetriever` settings.
C#
**ContentItemRetrieverService.cs**
Copy
```
...
public async Task<T?> RetrieveFirstWebPageOfType<T>(
    int depth = 1,
    Action<RetrievePagesQueryParameters>? queryParameters = null,
    bool includeSecuredItems = true,
    string? languageName = null,
    ContentItemRetrieverServiceCacheConfig? cacheConfig = null)
    where T : IWebPageFieldsSource, new()
{
    var parameters = new RetrievePagesParameters
    {
        LinkedItemsMaxLevel = depth,
        LanguageName = languageName ?? preferredLanguageRetriever.Get(),
        IsForPreview = webSiteChannelContext.IsPreview,
        IncludeSecuredItems = includeSecuredItems,
    };

    Action<RetrievePagesQueryParameters> combinedQueryParameters = config =>
    {
        queryParameters?.Invoke(config);
        config.TopN(1);
    };

    string[] cacheNameParts = [
            nameof(ContentItemRetrieverService),
            nameof(RetrieveFirstWebPageOfType),
            typeof(T).Name,
            $"depth|{depth}",
            $"lang|{parameters.LanguageName}",
            $"preview|{parameters.IsForPreview}",
            $"secured|{parameters.IncludeSecuredItems}"
        ];

    var cacheSettings = CreateCacheSettings(cacheNameParts, cacheConfig);

    var pages = await contentRetriever.RetrievePages<T>(
        parameters: parameters,
        additionalQueryConfiguration: combinedQueryParameters,
        cacheSettings: cacheSettings);
    return pages.FirstOrDefault();
}

public async Task<T?> RetrieveCurrentPage<T>(
    int depth = 1,
    Action<RetrieveCurrentPageQueryParameters>? queryParameters = null,
    bool includeSecuredItems = true,
    string? languageName = null,
    ContentItemRetrieverServiceCacheConfig? cacheConfig = null)
    where T : IWebPageFieldsSource, new()
{
    var parameters = new RetrieveCurrentPageParameters
    {
        LinkedItemsMaxLevel = depth,
        LanguageName = languageName ?? preferredLanguageRetriever.Get(),
        IsForPreview = webSiteChannelContext.IsPreview,
        IncludeSecuredItems = includeSecuredItems
    };

    string[] cacheNameParts = [
            nameof(ContentItemRetrieverService),
            nameof(RetrieveCurrentPage),
            typeof(T).Name,
            $"depth|{depth}",
            $"lang|{parameters.LanguageName}",
            $"preview|{parameters.IsForPreview}",
            $"secured|{parameters.IncludeSecuredItems}"
        ];

    var cacheSettings = CreateCacheSettings(cacheNameParts, cacheConfig);

    queryParameters ??= RetrieveCurrentPageQueryParameters.Default;

    return await contentRetriever.RetrieveCurrentPage<T>(
        parameters: parameters,
        additionalQueryConfiguration: queryParameters,
        cacheSettings: cacheSettings);
}

public async Task<IEnumerable<T>> RetrieveWebPageChildrenByPath<T>(
    string path,
    int depth = 1,
    bool includeSecuredItems = true,
    Action<RetrievePagesQueryParameters>? queryParameters = null,
    string? languageName = null,
    ContentItemRetrieverServiceCacheConfig? cacheConfig = null)
    where T : IWebPageFieldsSource, new()
{
    var parameters = new RetrievePagesParameters
    {
        LinkedItemsMaxLevel = depth,
        LanguageName = languageName ?? preferredLanguageRetriever.Get(),
        IsForPreview = webSiteChannelContext.IsPreview,
        PathMatch = PathMatch.Children(path),
        IncludeSecuredItems = includeSecuredItems
    };

    string[] cacheNameParts = [
            nameof(ContentItemRetrieverService),
            nameof(RetrieveWebPageChildrenByPath),
            typeof(T).Name,
            $"path|{path}",
            $"depth|{depth}",
            $"lang|{parameters.LanguageName}",
            $"preview|{parameters.IsForPreview}",
            $"secured|{parameters.IncludeSecuredItems}"
        ];

    var cacheSettings = CreateCacheSettings(cacheNameParts, cacheConfig);

    return await contentRetriever.RetrievePages<T>(
        parameters: parameters,
        additionalQueryConfiguration: queryParameters,
        cacheSettings: cacheSettings);
}
...
 



 
 
 
 
 
 
 
 
 
 
 



 



 



 
 
 
 
 
 
 
 
 
 
 



 



 



 
 
 
 
 
 
 
 
 
 
 
 



 


```

In the end, your `ContentItemRetrieverService` should look like this:
[ContentItemRetrieverService.cs](/docsassets/guides/display-an-upgraded-page/ContentItemRetrieverService.cs)
### Register your service
Now, let’s set up the service and register it.
Create an interface with corresponding signatures for the public methods.
C#
**IContentItemRetrieverService.cs**
Copy
```
using CMS.Websites;
using Kentico.Content.Web.Mvc;

namespace DancingGoat.Web.Services.Shared;

public interface IContentItemRetrieverService
{
    /// <summary>
    /// Retrieves the first web page item of the provided type.
    /// </summary>
    /// <param name="depth">The maximum level of recursively linked content items that should be included in the results. Default value is 1.</param>
    /// <param name="queryParameters">Optional parameters to customize the query.</param>
    /// <param name="includeSecuredItems">If true, secured items will be included in the results. Default is true.</param>
    /// <param name="languageName">The language to query. If null, the language will be inferred from the URL of the current request.</param>
    /// <param name="cacheConfig">Settings to determine how the page data is cached.</param>
    /// <returns>The first web page content item of the specified type, or null if not found.</returns>
    Task<T?> RetrieveFirstWebPageOfType<T>(
        int depth = 1,
        Action<RetrievePagesQueryParameters>? queryParameters = null,
        bool includeSecuredItems = true,
        string? languageName = null,
        ContentItemRetrieverServiceCacheConfig? cacheConfig = null)
        where T : IWebPageFieldsSource, new();

    /// <summary>
    /// Retrieves the current page using ContentRetriever API
    /// </summary>
    /// <param name="depth">The maximum level of recursively linked content items that should be included in the results. Default value is 1.</param>
    /// <param name="queryParameters">Optional parameters to customize the query.</param>
    /// <param name="includeSecuredItems">If true, secured items will be included in the results. Default is true.</param>
    /// <param name="languageName">The language to query. If null, the language will be inferred from the URL of the current request.</param>
    /// <param name="cacheConfig">Settings to determine how the page data is cached.</param>
    /// <returns>The current web page content item of the specified type, or null if not found.</returns>
    Task<T?> RetrieveCurrentPage<T>(
        int depth = 1,
        Action<RetrieveCurrentPageQueryParameters>? queryParameters = null,
        bool includeSecuredItems = true,
        string? languageName = null,
        ContentItemRetrieverServiceCacheConfig? cacheConfig = null)
        where T : IWebPageFieldsSource, new();

    /// <summary>
    /// Retrieves the child pages of a given web page tree path.
    /// </summary>
    /// <param name="path">Path of the parent page whose children should be retrieved.</param>
    /// <param name="depth">The maximum level of recursively linked content items that should be included in the results. Default value is 1.</param>
    /// <param name="includeSecuredItems">If true, secured items will be included in the results. Default is true.</param>
    /// <param name="additionalQueryConfiguration">Optional parameters to further customize the query.</param>
    /// <param name="languageName">The language to query. If null, the language will be inferred from the URL of the current request.</param>
    /// <param name="cacheConfig">Settings to determine how the page data is cached.</param>
    /// <returns>An enumerable collection of child web page content items of the specified type.</returns>
    public Task<IEnumerable<T>> RetrieveWebPageChildrenByPath<T>(
        string path,
        int depth = 1,
        bool includeSecuredItems = true,
        Action<RetrievePagesQueryParameters>? queryParameters = null,
        string? languageName = null,
        ContentItemRetrieverServiceCacheConfig? cacheConfig = null)
        where T : IWebPageFieldsSource, new();
}
```

Now, register the `ContentItemRetrieverService` class as the implementation of this interface with the DI container in the `ServiceCollectionExtensions` class.
C#
**~/Helpers/ServiceCollectionExtensions.cs**
Copy
```
using DancingGoat.Models;
using DancingGoat.Web.Services.Shared;

namespace DancingGoat
{
    public static class ServiceCollectionExtensions
    {
        public static void AddDancingGoatServices(this IServiceCollection services)
        {
            ...
            services.AddTransient<IContentItemRetrieverService, ContentItemRetrieverService>();
            ...
        }
        ...
    }
}
```

## Transfer the repositories
With this shared querying logic in place, we can start bringing over the repositories used in the KX13 site.
### Contact repository
The KX13 `ContactRepository` has one method called `GetCompanyContact`. It retrieves the first page of the _Contact_ type, and caches the result using the name of the repository and the method.
C#
**ContactRepository.cs (KX13 Dancing Goat)**
Copy
```
...
// Populated with dependency injection
private readonly IPageRetriever pageRetriever;
...
/// <summary>
/// Returns company's contact information.
/// </summary>
public Contact GetCompanyContact()
{
    return pageRetriever.Retrieve<Contact>(
        query => query
            .TopN(1),
        cache => cache
            .Key($"{nameof(ContactRepository)}|{nameof(GetCompanyContact)}"))
        .FirstOrDefault();
}
...
```

With the `RetrieveFirstWebPageOfType` method, we can replicate this functionality here. Add a new file located at _~/Models/Contacts/_ in the _DancingGoat.Web_ project, or copy and modify the repository file from KX13.
Extend the existing cache suffix with the name of the method and the repository.
C#
**ContactRepository.cs**
Copy
```
using DancingGoat.Web.Services.Shared;
using DancingGoatCore;

namespace DancingGoat.Models;

public class ContactRepository
{
    // Use dependency injection to populate this service
    private readonly IContentItemRetrieverService contentItemRetrieverService;

    ...

    public async Task<Contact?> GetCompanyContact()
    {
        return await contentItemRetrieverService.RetrieveFirstWebPageOfType<Contact>(
            cacheConfig: new()
            {
                CacheExpiration = 30,
                ExtraCacheNameParts = [nameof(ContactRepository), nameof(GetCompanyContact)]
            }
        );
    }
}
```

### Cafe repository
The `CafeRepository` class in KX13 has one method that is involved in the delivery of the _Contacts_ page. It retrieves the cafe pages located under the provided path, filtering the query to contain only company cafes.
C#
**CafeRepository.cs (KX13 Dancing Goat)**
Copy
```
...
// Use dependency injection to populate this.
private readonly IPageRetriever pageRetriever;

...

/// <summary>
/// Returns an enumerable collection of company cafes ordered by a position in the content tree.
/// </summary>
/// <param name="nodeAliasPath">The node alias path of the articles section in the content tree.</param>
/// <param name="count">The number of cafes to return. Use 0 as value to return all records.</param>
public IEnumerable<Cafe> GetCompanyCafes(string nodeAliasPath, int count = 0)
{
    return pageRetriever.Retrieve<Cafe>(
        query => query
            .Path(nodeAliasPath, PathTypeEnum.Children)
            .TopN(count)
            .WhereTrue("CafeIsCompanyCafe")
            .OrderBy("NodeOrder"),
        cache => cache
            .Key($"{nameof(CafeRepository)}|{nameof(GetCompanyCafes)}|{nodeAliasPath}|{count}")
            // Include path dependency to flush cache when a new child page is created or page order is changed.
            .Dependencies((_, builder) => builder.PagePath(nodeAliasPath, PathTypeEnum.Children).PageOrder()));
}
```

We can use our `RetrieveWebPageChildrenByPath` method to replicate this functionality. Add a new file located at _~/Models/Cafes/_ in the _DancingGoat.Web_ project, or copy and modify the KX13 repository.
C#
**CafeRepository.cs**
Copy
```
using DancingGoat.Web.Services.Shared;
using DancingGoatCore;
using Kentico.Content.Web.Mvc;

namespace DancingGoat.Models;

public partial class CafeRepository
{
    // Use dependency injection to populate this service
    private readonly IContentItemRetrieverService contentItemRetrieverService;

    ...

    public async Task<IEnumerable<Cafe>> GetCompanyCafes(string parentPagePath, int count = 0)
    {
        Action<RetrievePagesQueryParameters> queryConfig = count > 0
            ? config => config
                .TopN(count)
                .Where(where => where.WhereTrue(nameof(Cafe.CafeIsCompanyCafe)))
                .OrderBy(nameof(Cafe.SystemFields.WebPageItemOrder))
            : config => config
                .Where(where => where.WhereTrue(nameof(Cafe.CafeIsCompanyCafe)))
                .OrderBy(nameof(Cafe.SystemFields.WebPageItemOrder));

        var pages = await contentItemRetrieverService.RetrieveWebPageChildrenByPath<Cafe>(
            parentPagePath,
            depth: 3,
            queryParameters: queryConfig,
            cacheConfig: new()
            {
                CacheExpiration = 30,
                ExtraCacheNameParts = [nameof(CafeRepository), nameof(GetCompanyCafes), $"count|{count}"],
            }
        );
        return pages;
    }
}
```

Thanks to the way we set up caching in the `RetrieveWebPageChildrenByPath` method, we don’t need to specify the dependency on sibling pages in the repository; it happens automatically.
### Country repository
In the KX13 site, two methods from the `CountryRepository` are used when retrieving the _Contacts_ page. They use the appropriate info providers to retrieve countries and states by name, then cache the results.
C#
**CountryRepository.cs (KX13 Dancing Goat)**
Copy
```
...
// Populated with dependency injection.
private readonly ICountryInfoProvider countryInfoProvider;
private readonly IStateInfoProvider stateInfoProvider;
private readonly RepositoryCacheHelper repositoryCacheHelper;
...

/// <summary>
/// Returns the country with the specified code name.
/// </summary>
/// <param name="countryName">The code name of the country.</param>
/// <returns>The country with the specified code name, if found; otherwise, null.</returns>
public CountryInfo GetCountry(string countryName)
{
    return repositoryCacheHelper.CacheObject(() =>
    {
        return countryInfoProvider.Get(countryName);
    }, $"{nameof(CountryRepository)}|{nameof(GetCountry)}|{countryName}");
}
...

/// <summary>
/// Returns the state with the specified code name.
/// </summary>
/// <param name="stateName">The code name of the state.</param>
/// <returns>The state with the specified code name, if found; otherwise, null.</returns>
public StateInfo GetState(string stateName)
{
    return repositoryCacheHelper.CacheObject(() =>
    {
        return stateInfoProvider.Get(stateName);
    }, $"{nameof(CountryRepository)}|{nameof(GetState)}|{stateName}");
}
...
```

With Xperience by Kentico’s new generic-typed info provider, we can make these data retrieval methods asynchronous.
For the sake of brevity, we’ll add object caching directly in the repository code instead of recreating the `RepositoryCacheHelper` class from KX13. In your real-world projects, consider using a dedicated class.
Add a new _CountryRepository.cs_ file located at _~/Models/Contacts/_ in the _DancingGoat.Web_ project, or copy and modify the repository from KX13.
C#
**CountryRepository.cs**
Copy
```
using CMS.DataEngine;
using CMS.Globalization;
using CMS.Helpers;

namespace DancingGoat.Models;

/// <summary>
/// Represents a collection of countries and states.
/// </summary>
public class CountryRepository
{
    // Use dependency injection to populate these services
    private readonly IInfoProvider<CountryInfo> countryInfoProvider;
    private readonly IInfoProvider<StateInfo> stateInfoProvider;
    private readonly IProgressiveCache progressiveCache;

    ...

    /// <summary>
    /// Returns the country with the specified code name.
    /// </summary>
    /// <param name="countryName">The code name of the country.</param>
    /// <returns>The country with the specified code name, if found; otherwise, null.</returns>
    public async Task<CountryInfo?> GetCountry(string countryName) =>
        await progressiveCache.LoadAsync(async cacheSettings =>
        {
            var result = await countryInfoProvider.GetAsync(countryName);

            cacheSettings.CacheDependency = CacheHelper.GetCacheDependency($"cms.country|byid|{result?.CountryID ?? 0}");

            return result;
        }, new CacheSettings(10, true, [nameof(CountryRepository), nameof(GetCountry), countryName]));

    /// <summary>
    /// Returns the state with the specified code name.
    /// </summary>
    /// <param name="stateName">The code name of the state.</param>
    /// <returns>The state with the specified code name, if found; otherwise, null.</returns>
    public async Task<StateInfo?> GetState(string stateName) =>
        await progressiveCache.LoadAsync(async cacheSettings =>
        {
            var result = await stateInfoProvider.GetAsync(stateName);

            cacheSettings.CacheDependency = CacheHelper.GetCacheDependency($"cms.state|byid|{result?.StateID ?? 0}");

            return result;
        }, new CacheSettings(10, true, [nameof(CountryRepository), nameof(GetState), stateName]));
}
```

### Register the repositories
Now, switch to the _ServiceCollectionExtensions.cs_ file once more and uncomment the lines for the _Cafe_ , _Contact_ , and _Country_ repositories in the `AddRepositories` method.
C#
**ServiceCollectionExtensions.cs**
Copy
```
...
private static void AddRepositories(IServiceCollection services)
{
    services.AddSingleton<CafeRepository>();
    services.AddSingleton<ContactRepository>();
    services.AddSingleton<CountryRepository>();
    //services.AddSingleton<ArticleRepository>();
}
...
 
 
 


```

## Display the page
Now that we’ve handled data retrieval, let’s make sure the page displays.
### View models
Many of the view models in the Dancing Goat KX13 project are more than just DTOs; they contain static methods to construct the view model based on Xperience objects. Let’s bring over the view models we need for displaying the _Contacts_ page.
#### Contact view model
The KX13 version of Dancing Goat customizes the generated classes for `Contact` and `Cafe` so that they both implement an interface called `IContact`. This is still possible in Xperience by Kentico, but will be left out of this example for the sake of brevity.
In the _~/Models/Contacts/_ folder of the _DancingGoat.Web_ project, create a new file for the Contact view model and copy the _CountryStateName.cs_ file from KX13.
Copy the contents of the `ContactViewModel` from the corresponding file in KX13, providing default values for the properties and changing the `GetViewModel` class to take a `Contact` parameter instead of `IContact`.
C#
**ContactViewModel.cs**
Copy
```
using DancingGoatCore;
using Microsoft.Extensions.Localization;

namespace DancingGoat.Models;

public class ContactViewModel
{
    public string Name { get; set; } = string.Empty;

    public string Phone { get; set; } = string.Empty;

    public string Email { get; set; } = string.Empty;

    public string ZIP { get; set; } = string.Empty;

    public string Street { get; set; } = string.Empty;

    public string City { get; set; } = string.Empty;

    public string Country { get; set; } = string.Empty;

    public string CountryCode { get; set; } = string.Empty;

    public string State { get; set; } = string.Empty;

    public string StateCode { get; set; } = string.Empty;

    public ContactViewModel()
    {
    }

    public ContactViewModel(Contact contact)
    {
        Name = contact.ContactName;
        Phone = contact.ContactPhone;
        Email = contact.ContactEmail;
        ZIP = contact.ContactZipCode;
        Street = contact.ContactStreet;
        City = contact.ContactCity;
    }

    public static async Task<ContactViewModel> GetViewModel(Contact contact, CountryRepository countryProvider, IStringLocalizer localizer)
    {
        var countryStateName = CountryStateName.Parse(contact.ContactCountry);
        var country = await countryProvider.GetCountry(countryStateName.CountryName);
        var state = await countryProvider.GetState(countryStateName.StateName);

        var model = new ContactViewModel(contact)
        {
            CountryCode = country?.CountryTwoLetterCode ?? string.Empty,
            Country = localizer[country?.CountryDisplayName ?? string.Empty],
            StateCode = state?.StateCode ?? string.Empty,
            State = localizer[state?.StateDisplayName ?? string.Empty]
        };

        return model;
    }
}
```

#### Cafe view model
In the _~/Models/Cafes/_ folder of _DancingGoat.Web_ , copy _CafeViewModel.cs_ files from KX13.
In the cafe view model, replace the `ContactViewModel` property with direct address properties. This change is necessary due to the absence of `IContact` discussed earlier.
Then set default values for all the properties and adjust the `GetViewModel` method to populate the new properties based on the XbyK content types.
C#
**CafeViewModel**
Copy
```
using DancingGoatCore;
using Microsoft.Extensions.Localization;

namespace DancingGoat.Models;

public class CafeViewModel
{
    public string PhotoPath { get; set; } = string.Empty;

    public string Note { get; set; } = string.Empty;

    public string Name { get; set; } = string.Empty;

    public string Phone { get; set; } = string.Empty;

    public string Email { get; set; } = string.Empty;

    public string ZIP { get; set; } = string.Empty;

    public string Street { get; set; } = string.Empty;

    public string City { get; set; } = string.Empty;

    public string Country { get; set; } = string.Empty;

    public string CountryCode { get; set; } = string.Empty;

    public string State { get; set; } = string.Empty;

    public string StateCode { get; set; } = string.Empty;

    public static async Task<CafeViewModel> GetViewModel(Cafe cafe, CountryRepository countryRepository, IStringLocalizer<SharedResources> localizer)
    {
        var countryStateName = CountryStateName.Parse(cafe.CafeCountry);
        var country = await countryRepository.GetCountry(countryStateName.CountryName);
        var state = await countryRepository.GetState(countryStateName.StateName);

        var photo = cafe.CafePhoto.FirstOrDefault() as Legacy.Attachment;

        return new CafeViewModel
        {
            PhotoPath = photo?.LegacyAttachmentAsset?.Url ?? string.Empty,
            Note = cafe.CafeAdditionalNotes,
            City = cafe.CafeCity,
            Name = cafe.CafeName,
            Phone = cafe.CafePhone,
            ZIP = cafe.CafeZipCode,
            Street = cafe.CafeStreet,
            Country = localizer[country?.CountryDisplayName ?? string.Empty],
            CountryCode = country?.CountryTwoLetterCode ?? string.Empty,
            State = localizer[state?.StateDisplayName ?? string.Empty],
            StateCode = state?.StateName ?? string.Empty
        };
    }
}
```

**Handling legacy attachments and media files**
By default, certain versions the migration tool create a field of the _Pages and reusable content_ data type for attachment fields on pages, with two allowed content types: _Legacy attachment_ and _Legacy media file_. During [code generation](/documentation/developers-and-admins/api/generate-code-files-for-system-objects), this translates to a property of the type `IEnumerable<IContentItemFieldsSource>`.
In this case, we know the field will _always hold attachments_ , so there are two potential ways to handle this:
First, you can **cast** the photo field into an attachment, as we did in this code sample.
The other is to remove _Legacy media file_ as an allowed content type in the Xperience UI and **re-generate code files**. Then, the property will be strongly-typed and will not need casting. This option is more robust, but requires extra steps, so we opted for the quicker alternative in this walkthrough.
#### Contact index view model
The contact index view model aggregates the company’s contact info with a list of cafes, and does not have any special logic. You can copy _ContactsIndexViewModel.cs_ from KX13 and add default values for its properties.
C#
**ContactsIndexViewModel.cs**
Copy
```
namespace DancingGoat.Models
{
    public class ContactsIndexViewModel
    {
        public ContactViewModel CompanyContact { get; set; } = new();

        public List<CafeViewModel> CompanyCafes { get; set; } = [];
    }
}
```

### Views
The Contacts page in the KX13 Dancing Goat is rendered by the _~/Views/Contacts/Index.cshtml_ view.
#### Contacts view
Copy this view into the same location within the _DancingGoat.Web_ project in your Xperience by Kentico solution.
Then copy _~/Views/Shared/_GoogleMaps.cshtml_ from KX13 into the corresponding folder in XbyK’s _DancingGoat.Web_ project. This partial view is rendered by the Index view, and can be left unaltered.
As for the Index view, thanks to the _ViewStart_ and _ViewImports_ files we set up in [the global code guide](/guides/upgrade-to-xbyk/upgrade-walkthrough/adjust-global-code#shared-views), only small changes are necessary. Simply adjust the allowed widgets to only include the form widget’s identifier, as we have not migrated the other widgets in this example.
cshtml
**Index.cshtml**
Copy
```
@model DancingGoat.Models.ContactsIndexViewModel

@{
    ViewBag.Title = HtmlLocalizer["Contacts"].Value;
}

@section scripts {
    <partial name="_GoogleMaps" />
}

<div class="contacts-page">
    <div class="col-md-12">
        <div class="col-md-6">
            <h2 class="contact-title">@HtmlLocalizer["Roastery"]</h2>
            <ul class="contact-info">
                <li>@Model.CompanyContact.Phone</li>
                <li>
                    <email address="@Model.CompanyContact.Email" />
                </li>
                <li>
                    <a href="javascript:void(0)" data-address="@Model.CompanyContact.City, @Model.CompanyContact.Street" class="js-scroll-to-map">
                        @Model.CompanyContact.Street @Model.CompanyContact.City,<br />
                        @Model.CompanyContact.ZIP, @Model.CompanyContact.CountryCode, @Model.CompanyContact.State<br />
                    </a>
                </li>
            </ul>
        </div>

        <div class="col-md-6">
            <h2>@HtmlLocalizer["Send us a message"]</h2>
            <div id="message-form" class="contact-us-form">
                <editable-area area-identifier="ContactUs" area-options-allowed-widgets="new[] { SystemComponentIdentifiers.FORM_WIDGET_IDENTIFIER }" />
            </div>
        </div>
    </div>

    <div class="row"><h2>@HtmlLocalizer["Our cafes"]</h2></div>
    <div class="row">
        @foreach (var cafe in @Model.CompanyCafes)
        {
            <div class="col-md-6 col-lg-3">
                <div class="cafe-tile cursor-hand js-scroll-to-map" data-address="@cafe.City, @cafe.Street">
                    <div class="cafe-tile-content">
                        <h3 class="cafe-tile-name">@cafe.Name</h3>
                        <address class="cafe-tile-address">
                            <a href="javascript:void(0)" class="cafe-tile-address-anchor">
                                @cafe.Street, @cafe.City<br />
                                @cafe.ZIP, @cafe.Country, @cafe.State
                            </a>
                        </address>
                        <p class="cafe-tile-phone">@cafe.Phone</p>
                    </div>
                </div>
            </div>
        }
    </div>

    <h2 class="map-title">@HtmlLocalizer["Drop in"]</h2>
    <div class="map js-map"></div>
</div>
```

#### Layout view
With our standard view ready, let’s double-check that we have a layout view to wrap it.
You copied the __Layout.cshtml_ file from the KX13 site in the previous part of this walkthrough, and modified it to get around some compiler errors. If it’s giving you trouble, make sure it looks something like this:
[_Layout.cshtml](/docsassets/guides/display-an-upgraded-page/_Layout.cshtml)
### Controller
The `ContactsController` class in the KX13 Dancing Goat uses the repositories and `GetViewModel` methods from earlier to gather the necessary data for the Contacts page.
Copy it to the _~/Controllers/_ folder in your Xperience by Kentico solution. Then adjust the private methods to handle the asynchronous nature of our new repository methods, and to better handle `null` values.
You’ll also need to change `RegisterPageRoute` to `RegisterWebPageRoute` in the registration attribute, and swap the old page type class name for the new content type identifier.
Thanks to the name and location of the index view, we do not need to include its path in the registration attribute. It will work automatically.
C#
**ContactsController.cs**
Copy
```
using DancingGoat.Controllers;
using DancingGoat.Models;
using DancingGoatCore;
using Kentico.Content.Web.Mvc.Routing;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Localization;

[assembly: RegisterWebPageRoute(
    Contacts.CONTENT_TYPE_NAME,
    typeof(ContactsController))]

namespace DancingGoat.Controllers;
public class ContactsController : Controller
{
    private readonly ContactRepository contactRepository;
    private readonly CountryRepository countryRepository;
    private readonly CafeRepository cafeRepository;
    private readonly IStringLocalizer<SharedResources> localizer;

    public ContactsController(ContactRepository contactRepository,
        CountryRepository countryRepository,
        CafeRepository cafeRepository,
        IStringLocalizer<SharedResources> localizer)
    {
        this.countryRepository = countryRepository;
        this.contactRepository = contactRepository;
        this.cafeRepository = cafeRepository;
        this.localizer = localizer;
    }

    public async Task<IActionResult> Index()
    {
        var model = await GetIndexViewModel();

        return View(model);
    }

    private async Task<ContactsIndexViewModel> GetIndexViewModel()
    {
        var cafes = await cafeRepository.GetCompanyCafes(ContentItemIdentifiers.CAFES, 5);

        return new ContactsIndexViewModel
        {
            CompanyContact = await GetCompanyContactModel(),
            CompanyCafes = await GetCompanyCafesModel(cafes)
        };
    }

    private async Task<ContactViewModel> GetCompanyContactModel()
    {
        var contact = await contactRepository.GetCompanyContact();
        return contact is not null
            ? await ContactViewModel.GetViewModel(contact, countryRepository, localizer)
            : new ContactViewModel();
    }

    private async Task<List<CafeViewModel>> GetCompanyCafesModel(IEnumerable<Cafe> cafes)
    {
        List<CafeViewModel> cafeModels = new();

        foreach (Cafe cafe in cafes)
        {
            cafeModels.Add(await CafeViewModel.GetViewModel(cafe, countryRepository, localizer));
        }

        return cafeModels;
    }
}
```

### Check your progress
Try running the site in debug mode. We haven’t brought over any of the home page code, so you’ll see an error when it loads, but if you visit the _/Contacts_ path, you should see something like this:
[![Contacts page with no form](/docsassets/guides/display-an-upgraded-page/PageWithNoSection.png)](/docsassets/guides/display-an-upgraded-page/PageWithNoSection.png)
If you’re seeing an error that the application cannot find the index view, you can either adjust the registration attribute to point to where you’ve put your _Index.cshtml_ file, or move the file to the location referenced by the error.
### Page Builder sections
If you look at the the Contacts page in KX13, you’ll notice that it holds a form widget that’s missing from the XbyK version.
In order to display this form widget, we’ll need to bring over the [Page Builder section](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) that the widget lives in.
From the _~/Components/Sections/_ folder of the KX13 Dancing Goat project, copy the **__DancingGoat_SingleColumnSection.cshtml_** and **_ThemeSectionProperties.cs_** files into a new _~/Components/Sections/_ folder in the _DancingGoat.Web_ project of your XbyK solution. We don’t need to change either of these files.
C#
**_DancingGoat_SingleColumnSection.cshtml**
Copy
```
@using DancingGoat.Sections

@model Kentico.PageBuilder.Web.Mvc.ComponentViewModel<ThemeSectionProperties>

<div class="row @Model.Properties.Theme">
    <div class="col-md-12">
        <widget-zone />
    </div>
</div>
```

C#
**ThemeSectionProperties.cs**
Copy
```
using Kentico.Forms.Web.Mvc;
using Kentico.PageBuilder.Web.Mvc;

namespace DancingGoat.Sections
{
    /// <summary>
    /// Section properties to define the theme.
    /// </summary>
    public class ThemeSectionProperties : ISectionProperties
    {
        /// <summary>
        /// Theme of the section.
        /// </summary>
        [EditingComponent(DropDownComponent.IDENTIFIER, Label = "Color scheme", Order = 1)]
        [EditingComponentProperty(nameof(DropDownProperties.DataSource), ";None\r\nsection-white;Flat white\r\nsection-cappuccino;Cappuccino")]
        public string Theme { get; set; }
    }
}
```

Now, in the _ComponentIdentifiers.cs_ file that you [copied earlier](/guides/upgrade-to-xbyk/upgrade-walkthrough/adjust-global-code#identifiers), un-comment the `SINGLE_COLUMN_SECTION` constant.
Then copy _PageBuilderComponentRegister.cs_ from the _~/Components/_ folder in KX13 into the corresponding folder in XbyK, and comment out everything except for the `RegisterSection` attribute that registers the single column section.
C#
**ComponentIdentifiers.cs**
Copy
```
namespace DancingGoat
{
    /// <summary>
    /// Encapsulated identifiers of components.
    /// </summary>
    public static class ComponentIdentifiers
    {
        // Sections
        public const string SINGLE_COLUMN_SECTION = "DancingGoat.SingleColumnSection";
    }
}
```

C#
**PageBuilderComponentRegister.cs**
Copy
```
using DancingGoat;
using DancingGoat.Sections;
using Kentico.PageBuilder.Web.Mvc;

// Sections
[assembly: RegisterSection(ComponentIdentifiers.SINGLE_COLUMN_SECTION, "Single column", typeof(ThemeSectionProperties), "~/Components/Sections/_DancingGoat_SingleColumnSection.cshtml", Description = "Single-column section with one zone.", IconClass = "icon-square")]
```

### Page builder registration
Finally, for our page builder section to work, we need to enable Page Builder for the _contacts_ content type.
Use the `UserPageBuilder` extension within the `IFeaturesBuilder` action of the `UseKentico` service collection extension method.
C#
**Program.cs**
Copy
```
using DancingGoat;
using DancingGoatCore;
using Kentico.Content.Web.Mvc.Routing;
using Kentico.PageBuilder.Web.Mvc;
using Kentico.Web.Mvc;

...
builder.Services.AddKentico(features =>
{
    features.UsePageBuilder(new PageBuilderOptions {
        ContentTypeNames = new[] {
            Contacts.CONTENT_TYPE_NAME,
        }
    });

    ...
});
...
```

### See the result
Now restart the site. Navigating to the _/Contacts_ page, you should see the “Send us a message” form now correctly displayed.
[![Contacts page with no form](/docsassets/guides/display-an-upgraded-page/PageWithSection.png)](/docsassets/guides/display-an-upgraded-page/PageWithSection.png)
### Walkthrough progress
You’re almost done. See [what else](/guides/upgrade-to-xbyk/upgrade-walkthrough/next-steps) can you learn about Xperience by Kentico.
![]()
[]()[]()
