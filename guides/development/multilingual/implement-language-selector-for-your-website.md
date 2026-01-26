---
source: https://docs.kentico.com/guides/development/multilingual/implement-language-selector-for-your-website
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Multilingual](/guides/development/multilingual)
  * Implement a language selector for your website 


# Implement a language selector for your website
Offering your content to visitors in multiple languages is an important step in reaching a broader audience.
If you followed the first part of the **Multilingual content** mini-series, your website can now display content in multiple languages, and it’s time to make switching between those languages seamless for your visitors.
In the second part of the series, you’ll enhance the user experience by handling multilingual URLs and implementing a simple language selector that ties it all together.
**Multilingual content series**
**This is the second and last part of the[Set up multilingual](/guides/development/multilingual) mini-series.** Using a concrete example, the mini-series demonstrates best practices and necessary steps you need to perform in order to present your website content in multiple languages in a user-friendly way.
If you have followed along with [the previous part of the series](/guides/development/multilingual/display-your-website-content-in-multiple-languages), you now:
  * understand the basic terminology related to multilingual support in Xperience by Kentico.
  * have your home page and cookie consents in two language versions: English and Spanish.


Continue with this series and learn how to handle navigating and working with URLs for multilingual pages. To tie everything together, you will implement a simple language selector control for the visitor, similar to the gif below:
[![Screen recording gif of a working language selector](/docsassets/guides/implement-language-selector-for-your-website/language-select.gif)](/docsassets/guides/implement-language-selector-for-your-website/language-select.gif)
## Prerequisites
This article is a direct continuation of [the first part](/guides/development/multilingual/display-your-website-content-in-multiple-languages) in this mini-series. However, it is not mandatory that you have completed it, as long as you are familiar with the concepts mentioned above. 
To follow along, you should have the following:
  * running instance of **Xperience by Kentico version 30.11.1 or higher** (for guidance, follow [Install a specific version of Xperience by Kentico](/guides/development/get-started/install-a-specific-version-of-xperience-by-kentico))
  * **a website channel** set up with at least one page displaying a content item stored in the Content hub (for guidance, follow [our Kickstart series](/guides/development/developer-kickstart/xperience-by-kentico-overview))
  * **content items and pages existing in at least two different languages** (this first part of the Multilingual series uses [English and Spanish as an example](/guides/development/multilingual/display-your-website-content-in-multiple-languages))
  * some version of **routing implemented** in your application (a link navigating from one page to another is sufficient)


