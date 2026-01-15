# Set up content tree-based routing
  * [ Copy page link ](documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing#) | [Get HelpService ID](documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing#)
Core MVC 5


[✖](documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing#)
Content tree-based routing handles URL generation and routing for pages in website channels. A URL is granted to pages upon creation based on their name and position in the content tree. For a detailed overview, see [Content tree-based routing](documentation/developers-and-admins/development/routing/content-tree-based-routing).
This page describes the steps needed to fully integrate the routing feature into your project.
The routing scheme requires [content types](documentation/developers-and-admins/development/content-types) for pages with the _Include in routing_ option set. The option indicates that you want pages created from this content type to be accessible using a URL. When used with content tree-based routing, the system automatically generates URLs for new pages.
When handling content types with the [Page Builder](documentation/developers-and-admins/development/builders/page-builder) feature enabled, there are a couple of additional prerequisites to keep in mind when setting up the routing scheme. See [Handle Page Builder-enabled content types](documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing#Setupcontenttreebasedrouting-PageBuilder).
When developing views for pages you need to provide your own controller together with a view model. This grants you full control over the data and processing logic. You can execute arbitrary code within the controller, pass additional data required by the view, or switch between different views as needed.
When configuring routing, you need to:
  1. [Decide which content types and sections of the content tree will be managed using your route](documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing#Setupcontenttreebasedrouting-RoutingOverview).
  2. [Implement the controller logic](documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing#Setupcontenttreebasedrouting-CustomRouteImp) (together with view models and views).
  3. [Register the controllers in the system together with the routes for which they are responsible](documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing#Setupcontenttreebasedrouting-RoutingOverview).


When a request targets the URL of a page in the content tree of a website channel, the system reroutes the request to a matching custom controller. A matching controller is found based on the content type and tree path (location in the content tree) of the requested page. You provide this information using the `RegisterWebPageRoute` assembly attribute (`Kentico.Content.Web.Mvc.Routing` namespace). 
The `RegisterWebPageRoute` attribute requires the following parameters:
  * **ContentTypeName** – the code name of the content type this controller handles. The code name is in the _namespace.codename_ format. Both are set during content type creation. [Generated classes](documentation/developers-and-admins/api/generate-code-files-for-system-objects) expose the class name under the `CONTENT_TYPE_NAME` constant.
  * **MarkedType** – the `System.Type` of the controller assigned to handling the requests for the content type.


When a controller is registered using only these required parameters, the system invokes the controller’s `Index` action in response to all requests matched with pages of the supplied content type.
To further tailor the response, you can provide additional parameters when registering the route:
  * **Path** – the tree path of an item in the content tree for which you want to use this controller. Use this property if you wish to provide special handling for a particular page. 
    * If there are multiple matches to a single request, the longest (most specific) path wins.
    * If not specified, the registered controller is used to respond to all requests for pages of the corresponding content type.
  * **ActionName** – allows you to target which controller action responds to the matched request. If not specified, the default `Index` action is used.
  * **WebsiteChannelNames** – allows you to specify a list of website channels for which you want to use this controller. 
    * If not specified, the registered controller is used to respond to all requests for pages from all website channels.


The registration parameters can be combined, for example, to:
  * have multiple content types handled by a single controller and action on specific website channels
  * have multiple content types handled by a single controller and different actions
  * have a single content type handled by different controllers depending on the location of specific pages in the content tree


**Multiple identical registrations**
Multiple identical registrations are not supported (e.g., registering multiple controllers with the same path and action for a single content type). If the system detects duplicate registrations, it throws an exception during application startup.
#### Examples
The following are examples of route registrations:
**Multiple content types - single controller - single action - specific website channels**
C#
Copy
```
// Requests for all pages in the 'DancingGoatPages' and 'DancingGoatBetaPages' website channels that are of the 'Coffee', 'Brewer' and 'ElectricGrinder' content types are handled using the 'Detail' action of the Articles controller
[assembly: RegisterWebPageRoute(Coffee.CONTENT_TYPE_NAME, typeof(ArticlesController), ActionName = "Detail", WebsiteChannelNames = new[] {"DancingGoatPages", "DancingGoatBetaPages"})]
[assembly: RegisterWebPageRoute(Brewer.CONTENT_TYPE_NAME, typeof(ArticlesController), ActionName = "Detail", WebsiteChannelNames = new[] {"DancingGoatPages", "DancingGoatBetaPages"})]
[assembly: RegisterWebPageRoute(ElectricGrinder.CONTENT_TYPE_NAME, typeof(ArticlesController), ActionName = "Detail", WebsiteChannelNames = new[] {"DancingGoatPages", "DancingGoatBetaPages"})]
```

**Multiple content types - single controller - different actions**
C#
Copy
```
// Requests for all pages of the 'ArticleSection' content type are handled using the 'Index' action of the Articles controller
[assembly: RegisterWebPageRoute(ArticleSection.CONTENT_TYPE_NAME, typeof(ArticlesController))]
// Requests for all pages of the 'Article' content type are handled using the 'Show' action of the Articles controller
[assembly: RegisterWebPageRoute(Article.CONTENT_TYPE_NAME, typeof(ArticlesController), ActionName = "Show")]
```

**Single content type - multiple controllers - each for a different page in the content tree**
C#
Copy
```
// Requests for a page of the 'ProductSection' content type located at '/Articles/Brewers' is handled using the 'Index' action of the Brewers controller
[assembly: RegisterWebPageRoute(ArticlesSection.CONTENT_TYPE_NAME, typeof(BrewersController), Path = "/Articles/Brewers")]
// Requests for a page of the 'ProductSection' content type located at '/Articles/Grinders' is handled using the 'Index' action of the Grinders controller
[assembly: RegisterWebPageRoute(ArticlesSection.CONTENT_TYPE_NAME, typeof(GrindersController), Path = "/Articles/Grinders")]
// Requests for a page of the 'ProductSection' content type located at '/Articles/Coffees' is handled using the 'Index' action of the Coffees controller
[assembly: RegisterWebPageRoute(ArticlesSection.CONTENT_TYPE_NAME, typeof(CoffeesController), Path = "/Articles/Coffees")]
```

As described in the previous section, routes require a controller that takes over when the request matches certain criteria. When the system delegates handling to such a controller, the rest of the processing logic is completely in your hands. You can run any custom code within the controller, pass any type of required data to the view, or switch between completely different views based on the current scenario.
The development process for routing follows standard MVC practices:
  1. Create a new controller class in your project.
  2. Implement the `Index` action and any other required actions.
     * If you need to work with the data context of the currently requested page, use the `Retrieve` method of the `IWebPageDataContextRetriever` interface. The method returns a `WebPageDataContext` object exposing a `WebPage` property, which gives you access to the ID, [language](documentation/developers-and-admins/configuration/languages), [content type](documentation/developers-and-admins/development/content-types) and [channel](documentation/developers-and-admins/configuration/website-channel-management) information of the page. If you need to load the content fields of the page, [retrieve the page object](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content) using the [ContentRetriever API](documentation/developers-and-admins/api/content-item-api/content-retriever-api).
C#
Copy
```
// An instance of IWebPageDataContextRetriever (e.g., obtained via dependency injection)
private readonly IWebPageDataContextRetriever retriever;

// Access the current page data
var page = retriever.Retrieve().WebPage;

// Access the properties of the page object
int webPageId = page.WebPageItemID;
int languageId = page.LanguageID;
string languageName = page.LanguageName;
int contentTypeId = page.ContentTypeID;
string contentTypeName = page.ContentTypeName;
int channelId = page.WebsiteChannelID;
string channelName = page.WebsiteChannelName;
```

  3. Create any required view model classes used to pass data from the controller to the view.
  4. Create views required by the controller actions.
     * The output must be a full HTML page, so the view must include the following:
       * Full HTML markup, including the `html`, `head`, and `body` elements
       * Links to all necessary resources, such as stylesheets and scripts
     * Use MVC layouts with the view for any shared output code (for example your site’s main layout).
  5. [Register](documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing#Setupcontenttreebasedrouting-RoutingOverview) the route in the system.


The registered controller handles the requests matching the criteria specified in the route registration attribute.
For content types that use content tree-based routing and also have [Page Builder](documentation/developers-and-admins/development/builders/page-builder) enabled, you need to ensure certain prerequisites, depending on the use of [page templates](documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder).
  * If the page is based on a page template: 
    * The controller action handling the request needs to return a `TemplateResult` object. You do not need to provide any additional information to the constructor. The page to render is retrieved from information provided by the router when handling the request.
C#
Copy
```
return new TemplateResult();
```

  * If the page is NOT based on a page template: 
    * The view needs to include [Page Builder scripts and styles](documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas) to ensure page content is loaded correctly (either directly or through the assigned layout).


## Handle POST actions
Content tree-based routing automatically works for GET requests, but does not handle POST requests. You need to manually register corresponding routes for all POST actions in your project.
For example, you can use attribute routing to target POST actions within controllers handling routes. Targeting specific actions lowers the risk of accidentally registering routes that capture a broader spectrum of requests than intended, resulting in potential routing issues.
### Access the data of the current page
The data of the current page (name, custom fields, etc.) is by default not accessible in controller actions that handle POST requests. Such requests don’t contain sufficient information to identify the page from which they originate. 
To access the page data in POST actions, you need to include information about the current page into the data submitted by the corresponding form by calling the `Html.Kentico().PageData` extension method within the given `form` tag.
cshtml
Copy
```
@using Kentico.Web.Mvc
@using Kentico.PageBuilder.Web.Mvc

@using (Html.BeginForm("HandlePost", "RouteController", FormMethod.Post))
{
    ...

    @Html.Kentico().PageData()

    <input type="submit" value="Submit" />
}
```

The method renders a hidden form field that persists information about the current page. The page data can be retrieved via the `IWebPageDataContextRetriever` service and [content item API](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content) in the corresponding controller action.
Obtain an instance of the `IWebPageDataContextRetriever` service (using dependency injection) and call its `Retrieve` method. The returned object’s `WebPage` property contains basic information about the current page, which you can use to [retrieve the current page object](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content).
C#
Copy
```
// An instance of IWebPageDataContextRetriever (e.g., obtained via dependency injection)
private readonly IWebPageDataContextRetriever retriever;

// Access the current page data
var page = retriever.Retrieve().WebPage;

// Access the properties of the page object
int webPageId = page.WebPageItemID;
int languageId = page.LanguageID;
string languageName = page.LanguageName;
int contentTypeId = page.ContentTypeID;
string contentTypeName = page.ContentTypeName;
int channelId = page.WebsiteChannelID;
string channelName = page.WebsiteChannelName;
```

## Retrieve generated page URLs using the API
URLs generated for pages can be retrieved using the API. See [Retrieve page URLs](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls) to learn about the available options.
## Combine content tree-based and ASP.NET routing
Using content tree-based routing does not exclude your projects from using the conventional routing framework provided by ASP.NET Core. If content tree-based routing does not match an incoming URL to any of the site’s pages, it delegates the request to routes registered further down the routing table.
At this point, request processing continues using the conventional routing model with the framework attempting to match the request to site-specific routes. See the following diagram for illustration.
[![Request handling under content tree-based routing](docsassets/documentation/set-up-content-tree-based-routing/Routing_SimplifiedFlow.png)](https://docs.kentico.com/docsassets/documentation/set-up-content-tree-based-routing/Routing_SimplifiedFlow.png)
You can take advantage of this if you wish to serve pages with content that does not need to be stored and managed in Xperience. For example when creating dynamic pages such as search interfaces with search results, or other pages with fully data-driven content. 
### Preserving thread language across routing modes
On multilingual pages, the language for the **handling thread** is supplied by the router (based on the [language](documentation/developers-and-admins/configuration/languages) code name of the requested page). If you wish to invoke custom actions from the context of a page served by the router – that is, you wish to construct a URL that maps to a custom route defined for your application – you need to ensure the **thread language** of the handling thread matches the language supplied by the router. Otherwise, when opening links handled by custom actions, users may be redirected to a different language variant of the page.
For this purpose, the system provides the `LanguageNameRouteValuesKey` property. The property can be set using the `WebPageRoutingOptions` object when [enabling the content tree-based routing feature](documentation/developers-and-admins/development/routing/content-tree-based-routing/enable-content-tree-based-routing) (provided as an optional parameter to the `UseWebPageRouting` method).
C#
Copy
```
...

builder.Services.AddKentico(features =>
{
    // Enables content tree-based routing
    features.UseWebPageRouting(new WebPageRoutingOptions {LanguageNameRouteValuesKey = "lang"});
});
```

The `LanguageNameRouteValuesKey` property:
  * Sets the name of the key under which the language code of the current router-handled page is stored in the [RouteValueDictionary](https://learn.microsoft.com/en-us/dotnet/api/system.web.routing.routevaluedictionary) object of the request.
  * Enables the framework to retrieve the current language from route values when building URLs corresponding to your custom routes. The routes need to contain a wildcard parameter expecting a language code, with a name matching the key set in this property.


#### Example
The following example demonstrates the usage of the `LanguageNameRouteValuesKey` property on the registration of a single site-specific route.
First, set the `LanguageNameRouteValuesKey` property when enabling content tree-based routing:
C#
Copy
```
...

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddKentico(features =>
{
    // Enables content tree-based routing
    features.UseWebPageRouting(new WebPageRoutingOptions {LanguageNameRouteValuesKey = "lang"});
});
```

Then, register a route containing a wildcard parameter matching the key set in the `LanguageNameRouteValuesKey` property –  _{lang}_ in this case.
C#
Copy
```
...

var app = builder.Build();

app.MapControllerRoute(
    name: "MyRoute",
    pattern: "{lang}/{controller}/{action}"
);
```

Now, when building URLs to the “MyRoute” route from the context of pages served by content tree-based routing (e.g., using `UrlHelper.Action("Index", "CustomController")`), the framework:
  1. Matches the route “MyRoute.”
  2. Searches the `RouteValues` object for entries corresponding to the wildcards provided in the URL template. The _language_ key is set and populated by the router when serving the source page.
  3. Generates a fully qualified URL preserving the language of the original page.


### Manually initialize page data context
Some Xperience features, such as [Page Builder](documentation/developers-and-admins/development/builders/page-builder), require context data that is automatically available from the current page when using content tree-based routing. If you use custom .NET routing to display [pages](documentation/business-users/website-content) from a website channel’s content tree, you need to manually initialize the page data context to use such features.
Use the `IWebPageDataContextInitializer` service and supply the context via the properties of the `RoutedWebPage` parameter. Get the required data by [retrieving the page](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content) related to the given route.
Only initialize page data context for custom routes that are associated with a page in the Xperience content tree.
Otherwise you will not be able to set all `RoutedWebPage` properties (e.g., the `WebPageItemID`). Working with incomplete page data context may result in errors.
C#
**Initializing page data context in a custom route controller**
Copy
```
using CMS.ContentEngine;
using CMS.DataEngine;
using CMS.Websites;

using Kentico.Content.Web.Mvc;

using Microsoft.AspNetCore.Mvc;
using System.Threading.Tasks;

public class CustomRouteController : Controller
{
    private readonly IContentRetriever contentRetriever;
    private readonly IInfoProvider<ContentLanguageInfo> languageProvider;
    private readonly IInfoProvider<ChannelInfo> channelProvider;
    private readonly IInfoProvider<WebsiteChannelInfo> websiteChannelProvider;
    private readonly IWebPageDataContextInitializer webPageDataContextInitializer;

    public CustomRouteController(
        IContentRetriever contentRetriever,
        IInfoProvider<ContentLanguageInfo> languageProvider,
        IInfoProvider<ChannelInfo> channelProvider,
        IInfoProvider<WebsiteChannelInfo> websiteChannelProvider,
        IWebPageDataContextInitializer webPageDataContextInitializer)
    {
        // Gets instances of required services using dependency injection
        this.contentRetriever = contentRetriever;
        this.languageProvider = languageProvider;
        this.channelProvider = channelProvider;
        this.websiteChannelProvider = websiteChannelProvider;
        this.webPageDataContextInitializer = webPageDataContextInitializer;
    }

    public async Task<IActionResult> Index()
    {
        // Retrieves the website channel page associated with the custom route
        // Assuming 'ArticlePage' is the generated model class for the page's content type
        var page = (await contentRetriever.RetrievePages<ArticlePage>(
            new RetrievePagesParameters
            {
                PathMatch = PathMatch.Single("/Custom/Page/Path"),
            })).FirstOrDefault();

        // Prepares a RoutedWebPage object with the required page data
        RoutedWebPage routedWebPage = GetRoutedWebPage(page.SystemFields);

        // Initializes the page data context
        webPageDataContextInitializer.Initialize(routedWebPage);

        return View();
    }

    // Returns a RoutedWebPage object with data from the system fields of a page
    private RoutedWebPage GetRoutedWebPage(WebPageFields pageSystemFields)
    {
        var webPageItemId = pageSystemFields.WebPageItemID;
        var webPageItemGuid = pageSystemFields.WebPageItemGUID;
        var contentTypeId = pageSystemFields.ContentItemContentTypeID;
        var languageId = pageSystemFields.ContentItemCommonDataContentLanguageID;
        var websiteChannelId = pageSystemFields.WebPageItemWebsiteChannelId;
        var channelId = websiteChannelProvider.Get(websiteChannelId).WebsiteChannelChannelID;
        var channelName = channelProvider.Get(channelId).ChannelName;

        return new RoutedWebPage
        {
            WebPageItemID = webPageItemId,
            WebPageItemGUID = webPageItemGuid,
            LanguageID = languageId,
            LanguageName = languageProvider.Get(languageId).ContentLanguageName,
            ContentTypeID = contentTypeId,
            ContentTypeName = DataClassInfoProvider.GetClassName(contentTypeId),
            WebsiteChannelID = websiteChannelId,
            WebsiteChannelName = channelName
        };
    }
}
```