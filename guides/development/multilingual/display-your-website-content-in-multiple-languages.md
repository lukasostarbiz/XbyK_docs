---
source: https://docs.kentico.com/guides/development/multilingual/display-your-website-content-in-multiple-languages
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Multilingual](/guides/development/multilingual)
  * Display your website content in multiple languages 


# Display your website content in multiple languages
Offering your website content in multiple languages is an important step in reaching a broader audience. To provide a better and more inclusive user experience for your visitors, Xperience by Kentico supports localization and language variants.
Preparing different language versions of your content is a task for the editors. However, development effort is required to properly set up different language support in your website.
**Multilingual content series**
**This is the first part of the[Set up multilingual](/guides/development/multilingual) mini-series.** Using a simple concrete example, the mini-series demonstrates best practices and necessary steps you need to perform in order to present your website content in multiple languages in a user-friendly way.
In this section, you will learn:
  * the basic terminology related to multilingual support in Xperience by Kentico.
  * how to display your home page and cookie consents in two language versions: English and Spanish.


[![screen recording gif that demonstrates language switching by URL](/docsassets/guides/display-your-website-content-in-multiple-languages/Switch-language-manual.gif)](/docsassets/guides/display-your-website-content-in-multiple-languages/Switch-language-manual.gif)
This section focuses on the **developer tasks** of the process, but it also references actions editors will typically take. Feel free to follow those steps as well to see and enjoy the fruits of your effort at the end. 🙂
## Prerequisites
To follow along, you should have the following:
  * a running instance of **Xperience by Kentico version 30.11.1 or higher** (for guidance, follow [Install a specific version of Xperience by Kentico](/guides/development/get-started/install-a-specific-version-of-xperience-by-kentico))
  * **a website channel** set up with at least one **page displaying a content item** stored in the Content hub (for guidance, follow [our Kickstart series](/guides/development/developer-kickstart/xperience-by-kentico-overview))


The main branch of [this repository](https://github.com/Kentico/xperience-by-kentico-training-guides) is a good starting point that has all of the above set up. You will find the instructions to run in the README file.
Additionally, we assume you have followed [Create a cookie preferences widget guide](/guides/development/data-protection/create-a-cookie-preferences-widget) and [Build a tracking consent banner guide](/guides/development/data-protection/build-a-tracking-consent-banner) (or implemented your own cookies widget and tracking consent banner in a similar way.) The widget and tracking consent banner you will be working with should look like this:
[![screenshot of cookie consent page](/docsassets/guides/display-your-website-content-in-multiple-languages/cookies-consents.png)](/docsassets/guides/display-your-website-content-in-multiple-languages/cookies-consents.png)
If you prefer to see the finished implementation of multilingual right away, take a look at the [finished branch of our repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished). It contains both the cookie widget and tracking consent banner.
## Understand languages in Xperience by Kentico
Before diving in, let’s get familiar with the basic concepts of working with [languages in Xperience by Kentico](/documentation/developers-and-admins/configuration/languages).
Languages are defined in the administration interface using the **Languages** application.
When more than one language is defined, [editors can create **multiple language variants**](/documentation/business-users/content-hub/content-items#translate-content-items) of [website channel pages](/documentation/business-users/website-content), [content items](/documentation/business-users/content-hub), and [consent texts](/documentation/developers-and-admins/data-protection/consent-management#create-consents) (one variant per language).
One language must always be set as a [**default language**](/documentation/developers-and-admins/configuration/languages#set-up-a-new-language).
[![Default language in Xperience by Kentico](/docsassets/guides/display-your-website-content-in-multiple-languages/default-language.png)](/docsassets/guides/display-your-website-content-in-multiple-languages/default-language.png)
For example, if the default language is English, any content items editors create are, by default, English language variants. Editors can create a different language variant by [manually picking a different language](/documentation/business-users/content-hub/content-items).
[A fresh install of Xperience by Kentico boilerplate project](/guides/development/get-started/install-a-specific-version-of-xperience-by-kentico), comes with English pre-set as a default language. 
Each language can be assigned a [**fallback language**](/documentation/developers-and-admins/configuration/languages#language-fallbacks). If you set up a fallback language and try to retrieve content in a language it is not translated into, the content will come back in the fallback language instead.
Every [website channel](/documentation/developers-and-admins/configuration/website-channel-management) has a **primary language** (typically assigned at the time of channel creation). This is a “default” language for the channel. When a visitor navigates to your website without specifying the language code in the URL (e.g., _baseURL/page_), they will see the page content in the primary language. Navigating to _baseURL/ <language_code_name>/page_ will return the page content in a specific language. We talk more about [multilingual URLs](/documentation/developers-and-admins/development/routing/content-tree-based-routing#multilingual-urls) in [the next part of this mini-series](/guides/development/multilingual/implement-language-selector-for-your-website).
**Multilingual in multichannel setup**
Languages are global and exist above website channels. **All website channels are automatically available in all defined languages**. At this time, there is no way (out of the box) to configure a channel to be available only in a subset of languages.
This means that you can get in a situation where one is able to retrieve your website pages in a language they are not (and are not meant to be) translated into.
_For example, you have three languages defined in your solution: English, Spanish (with a fallback to English), and French (with a fallback to English). Your instance of Xperience contains two website channels, each translated into two language versions:_
  1. _Channel A in English and Spanish_
  2. _Channel B in English and French_


_Even though by design, pages of Channel A are not supposed to be available in French, when you request https://channelA/fr, the system will still return[200 OK](https://www.rfc-editor.org/rfc/rfc9110.html#name-200-ok) because French exists globally (content will come back in the fallback language, English)._
This is a behavior to be mindful of when you work with multichannel and multilingual solutions. The best way of handling it will depend on your implementation, need and preference and is out of the scope of this guide. 
**Editor’s task (optional): Add Spanish language with a fallback to English**
Sign in to the administration interface to experience the theory in practice. If you have been working off the main branch of our Training guides repository, the only language in your Xperience instance is English at the moment.
  1. [Follow the documentation](/documentation/developers-and-admins/configuration/languages#set-up-a-new-language) to create a new language. Call it **Español** , with language code **es** , do NOT and set the fallback language yet.
  2. Try to navigate to different pages of your website, adding the code _/es_ to the base URL (e.g., _http://localhost:53415/es_ or _http://localhost:53415/es/cookie-policy_). The system will return a [404 Not Found](https://www.rfc-editor.org/rfc/rfc9110.html#name-404-not-found) error each time because there is no Spanish version of your content to display.
  3. Now, go back to **Languages** in the administration interface and set the fallback language to **English.** Notice that you can now see your pages, and the content comes back in the primary language - English.


## Implement multilingual support for your site
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


You can see this `IStringLocalizer` approach applied to the cookie preferences widget and the tracking consent banner in [the finished branch of our Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished). We’ll use this approach to make some improvements [later in this series](/guides/development/multilingual/implement-language-selector-for-your-website):
  * [Cookie preferences widget](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/DataProtection/Widgets/CookiePreferences/CookiePreferencesWidgetViewComponent.cs)
  * [Tracking consent view component](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/DataProtection/ViewComponents/TrackingConsent/TrackingConsentViewComponent.cs)


Your website visitors can now view individual pages’ content in English (the default language) and Spanish (by typing ‘/es’ after the base URL in the browser navigation bar):
Your browser does not support the video tag. 
## What’s next
The [next part of this mini-series](/guides/development/multilingual/implement-language-selector-for-your-website) explains how to handle navigation between multilingual pages. You will also implement a simple language selector, enabling visitors to switch between languages in a more user-friendly way.
![]()
[]()[]()
