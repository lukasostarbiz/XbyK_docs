---
source: https://docs.kentico.com/modules/email-builder/implement-structured-data-template
scrape_date: 2026-01-26
---

Module: Email Builder
9 of 13 Pages
# Implement the structured data template
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
[ Previous page ](/modules/email-builder/design-structured-data-template)
9 of 13
[ Mark complete and continue ](/modules/email-builder/prepare-template-styling-options)
![]()
[]()[]()
