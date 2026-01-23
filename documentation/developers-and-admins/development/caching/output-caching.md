---
source: https://docs.kentico.com/documentation/developers-and-admins/development/caching/output-caching
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Caching](/documentation/developers-and-admins/development/caching)
  * Output caching 


# Output caching
Output caching can significantly improve the performance and scalability of an application by reducing the server-side work required to generate page output. Caching works best with content that changes infrequently and is expensive to generate.
Xperience supports caching for standard page content and the output of [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) [widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder).
  * [Caching page output](#cache-page-output)
  * [Caching the output of Page Builder widgets](#cache-the-output-of-page-builder-widgets)


## Cache page output
You can cache the output of pages using the [cache Tag Helper](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/built-in/cache-tag-helper) provided by ASP.NET Core. The Tag Helper is highly flexible, allowing for configuration of cache priority, expiration, optional deactivation of the caching functionality, etc.
cshtml
**Cache Tag Helper usage example**
Copy
```
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers

<cache priority="High" expires-sliding="@TimeSpan.FromSeconds(60)" vary-by-user="true">
    @* Cached content... *@
</cache>
```

To ensure the correct behavior of [Forms](/documentation/developers-and-admins/development/builders/form-builder), the system automatically disables output caching of pages containing a _Form_ widget.
### Add cache dependencies
When cached content contains objects from the Xperience database, you need to ensure the cache is cleared whenever such objects are modified to always display the most up-to-date information. For this purpose, the system allows you to specify [cache dependencies](/documentation/developers-and-admins/development/caching/cache-dependencies) on system objects from within `cache` Tag Helper sections. Cache dependencies instruct the application to automatically clear cached data when referenced objects are modified.
Specify cache dependencies by adding the `cache-dependency` Tag Helper within sections encapsulated by the `cache` tag. The helper provides two attributes:
  * `cache-keys` – an array of cache dependency dummy keys.
  * `enabled` – indicates whether the dependencies get injected. Enabled by default. See [Control cache dependency injection](#control-cache-dependency-injection).


cshtml
**Adding cache dependencies on Xperience objects**
Copy
```
@* Adds the Tag Helper to the view *@
@addTagHelper Kentico.Web.Mvc.Caching.CacheDependencyTagHelper, Kentico.Web.Mvc

@{
    // Creates cache dummy keys targeting all member objects in the system
    var cacheKeys = new[] { "cms.member|all" };
}

<cache>
    @* Ensures that contents of this cached section are evicted whenever any member in the system or the /Home page is modified *@
    <cache-dependency cache-keys="@cacheKeys" />

    Time: @DateTime.Now
</cache>
```

### Control cache dependency injection
The `cache-dependency` Tag Helper can be programmatically enabled or disabled via its `enabled` attribute. This can be paired together with the `enabled` attribute of the `cache` Tag Helper to eliminate unnecessary overhead when caching is disabled:
cshtml
**Example**
Copy
```
using CMS.Websites.Routing

@inject IWebsiteChannelContext WebsiteChannelContext

@{
    // Adds a cache dependency on all pages of the my.article content type that exist on 'mysite'
    var articlesCacheKeys = new[] { "webpageitem|bychannel|mychannel|bycontenttype|my.article|all" };

    // Checks whether the page is accessed in preview mode (in a website channel application in the admin UI)
    var cacheEnabled = !WebsiteChannelContext.IsPreview;
}

@* Enables or disables the cache and cache dependencies based on a dynamic condition (caching is disabled for preview mode in this case) *@
<cache enabled="@cacheEnabled">
    <cache-dependency cache-keys="@articlesCacheKeys" enabled="@cacheEnabled" />
    @* Custom logic (e.g., render all articles) *@
</cache>
```

## Cache different versions of page output
In some cases, pages serve different content based on various conditions. Typical scenarios are multilingual websites or pages that leverage [content personalization](/documentation/developers-and-admins/digital-marketing-setup/content-personalization). Another case is caching within [page templates](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder), which are then used for different pages that have their own specific content.
To ensure that output caching works correctly in these scenarios, you need to take additional steps and adjust your application to cache separate output versions. Configure output cache varying by setting the `vary-by` attributes of the [cache Tag Helper](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/built-in/cache-tag-helper).
The following examples demonstrate how you can vary the output cache:
  * [Page template content](#page-template-content)
  * [Selected cookie level](#request-cookie-levels)


### Page template content
[Page templates](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder) allow different pages to use the same general layout or share fixed sections of content. However, parts of the template typically load data from the actual page, so the final content differs for each page.
If you use the `cache` Tag Helper for the page-specific sections of a page template, you need to **_ensure that every page stores its own version of the cache_**.
We recommend using the `vary-by-route` attribute of the `cache` [Tag Helper](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/built-in/cache-tag-helper). Pages have their own unique route, regardless of the shared page template. Set the `vary-by-route` attribute’s value to the `CacheVaryByConstants.ROUTE_URL_SLUG` constant. The constant stores the name of the route parameter that holds the URL slug value for page routes.
cshtml
**Caching within a page template**
Copy
```
@using Kentico.PageBuilder.Web.Mvc;

...

<cache expires-after="@TimeSpan.FromMinutes(5)" vary-by-culture="true" vary-by-route="@CacheVaryByConstants.ROUTE_URL_SLUG">
    @* Cached content... *@
</cache>
```

### Request cookie levels
Xperience classifies cookies into several levels according to their purpose via [cookie levels](/documentation/developers-and-admins/data-protection/cookies). If you display different content to visitors based on their preferred cookie level, you can vary the cache accordingly.
Use the `ICurrentCookieLevelProvider` service to obtain the cookie level for the current request and pass it to the view via a model class:
C#
**Controller class**
Copy
```
using CMS.Helpers;

public async Task<ActionResult> Index([FromServices] ICurrentCookieLevelProvider cookieLevelProvider)
{
    // Gets the cookie level for the current request
    string varyByCookieLevel = $"CookieLevel={cookieLevelProvider.GetCurrentCookieLevel()}";

    var viewModel = new HomeIndexViewModel
    {
        VaryByCookieLevel = varyByCookieLevel
    };

    return View(viewModel);
}
```

In the view, assign the cookie level to the `vary-by` attribute of the [Cache Tag Helper](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/built-in/cache-tag-helper):
cshtml
**View template**
Copy
```
@* Ensures a separate cache entry for every cookie level detected in requests *@
<cache vary-by="@Model.VaryByCookieLevel">
    ...
</cache>
```

If you wish to vary the cache according to multiple variables, you can pass a string containing the concatenated name-value pairs of all variables. The framework creates a separate entry for each unique string.
## Cache the output of Page Builder widgets
Page Builder [widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) can be configured to automatically cache their output. The bulk of the caching configuration is performed in [editable areas](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas). This allows developers to create flexible caching strategies for individual pages.
To enable and configure caching for a widget:
  1. Set the `AllowCache` property of the `RegisterWidget` attribute to `true` for the selected widget. This marks the widget as cacheable for individual editable areas.
C#
Copy
```
[assembly: RegisterWidget("CompanyName.CustomWidget", typeof(CustomWidgetViewComponent), "Custom widget", AllowCache = true)]
```

  2. Enable and configure caching within individual editable areas in your project.
    1. Enable caching for the editable area via the `AllowWidgetOutputCache` property of the `EditableAreaOptions` object. The property indicates whether the output of individual widgets placed into the area can be cached. This value is combined with the `AllowCache` property of contained widgets to determine whether caching should be enabled. `false` by default.
    2. Select a caching strategy for the editable area via the following properties of the `EditableAreaOptions` object:  
`WidgetOutputCacheExpiresOn` – a `DateTimeOffset` value that sets the absolute expiration date for cached content.  
`WidgetOutputCacheExpiresAfter` – a `TimeSpan` value that sets the duration from the time of the first request when the content was cached.  
`WidgetOutputCacheExpiresSliding` – a `TimeSpan` value that defines a sliding window of expiration for the cached content. Content not accessed within the specified time frame is evicted.
These configuration options can also be set via the [editable-area Tag Helper](/documentation/developers-and-admins/development/reference-tag-helpers).
cshtml
Copy
```
@using Kentico.PageBuilder.Web.Mvc
@using Kentico.Web.Mvc

@{
    // Sets the sliding expiration for cached widgets to two minutes
    var options = new EditableAreaOptions() { AllowWidgetOutputCache = true,
                                              WidgetOutputCacheExpiresSliding = TimeSpan.FromMinutes(2) };
}

@await Html.Kentico().EditableAreaAsync("area1", options)
```

  3. The system automatically adds a [cache dependency](/documentation/developers-and-admins/development/caching/cache-dependencies) tied to pages where the widget is rendered – when the content of the page is modified, the cached output of individual widgets is evicted.


You have configured caching for your widget. To further customize the caching behavior, you can:
  * [Specify additional cache dependencies for the widget](#add-custom-cache-dependencies)
  * [Configure caching for personalized content](#cache-personalized-widget-output)


### Add custom cache dependencies
The system automatically adds a [cache dependency](/documentation/developers-and-admins/development/caching/cache-dependencies) on the page where the widget is rendered. In addition, widget developers can provide additional cache dependencies when developing a widget. However, this approach requires access to the component view model and is therefore only supported for [widgets based on view components](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder).
Cache dependencies can be added within the view component’s `Invoke` (`InvokeAsync`) method via the `CacheDependencies` property of the `ComponentViewModel` parameter. Pass a string collection of desired cache dependency keys into the object’s `CacheKeys` property:
C#
Copy
```
using Kentico.PageBuilder.Web.Mvc;

public async Task<IViewComponentResult> InvokeAsync(ComponentViewModel<AttachmentListWidgetProperties> viewModel)
{
    // Adds a cache dependency on all page attachments
    viewModel.CacheDependencies.CacheKeys = new [] { "cms.attachment|all" };
    ...
}
```

### Cache personalized widget output
When using caching to improve the performance of your website, you need to take additional steps to ensure that the output cache correctly stores personalized widget output. Because personalized widgets serve different content based on various personalization conditions, you need to adjust your application to cache separate output versions according to the widget’s available [personalization criteria](/documentation/developers-and-admins/digital-marketing-setup/content-personalization).
For this purpose, the system provides the `CacheVaryBy` attribute. The attribute allows you to specify which request variables affect the contents of the cache. From the specified variables, the system constructs a cache key under which it caches the widget output corresponding to the particular configuration. Should any of the request values change, the output is cached as a separate entry in the cache. The lifetime and eviction strategy of individual entries is controlled by the caching configuration of the corresponding editable area. 
The `CacheVaryBy` attribute must decorate the **view component** class of the corresponding widget. As such, only [widgets based on view components](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) support this approach.
The attribute supports the following personalization variables:
Property |  Type |  Description  
---|---|---  
`VaryByUser` |  bool |  Varies the cache based on the current [application user](/documentation/developers-and-admins/development/registration-and-authentication).  
`VaryByCulture` |  bool |  Varies the cache based on the current request culture. Enabled by default.  
`VaryByHost` |  bool |  Varies the cache based on the current host (the domain and port number of the server to which the request is being sent).  
`VaryByCookieLevel` |  bool |  Varies the cache based on the [cookie level](/documentation/developers-and-admins/data-protection/cookies) of the current request.  
`VaryByQuery` |  string |  Varies the cache based on parameters in the URL query string. Specify a comma-delimited set of query string parameters used to vary the cached content.  
`VaryByCookie` |  string |  Varies the cache based on cookie values. Specify a comma-delimited set of cookie names used to vary the cached content.   
`VaryByHeader` |  string |  Varies the cache based on request headers. Specify a comma-delimited set of request headers used to vary the cached content.  
`VaryByRoute` |  string |  Varies the cache based on route data parameters. Specify a comma-delimited set of application route data parameters used to vary the cached content. **Example** C# **Startup.cs** Copy ```
endpoints.MapControllerRoute( name: "default",
                              pattern: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```
C# **WidgetViewComponent.cs** Copy ```
[CacheVaryBy(VaryByRoute="Make;Model")]
public class WidgetViewComponent : ViewComponent
```
**Note** : For pages that use [content tree-based routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing), you can access the name of the route parameter that holds the URL slug value for page routes in the `CacheVaryByConstants.ROUTE_URL_SLUG` constant.  
`VaryByOptionTypes` |  Type[] |  An array of `System.Type` values referencing custom _vary by_ implementations. You can implement custom vary by conditions that suit your particular scenarios. See [Implement custom personalization options](#implement-custom-personalization-options).  
C#
**CacheVaryBy usage example**
Copy
```
using Kentico.PageBuilder.Web.Mvc;

[CacheVaryBy(VaryByUser = true, VaryByCookie = "cookie_1,cookie_2", VaryByCookieLevel = true, VaryByCulture = false, VaryByHeader = "header_1,header_2",
    VaryByHost = true, VaryByQuery = "query_param1,query_param2", VaryByRoute = "param_1,param_2")]
public class MyWidgetViewComponent : ViewComponent
```

The example above configures caching for a broad range of variables contained within each request. This is unnecessary in most cases. You should cache according to personalization conditions used by your widgets.
Always balance website performance, memory usage on the server, and the risk of displaying incorrect cached content.
**Automatic widget cache varying**
By default, the system stores separate output cache versions of widgets for every [widget personalization variant](/documentation/developers-and-admins/digital-marketing-setup/content-personalization/develop-personalization-condition-types). This ensures that cached widgets automatically display the correct content when using the native personalization features, and no handling is required from developers.
### Implement custom personalization options
In addition to the variables supported by the `CacheVaryBy` attribute, you can implement cache personalization based on other request variables. Such custom personalization options need to be developed as separate classes implementing the `ICacheVaryByOption` interface. The interface describes a `GetKey` method that must return a string containing a key-value pair identifying the personalization variable and its value for the current request. 
C#
Copy
```
using Kentico.PageBuilder.Web.Mvc

public class VaryByOption : ICacheVaryByOption
{
    // Returns a key identifying the cache variant
    public string GetKey()
    {
        return $"MyKey={ComputeVariable()}";
    };

    private string ComputeVariable()
    {
        // custom logic...
        return myVariable;
    };
}
```

#### Example
The following example implements a personalization option that partitions the cache based on the currently authenticated user’s first name. This approach leads to fewer redundant cache entries in scenarios where the output does not vary per object, but rather according to properties whose values are shared among multiple objects. As a result, it creates a more economical caching strategy that consumes less application memory.
Note that this example assumes an [extended member class](/documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects) with a `FirstName` property.
Create a new class, implement the `ICacheVaryByOption` interface, and define the `GetKey` method.
C#
**VaryByUserFirstName.cs**
Copy
```
public class VaryByUserFirstName : ICacheVaryByOption
{
    private readonly IHttpContextAccessor httpContextAccessor;
    private readonly UserManager<ExtendedApplicationUser> userManager;

    public VaryByUserFirstName(IHttpContextAccessor httpContextAccessor,
                          UserManager<ExtendedApplicationUser> userManager)
    {
        this.httpContextAccessor = httpContextAccessor;
        this.userManager = userManager;
    }

    public string GetKey()
    {
        // Gets the currently authenticated member
        var user = userManager.
                        FindByIdAsync(httpContextAccessor.HttpContext.User.
                            FindFirstValue(userManager.Options.ClaimsIdentity.UserIdClaimType)).Result;

        string userFirstName = user?.FirstName ?? String.Empty;

        return $"UserFirstName={userFirstName}";
    }
}
```

Pass the created `VaryByUserName` class to the `CacheVaryBy` attribute in your widget’s view component:
C#
**MyWidgetViewComponent.cs**
Copy
```
using Kentico.PageBuilder.Web.Mvc;

// Caches a separate output for each distict user name
[CacheVaryBy(typeof(VaryByUserFirstName))]
public class MyWidgetViewComponent : ViewComponent
```

The application now creates a distinct cache entry for each user with a unique first name that views content rendered by your widget. 
![]()
[]()[]()
