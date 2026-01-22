---
source: https://docs.kentico.com/modules/multilingual/implement-multilingual-support
scrape_date: 2026-01-22
---

Module: Multilingual content
3 of 6 Pages
# Implement multilingual support for your site
Although creating the language versions of content is a task for editors, as a developer, you must take steps to ensure a consistent experience for your website visitors.
### Retrieve preferred language
The first important building block is the **preferred language retriever.** Its `Get()` method returns the language of the request (if defined) or the primary language of the current website channel. You can simply inject `IPreferredLanguageRetriever` anywhere you need, using dependency injection. For example:
C#
Copy
```
using Kentico.Content.Web.Mvc.Routing;

public class MyClass
{
    private readonly IPreferredLanguageRetriever preferredLanguageRetriever;

    public MyClass(IPreferredLanguageRetriever preferredLanguageRetriever)
    {
        this.preferredLanguageRetriever = preferredLanguageRetriever;
    }

    public string DoThings()
    {
        // Retrieve the preferred language
        var currentLanguage = preferredLanguageRetriever.Get();
        ...
    }
}
```

The value returned is a **Code name** that has been defined for the language in the Xperience administration interface. 
[![Code name of a language in Xperience administration](/docsassets/guides/display-your-website-content-in-multiple-languages/code-name.png)](/docsassets/guides/display-your-website-content-in-multiple-languages/code-name.png)
### Retrieve localized content items
Based on whether you are using the [ContentRetriever API](/documentation/developers-and-admins/api/content-item-api/content-retriever-api) or the [Content item query API](/documentation/developers-and-admins/api/content-item-api/content-item-query-api), to retrieve a specific language variant of a content item, you:
  1. Pass the language **Code name** (see picture above) as the `languageName` parameter to the ContentRetriever’s extension method (ContentRetriever API).
  2. Call the `InLanguage` extension method of [ContentItemQueryBuilder](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query), passing it a language **Code name** as a parameter (Content item query API).


