---
source: https://docs.kentico.com/modules/multilingual/add-language-selector
scrape_date: 2026-01-22
---

Module: Multilingual content
5 of 6 Pages
# Add a language selector
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
[ Previous page ](/modules/multilingual/handle-multilingual-urls)
5 of 6
[ Mark complete and continue ](/modules/multilingual/final)
![]()
[]()[]()
