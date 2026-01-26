---
source: https://docs.kentico.com/documentation/developers-and-admins/development/caching/cache-dependencies
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Caching](/documentation/developers-and-admins/development/caching)
  * Cache dependencies 


# Cache dependencies
Cache dependencies inform the system whenever the source data of a cached entity changes and prompt it to revoke the corresponding cache entry.
To create dependencies between cached data and its source, the system uses **dummy cache keys**. Dummy keys are cache items without any data that target objects or groups of objects. Each time an object is modified, the system touches all dummy keys that correspond to the object, which clears all cache items targeting at least one of the touched keys.
For example, assume an _Articles_ page with the _/Articles_ path in the content tree of a website channel _News_. On this page, there are short snippets of various articles, placed as the page’s children (_/Articles/Best cities to live in_ , _/Articles/New theme park in Georgia_ , etc.). As is good practice, all article snippets displayed on the page are cached.
Whenever any of the child pages change, the cache needs to be notified in order to revoke the cached article snippets; otherwise, the _Articles_ page continues displaying outdated content. To notify the cache, we introduce a _dummy cache key dependency_ on the key `webpageitem|bychannel|News|childrenofpath|/Articles` when [caching the data](/documentation/developers-and-admins/development/caching/data-caching). With this dependency, any change to child pages on the [tree path](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#filter-pages-based-on-content-tree-structure) _/Articles_ under the channel _News_ removes the corresponding cached article snippet from the cache, prompting the system to load fresh data from the database on a subsequent request.
The system provides the `CacheDependencyBuilder` API that allows you to generate cache dependency keys from retrieved data ([pages](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content), [content items](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items), [media files](/documentation/business-users/media-libraries), etc.) and use them when [caching data](/documentation/developers-and-admins/development/caching/data-caching).
## CacheDependencyBuilder API
`CacheDependencyBuilder` provides a fluent API for building cache dependencies from retrieved data. The API works with collections of system objects such as pages and content items retrieved via [content item query](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) or info objects retrieved via [database APIs](/documentation/developers-and-admins/api/database-table-api) and returns a `CMSCacheDependency` object that plugs into [IProgressiveCache](/documentation/developers-and-admins/development/caching/data-caching).
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
* * *
Kentico.Content.Web.Mvc – Web application-specific extensions |  Creates cache dependencies on [content items](/documentation/business-users/content-hub/content-items). See [Content items](#content-items).  
ForWebPageItems |  CMS.Websites
* * *
Kentico.Content.Web.Mvc – Web application-specific extensions |  Creates cache dependencies on [pages](/documentation/developers-and-admins/development/content-types). See [Pages](#pages).  
ForReusableFieldSchemas |  CMS.ContentEngine |  Creates cache dependencies on [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas). See [Reusable field schemas](#reusable-field-schemas).  
ForInfoObjects |  CMS.DataEngine |  Creates cache dependencies on [info object instances](/documentation/developers-and-admins/api/database-table-api). See [Info objects](#info-objects).  
ForSettingKeys |  CMS.DataEngine |  Creates cache dependencies on [setting keys](/documentation/developers-and-admins/configuration/settings). See [Settings keys](#settings-keys).  
ForMediaFiles |  CMS.MediaLibrary |  Creates cache dependencies on [media files](/documentation/business-users/media-libraries). See [Media files](#media-files).  
AddDependency |  CMS.Helpers |  Not an extension method, available on the builder instance by default. Allows you to add a manually specified dependency to the set of constructed keys; for example, on a [collection of linked items](#additional-dependencies). See [Reference - Cache dependency keys](/documentation/developers-and-admins/development/caching/cache-dependencies-reference) for an overview of supported cache dependencies and their required format.  
The final cache dependencies are created by calling `Build` on the dependency builder instance after specifying all requirements. The method returns a `CMSCacheDependency` instance that can be passed to `IProgressiveCache` when [caching data](/documentation/developers-and-admins/development/caching/data-caching).
```
using CMS.Helpers;

// Creates a new instance of 'CacheDependencyBuilder'
CacheDependencyBuilder cacheDependencyBuilder =
    cacheDependencyBuilderFactory.Create();

CMSCacheDependency dependency = cacheDependencyBuilder
    // Specify all dependencies and call Build(), which
    // finalizes the dependency object for use in caching APIs
    .Build();
 


```

### Content items
The `ForContentItems` method by default allows you to create dependencies per individual **published** content items, across all items of a particular content type, or across all content items in the system. To capture not just changes to published items, but to all [workflow state](/documentation/developers-and-admins/configuration/workflows) transitions, set the optional `inAllWorkflowStates` parameter of `ForContentItems` to `true`.
#### All items
The following API creates a dependency on all content items in the system; the corresponding cached entry is removed every time any content item in the system is modified.
C#
**Create a dependency on all content items**
Copy
```
using CMS.ContentEngine;

// Creates a new instance of 'CacheDependencyBuilder'
CacheDependencyBuilder cacheDependencyBuilder =
  cacheDependencyBuilderFactory.Create();

// Creates a cache dependency on all content items in the system
CMSCacheDependency dependency = cacheDependencyBuilder

  // By default, this targets only published content items.
  // To track changes across all workflow states,
  // use: .ForContentItems(inAllWorkflowStates: true)
  .ForContentItems()

    // Add a dependency key that targets all content items.
    // Any change to any published content item will invalidate
    // caches using this dependency. (Or any content item in any
    // workflow state if 'inAllWorkflowStates' was set to true).
    //
    // Example key (published): "contentitem|all"
    // Example key (all states): "contentitem|allstates|all"
    .All()

    .Builder()
  .Build();
```

#### By content type
Use the `ByContentType<T>` API to target all items of a specific [reusable content type](/documentation/developers-and-admins/development/content-types). Note that to target a content type, the example uses its corresponding [generated class](/documentation/developers-and-admins/api/generate-code-files-for-system-objects). The API offers additional overloads of the `ByContentType` method that target the content type by its code name (with identical usage).
When targeting language variants, use the language code name as specified in the [Languages](/documentation/developers-and-admins/configuration/languages) application. To create a dependency on the language from the current request, use the `ByContentTypeWithLanguageContext<T>` extension from the `Kentico.Content.Web.Mvc` namespace.
C#
**Create a dependency on reusable content items**
Copy
```
using CMS.ContentEngine;
using Kentico.Content.Web.Mvc;

// Creates a new instance of 'CacheDependencyBuilder'
CacheDependencyBuilder cacheDependencyBuilder =
  cacheDependencyBuilderFactory.Create();

CMSCacheDependency dependency = cacheDependencyBuilder
  .ForContentItems()

    // Dependency that targets *all published language variants*
    // of the 'Coffee' content type.
    // Invalidates the cache if *any* published 'Coffee' item is
    // modified, created, or deleted.
    // Example key: "contentitem|bycontenttype|<CoffeeCodeName>"
    .ByContentType<Coffee>()

    // Dependency that targets *only the specified language variant*
    // ('spanish') of the 'Coffee' content type.
    // Invalidates the cache if the 'spanish' language variant of *any* published
    // 'Coffee' item is modified, created, or deleted.
    //
    // Example key (using lang: 'spanish'):
    // "contentitem|bycontenttype|<CoffeeCodeName>|spanish"
    .ByContentType<Coffee>("spanish")

    // Add dependency key: Targets the 'Coffee' content type,
    // but *only* for the language from the current request context.
    // Invalidates the cache if the language variant (matching the
    // request's language) of *any* published 'Coffee' item changes.
    //
    // Example key (if request lang: 'spanish'):
    // "contentitem|bycontenttype|<CoffeeCodeName>|spanish"
    .ByContentTypeWithLanguageContext<Coffee>()

    .Builder()
  .Build();
```

#### By ID/GUID/CodeName
The following API creates dependencies on collections of specific content items. Only by modifying one of the provided content items is the corresponding cache entry removed. The `ByGuid`, `ById`, `ByCodeName` methods can take either GUIDs, IDs, and CodeNames or collections of [generated object classes](/documentation/developers-and-admins/api/generate-code-files-for-system-objects).
The `WithLanguageContext` variants of these methods create dependencies on the content language taken from the current request. These extensions can be found in the `Kentico.Content.Web.Mvc` namespace.
Treat the code example only as a preview of the available API. In real implementations, choose only one of these identifiers to create the cache dependency.
C#
**Create a dependency on items using ID/GUID/CodeName**
Copy
```
using CMS.ContentEngine;
using Kentico.Content.Web.Mvc;

// Assume a collection of content items on which to create the dependency
IEnumerable<Coffee> contentItems = GetContentItems();

string languageName = "spanish";

// Creates a new instance of 'CacheDependencyBuilder'
CacheDependencyBuilder cacheDependencyBuilder =
  cacheDependencyBuilderFactory.Create();

CMSCacheDependency dependency = cacheDependencyBuilder
  .ForContentItems()

    // Targets each content item in the provided collection
    // using their IDs.
    // Cache invalidates if *any* of these specific items
    // in *any* language change.
    //
    // Example key:
    // "contentitem|byid|<itemID>"
    .ById(contentItems)

    // Targets *only the specified language variant* ('spanish')
    // for each item in the provided collection using item ID.
    // Cache invalidates if *any* of these specific
    // items in the *spanish* language variant change.
    //
    // Example key (using lang: 'spanish'):
    // "contentitem|byid|<itemID>|spanish"
    .ById(contentItems, languageName)

    // Targets each item in the provided collection using their ID
    // and the language from the *current request context*.
    //
    // Example key (if request lang: 'spanish'):
    // "contentitem|byid|<itemID>|spanish"
    .ByIdWithLanguageContext(contentItems)

    // Targets each content item in the provided collection using
    // their GUIDs. Cache invalidates if *any* of these specific
    // items in *any* language change.
    //
    // Example key:
    // "contentitem|byguid|<itemGUID>"
    .ByGuid(contentItems)

    // Targets *only the specified language variant* ('spanish')
    // for each item in the provided collection using item GUID.
    // Cache invalidates if *any* of these items in the *spanish*
    // language variant change.
    //
    // Example key (using lang: 'spanish'):
    // "contentitem|byguid|<itemGUID>|spanish"
    .ByGuid(contentItems, languageName)

    // Targets each item in the provided collection using their GUID
    // and the language from the *current request context*.
    //
    // Example key (if request lang: 'spanish'):
    // "contentitem|byguid|<itemGUID>|spanish"
    .ByGuidWithLanguageContext(contentItems)

    // Targets each content item in the provided collection
    // using their code names. Cache invalidates if *any* of
    // these items in *any* language change.
    //
    // Example key:
    // "contentitem|byname|<itemCodeName>"
    .ByCodeName(contentItems)

    // Targets *only the specified language variant* ('spanish') for each item in the
    // provided collection using item code names. Cache invalidates if *any* of these
    // items in the *spanish* language variant change.
    //
    // Example key (using lang: 'spanish'):
    // "contentitem|byname|<itemCodeName>|spanish"
    .ByCodeName(contentItems, "spanish")

    // Targets each item in the provided collection using their code names
    // and the language from the *current request context*.
    //
    // Example key (if request lang: 'spanish'):
    // "contentitem|byname|<itemCodeName>|spanish"
    .ByCodeNameWithLanguageContext(contentItems)

    .Builder()
  .Build();
```

#### By reusable field schema usage 
Use the `ByReusableFieldSchema` method to create cache dependencies targeting all content items with specific [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) assigned. Provide the schema name (or a collection of schema names). This method also supports an optional language parameter, which can scope the selection to only specific [language variants](/documentation/developers-and-admins/configuration/languages) of items using the schema.
For web applications, the `ByReusableFieldSchemaWithLanguageContext` extension allows you to wire contextual dependencies based on the language from the current request. The extension is located in the `Kentico.Content.Web.Mvc` namespace.
C#
**Create a dependency on content items by reusable field schemas**
Copy
```
using CMS.ContentEngine;
using Kentico.Content.Web.Mvc;

// Assume the following contextual values
string schemaName = "My.ReusableSchema";
string languageName = "spanish";

// Creates a new instance of 'CacheDependencyBuilder'
CacheDependencyBuilder cacheDependencyBuilder =
  cacheDependencyBuilderFactory.Create();

CMSCacheDependency dependency = cacheDependencyBuilder
  .ForContentItems()

    // Targets *all published language variants* of content items that use
    // the specified reusable field schema.
    // Cache invalidates if *any* published content item using this schema
    // is modified, created, or deleted.
    //
    // Example key: "contentitem|byschema|<schemaName>"
    .ByReusableFieldSchema(schemaName)

    // Targets *only the specified language variant* ('spanish') of
    // content items using the specified schema. Cache invalidates if
    // the 'spanish' variant of *any* published item using this schema changes.
    //
    // Example key (using lang: 'spanish'):
    // "contentitem|byschema|<schemaName>|spanish"
    .ByReusableFieldSchema(schemaName, languageName)

    // Targets items using the specified schema, but *only* for the language
    // determined from the current request context.
    // Cache invalidates if the language variant (matching the request's language)
    // of *any* published item using the specified schema changes.
    //
    // Example key (if request lang: 'spanish'):
    // "contentitem|byschema|<schemaName>|spanish"
    .ByReusableFieldSchemaWithLanguageContext(schemaName)

    .Builder()
  .Build();
```

### Pages
The `ForWebPageItems` method allows you to create dependencies per individual **published** [web page items](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content), all pages of a specific content type, or across all pages in the system. To capture not just changes to published pages, but to all [workflow state](/documentation/developers-and-admins/configuration/workflows) transitions, set the optional `inAllWorkflowStates` parameter of `ForWebPageItems` to `true`.
#### All pages
The following API creates a dependency on all pages in the system. Corresponding cache items associated with this dependency are evicted every time any page in the system is modified.
The `All` method optionally allows a `channelName` parameter that limits the created dependency to all pages belonging to the specified [website channel](/documentation/developers-and-admins/configuration/website-channel-management). For web applications, the `AllWithChannelContext` extension allows you to wire contextual dependencies based on the channel from the current request. The extension is located in the `Kentico.Content.Web.Mvc` namespace.
C#
**Create a dependency on all pages**
Copy
```
using CMS.Websites;
using Kentico.Content.Web.Mvc;

string channelName = "MyChannel"

// Creates a new instance of 'CacheDependencyBuilder'
CacheDependencyBuilder cacheDependencyBuilder =
  cacheDependencyBuilderFactory.Create();

// Creates a cache dependency on all pages in the system
CMSCacheDependency dependency = cacheDependencyBuilder

  // Specify that the dependencies are related to web page items.
  // By default, this tracks changes only to the published pages.
  // To track changes across all workflow states, use:
  // .ForWebPageItems(inAllWorkflowStates: true)
  .ForWebPageItems()

    // Targets *all* web page items across *all* channels.
    // Cache invalidates if *any* matching page in *any* channel
    //  is modified, created, or deleted.
    // Example key (published): "webpageitem|all"
    // Example key (all states): "webpageitem|allstates|all"
    .All()

    // Targets *all* web page items but *only* within the specified 'channelName'.
    // Cache invalidates if *any* matching page *within the specified channel*
    // is modified, created, or deleted.
    // Example key (published): "webpageitem|bychannel|<channelName>|all"
    // Example key (all states): "webpageitem|allstates|bychannel|<channelName>|all"
    .All(channelName)

    // Targets *all* Web Page Items but scopes the dependency *only*
    // to the website channel determined from the *current request context*.
    // Cache invalidates if *any* matching page *within the current request's
    // channel* is modified, created, or deleted.
    //
    // Example key (published, if request is for 'MyChannel'):
    // "webpageitem|bychannel|mychannel|all"
    // Example key (all states, if request is for 'MyChannel'):
    // "webpageitem|bychannel|mychannel|allstates|all"
    .AllWithChannelContext();

    .Builder()
  .Build();
```

#### By content type
The following API targets all pages of a specific [content type](/documentation/developers-and-admins/development/content-types). Note that to target the content type, the example uses its [generated class](/documentation/developers-and-admins/api/generate-code-files-for-system-objects). The API offers additional overloads of the `ByContentType<T>` method that target the content type by its code name (with identical usage).
When using `ByContentType`, you **must** scope the dependency to a specific [website channel](/documentation/developers-and-admins/configuration/website-channel-management). When targeting language variants, use the language code name as specified in the [Languages](/documentation/developers-and-admins/configuration/languages) application.
In web applications, you can use extension methods capable of wiring dependencies based on the current request context. See the examples below. The extensions are located in the `Kentico.Content.Web.Mvc` namespace.
C#
**Create a dependency on pages of specific type**
Copy
```
using CMS.Websites;
using Kentico.Content.Web.Mvc;

string channelName = "mychannel";
string languageName = "spanish";

// Creates a new instance of 'CacheDependencyBuilder'
CacheDependencyBuilder cacheDependencyBuilder =
  cacheDependencyBuilderFactory.Create();

CMSCacheDependency dependency = cacheDependencyBuilder
  .ForWebPageItems()

    // Targets *all published language variants* of 'CoffeeDetailPage'
    // pages within the specified 'channelName'.
    // Invalidates the cache if *any* page of the 'CoffeeDetailPage'
    // content type within 'channelName' changes.
    //
    // Example generated key:
    // "webpageitem|bychannel|<channelName>|bycontenttype|<CoffeeDetailPageCodeName>"
    .ByContentType<CoffeeDetailPage>(channelName)

    // Targets *only the specified language variant* of pages with
    // the 'CoffeeDetailPage' content type within the specified 'channelName'.
    // Invalidates the cache if the 'languageName' variant of *any* page based
    // on the 'CoffeeDetailPage' content type within 'channelName' changes.
    //
    // Example generated dependency:
    // "webpageitem|bychannel|<channelName>|bycontenttype|<CoffeeDetailPageCondeName>|<languageName>"
    .ByContentType<CoffeeDetailPage>(channelName, languageName)

    // --- Context-Aware Dependencies (Require Kentico.Content.Web.Mvc) ---

    // Targets 'CoffeeDetailPage' pages, using the language determined
    // from the *current request context*.
    // Invalidates the cache if the language variant (matching the request's language)
    // of *any* 'CoffeeDetailPage' page within the specified channel changes.
    //
    // Example generated key (if lang: 'spanish'):
    // "webpageitem|bychannel|mychannel|bycontenttype|<CoffeeDetailPageCondeName>|spanish"
    .ByContentTypeWithLanguageContext<CoffeeDetailPage>(channelName)

    // Targets 'CoffeeDetailPage' pages, using the channel determined from
    // the *current request context*. Includes *all languages* within that channel.
    // Invalidates the cache if *any* language variant of *any* 'CoffeeDetailPage'
    // page within the request's channel changes.
    //
    // Example generated key (if channel: 'mychannel'):
    // "webpageitem|bychannel|mychannel|bycontenttype|<CoffeeDetailPageCondeName>"
    .ByContentTypeWithChannelContext<CoffeeDetailPage>()

    // Targets 'CoffeeDetailPage' pages, using *both* the channel
    // *and* the language determined from the *current request context*.
    // Cache invalidates if the language variant (matching the request's language)
    // of *any* 'CoffeeDetailPage' page within the request's channel changes.
    //
    // Example generated key (if channel: 'mychannel', lang: 'spanish'):
    // "webpageitem|bychannel|mychannel|bycontenttype|<CoffeeDetailPageCondeName>|spanish"
    .ByContentTypeWithContext<CoffeeDetailPage>()

    .Builder()
  .Build();
```

#### By ID/GUID/CodeName
The following API methods create cache dependencies that target specific collections of pages, identified by their GUID, ID, or CodeName. Corresponding cache entries are invalidated only when one of the specifically referenced pages is modified.
The `ByGuid`, `ById`, and `ByCodeName` methods accept either collections of the respective identifiers (GUIDs, IDs, CodeNames) or collections of the corresponding [generated object classes](/documentation/developers-and-admins/api/generate-code-files-for-system-objects). For web application scenarios, `WithLanguageContext` variants of these methods are available in the `Kentico.Content.Web.Mvc` namespace; they generate dependencies automatically scoped to the language identified from the current request.
Treat the subsequent code example only as a preview of the available API methods. In real-world implementations, you should select only one identifier type (GUID, ID, or CodeName) when defining the cache dependency.
C#
**Create a dependency on pages using ID/GUID/CodeName**
Copy
```
using CMS.Websites;
using Kentico.Content.Web.Mvc;

// Assume a collection of pages on which to create the dependency
IEnumerable<CoffeeDetailPage> webPages = GetDependencies();

string languageName = "spanish";

// Creates a new instance of 'CacheDependencyBuilder'
CacheDependencyBuilder cacheDependencyBuilder =
    cacheDependencyBuilderFactory.Create();

// NOTE: The example below chains dependencies based on multiple identifiers
// for demonstration. In a real implementation, you would typically choose
// *one* identifier type (GUID, ID, or CodeName) and its relevant
// language/context variations for a specific cache item.
CMSCacheDependency dependency = cacheDependencyBuilder
    .ForWebPageItems()

        // --- Dependencies by ID ---

        // Targets each web page item in the provided collection
        // using their IDs.
        // Invalidates the cache if *any* of these specific items
        // in *any* language change.
        // Example key: "webpageitem|byid|<itemID>"
        .ById(webPages)


        // Targets *only the specified language variant* for each
        // page using its ID.
        // Invalidates the cache if *any* of these specific items in
        // 'languageName' language change.
        // Example key: "webpageitem|byid|<itemID>|<languageCodeName>"
        .ById(webPages, languageName)


        // Targets each web page item usign its ID and the language
        // from the *current request context*.
        // Invalidates the cache if *any* of these specific
        // items in the request's language change.
        //
        // Example key (if request lang: 'spanish'):
        // "webpageitem|byid|<itemID>|spanish"
        .ByIdWithLanguageContext(webPages)

        // --- Dependencies by GUID ---

        // Targets each web page item in the 'webPages' collection
        // using their GUID.
        // Cache invalidates if *any* of these specific pages change.
        // Example key: "webpageitem|byguid|<itemGUID>"
        .ByGuid(webPages)

        // Targets *only the specified language variant* for each
        // web page item usign its GUID.
        // Cache invalidates if the specified language variant of
        // *any* specified page changes.
        // Example key: "webpageitem|byguid|<itemGUID>|<languageCodeName>"
        .ByGuid(webPages, languageName)

        // Targets each web page item using its GUID and
        // the language from the *current request context*.
        // Invalidates the cache if the language variant (matching request)
        // of *any* specified page changes.
        //
        // Example key (if request lang: 'spanish'):
        // "webpageitem|byguid|<itemGUID>|spanish"
        .ByGuidWithLanguageContext(webPages)

        // --- Dependencies by code name ---

        // Targets each web page item in the 'webPages'
        // collection using its code name.
        // Invalidates the cache if *any* of these specific pages change.
        // Example key: "webpageitem|byname|<itemCodeName>"
        .ByCodeName(webPages)

        // Targets *only the specified language variant* for
        // each item using its code name.
        // Invalidates the cache if *any* of these specific pages change.
        // Example key: "webpageitem|byname|<itemCodeName>|<languageCodeName>"
        .ByCodeName(webPages, languageName)

        // Targets each item using its code name and the language
        // from the *current request context*.
        // Invalidates the cache if *any* of these specific pages change.
        //
        // Example key (if request lang: 'spanish'):
        //"webpageitem|byname|<itemCodeName>|spanish"
        .ByCodeNameWithLanguageContext(webPages)

        .Builder()
    .Build();
```

#### By content tree path
The following API uses the position in the [channel content tree](/documentation/business-users/website-content) to create cache dependencies. Whenever pages on the specified path are modified, the corresponding cache entry is removed. The examples include the resulting cache dependency keys, which can also be constructed directly - see [Reference - Cache dependency keys](/documentation/developers-and-admins/development/caching/cache-dependencies-reference).
C#
**Create a dependency on content tree path**
Copy
```
using CMS.Websites;
using Kentico.Content.Web.Mvc;

// Assume a page on which to create the dependency
CoffeeDetailPage article = GetDependencies();

// Creates a path object targeting only the specific page
// identified by its content tree path. Any change to this
// specific page will invalidate caches using this dependency.
CacheDependencyPath singlePagePath =
  CacheDependencyPath.Single(article.SystemFields.WebPageItemTreePath);

// Creates a path object targeting all children on all levels
// under the specified page's content tree path. Any change to
// any child page under 'article' will invalidate caches using this dependency.
CacheDependencyPath childPagesPath =
  CacheDependencyPath.Children(article.SystemFields.WebPageItemTreePath);

// Defines an explicit channel name. Used when you want to tie the dependency
// to a specific channel, regardless of the current request's context.
string channelName = "MyChannel";

// Creates a new instance of 'CacheDependencyBuilder'
CacheDependencyBuilder cacheDependencyBuilder =
  cacheDependencyBuilderFactory.Create();

CMSCacheDependency dependency = cacheDependencyBuilder
  .ForWebPageItems()

    // Dependency on a specific page path within a specific channel.
    // Example key:
    // "webpageitem|bychannel|<channelName>|bypath|<treePath>"
    .ByPath(singlePagePath, channelName)

    // Dependency on all children under the path within a specific channel.
    // Example key:
    // "webpageitem|bychannel|<channelName>|childrenofpath|<treePath>"
    .ByPath(childPagesPath, channelName)

    // Dependency on a specific page path, specific channel,
    // and the language from the current request.
    //
    // Example generated key (if request lang: 'spanish'):
    // "webpageitem|bychannel|<channelName>|bypath|<treePath>|spanish"
    .ByPathWithLanguageContext(singlePagePath, channelName)

    // Dependency on a specific page path, automatically using
    // the website channel associated with the current request.
    //
    // Example generated key (if request channel: 'MyChannel'):
    // "webpageitem|bychannel|mychannel|bypath|<treePath>"
    .ByPathWithChannelContext(singlePagePath)

    // Dependency on a specific page path, automatically using both
    // the channel and language from the current request.
    //
    // Example generated key (if request channel: 'MyChannel', lang: 'spanish'):
    // "webpageitem|bychannel|mychannel|bypath|<treePath>|spanish"
    .ByPathWithContext(singlePagePath)

    .Builder()
  .Build();
```

#### By reusable field schema usage 
To create dependencies targeting all pages using specific [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas), use the `ByReusableFieldSchema` method. Provide the schema name (or a collection of schema names) and the required `channelName`. This method also supports an optional `languageName` parameter to target only specific language variants.
For web application scenarios, `WithContext` variants of these methods are available in the `Kentico.Content.Web.Mvc` namespace. They generate dependencies automatically scoped to the language or website channel identified from the current request.
C#
**Create a dependency on pages via reusable field schemas**
Copy
```
using CMS.Websites;
using Kentico.Content.Web.Mvc;

// Assumed values
string schemaName = "My.Schema";
string channelName = "MyChannel";
string languageName = "spanish";

// Creates a new instance of 'CacheDependencyBuilder'
CacheDependencyBuilder cacheDependencyBuilder =
  cacheDependencyBuilderFactory.Create();

CMSCacheDependency dependency = cacheDependencyBuilder
  .ForWebPageItems()

    // Targets *all published language variants* of pages within the
    // specified 'channelName' that use the specified 'schemaName'.
    // Cache invalidates if *any* matching page (using the schema,
    // in the channel) is modified, created, or deleted.
    //
    // Example generated key:
    // "webpageitem|bychannel|<channelName>|byschema|<schemaName>"
    .ByReusableFieldSchema(schemaName, channelName)

    // Targets *only the specified language variant* ('languageName')
    // of pages within the specified 'channelName' that use the specified 'schemaName'.
    // Cache invalidates if the 'languageName' variant of *any* matching page
    // (using the schema, in the channel) changes.
    //
    // Example generated key:
    // "webpageitem|bychannel|<channelName>|byschema|<schemaName>|<languageName>"
    .ByReusableFieldSchema(schemaName, channelName, languageName)

    // --- Context-aware extensions (under Kentico.Content.Web.Mvc) ---

    // Targets pages using the specified 'schemaName' within the explicit 'channelName',
    // scoped automatically to the language variant from the current request context.
    // Cache invalidates if the request context's language variant of *any* matching page
    // (using the schema, in the specified channel) changes.
    //
    // Example generated key (if request language is 'spanish'):
    // "webpageitem|bychannel|<channelName>|byschema|<schemaName>|spanish"
    .ByReusableFieldSchemaWithLanguageContext(schemaName, channelName)

    // Targets pages using the specified 'schemaName' within the channel derived
    // from the current request context, scoped explicitly to the specified 'languageName'.
    // Cache invalidates if the specified 'languageName' variant of *any* matching page
    // (using the schema, in the request's channel) changes.
    //
    // Example generated key (if request channel is 'MyChannel', lang: 'spanish'):
    // "webpageitem|bychannel|mychannel|byschema|<schemaName>|spanish"
    .ByReusableFieldSchemaWithChannelContext(schemaName, languageName)

    // Targets pages using the specified 'schemaName', automatically scoped to *both*
    // the channel *and* the language derived from the current request context.
    // Cache invalidates if the request context's language variant of *any* matching page
    // (using the schema, in the request's channel) changes.
    //
    // Example generated key (if request is for 'MyChannel', lang: 'spanish'):
    // "webpageitem|bychannel|mychannel|byschema|<schemaName>|spanish"
    .ByReusableFieldSchemaWithContext(schemaName)

    .Builder()
  .Build();
```

### Reusable field schemas
The `ForReusableFieldSchemas` method allows you to create dependencies directly on [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas). Any change to the specified schema definition (e.g., adding or removing fields, changing assigned content types) will touch the corresponding dummy key and invalidate the associated cache item.
C#
**Create a dependency on reusable field schemas**
Copy
```
// Assume schema names
string schemaName = "My.SchemaA";
IEnumerable<string> schemaNames = new [] { "My.SchemaB", "My.SchemaC" };

// Creates a new instance of 'CacheDependencyBuilder'
CacheDependencyBuilder cacheDependencyBuilder =
  cacheDependencyBuilderFactory.Create();

// Creates a cache dependency on the specified reusable field schemas by name
CMSCacheDependency dependency = cacheDependencyBuilder
  .ForReusableFieldSchemas()
    // Creates a dependency using the name of the schema
    .ByName(schemaName)
    // Creates dependencies using a collection of schema names
    .ByName(schemaNames)
    .Builder()
  .Build();
```

The preceding example creates dependencies on the schemas `My.SchemaA`, `My.SchemaB`, and `My.SchemaC`. Modifying any of these schemas will invalidate the cached item associated with this dependency.
### Info objects
The `ForInfoObjects<T>` method creates cache dependencies on [info object](/documentation/developers-and-admins/api/database-table-api) instances of `T`.
C#
**Create a dependency on all objects**
Copy
```
// Creates a new instance of 'CacheDependencyBuilder'
CacheDependencyBuilder cacheDependencyBuilder =
    cacheDependencyBuilderFactory.Create();

// Creates a dependency on all member accounts in the system
CMSCacheDependency cacheDependency = cacheDependencyBuilder
    .ForInfoObjects<MemberInfo>()
        .All()
        .Builder()
    .Build();
```

The preceding example creates a cache dependency on all [MemberInfo](/documentation/developers-and-admins/development/registration-and-authentication) objects in the system.
C#
**Create a dependency on specific object instances**
Copy
```
// Sample data containing the currently authenticated member account
MemberInfo currentMember = GetCurrentMember();

// Creates a new instance of 'CacheDependencyBuilder'
CacheDependencyBuilder cacheDependencyBuilder =
    cacheDependencyBuilderFactory.Create();

CMSCacheDependency cacheDependency = cacheDependencyBuilder
    .ForInfoObjects<MemberInfo>()
        .ById(currentMember.MemberID)
        .Builder()
    .Build();
```

The preceding example creates a dependency on the currently authenticated [member account](/documentation/developers-and-admins/development/registration-and-authentication). To retrieve the currently authenticated member account, see [Retrieve the currently authenticated member](/documentation/developers-and-admins/development/registration-and-authentication#retrieve-the-currently-authenticated-member).
The `ForInfoObjects` API allows creating dependencies using object:
  * identifiers – the `ById` method
  * GUIDs – the `ByGuid` method
  * code names – the `ByCodeName` method


### Settings keys
The `ForSettingKeys` method allows you to create dependencies on [settings](/documentation/developers-and-admins/configuration/settings).
C#
**Create dependencies on setting keys**
Copy
```
// Creates a new instance of 'CacheDependencyBuilder'
CacheDependencyBuilder cacheDependencyBuilder =
    cacheDependencyBuilderFactory.Create();

// Creates a cache dependency on system settings
CMSCacheDependency dependency = cacheDependencyBuilder
    .ForSettingKeys()
        .ByCodeName("CMSCIEnabled")
        .Builder()
    .Build();
```

The preceding example creates a dependency on the [Continuous Integration](/documentation/developers-and-admins/ci-cd) setting.
`ForSettingKeys` works only with default system settings. To create dependencies on [custom settings](/guides/development/customizations-and-integrations/create-basic-module), add a dependency on the custom [info object](#info-objects) representing your settings.
### Media files
**Media libraries sunset**
Media libraries have been officially sunset, and their related API members have been marked as obsolete. Support for media libraries will continue for one more year (until July 24, 2026), after which the feature and all associated APIs will be completely removed.
See [Media library migration](/guides/architecture/media-libraries-migration-guidance) for instructions on how to migrate your media library files to [Content hub](/documentation/business-users/content-hub).
The `ForMediaFiles` method allows you to create dependencies on [media library](/documentation/developers-and-admins/configuration/media-library-configuration) files.
C#
**Create a dependency on media library files**
Copy
```
// Sample media file on which to create the dependency
MediaFileInfo mediaFile = GetMediaFile();

// Creates a new instance of 'CacheDependencyBuilder'
CacheDependencyBuilder cacheDependencyBuilder =
    cacheDependencyBuilderFactory.Create();

// Creates a cache dependency on the provided media file
CMSCacheDependency dependency = cacheDependencyBuilder
    .ForMediaFiles()
        // Pass either a single GUID or a collection
        .ByGuid(mediaFile.FileGUID)
        .Builder()
    .Build();
```

The preceding example creates a dependency on a media library file using its GUID. See [Retrieve content from media libraries](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-from-media-libraries) for an overview of the media library data retrieval API.
### Additional dependencies
To add dependencies on other keys that cannot be created using `CacheDependencyBuilder` APIs, use the `AddDependency` method. A common scenario is including dependencies on linked items. See: [Cache dependencies on linked content items](/documentation/developers-and-admins/development/caching/data-caching#cache-dependencies-on-linked-content-items).
The method also provides an overload that allows you to add a `CMSCacheDependency` object directly to the dependencies stored in the `CacheDependencyBuilder` instance. Useful when you already have a `CMSCacheDependency` object and want to include its keys and file names in the current dependency set.
C#
**Specify additional dependencies**
Copy
```
// Additional dependency keys, for example on linked items
// obtained from 'ILinkedItemsDependencyAsyncRetriever'
IEnumerable<string> linkedDependencies = GetLinkedDependencies();

// Assume an existing CMSCacheDependency object
CMSCacheDependency existingDependency = GetExistingDependency();

// Creates a new instance of 'CacheDependencyBuilder'
CacheDependencyBuilder cacheDependencyBuilder =
    cacheDependencyBuilderFactory.Create();

CMSCacheDependency dependency = cacheDependencyBuilder
    // Adds the keys from 'linkedDependencies' to the final result
    .AddDependency(linkedDependencies)
    .AddDependency(existingDependency)
    .Build();
 



 



 
 


```

See the [cache dependency key reference](/documentation/developers-and-admins/development/caching/cache-dependencies-reference) for information about supported key constructions.
![]()
[]()[]()