By passing in the [preferred language](#retrieve-preferred-language), you will get your content item(s) in a language variant consistent with your current context.
Take a look at the place in your code where you call the `ContentRetriever` or the `ContentItemQueryBuilder`. If you have been working off [the main branch of our Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/main) or are following along looking at [the finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished), one example is located in the _TrainingGuides.Web/Features/Shared/Services/ContentItemRetrieverService.cs_. 
C#
**ContentItemRetrieverService.cs (main branch)**
Copy
```
...
// retrieving content using ContentRetriever API
public async Task<T?> RetrieveCurrentPage<T>(
        int depth = 1,
        string? languageName = null)
        where T : IWebPageFieldsSource, new()
{
    var parameters = new RetrieveCurrentPageParameters
    {
        LinkedItemsMaxLevel = depth,
        LanguageName = languageName ?? preferredLanguageRetriever.Get(),
        IsForPreview = webSiteChannelContext.IsPreview
    };

    return await contentRetriever.RetrieveCurrentPage<T>(parameters);
}
...
// retrieving content using Content item query API
private async Task<IEnumerable<IWebPageFieldsSource>> RetrieveWebPages(
    Action<ContentQueryParameters> parameters,
    int depth,
    string? languageName = null)
{
    var builder = new ContentItemQueryBuilder()
        .ForContentTypes(query => query
        .WithLinkedItems(depth, options => options.IncludeWebPageData(true))
        .ForWebsite(webSiteChannelContext.WebsiteChannelName))
    .Parameters(parameters)
    .InLanguage(languageName ?? preferredLanguageRetriever.Get());

    var queryExecutorOptions = new ContentQueryExecutionOptions
    {
        ForPreview = webSiteChannelContext.IsPreview
    };

    return await contentQueryExecutor.GetMappedResult<IWebPageFieldsSource>(builder, queryExecutorOptions);
}

public async Task<IWebPageFieldsSource?> RetrieveWebPageById(
    int webPageItemId,
    int depth = 2,
    string? languageName = null)
{
    var pages = await RetrieveWebPages(parameters =>
        {
            parameters.Where(where => where.WhereEquals(nameof(WebPageFields.WebPageItemID), webPageItemId));
        },
        depth,
        languageName ?? preferredLanguageRetriever.Get());

    return pages.FirstOrDefault();
}
...
 



 


```

**Referenced content items**
When retrieving a deeper structure of content items (e.g., a page that references another content item, which also references another content item), the call will return the whole structure of content items localized.
**Editor’s task (optional): Display Home page in Spanish**
Now, if your implementation is correct, your application is able to retrieve and display content items in different languages. You can test this, for example, by creating a new language version of one of your pages in the Xperience administration.
  1. Navigate to **Channels → Training guides pages** and [translate the Home page into the new language](/documentation/business-users/website-content/translate-pages).
  2. Remember to [translate and publish the reusable content item](/documentation/business-users/content-hub/content-items#translate-content-items) referenced by the Home page (Landing content) as well.
     * _Welcome to Training guides!_ in Spanish is _¡Bienvenidos a las guías de formación!_.
     * _Hello world!_ translates into _¡Hola Mundo!_.
  3. Visit the root of your website and add _/es_ to the end of the URL (e.g., _http://localhost:53415/es_). You should see your home page content in Spanish, like in the screenshot below.  
[![Image of the home page displaying Spanish content](/docsassets/guides/display-your-website-content-in-multiple-languages/homepage-es.png)](/docsassets/guides/display-your-website-content-in-multiple-languages/homepage-es.png)


### Retrieve localized consent texts
Localizing consent texts looks very similar to [localizing content items](#retrieve-localized-content-items) - you utilize the `IPreferredLanguageRetriever`. Simply pass the **preferred language** to the `GetConsentTextAsync` method as a parameter:
C#
Copy
```
await consent.GetConsentTextAsync(preferredLanguageRetriever.Get()))
```

A good example of this in practice is a cookie preferences widget or a tracking consent banner. If you have previously followed our [Create a cookie preferences widget](/guides/development/data-protection/create-a-cookie-preferences-widget) and [Build a tracking consent banner](/guides/development/data-protection/build-a-tracking-consent-banner) guides, your code likely already handles multilingual situations, but let’s take a closer look.
  1. **Cookie preferences widget**
Navigate to _CookiePreferencesWidgetViewComponent.cs_ in _TrainingGuides.Web/Features/DataProtection/Widgets/CookiePreferences:_
C#
**CookiePreferencesWidgetViewComponent.cs**
Copy
```
...
public async Task<ViewViewComponentResult> InvokeAsync(CookiePreferencesWidgetProperties properties)
{
    ...
    return View("~/Features/DataProtection/Widgets/CookiePreferences/CookiePreferencesWidget.cshtml", new CookiePreferencesWidgetViewModel
    {
        // In this example Essential header and description are defined in the cookies widget (not as a separate consent in Data protection).
        // The correct language version will come back together with the page content.
        EssentialHeader = properties.EssentialHeader,
        EssentialDescription = properties.EssentialDescription,

        PreferenceHeader = preferenceCookiesConsent.ConsentDisplayName ?? CONSENT_MISSING_HEADER,
        // Retrieve consent text in preferred language and display its FullText property value as PreferenceDescriptionHtml
        PreferenceDescriptionHtml = new HtmlString((await preferenceCookiesConsent.GetConsentTextAsync(preferredLanguageRetriever.Get())).FullText) ?? consentMissingDescriptionHtml,

        AnalyticalHeader = analyticalCookiesConsent.ConsentDisplayName ?? CONSENT_MISSING_HEADER,
        // Retrieve consent text in preferred language and display its FullText property value as  AnalyticalDescriptionHtml
        AnalyticalDescriptionHtml = new HtmlString((await analyticalCookiesConsent.GetConsentTextAsync(preferredLanguageRetriever.Get())).FullText) ?? consentMissingDescriptionHtml,

        MarketingHeader = marketingCookiesConsent.ConsentDisplayName ?? CONSENT_MISSING_HEADER,
        // Retrieve consent text in preferred language and display its FullText property value as MarketingDescriptionHtml
        MarketingDescriptionHtml = new HtmlString((await marketingCookiesConsent.GetConsentTextAsync(preferredLanguageRetriever.Get())).FullText) ?? consentMissingDescriptionHtml,

        ...
    });
}
...
```

If you are showing the _ConsentDisplayName_ to the visitor (e.g., as a consent header), be advised, that _ConsentDisplayName_ does **not** support multiple language versions at this time. We recommend utilizing stringLocalizer and Shared resources in your .Net project to handle this, as you will see in [the next section](#translate-other-application-texts).
See [this guide](/guides/development/data-protection/create-a-cookie-preferences-widget#WidgetViewComponent) to view the code in context.
  2. **Tracking consent banner**
Navigate to _TrackingConsentViewComponent.cs_ in _TrainingGuides.Web/Features/DataProtection/ViewComponents/TrackingConsent:_
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
        var currentContactInfo = ContactManagementContext.GetCurrentContact(false);

        string text = "<ul>";
        List<string> codenames = [];
        bool isAgreed = false;
        foreach (var consent in consents)
        {
            ...

            // When iterating through the list of consents to render a list of short texts:
            // Retrieve consent text in preferred language and access its ShortText property
            text += $"<li>{(await consent.GetConsentTextAsync(preferredLanguageRetriever.Get())).ShortText}</li>";

            ...
        }
        text += "</ul>";

        ...
        // Displays a view with tracking consent information and actions
        return View("~/Features/DataProtection/ViewComponents/TrackingConsent/TrackingConsent.cshtml", consentModel);
    }

    return Content(string.Empty);
}
...
```

Feel free to look over this code [in the context of its guide](/guides/development/data-protection/build-a-tracking-consent-banner#BannerViewComponent).


**Editor’s task (optional): Display cookie texts in Spanish**
  1. Navigate to **Data protection** in your Xperience administration interface and follow [the documentation to translate your consent texts](/documentation/developers-and-admins/data-protection/consent-management#translate-consents-to-other-languages) into Spanish (Español).
  2. Navigate to **Training guides pages** website channel (**Channels → Training guides pages)** , [translate the Cookie policy page](/documentation/business-users/website-content/translate-pages), including the Cookie preferences widget, and publish.
  3. Visit the Spanish version of your Cookie policy page by inserting the ‘es’ language code after the base URL (e.g., _http://localhost:53415/es/cookie-policy_). You should see your translated Cookie preferences banner content.  
[![Image of the cookie policy page displaying consent texts in Spanish, but not consent names](/docsassets/guides/display-your-website-content-in-multiple-languages/cookie-policy-spanish-first-step.png)](/docsassets/guides/display-your-website-content-in-multiple-languages/cookie-policy-spanish-first-step.png)
  4. Open your site in a new browser incognito window, appending _/es_ to the base URL. You should see your Tracking consent banner, looking similar to the picture below:
[![Screenshot of the tracking consent banner showing consent texts in Spanish, but not other texts](/docsassets/guides/display-your-website-content-in-multiple-languages/tracking-consent_half-done.png)](/docsassets/guides/display-your-website-content-in-multiple-languages/tracking-consent_half-done.png)


### Translate other application texts
Your application can now display localized consent texts. However, there are still some visitor-facing strings in your application that are not part of content items or consents. For example, the title in the header, the texts in the footer, cookie headers, or the **Configure cookies** link in the Tracking consent banner.
For these hardcoded application strings, you can use standard ASP.NET Core localization features. In this example, we will use [IStringLocalizer](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/localization/make-content-localizable?view=aspnetcore-8.0#istringlocalizer) and [Shared resources](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/localization/make-content-localizable?view=aspnetcore-8.0#shared-resources).
If you are following along and working off the [Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides), you can see the _TrainingGuides.Web_ project already contains a _Resources_ folder with a _SharedResources_ class. Let’s translate your texts into Spanish. Start with the “Training guides” title in the Header view component. 
  1. Locate _HeaderViewComponent.cs_ file under _TrainingGuides.Web/Features/Header_.
  2. Inject _IStringLocalizer_ using constructor injection and translate the `Heading` property as you see in the code below:
C#
**HeaderViewComponent.cs**
Copy
```
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Localization;

namespace TrainingGuides.Web.Features.Header;
public class HeaderViewComponent : ViewComponent
{

    // Inject the IStringLocalizer passing in the SharedResources type.
    private readonly IStringLocalizer<SharedResources> stringLocalizer;
    public HeaderViewComponent(IStringLocalizer<SharedResources> stringLocalizer)
    {
        this.stringLocalizer = stringLocalizer;
    }

    public IViewComponentResult Invoke()
    {
        var model = new HeaderViewModel()
        {
            // Localize the "Training guides" string.
            Heading = stringLocalizer["Training guides"]
        };
        return View("~/Features/Header/Header.cshtml", model);
    }
}
```

  3. In the _Resources_ folder, create a _SharedResources.es.resx_ file.
It is a good practice to use the two-letter ISO language name, based on [IETF or one of the ISO-639 standards](https://www.venea.net/web/culture_code) - “es” for Spanish.
The example implementation in this series requires the language name to match the **Code name** defined for your language in Xperience administration.
`IStringLocalizer` doesn’t require storing the default language strings in a resource file. Simply call the localizer on the default strings (like in the code sample above).
While the older approach of creating _SharedResources.resx_ file and defining a code name for each string is still valid and works, [Microsoft documentation](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/localization/make-content-localizable?view=aspnetcore-8.0#istringlocalizer) of the [IStringLocalizer](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/localization/make-content-localizable?view=aspnetcore-8.0#istringlocalizer) refers to the newer practice as preferred.
  4. Add the Spanish translation:
     * name: **Training guides** (the text in primary language)
     * value: **Guías de formación**
Visual Studio will allow you to modify the file in a table view. If you use a different editor (e.g., Visual Studio Code), you may have to manually edit the XML inside the file. To add a text translation, add a `<data>` tag inside the root:
XML
Copy
```
<data name="Training guides" xml:space="preserve">
    <value>Guías de formación</value>
</data>
```

  5. Find other strings in the project that are “hardcoded” in the application, rather than stored in Xperience by Kentico, and repeat the same steps.
To localize strings inside views, simply `@inject IStringLocalizer<SharedResources> StringLocalizer` in the view or in your __ViewImports.cshtml_ file. Then, you can call StringLocalizer just like in the example above. For instance, `@StringLocalizer["Xperience by Kentico training guide samples."]` to translate one of the footer texts.
Here is a [link to the full _SharedResources.es.resx_ file in our repository](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/2d03b90663e2853d23895688d25f06fae2719d7d/src/TrainingGuides.Web/Resources/SharedResources.es.resx), for your reference. It contains texts from _TrainingGuides.Web_ project translated into Spanish.


You can see this `IStringLocalizer` approach applied to the cookie preferences widget and the tracking consent banner in [the finished branch of our Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished). We’ll use this approach to make some improvements [later in this series](/modules/multilingual/handle-multilingual-urls):
  * [Cookie preferences widget](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/DataProtection/Widgets/CookiePreferences/CookiePreferencesWidgetViewComponent.cs)
  * [Tracking consent view component](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/DataProtection/ViewComponents/TrackingConsent/TrackingConsentViewComponent.cs)


Your website visitors can now view individual pages’ content in English (the default language) and Spanish (by typing ‘/es’ after the base URL in the browser navigation bar):
Your browser does not support the video tag. 
[ Previous page ](/modules/multilingual/explore-languages-in-xperience)
3 of 6
[ Mark complete and continue ](/modules/multilingual/handle-multilingual-urls)
![]()
[]()[]()
