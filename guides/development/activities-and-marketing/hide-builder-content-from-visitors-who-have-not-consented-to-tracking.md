---
source: https://docs.kentico.com/guides/development/activities-and-marketing/hide-builder-content-from-visitors-who-have-not-consented-to-tracking
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Activities and digital marketing](/guides/development/activities-and-marketing)
  * Hide builder content from visitors who have not consented to tracking 


# Hide builder content from visitors who have not consented to tracking
The [activity tracking documentation](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities#activity-logging-based-on-legitimate-interest) explains that Xperience logs the _Form submit_ activity automatically, even for users who have not consented to tracking. As the page notes, this means you need to ensure that usage of personal data collected through such activities falls under legitimate interest purposes.
To avoid potential legal complications, you may want to display certain forms only to users who have consented to tracking.
Let’s go over the process of creating a [Page Builder section](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) which hides its contents from users who have not given consent for tracking. The code you develop will add an alternate version of the existing _Form column section_ included in [the Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides).
## Before you start
The code samples here rely on the value of the [_CMSCookieLevel_ cookie](/documentation/developers-and-admins/data-protection/cookies#cookie-levels), and assumes this has been properly mapped to consent in your application. 
You can find an example of this in the [data protection series](/guides/development/data-protection) of training guides.
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](/documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


The examples in this guide require that you:
  * Have followed along with the samples from the _Consents_ section of the [Data protection series](/guides/development/data-protection) and the [Previous guides in the series](/guides/development/activities-and-marketing).


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Edit the view model
This form section needs to execute complex logic and change its behavior accordingly, so you should base it on a view component.
A view component will allow you to pass your own model to the section, rather than relying on the _ComponentViewModel <TPropertyModel>_ object Xperience provides for basic sections.
You need to create this view model before you can use it in the view component.
  1. Navigate to _TrainingGuides.Web/Features/Shared/Sections/FormColumn/FormColumnSectionViewModel.cs_.
  2. Add a _boolean_ field to represent whether the form section should render its content.
  3. Add three `string` fields to hold a message that will display when visitors have not consented to tracking, along with the text and URL of a link to the cookie policy page.


The `SectionAnchor` property corresponds to a property of the same name from the existing _FormColumnSectionProperties_ class. It lets editors specify the value of an `id` attribute added to the HTML `<section>` tag that wraps the contents of the _FormColumnSection._
C#
**FormColumnSectionViewModel.cs**
Copy
```
namespace TrainingGuides.Web.Features.Shared.Sections.FormColumn;

public class FormColumnSectionViewModel
{
    public bool ShowContents { get; set; }
    public string SectionAnchor { get; set; } = string.Empty;
    public string NoConsentMessage { get; set; } = string.Empty;
    public string NoConsentLinkText { get; set; } = string.Empty;
    public string NoConsentLinkUrl { get; set; } = string.Empty;
}
```

## Alter the view
Next, you need to account for the new `ShowContents` and `NoConsentHtml` properties of the `FormColumnSectionViewModel` in the view.
  1. Navigate to _TrainingGuides.Web/Features/Shared/Sections/FormColumn/FormColumnSection.cshtml_.
  2. Add a conditional that only shows the view’s contents if the `ShowContents` property of the model is `true`, and otherwise renders the no-consent message and link.


cshtml
**FormColumnSection.cshtml**
Copy
```
@using TrainingGuides.Web.Features.Shared.Sections.FormColumn
@model FormColumnSectionViewModel

@if(Model.ShowContents)
{
    var sectionAnchor = !string.IsNullOrWhiteSpace(Model.SectionAnchor) ? $"id={Model.SectionAnchor}" : "";

    <section class="c-section form" @sectionAnchor>
        <div class="container">
            <div class="row justify-content-center">
                <div class="col-lg-8">
                    <widget-zone />
                </div>
            </div>
        </div>
    </section>
}
else
{
    <div>
        @Model.NoConsentMessage
    </div>
    <div>
        <a href="@Model.NoConsentLinkUrl">@Model.NoConsentLinkText</a>
    </div>
}
```

## Handle the view components
### Create a new view component
[Earlier in this series](/guides/development/activities-and-marketing/enable-activity-tracking), you saw how you can use the value of the _CMSCookieLevel_ cookie to determine whether the current visitor is trackable. Then, you created a method called `CurrentContactCanBeTracked` to determine whether or not Xperience should track the current user.
If you arrived directly at this example or did not code along earlier in the series, you can simply add this method to your cookie consent service:
C#
**CookieConsentService.cs**
Copy
```
...
// Use dependency injection to populate this service.
private readonly ICurrentCookieLevelProvider cookieLevelProvider;
...
/// <summary>
/// Checks if the current contact's CMSCookieLevel is All (1000) or higher
/// </summary>
/// <returns>True if CMSCookieLevel is greater than or equal to 1000, false otherwise</returns>
public bool CurrentContactCanBeTracked() =>
    cookieLevelProvider.GetCurrentCookieLevel() >= 1000;
...
```

You can use this method in a new view component to create a view model and pass it along to the view.
Start by creating a new view component called _FormColumnSectionConsentViewComponent.cs_ to the _TrainingGuides.Web/Features/Shared/Sections/FormColumn_ folder.
  1. Use dependency injection to get instances of the following: 
     * `ICookieConsentService`
     * `IStringLocalizer<SharedResources>`
     * `IHttpRequestService`
     * `IHttpContextAccessor`
  2. Set the `Invoke` method to take a `ComponentViewModel<FormColumnSectionProperties>` object as a parameter. 
Xperience will supply this object automatically. You can use it to access properties configured by editors from the `Invoke` method. 
  3. Use `sectionProperties.Properties.SectionAnchor` to populate the corresponding view model property.
  4. Set the `ShowContents` property to `true` based on the `CurrentContactCanBeTracked` method of the cookie consent service, or if the site is in **Preview** or **Page Builder** mode.
  5. Set the message to display when the section is hidden, and configure the properties for the link prompting visitors to visit the _cookie policy_ page and consent to tracking.
  6. Define an identifier and use it to register the view component as a Page Builder section.


C#
**FormColumnSectionConsentViewComponent.cs**
Copy
```
using CMS.Base.Internal;
using Kentico.Content.Web.Mvc;
using Kentico.PageBuilder.Web.Mvc;
using Kentico.Web.Mvc;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Localization;
using TrainingGuides.Web.Features.DataProtection.Services;
using TrainingGuides.Web.Features.Shared.Sections.FormColumn;
using TrainingGuides.Web.Features.Shared.Services;

[assembly: RegisterSection(
    identifier: FormColumnSectionConsentViewComponent.IDENTIFIER,
    viewComponentType: typeof(FormColumnSectionConsentViewComponent),
    name: "Form column: Consent-based",
    propertiesType: typeof(FormColumnSectionProperties),
    Description = "Form column section that hides its contents if the visitor has not consented to tracking.",
    IconClass = "icon-square")]

namespace TrainingGuides.Web.Features.Shared.Sections.FormColumn;

public class FormColumnSectionConsentViewComponent : ViewComponent
{
    public const string IDENTIFIER = "TrainingGuides.FormColumnSectionConsent";

    private readonly ICookieConsentService cookieConsentService;
    private readonly IStringLocalizer<SharedResources> stringLocalizer;
    private readonly IHttpRequestService httpRequestService;
    private readonly IHttpContextAccessor httpContextAccessor;


    public FormColumnSectionConsentViewComponent(ICookieConsentService cookieConsentService,
        IStringLocalizer<SharedResources> stringLocalizer,
        IHttpRequestService httpRequestService,
        IHttpContextAccessor httpContextAccessor)
    {
        this.cookieConsentService = cookieConsentService;
        this.stringLocalizer = stringLocalizer;
        this.httpRequestService = httpRequestService;
        this.httpContextAccessor = httpContextAccessor;
    }

    public IViewComponentResult Invoke(ComponentViewModel<FormColumnSectionProperties> sectionProperties)
    {
        var httpContext = httpContextAccessor.HttpContext;

        bool showContents = cookieConsentService.CurrentContactCanBeTracked() // Display if the visitor has consented to tracking.
            || httpContext.Kentico().PageBuilder().GetMode() != PageBuilderMode.Off // Display if the page is in Page Builder mode.
            || httpContext.Kentico().Preview().Enabled; // Display if the page is in Preview mode.

        var cookiePolicyUrlBuilder = new UriBuilder(httpRequestService.GetBaseUrlWithLanguage());
        cookiePolicyUrlBuilder.Path = httpRequestService.CombineUrlPaths(cookiePolicyUrlBuilder.Path, "cookie-policy");

        var model = new FormColumnSectionViewModel()
        {
            SectionAnchor = sectionProperties.Properties.SectionAnchor,
            ShowContents = showContents,
            NoConsentMessage = stringLocalizer["The content of this section includes tracking functionality. To view it, please consent to Marketing cookies."],
            NoConsentLinkText = stringLocalizer["Configure cookies"],
            NoConsentLinkUrl = cookiePolicyUrlBuilder.ToString()
        };

        return View("~/Features/Shared/Sections/FormColumn/FormColumnSection.cshtml", model);
    }
}
```

### Tweak the existing section
Now that you’ve added new properties to hide the section’s contents, you need to make sure this only happens for the consent-based version.
Adjust the existing _FormColumnSectionViewComponent.cs_ file to account for the new `ShowContents` property of the view model.
C#
**FormColumnSectionViewComponent.cs**
Copy
```
...
public IViewComponentResult Invoke(ComponentViewModel<FormColumnSectionProperties> sectionProperties)
{
    var model = new FormColumnSectionViewModel()
    {
        SectionAnchor = sectionProperties.Properties.SectionAnchor,
        ShowContents = true
    };
    return View("~/Features/Shared/Sections/FormColumn/FormColumnSection.cshtml", model);
}
...
```

## Add an identifier
Now, in case the identifier of the new consent-based component needs to be accessed externally, add it to the component identifiers file.
Add a new constant to the `Sections` class in _TrainingGuides.Web/ComponentIdentifiers.cs_ that references the constant from the view component.
C#
**ComponentIdentifiers.cs**
Copy
```
...
public static class ComponentIdentifiers
{
    public static class Sections
    {
        ...
        public const string FORM_COLUMN_CONSENT = FormColumnSectionViewComponent.IDENTIFIER;
        ...
    }
    ...
}
```

## See the result
You have implemented a layout and logic that displays or hides content on the website depending on a visitor tracking consent. In order to see it in action, you need to use it on a page.
  1. Run your project locally, sign in to the administration interface, and navigate to the **Training guides pages** channel.
  2. Click **Edit → Create new version** to edit the  _Contact-us_ page.
  3. Set Page Builder to use the _Form column: Consent-based_ section, then **Save** and **Publish** the web page.  
[![Screenshot of consent-based form section](/docsassets/guides/hide-builder-content-from-visitors-who-have-not-consented-to-tracking/consent-based-form-section.png)](/docsassets/guides/hide-builder-content-from-visitors-who-have-not-consented-to-tracking/consent-based-form-section.png)
  4. Now pretend that you are a visitor and navigate to your website in a browser’s incognito window. Visit the _Cookie policy_ page, set the cookie level to essential and then visit your _Contact us_ page. The section should not display.
  5. Go back to the _Cookie policy_ page, change your consent to _Marketing_ and revisit _Contact us_ page. If you’ve implemented the functionality properly, you’ll see the form displayed on the page.

Your browser does not support the video tag. 
Your site now includes a section with a powerful custom logic. Your website editors can use the new section to hide forms on the website from specific visitors. But they are not limited to just forms. They can hide any custom widgets which might lead to, for example, a GDPR compliance breach if displayed for all the visitors.
## What’s next
The [next guide](/guides/development/activities-and-marketing/implement-cross-site-activities) provides a simple example of implementing cross-site activities.
![]()
[]()[]()