Alternatively, feel free to take a look at the [finished branch of our Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) to see the completed implementation of both multilingual URL handling and a language selector. All the code samples below are sourced from the repository.
## Handle URLs and routing
Setting up multiple languages in Xperience by Kentico affects URL generation for web pages. For any non-primary language, [the URL has to contain the language code](/documentation/developers-and-admins/development/routing/content-tree-based-routing#multilingual-urls) (e.g., _~/es/page_).
As a developer, you must ensure the links across your site work properly, preserving the language context, as visitors navigate your site.
If you have followed along with the [first part of this mini-series](/guides/development/multilingual/display-your-website-content-in-multiple-languages) or are running the app from the [finished branch in our repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished), your site contains a _Tracking consent banner_ and a _Cookie policy page_ that look similar to the screenshot below:
[![Screenshot of the cookie policy page](/docsassets/guides/implement-language-selector-for-your-website/cookies-consents.png)](/docsassets/guides/implement-language-selector-for-your-website/cookies-consents.png)
Let’s look at an example of routing on the [banner](/guides/development/data-protection/build-a-tracking-consent-banner). It includes a **Configure cookies** link. This link is supposed to navigate the visitor to the _Cookie policy page_ so they can set their preferences with more granularity. However, right now, it always navigates the visitor to the _Cookie policy page_ in the **primary language** , regardless of previous context. Navigate to the _TrackingConsent.cshtml_ file to find out why:
In the _TrackingConsent_ view, the _Configure cookies_ anchor tag points to a URL composed of `BaseUrl` and the relative `RedirectUrl`.
cshtml
**TrackingConsent.cshtml**
Copy
```
...
<a href="@string.Format("{0}{1}", Model.BaseUrl, Model.RedirectUrl)" class="xpcookiebanner__cta" data-tracking-code="CookieBar_More">
    @Model.ConfigureMessage
</a>
...
```

Look inside the _TrackingConsentViewComponent.cs_ and notice that the `BaseUrl` property is populated by the `GetBaseUrl` method of `HttpRequestService`. This is a problem, as the `GetBaseUrl` method returns the site’s base URL **without a language codename**.
You need to define a new method that will consider the current request’s language and add relevant language code to the base URL.
To handle [preserving language thread in routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing#preserving-thread-language-across-routing-modes), Xperience by Kentico provides an option to define a **custom language name key** in the [RouteValueDictionary](https://learn.microsoft.com/en-us/dotnet/api/system.web.routing.routevaluedictionary) object of the request:
  1. Define a new constant in _TrainingGuides.Web/Features/Shared/Helpers/ApplicationConstants.cs_.
C#
**ApplicationConstants.cs**
Copy
```
namespace TrainingGuides.Web.Features.Shared.Helpers;

internal static class ApplicationConstants
{
    public const string LANGUAGE_KEY = "language";
}
```

  2. In your Program.cs file, locate the `UseWebPageRouting` method call that [enables content tree-based routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing/enable-content-tree-based-routing). Pass in your custom language key:
C#
**Program.cs**
Copy
```
...
builder.Services.AddKentico(async features =>
{
    ...
    // Pass in the constant as a custom LanguageNameRouteValuesKey using WebPageRoutingOptions object.
    features.UseWebPageRouting(new WebPageRoutingOptions {
        LanguageNameRouteValuesKey = ApplicationConstants.LANGUAGE_KEY
    });
});
...
```

A custom language code name in the request can come in handy in several scenarios. In the next step of this section, we utilize it to manually extract the language from the request URL.
Another example would be invoking a custom action from the context of the page served by the router.
[Read more about LanguageNameRouteValuesKey and its usage in our documentation](/documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing#preserving-thread-language-across-routing-modes).
  3. Add a new `GetBaseUrlWithLanguage` public method declaration into the _IHttpRequestService_ interface (in _TrainingGuides.Web/Features/Shared/Services_).
  4. Implement the `GetBaseUrlWithLanguage` method in the _HttpRequestService_ class:
C#
**HttpRequestService.cs**
Copy
```
...
private HttpRequest RetrieveCurrentRequest() => httpContextAccessor?.HttpContext?.Request
        ?? throw new NullReferenceException("Unable to retrieve current request context.");
...
/// <summary>
/// Retrieves Base URL from the current request context. If current site is in a language variant, returns language with the base URL as well
/// </summary>
/// <returns>The base URL in current language variant. (e.g. website.com or website.com/es)</returns>
public string GetBaseUrlWithLanguage()
{
    // Use httpContextAccessor (already injected in the Service using DI) to access current request context.
    var currentRequest = RetrieveCurrentRequest();

    //Access the language of the request using the LANGUAGE_KEY constant defined in steps 1. and 2.
    string language = (string?)currentRequest.RouteValues[ApplicationConstants.LANGUAGE_KEY] ?? string.Empty;

    // Parse the current request URL to find out if it contains the language code.
    // No language code in the URL means the request language is the primary language.
    var webPageUrlPathList = ((string?)currentRequest.RouteValues[WEB_PAGE_URL_PATHS])?.Split('/').ToList() ?? [];
    bool notPrimaryLanguage = webPageUrlPathList.Contains(language);

    // Call existing private GetBaseUrl method to get the base URL.
    // If the language of the current request is not primary, add language code to the URL.
    var url = new UriBuilder(GetBaseUrl(currentRequest))
    {
        Path = notPrimaryLanguage
            ? $"/{language}"
            : string.Empty
    };

    return url.ToString();
}
...
```

Notice that our example appends the language to the base URL only when it does not equal the primary language.
If you navigate to a URL with a language code of the primary language, the system will NOT redirect you to a URL without a language code. Instead, it returns an [HTTP 404 (Not found)](https://www.rfc-editor.org/rfc/rfc9110.html#name-404-not-found) error.
For example, if the primary language of your channel is English, _http://localhost:53415/en/page_ will return [HTTP 404](https://www.rfc-editor.org/rfc/rfc9110.html#name-404-not-found).
The `GetBaseUrlWithLanguage` method in this code sample depends on `Kentico.WebPageUrlPaths` in the route values, and may not work outside the context of Xperience’s [Content tree-based router](/documentation/developers-and-admins/development/routing/content-tree-based-routing).
You can find an example of an overload that operates outside this context in the [member registration widget guide](/guides/development/members/implement-member-registration#assemble-a-url-for-the-form).
  5. Now, let’s propagate a new property to the view.  
Navigate to the _~/Features/DataProtection/ViewComponents/TrackingConsent_ folder and add a new `BaseUrlWithLanguage` property into `TrackingConsentViewModel`.
C#
**TrackingConsentViewModel.cs**
Copy
```
using Microsoft.AspNetCore.Html;
namespace TrainingGuides.Web.Features.DataProtection.ViewComponents.TrackingConsent;

public class TrackingConsentViewModel
{
    public bool CookieAccepted { get; set; }
    public bool IsAgreed { get; set; }
    public HtmlString CookieHeaderHtml { get; set; } = HtmlString.Empty;
    public HtmlString CookieMessageHtml { get; set; } = HtmlString.Empty;
    public string AcceptMessage { get; set; } = string.Empty;
    public string RedirectUrl { get; set; } = string.Empty;
    public string ConfigureMessage { get; set; } = string.Empty;
    public string ConsentMapping { get; set; } = string.Empty;
    public string BaseUrl { get; set; } = string.Empty;
    // new property to hold base URL plus language, if applicable
    public string BaseUrlWithLanguage { get; set; } = string.Empty;
}
```

Keep the original `BaseUrl` property. The Tracking consent banner still needs this one to POST to the `cookiebannersubmit` endpoint when the visitor clicks the submit button.
  6. Populate `BaseUrlWithLanguage` in the _TrackingConsentViewComponent_.
C#
**TrackingConsentViewComponent.cs**
Copy
```
...
public async Task<IViewComponentResult> InvokeAsync()
{
    ...
    if (consents.Count() > 0)
    {
        ...
        var consentModel = new TrackingConsentViewModel
        {
            ...
            BaseUrl = httpRequestService.GetBaseUrl(),

            // Populate new property to hold base URL with language (if applicable).
            BaseUrlWithLanguage = httpRequestService.GetBaseUrlWithLanguage()
        };

        // Display a view with tracking consent information and actions
        return View("~/Features/DataProtection/ViewComponents/TrackingConsent/TrackingConsent.cshtml", consentModel);
    }
    return Content(string.Empty);
}
...
```

  7. Finally, change the property used to populate the link in the  _TrackingConsent_ view from `Model.BaseUrl` to `Model.BaseUrlWithLanguage`.
cshtml
**TrackingConsent.cshtml**
Copy
```
...
<a href="@string.Format("{0}{1}", Model.BaseUrlWithLanguage, Model.RedirectUrl)" class="xpcookiebanner__cta" data-tracking-code="CookieBar_More">
    @Model.ConfigureMessage
</a>
...
```

  8. Build and run your solution. When visitors click the **Configure cookies** link, they stay within the context of the current language.
Your browser does not support the video tag.


## Add a language selector
If you followed along up to this point, your site visitors are now able to view and navigate your content in two different languages.
The last step is to tie everything together and allow the visitor to switch languages using a simple UI control (instead of changing the URL in the browser navigation bar).
[![Screenshot of the language selector dropdown](/docsassets/guides/implement-language-selector-for-your-website/dropdown.png)](/docsassets/guides/implement-language-selector-for-your-website/dropdown.png)
Because advanced styling and UI appearance are out of the scope of this series, let’s just use a simple Bootstrap dropdown select control:
  * The control will display the name of the current selected language (preferred language by default).
  * After the visitor opens the dropdown, it will show a list of available languages to choose from (all languages defined in the Xperience by Kentico instance).
  * When the visitor selects a language, the webpage will reload the current page to show its content in the selected language.


The main branch of our [Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides) already includes Bootstrap. If your project does not have it, add a reference to your __Layout.cshtml_ file. You can point to an [online resource or store it locally](https://getbootstrap.com/docs/5.3/getting-started/download/).
cshtml
**_Layout.cshtml**
Copy
```
...
<page-builder-scripts/>

<!-- add bootstrap -->
<script src="~/assets/bootstrap/js/bootstrap.bundle.min.js"></script>
...
```

### Create a Language dropdown view component
  1. Create and navigate to a new _TrainingGuides.Web/Features/Languages_ folder.
  2. Define the View model as follows:
C#
**LanguageDropdownViewModel.cs**
Copy
```
namespace TrainingGuides.Web.Features.Languages;

// Represents an item in the list of languages to select from (e.g., Spanish)
public class LanguageViewModel
{
    // Language name the visitor will see, e.g., Español
    public string DisplayName { get; set; } = string.Empty;
    // URL of the current page, including the language code (e.g., http://localhost:1234/es/cookie-policy)
    public string CurrentPageUrl { get; set; } = string.Empty;
}

// View model for the dropdown select control
public class LanguageDropdownViewModel
{
    // List of available languages stored as key-value pairs.
    // Key = string two-letter language code (e.g., es)
    // Value = instance of LanguageViewModel
    public Dictionary<string, LanguageViewModel> AvailableLanguages { get; set; } = [];
    // The two-letter language code of selected language (e.g., es)
    public string SelectedLanguage { get; set; } = string.Empty;
}
```

  3. Define the View:
cshtml
**LanguageDropdown.cshtml**
Copy
```
@using TrainingGuides.Web.Features.Languages

@model LanguageDropdownViewModel

<div class="dropdown">
    <button class="btn btn-secondary dropdown-toggle" type="button" id="dropdownMenuButton" data-bs-toggle="dropdown" aria-expanded="false">
        <!-- Render the selected language display name in the main button -->
        @Model.AvailableLanguages[Model.SelectedLanguage].DisplayName
    </button>
    <!-- dynamically create a menu of available languages for visitor to choose from -->
    <ul class="dropdown-menu" aria-labelledby="dropdownMenuButton">
        @foreach (KeyValuePair<string, LanguageViewModel> language in Model.AvailableLanguages)
        {
            <!-- Each menu item contains a language-specific link to the current page. -->
            <!-- On click, the page content will reload in the selected language. -->
            <li><a class="dropdown-item" href="@language.Value.CurrentPageUrl">@language.Value.DisplayName</a></li>
        }
    </ul>
</div>
```

  4. Create a method that generates a language-specific URL of the current page.
    1. Implement a new public method `GetCurrentPageUrlForLanguage` in _HttpRequestService.cs_ in _TrainingGuides.Web/Features/Shared/Services)_. Remember also to add a declaration in IHttpRequestService.
The method will utilize two new services you need to inject using dependency injection:
     * [`IWebPageDataContextRetriever`](/documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing#implement-routing) (to retrieve data about the current page)
     * [`IWebPageUrlRetriever`](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls) (to generate a language-specific URL of a page).
C#
**HttpRequestService.cs**
Copy
```
...
public class HttpRequestService : IHttpRequestService
{
    private readonly IHttpContextAccessor httpContextAccessor;
    private readonly IWebPageDataContextRetriever webPageDataContextRetriever;
    private readonly IWebPageUrlRetriever webPageUrlRetriever;

    public HttpRequestService(IHttpContextAccessor httpContextAccessor,
    IWebPageDataContextRetriever webPageDataContextRetriever,
    IWebPageUrlRetriever webPageUrlRetriever)
    {
        this.httpContextAccessor = httpContextAccessor;
        this.webPageDataContextRetriever = webPageDataContextRetriever;
        this.webPageUrlRetriever = webPageUrlRetriever;
    }
    ...

    /// <summary>
    /// Retrieves URL of the currently displayed page for a specific language
    /// </summary>
    /// <param>two-letter language code (e.g., "es" for Spanish, "en" for English)</param>
    /// <returns>Language specific URL of the current page (e.g. website.com/es/page)</returns>
    public async Task<string> GetCurrentPageUrlForLanguage(string language)
    {
        var currentPage = webPageDataContextRetriever.Retrieve().WebPage;
        var url = await webPageUrlRetriever.Retrieve(currentPage.WebPageItemID, language);
        return url.RelativePath;
    }
}
```

The code samples in this series rely on a [decorated version](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/main/src/TrainingGuides.Web/Features/Shared/Services/TrainingGuidesWebPageUrlRetriever.cs) of `IWebPageUrlRetriever` that includes exception handling.
If you do not plan to use a similar customization, make sure to handle errors that the `Retrieve` method may throw when it cannot find a page. 
  5. Define the logic in ViewComponent.
C#
**LanguageDropdownViewComponent.cs**
Copy
```
using Microsoft.AspNetCore.Mvc;
using CMS.ContentEngine;
using CMS.DataEngine;
using Kentico.Content.Web.Mvc.Routing;
using TrainingGuides.Web.Features.Shared.Services;

namespace TrainingGuides.Web.Features.Languages;
public class LanguageDropdownViewComponent : ViewComponent
{
    // Inject necessary services.
    private readonly IInfoProvider<ContentLanguageInfo> contentLanguageInfoProvider;
    private readonly IPreferredLanguageRetriever preferredLanguageRetriever;
    private readonly IHttpRequestService httpRequestService;


    public LanguageDropdownViewComponent(
        IInfoProvider<ContentLanguageInfo> contentLanguageInfoProvider,
        IPreferredLanguageRetriever preferredLanguageRetriever,
        IHttpRequestService httpRequestService)
    {
        this.contentLanguageInfoProvider = contentLanguageInfoProvider;
        this.preferredLanguageRetriever = preferredLanguageRetriever;
        this.httpRequestService = httpRequestService;
    }

    // Create a new LanguageDropdownViewModel and populate it with language data to display.
    public async Task<LanguageDropdownViewModel> GetLanguageMenu()
    {
        // Retrieve all languages that are defined in the Xperience by Kentico instance.
        var allLanguages = contentLanguageInfoProvider.Get().ToList();

        Dictionary<string, LanguageViewModel> languagesDictionary = [];

        // Create dictionary of AvailableLanguages by iterating through the list fo lanuages from Xperience
        foreach (var language in allLanguages)
        {
            string languageCode = language.ContentLanguageName;
            string languageDisplayName = language.ContentLanguageDisplayName;
            // Call the method defined in previous step, to generate language-specific URL of the current page
            string url = await httpRequestService.GetCurrentPageUrlForLanguage(languageCode);

            languagesDictionary.Add(
                languageCode,
                new LanguageViewModel() { DisplayName = languageDisplayName, CurrentPageUrl = url });
        };

        return new LanguageDropdownViewModel()
        {
            AvailableLanguages = languagesDictionary,
            SelectedLanguage = preferredLanguageRetriever.Get()
        };
    }

    public async Task<IViewComponentResult> InvokeAsync()
    {
        var model = await GetLanguageMenu();
        return View("~/Features/Languages/LanguageDropdown.cshtml", model);
    }
}
```



### Display language dropdown in the Header
Add a tag helper to the _TrainingGuides.Web/Features/Header/Header.cshtml_ to invoke the view component of the new _LanguageDropdown_.
cshtml
**Header.cshtml**
Copy
```
@model TrainingGuides.Web.Features.Header.HeaderViewModel

<header class="c-header sticky-top">
    <nav class="navbar d-flex p-2 container">
        <div id="navbar-container">
            <h1>@Model.Heading</h1>
        </div>
        <!-- Render Language dropdown. -->
        <vc:language-dropdown />
    </nav>
</header>
```

Build and run your application. Your visitor can now toggle between English and Spanish versions of your website.
Your browser does not support the video tag. 
## What’s next
Congratulations! 🙂 Your website is now set up to support multiple languages. Play around and translate other pages and widgets or add new languages in your Xperience instance. If you have been working with our [Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished), and are looking for an exercise, try localizing the [Page-like widget](/guides/development/activities-and-marketing/log-custom-activities).
![]()
[]()[]()
