---
source: https://docs.kentico.com/guides/development/email-marketing/display-structured-content-email-builder
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Email marketing](/guides/development/email-marketing)
  * Display structured content in Email Builder 


# Display structured content in Email Builder
Email Builder in Xperience by Kentico empowers marketers to control the visual design of emails with a flexible drag-and-drop interface.
However, some scenarios might call for less flexibility. For example: - You may want to consistently display structured content, such as product details, event information, or other reusable content items. - Your organization might have business requirements that set strict styling rules for certain emails. - Your marketers may not have the time to invest in planning and designing each issue of a recurring newsletter.
Unlike Xperience’s original implementation of [email templates](/documentation/developers-and-admins/digital-marketing-setup/email-templates), which only exist in the database, Email Builder allows developers to manage templates and other components in the application, where they can edit code without having to run the site and sign in.
Let’s explore the use of structured content in Email Builder, displaying this data with a rigid template that still includes some editable areas for drag and drop.
## Before you start
This guide uses resources from the [Use the Email Builder Starter Kit in your project](/guides/development/email-marketing/use-email-builder-starter-kit) and [Create an Email Builder widget for articles](/guides/development/email-marketing/create-an-email-widget-for-articles) guides.
If you haven’t already integrated the Email Builder Starter Kit into your project, you can see a step-by-step example [in this guide](/guides/development/email-marketing/use-email-builder-starter-kit).
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * Familiarity with [builders in Xperience by Kentico](/documentation/developers-and-admins/development/builders)
  * Familiarity with [ASP.NET Razor components](https://learn.microsoft.com/en-us/aspnet/core/blazor/components/), [Blazor development best practices](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#blazor-development-best-practices), and [MJML markup](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#mjml-markup).
  * A running instance of Xperience by Kentico, preferably [30.11.1](/documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide have only been tested in [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-9/overview).
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Explore the scenario
In this scenario, we will create a template for a **Nature spotlight** email newsletter: a regular publication that describes interesting species of organisms, geographical features, and other natural phenomena.
The Training guides project already includes a content type for this newsletter, called [NatureSpotlightEmail](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Entities/EmailContentTypes/NatureSpotlightEmail/NatureSpotlightEmail.generated.cs). Aside from the standard _Subject_ and _Preview text_ fields, it has fields representing the following:
  * The name of the spotlight’s topic
  * The main text of the newsletter issue
  * One or more images of the topic
  * One or more countries where the topic lives or occurs
  * One or more related articles from the website


Compared to the [previous scenario](/guides/development/email-marketing/create-an-email-widget-for-articles#implement-the-mapper), where we built a widget for displaying Articles, this example is more closely tied to a specific content type.
For this reason, we won’t use a mapper class to abstract the template from the content type and make it portable. However, if a mapper fits your requirements, it is still possible to implement in this scenario. Feel free to employ the lessons you learned in the earlier guide
## Enable Email Builder for the Nature spotlight newsletter
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the Training guides repository contains the `NatureSpotlightEmail`. However, it is not enabled for Email Builder yet.
[Earlier in this series](/guides/development/email-marketing/use-email-builder-starter-kit#enable-email-builder), you enabled Email Builder for the `BasicEmail` content type.
Navigate to **Program.cs** and add the `NatureSpotlightEmail` content type identifier to the configured `EmailBuilderOptions`.
C#
**~/Program.cs**
Copy
```
...
builder.Services.Configure<EmailBuilderOptions>(options =>
{
    // Allows Email Builder for the provided content types
    options.AllowedEmailContentTypeNames = [
        BasicEmail.CONTENT_TYPE_NAME,
        NatureSpotlightEmail.CONTENT_TYPE_NAME
    ];
    // Replaces the default Email Builder section
    options.RegisterDefaultSection = false;
    options.DefaultSectionIdentifier = FullWidthEmailSection.IDENTIFIER;
});
...
 
 
 
 


```

## Build a razor component for the template
Xperience uses [Razor components](https://learn.microsoft.com/en-us/aspnet/core/blazor/components/) for Email Builder templates.
We’ll use separate files to display the template and handle its code-behind in this example, though you can optionally use the same file depending on your project’s conventions.
The _single-file approach_ uses a **.razor** file with razor syntax and a `@code { ... }` block for its C# code.
The _two-file approach_ uses a **.razor** file with razor syntax for display, and a **.razor.cs** file for the code-behind file.
Aside from the files that directly make up the component, we’ll also include a **model** to hold the data we want the component to display, and a **service** that its code-behind will use to fetch data.
### Define a model for the Razor component’s data
Let’s start with a model to hold all of the data our template will display.
In the _TrainingGuides.Web_ project, create the folder **~/Features/Newsletters/NatureSpotlight**. We’ll primarily work in this folder throughout this example.
Define a model for the newsletter in a file called **NatureSpotlightEmailModel.cs** :
C#
**NatureSpotlightEmailModel.cs**
Copy
```
using Kentico.Xperience.Mjml.StarterKit.Rcl.Widgets;
using Microsoft.AspNetCore.Components;
using TrainingGuides.Web.Features.Articles.EmailWidgets;

namespace TrainingGuides.Web.Features.Newsletters.NatureSpotlight;

public class NatureSpotlightEmailModel
{
    public string Subject { get; set; } = string.Empty;

    public string PreviewText { get; set; } = string.Empty;

    public IEnumerable<string> Countries { get; set; } = [];

    public string Topic { get; set; } = string.Empty;

    public MarkupString Text { get; set; } = new MarkupString();

    public IEnumerable<ImageWidgetModel> Images { get; set; } = [];

    public IEnumerable<ArticleEmailWidgetModel> RelatedArticles { get; set; } = [];
}
```

This file uses the `ImageWidgetModel` from the Email Builder Starter Kit and the `ArticleWidgetModel` from the example earlier in this series. If you’re starting here, you can find these models in our repository:
  * [ImageWidgetModel](https://github.com/Kentico/xperience-by-kentico-email-builder-starter-kit/blob/main/src/Kentico.Xperience.Mjml.StarterKit.Rcl/Widgets/ImageWidget/ImageWidgetModel.cs)
  * [ArticleEmailWidgetModel](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Articles/EmailWidgets/ArticleEmailWidgetModel.cs)


### Display the component’s data
Add a new file called **NatureSpotlightEmailTemplate.razor**.
We haven’t defined its code-behind yet, but we’ll plan for it to have a `NatureSpotlightEmailModel` property called `Model`, and a `string` property called `CssContent` to mirror the [template](https://github.com/Kentico/xperience-by-kentico-email-builder-starter-kit/blob/main/src/Kentico.Xperience.Mjml.StarterKit.Rcl/Templates/EmailBuilderStarterKitTemplate.razor.cs) from the Email Builder Starter Kit).
In this example, let’s include some [editable areas](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#editable-areas), so that marketers have the option to add extra notices, notes, and other content within the pre-determined structure.
You can use Email Builder templates that do not have editable areas if you want a fully locked-down template that is part of your project’s source code.
cshtml
**NatureSpotlightEmailTemplate.razor**
Copy
```
@using Kentico.VisualBuilderComponents.Rcl.Components
<mjml>
    <mj-head>
        <mj-title>@Model.Subject</mj-title>
        <mj-style>
            @((MarkupString)CssContent)
        </mj-style>
        <EmailBuilderStyles />
        <mj-preview>@Model.PreviewText</mj-preview>
    </mj-head>
    <mj-body>
        <mj-section>
            <mj-text>
                <h1>Nature spotlight</h1>
            </mj-text>
            @foreach(var image in Model.Images)
            {
                <mj-image src="@image.ImageUrl" alt="@image.AltText" />
            }
        </mj-section>
        <EditableArea AreaIdentifier="MainContent" />
        <mj-section>
            <mj-text>
                <h2>@Model.Topic</h2>
                <p>
                    @foreach(string country in Model.Countries)
                    {
                        <em>
                            @(country == Model.Countries.Last() ? country : $"{country}, ")
                        </em>
                    }
                </p>
                <br/>
                @Model.Text

                <hr/>
                <h3>Related articles:</h3>
            </mj-text>
            @foreach(var article in Model.RelatedArticles)
            {
                <mj-image src="@article.ArticleTeaserImage.ImageUrl" alt="@article.ArticleTeaserImage.AltText" width="300px" />
                <mj-text>
                    <h4>@article.ArticleTitle</h4>
                    <p>
                        <em>@article.ArticleSummary</em>
                    </p>
                </mj-text>
                <mj-button href="@article.ArticleUrl">
                    Read More
                </mj-button>
            }
        </mj-section>
        <EditableArea AreaIdentifier="SecondaryContent" />
        <mj-text align="center">
            <a href="@Recipient.UnsubscriptionUrl" data-click-tracking="false">Unsubscribe</a>
        </mj-text>
        <EmailBuilderScripts />
    </mj-body>
</mjml>
```

You might notice that this uses the `mj-section` component to wrap some content, but does not wrap `EditableArea` components in the same way.
We avoided wrapping the editable area in this example because the Email Builder sections in this project use `mj-section` around their widget zones, and MJML does not support nesting `mj-section` components within one another.
This approach is not a requirement, but you should make sure the approach you decide on is consistent across all of the Email Builder components of each type within your project.
### Add a service for the component’s code-behind
The code-behind of the Razor component needs to populate the template’s data by filling a `NatureSpotlightEmailModel` with data from the database, alongside handling CSS.
For the sake of keeping things clean and testable, let’s move this code to a separate service.
**Interface:**
C#
**INatureSpotlightEmailService.cs**
Copy
```
namespace TrainingGuides.Web.Features.Newsletters.NatureSpotlight;

public interface INatureSpotlightEmailService
{
    /// <summary>
    /// Gets the Nature Spotlight email from the current email context.
    /// </summary>
    Task<NatureSpotlightEmail> GetNatureSpotlightEmailFromContext();

    /// <summary>
    /// Gets the model for the Nature Spotlight email.
    /// </summary>
    Task<NatureSpotlightEmailModel> GetNatureSpotlightEmailModel();
}
```

To implement the service, resolve an `IEmailContextAccessor` with dependency injection and use it to retrieve a `NatureSpotlightEmail`. You can populate a model based on the retrieved email.
Find the display names of the retrieved countries using an [implementation](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/Services/CountryService.cs) of [ICountryService](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/Services/ICountryService.cs).
When handling related articles, make sure to account for the multiple reusable content types that _ArticlePage_ items can reference.
**Implementation:**
C#
**NatureSpotlightEmailService.cs**
Copy
```
using Kentico.EmailBuilder.Web.Mvc;
using Kentico.Xperience.Mjml.StarterKit.Rcl.Widgets;
using Microsoft.AspNetCore.Components;
using TrainingGuides.Web.Features.Articles.EmailWidgets;
using TrainingGuides.Web.Features.Shared.Services;

namespace TrainingGuides.Web.Features.Newsletters.NatureSpotlight;

public class NatureSpotlightEmailService : INatureSpotlightEmailService
{
    private readonly IEmailContextAccessor emailContextAccessor;
    private readonly ICountryService countryService;

    public NatureSpotlightEmailService(
        IEmailContextAccessor emailContextAccessor,
        ICountryService countryService)
    {
        this.emailContextAccessor = emailContextAccessor;
        this.countryService = countryService;
    }

    /// <inheritdoc />
    public virtual async Task<NatureSpotlightEmail> GetNatureSpotlightEmailFromContext() =>
        await emailContextAccessor.GetContext().GetEmail<NatureSpotlightEmail>();

    /// <inheritdoc />
    public async Task<NatureSpotlightEmailModel> GetNatureSpotlightEmailModel()
    {
        var email = await GetNatureSpotlightEmailFromContext();

        var model = new NatureSpotlightEmailModel
        {
            Topic = email.NatureSpotlightTopic,
            Text = new MarkupString(email.NatureSpotlightText),
            Countries = countryService.GetCountryDisplayNamesByGuids(email.NatureSpotlightCountries),
            RelatedArticles = GetNatureSpotlightRelatedArticles(email),
            Images = GetNatureSpotlightImages(email),
        };

        return model;
    }

    private IEnumerable<ArticleEmailWidgetModel> GetNatureSpotlightRelatedArticles(NatureSpotlightEmail email) =>
        email.NatureSpotlightRelatedArticles
            .Select(article => new ArticleEmailWidgetModel
            {
                ArticleTitle = article.ArticlePageArticleContent?.FirstOrDefault()?.ArticleSchemaTitle
                    ?? article.ArticlePageContent?.FirstOrDefault()?.ArticleTitle
                    ?? string.Empty,
                ArticleSummary = article.ArticlePageArticleContent?.FirstOrDefault()?.ArticleSchemaSummary
                    ?? article.ArticlePageContent?.FirstOrDefault()?.ArticleSummary
                    ?? string.Empty,
                ArticleUrl = article.GetUrl().AbsoluteUrl,
                ArticleTeaserImage = new ImageWidgetModel
                {
                    ImageUrl = article.ArticlePageArticleContent?.FirstOrDefault()?.ArticleSchemaTeaser.FirstOrDefault()?.AssetFile.Url
                        ?? article.ArticlePageContent?.FirstOrDefault()?.ArticleTeaser.FirstOrDefault()?.AssetFile.Url
                        ?? string.Empty,
                    AltText = article.ArticlePageArticleContent?.FirstOrDefault()?.ArticleSchemaTeaser.FirstOrDefault()?.AssetAltText
                        ?? article.ArticlePageContent?.FirstOrDefault()?.ArticleTeaser.FirstOrDefault()?.AssetAltText
                        ?? string.Empty
                }
            });

    private IEnumerable<ImageWidgetModel> GetNatureSpotlightImages(NatureSpotlightEmail email) =>
        email.NatureSpotlightImages
            .Select(image => new ImageWidgetModel
            {
                ImageUrl = image.AssetFile.Url,
                AltText = image.AssetAltText
            });
}
```

Use the `GetListResult` method to improve performance while you retrieve the newsletter’s referenced countries.
Remember to register the service with your dependency injection container.
C#
**~/ServiceCollectionExtensions.cs**
Copy
```
public static class ServiceCollectionExtensions
{
    public static void AddTrainingGuidesServices(this IServiceCollection services)
    {
        ...
        services.AddScoped<IComponentModelMapper<ImageWidgetModel>, ImageEmailWidgetModelMapper>();
        services.AddScoped<IComponentModelMapper<ProductWidgetModel>, ProductEmailWidgetModelMapper>();
        services.AddScoped<IComponentModelMapper<ArticleEmailWidgetModel>, ArticleEmailWidgetModelMapper>();
        services.AddScoped<INatureSpotlightEmailService, NatureSpotlightEmailService>();
        services.AddScoped<IEmailDataMapper, TrainingGuidesEmailDataMapper>();
        ...
 


```

* * *
### Fill the template’s data in the component’s code-behind
Now we can utilize this new service in the code-behind for the Razor component (**NatureSpotlightEmailTemplate.razor.cs**).
First, register the email template, restricting its potential content types to the Nature spotlight email type exclusively.
You can use the [CssLoaderService](https://github.com/Kentico/xperience-by-kentico-email-builder-starter-kit/blob/main/src/Kentico.Xperience.Mjml.StarterKit.Rcl/CssLoaderService.cs) from the Email Builder Starter Kit to populate CSS for the template.
Lastly, use the `NatureSpotlightEmailService` to build the component’s model.
C#
**NatureSpotlightEmailTemplate.razor.cs**
Copy
```
using CMS.EmailMarketing;
using Kentico.EmailBuilder.Web.Mvc;
using Kentico.Xperience.Mjml.StarterKit.Rcl;
using Microsoft.AspNetCore.Components;
using TrainingGuides;
using TrainingGuides.Web.Features.Newsletters.NatureSpotlight;

[assembly: RegisterEmailTemplate(
    identifier: NatureSpotlightEmailTemplate.IDENTIFIER,
    name: "Nature spotlight template",
    componentType: typeof(NatureSpotlightEmailTemplate),
    ContentTypeNames = [NatureSpotlightEmail.CONTENT_TYPE_NAME])]

namespace TrainingGuides.Web.Features.Newsletters.NatureSpotlight;


public partial class NatureSpotlightEmailTemplate
{
    public const string IDENTIFIER = $"TrainingGuides.{nameof(NatureSpotlightEmailTemplate)}";

    private string? cssContent;

    protected string CssContent
    {
        get => cssContent ?? string.Empty;
        set => cssContent = value;
    }

    private EmailRecipientContext? recipient;

    protected EmailRecipientContext Recipient => recipient ??= RecipientContextAccessor.GetContext();

    [Inject]
    private IEmailRecipientContextAccessor RecipientContextAccessor { get; set; } = default!;

    [Inject]
    private INatureSpotlightEmailService NatureSpotlightEmailService { get; set; } = default!;

    [Inject]
    private CssLoaderService CssLoaderService { get; set; } = null!;

    public NatureSpotlightEmailModel Model { get; set; } = new();

    protected override async Task OnInitializedAsync()
    {
        Model = await NatureSpotlightEmailService.GetNatureSpotlightEmailModel();
        cssContent = await CssLoaderService.GetCssAsync();
    }
}
```

## See the results
Sign in to the Xperience by Kentico administration interface and access the **Training guides emails** channel.
Create a new **Regular** email of the **Nature spotlight email** content type, with the **Nature spotlight email template** option for its template.
[![screenshot of create-email dialogue](/docsassets/guides/display-structured-content-email-builder/see-results-create-email.png)](/docsassets/guides/display-structured-content-email-builder/see-results-create-email.png)
[![screenshot of template selection dialogue](/docsassets/guides/display-structured-content-email-builder/see-results-choose-template.png)](/docsassets/guides/display-structured-content-email-builder/see-results-choose-template.png)
After following through the steps and filling in the email fields, you should see your new template in action, displaying structured content with a rigid layout and optional editable areas for further customization.
[![screenshot of Content tab](/docsassets/guides/display-structured-content-email-builder/see-results-content-tab.png)](/docsassets/guides/display-structured-content-email-builder/see-results-content-tab.png)
[![screenshot of Email Builder tab](/docsassets/guides/display-structured-content-email-builder/see-results-builder-tab.png)](/docsassets/guides/display-structured-content-email-builder/see-results-builder-tab.png)
By following this approach, you can create robust, maintainable email templates that surface structured content in Email Builder. This way, marketers can easily deliver engaging, consistent communications while keeping your codebase clean and maintainable.
## Continue learning
Now that you have an email, try creating a recipient list for a subscription flow, and sending out a test email.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the Training guides repo already contains a content type for subscription confirmation, but you’ll still need to create a form, an autoresponder email, and other components as described in [the documentation](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers).
## What’s next
The [next guide in this series](/guides/development/email-marketing/build-a-flexible-email-builder-template) explores Email Builder’s flexibility, creating a template and section that allow marketers to create and style complex layouts.
![]()
[]()[]()
