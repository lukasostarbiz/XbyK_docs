# Cache dependencies
  * [ Copy page link ](documentation/developers-and-admins/development/caching/cache-dependencies#) | [Get HelpService ID](documentation/developers-and-admins/development/caching/cache-dependencies#)
Core MVC 5


[✖](documentation/developers-and-admins/development/caching/cache-dependencies# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/caching/cache-dependencies#)
Cache dependencies inform the system whenever the source data of a cached entity changes and prompt it to revoke the corresponding cache entry.
To create dependencies between cached data and its source, the system uses **dummy cache keys**. Dummy keys are cache items without any data that target objects or groups of objects. Each time an object is modified, the system touches all dummy keys that correspond to the object, which clears all cache items targeting at least one of the touched keys.
For example, assume an _Articles_ page with the _/Articles_ path in the content tree of a website channel _News_. On this page, there are short snippets of various articles, placed as the page’s children (_/Articles/Best cities to live in_ , _/Articles/New theme park in Georgia_ , etc.). As is good practice, all article snippets displayed on the page are cached.
Whenever any of the child pages change, the cache needs to be notified in order to revoke the cached article snippets; otherwise, the _Articles_ page continues displaying outdated content. To notify the cache, we introduce a _dummy cache key dependency_ on the key `webpageitem|bychannel|News|childrenofpath|/Articles` when [caching the data](documentation/developers-and-admins/development/caching/data-caching). With this dependency, any change to child pages on the [tree path](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#filter-pages-based-on-content-tree-structure) _/Articles_ under the channel _News_ removes the corresponding cached article snippet from the cache, prompting the system to load fresh data from the database on a subsequent request.
The system provides the `CacheDependencyBuilder` API that allows you to generate cache dependency keys from retrieved data ([pages](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content), [content items](documentation/developers-and-admins/development/content-retrieval/retrieve-content-items), [media files](documentation/business-users/media-libraries), etc.) and use them when [caching data](documentation/developers-and-admins/development/caching/data-caching).
## CacheDependencyBuilder API
`CacheDependencyBuilder` provides a fluent API for building cache dependencies from retrieved data. The API works with collections of system objects such as pages and content items retrieved via [content item query](documentation/developers-and-admins/api/content-item-api/content-item-query-api) or info objects retrieved via [database APIs](documentation/developers-and-admins/api/database-table-api) and returns a `CMSCacheDependency` object that plugs into [IProgressiveCache](documentation/developers-and-admins/development/caching/data-caching).
### Create the builder instance
Use `ICacheDependencyBuilderFactory` to create instances of the `CacheDependencyBuilder` class.
It is important to create a new `CacheDependencyBuilder` instance for each distinct cache dependency you intend to build. The builder object is stateful; if you reuse the same instance for multiple, unrelated dependency creation processes, it will retain and accumulate the dependencies from all previous calls. This results in a final `CMSCacheDependency` object that includes dependencies from all data processed by that single builder instance, potentially leading to incorrect or overly broad cache invalidation. Therefore, always request a fresh builder instance from the `ICacheDependencyBuilderFactory` for every new dependency set you need to define.
C#
**Set up an ICacheDependencyBuilderFactory instance**
Copy
```
using CMS.Helpers;

public class MyClass
{
    private readonly ICacheDependencyBuilderFactory cacheDependencyBuilderFactory;

    public MyClass(ICacheDependencyBuilderFactory cacheDependencyBuilderFactory)
    {
        // Injects a factory object used to
        // create 'CacheDependencyBuilder' instances.
        this.cacheDependencyBuilderFactory = cacheDependencyBuilderFactory;
    }
}
 


```

## Build cache dependencies
The API allows you to construct cache dependencies via the following extension methods:
Extension method |  Namespace |  Description  
---|---|---  
ForContentItems |  CMS.ContentEngine