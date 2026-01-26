---
source: https://docs.kentico.com/modules/email-builder/work-with-widget-model
scrape_date: 2026-01-26
---

Module: Email Builder
6 of 13 Pages
# Work with the widget model
With the article widget properties in place, we need to define the data the widget will display. Create a file called **ArticleEmailWidgetModel.cs** in the _~/Features/Articles/EmailWidgets_ folder.
The widget should not render the full text of the article directly in the email, because it is meant to drive traffic to the full version on the website. Properties for the _title_ , _teaser image_ , _summary_ , and _URL_ should be enough.
Since we are using the Email Builder Starter Kit, we can take advantage of the pre-existing `ImageWidgetModel` class to represent the _URL_ and _alt text_ of the article teaser image.
C#
**ArticleEmailWidgetModel.cs**
Copy
```
using Kentico.Xperience.Mjml.StarterKit.Rcl.Widgets;

namespace TrainingGuides.Web.Features.Articles.EmailWidgets;

public class ArticleEmailWidgetModel
{
    public string ArticleTitle { get; set; } = string.Empty;
    public ImageWidgetModel ArticleTeaserImage { get; set; } = new();
    public string ArticleSummary { get; set; } = string.Empty;
    public string ArticleUrl { get; set; } = string.Empty;

}
```

## Map content types to the widget model
Now, we need a mapper to populate the widget model based on the selected article. We’ll create a class that queries the **TrainingGuides.ArticlePage** content type and converts the resulting pages into `ArticleEmailWidgetModel` objects.
This process follows the conventions of the [Email Builder Starter Kit](https://github.com/Kentico/xperience-by-kentico-email-builder-starter-kit) and makes our widget more portable.
It separates the widget from the content types of any specific project, easing the process of using the widget elsewhere.
If you do not need a portable widget, you can include your data access logic directly in the code-behind of the widget’s view component, populating the widget model there, or even directly using your content type classes instead of a widget model.
### Implement the mapper
Add a file called **ArticleEmailWidgetModelMapper.cs** in the _~/Features/Articles/EmailWidgets_ folder. Implement the `IComponentModelMapper<TModel>` interface from the Starter Kit.
The **Article page** content type of the Training guides repository can link to multiple reusable article types— `Article` and `IArticleSchema`.
These types exist to show the process of handling content model expansion over time in the [advanced content module](/modules/advanced-content).
We’ll need to handle both potential scenarios in our mapping class.
C#
**ArticleEmailWidgetModelMapper.cs**
Copy
```
using Kentico.Xperience.Mjml.StarterKit.Rcl.Mapping;
using Kentico.Xperience.Mjml.StarterKit.Rcl.Widgets;
using Microsoft.Extensions.Localization;
using TrainingGuides.Web.Features.Shared.Services;

namespace TrainingGuides.Web.Features.Articles.EmailWidgets;

public class ArticleEmailWidgetModelMapper : IComponentModelMapper<ArticleEmailWidgetModel>
{
    private readonly IContentItemRetrieverService contentItemRetrieverService;
    private readonly IStringLocalizer<SharedResources> stringLocalizer;

    private ArticleEmailWidgetModel DefaultModel => new()
    {
        ArticleTitle = stringLocalizer["No article selected"],
        ArticleTeaserImage = new ImageWidgetModel(),
        ArticleSummary = stringLocalizer["Please select an article."],
        ArticleUrl = string.Empty,
    };


    public ArticleEmailWidgetModelMapper(
        IContentItemRetrieverService contentItemRetrieverService,
        IStringLocalizer<SharedResources> stringLocalizer)
    {
        this.contentItemRetrieverService = contentItemRetrieverService;
        this.stringLocalizer = stringLocalizer;
    }

    public async Task<ArticleEmailWidgetModel> Map(Guid contentItemGuid, string languageName)
    {
        if (contentItemGuid == Guid.Empty)
        {
            return DefaultModel;
        }

        var articlePage = await contentItemRetrieverService.RetrieveWebPageByContentItemGuid<ArticlePage>(contentItemGuid, 2, true, languageName);

        string articlePageUrl = articlePage.GetUrl().AbsoluteUrl;

        var schemaArticle = articlePage?.ArticlePageArticleContent.FirstOrDefault();
        var oldArticle = articlePage?.ArticlePageContent.FirstOrDefault();

        var imageAsset = schemaArticle?.ArticleSchemaTeaser?.FirstOrDefault()
                ?? oldArticle?.ArticleTeaser?.FirstOrDefault();
        string imageUrl = imageAsset?.AssetFile?.Url ?? string.Empty;

        return (articlePage is not null && (schemaArticle is not null || oldArticle is not null))
            ? new ArticleEmailWidgetModel()
            {
                ArticleTitle = schemaArticle?.ArticleSchemaTitle
                    ?? oldArticle?.ArticleTitle
                    ?? string.Empty,
                ArticleSummary = schemaArticle?.ArticleSchemaSummary
                    ?? oldArticle?.ArticleSummary
                    ?? string.Empty,
                ArticleTeaserImage = new ImageWidgetModel
                {
                    ImageUrl = imageUrl.ToString(),
                    AltText = imageAsset?.AssetAltText ?? string.Empty,
                },
                ArticleUrl = articlePageUrl
            }
            : DefaultModel;
    }
}
```

### Register the mapper
This new mapper won’t work unless we register it with the dependency injection container. Add the mapper to the `AddTrainingGuidesServices` method in **ServiceCollectionExtensions.cs** as a scoped service.
C#
**ServiceCollectionExtensions.cs**
Copy
```
...
services.AddScoped<IComponentModelMapper<ArticleEmailWidgetModel>, ArticleEmailWidgetModelMapper>();
...
```

[ Previous page ](/modules/email-builder/set-up-widget-properties)
6 of 13
[ Mark complete and continue ](/modules/email-builder/build-widget-component)
![]()
[]()[]()
