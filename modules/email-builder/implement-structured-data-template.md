# Implement the structured data template
  * [ Copy page link ](modules/email-builder/implement-structured-data-template#) | [Get HelpService ID](modules/email-builder/implement-structured-data-template#)
Core MVC 5


[✖](modules/email-builder/implement-structured-data-template# "Close page link panel") [Copy to clipboard](modules/email-builder/implement-structured-data-template#)
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