---
source: https://docs.kentico.com/guides/development/kenticopilot/implement-widgets-kenticopilot
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [KentiCopilot](/guides/development/kenticopilot)
  * Rapidly implement widgets with KentiCopilot 


# Rapidly implement widgets with KentiCopilot
With the [documentation MCP server](/documentation/developers-and-admins/installation/mcp-server), your agentic coding assistant has access to a rich collection of reference materials about Xperience by Kentico.
As part of our [KentiCopilot initiative](/guides/development/kenticopilot), we’ve put together some detailed prompts and reference materials to help your agent rapidly build functional widgets.
Check out this five minute video to see an example widget implementation from start to finish!
## Resources
### The repository
Find the resources we used in the [KentiCopilot GitHub repository](https://github.com/Kentico/xperience-by-kentico-kenticopilot/tree/main). You can find instructions to set it up in the README file that displays on the main page.
### The requirements
Here is the _requirements.md_ file we used for the widget from our video:
MD
**requirements.md**
Copy
```
# Featured article widget

## Selection

The widget allows marketers to choose between displaying the most recent published article page, or selecting a specific page to display.
The selector for articles should only be available when a marketer chooses to display a specific page, and should be hidden when they choose the most recent article.

## Presentation options

- Date: Toggle to show or hide the date (`ArticlePagePublishDate`)
- Summary: Toggle to show or hide the article summary (`ArticlePageSummary`)
- Call to action text: Determine the text of the CTA link

## Content retrieval

- Retrieve the relevant Article columns (`ArticleTitle`, `ArticlePageTeaser`, `ArticlePagePublishDate`, `ArticlePageSummary`)
- Use the IncludeUrlPath property in RetrievePagesParameters
- Include the linked teaser item
- Use the correct Xperience by Kentico APIs for webpage content retrieval and caching
    - When retrieving pages based on a `ContentItemReference`, do not use `RetrievePagesByGuids`, which requires WebPageItemGuid instead of ContentItemGuid. Instead, use `RetrievePages` with a where condition to specify `ContentItemGuid`, which you can get from the `ContentItemReference`.
- Generate the URL of the linked article
    - Set `IncludeUrlPath` property of `RetrievePagesParameters` to `true`.
    - Include columns required for URL generation with the `UrlPathColumns` extension method

## Styling

- Use the existing Site.css styles to match the design of other components on the site
- Ensure the teaser image fills the full width of the widget's container

## Error handling

- Missing articles: If selected articles are missing from the database, display a message to the marketer only (not visible in live mode)
- Missing images: If articles have no linked image, display a placeholder image

## Technical requirements

- Use proper caching mechanisms
- Use correct APIs for webpage content retrieval
- Generate URLs correctly for articles and assets
- Follow Xperience by Kentico best practices
```

### The prompts
Here are the prompts used throughout the video:
Text
**Prompt 1**
Copy
```
/widget-create-research.prompt.md

For the requirements described in featured-article-widget/requirements.md\
```

Text
**Prompt 2**
Copy
```
/widget-create-implementation.prompt.md

Follow instructions in: CREATION.instructions.md
```

Text
**Prompt 3**
Copy
```
The widget's error message currently only displays if page builder is in edit mode. It doesn't seem to account for page builder's read-only mode or preview mode.

Could you please make it so the error also displays in preview mode and read-only mode for page builder?
```

Text
**Prompt 4**
Copy
```
The data retrieval methods here are missing the columns specified in .github/featured-article-widget/requirements.md.

Can you please make sure the ContentRetriever queries also include those ArticlePage columns?
```

### The widget
These are the resulting widget files:
cshtml
**_FeaturedArticleWidget.cshtml**
Copy
```
@using DancingGoat.Widgets
@using Kentico.PageBuilder.Web.Mvc

@model DancingGoat.Widgets.FeaturedArticleWidgetViewModel

@{
    var pageBuilderMode = Context.Kentico().PageBuilder().GetMode();
    var isInPreviewMode = Context.Kentico().Preview().Enabled;
    var shouldShowErrorMessage = (pageBuilderMode == PageBuilderMode.Edit
                                  || pageBuilderMode == PageBuilderMode.ReadOnly
                                  || isInPreviewMode)
                                  && !string.IsNullOrEmpty(Model.ErrorMessage);
}

<div class="featured-article-widget">
    @if (shouldShowErrorMessage)
    {
        <div class="alert alert-warning">
            @Model.ErrorMessage
        </div>
    }

    @if (!string.IsNullOrEmpty(Model.ArticleTitle))
    {
        <div class="featured-article-image">
            <img src="@Url.Content(Model.TeaserImageUrl)" alt="@Model.ArticleTitle" style="width: 100%; display: block;" />
        </div>

        <div class="featured-article-content">
            <h2 class="featured-article-title">
                <a href="@Url.Content(Model.ArticleUrl)">@Model.ArticleTitle</a>
            </h2>

            @if (Model.PublishDate.HasValue)
            {
                <div class="featured-article-date">
                    @Model.PublishDate.Value.ToString("MMMM dd, yyyy")
                </div>
            }

            @if (!string.IsNullOrEmpty(Model.Summary))
            {
                <div class="featured-article-summary">
                    @Model.Summary
                </div>
            }

            <div class="featured-article-cta">
                <a href="@Url.Content(Model.ArticleUrl)" class="btn btn-primary">@Model.CallToActionText</a>
            </div>
        </div>
    }
</div>
```

C#
**FeaturedArticleWidgetProperties.cs**
Copy
```
using System;
using System.Collections.Generic;
using System.ComponentModel;

using CMS.ContentEngine;

using DancingGoat.Models;

using Kentico.PageBuilder.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;

namespace DancingGoat.Widgets
{
    /// <summary>
    /// Featured article widget properties.
    /// </summary>
    public class FeaturedArticleWidgetProperties : IWidgetProperties
    {
        /// <summary>
        /// Article selection mode.
        /// </summary>
        [DropDownComponent(
            Label = "{$dancinggoat.featuredarticlewidget.selectionmode.label$}",
            ExplanationText = "{$dancinggoat.featuredarticlewidget.selectionmode.explanation$}",
            Order = 1,
            Options = "MostRecent;{$dancinggoat.featuredarticlewidget.selectionmode.option.mostrecent$}\nSpecific;{$dancinggoat.featuredarticlewidget.selectionmode.option.specific$}")]
        public string ArticleSelectionMode { get; set; } = nameof(ArticleSelectionModeEnum.MostRecent);


        /// <summary>
        /// Selected article.
        /// </summary>
        [ContentItemSelectorComponent(
            ArticlePage.CONTENT_TYPE_NAME,
            Label = "{$dancinggoat.featuredarticlewidget.selectedarticle.label$}",
            ExplanationText = "{$dancinggoat.featuredarticlewidget.selectedarticle.explanation$}",
            MaximumItems = 1,
            Order = 2)]
        [VisibleIfEqualTo(nameof(ArticleSelectionMode), nameof(ArticleSelectionModeEnum.Specific), StringComparison.OrdinalIgnoreCase)]
        public IEnumerable<ContentItemReference> SelectedArticle { get; set; } = new List<ContentItemReference>();


        /// <summary>
        /// Show article publish date.
        /// </summary>
        [CheckBoxComponent(
            Label = "{$dancinggoat.featuredarticlewidget.showdate.label$}",
            ExplanationText = "{$dancinggoat.featuredarticlewidget.showdate.explanation$}",
            Order = 3)]
        public bool ShowDate { get; set; } = true;


        /// <summary>
        /// Show article summary.
        /// </summary>
        [CheckBoxComponent(
            Label = "{$dancinggoat.featuredarticlewidget.showsummary.label$}",
            ExplanationText = "{$dancinggoat.featuredarticlewidget.showsummary.explanation$}",
            Order = 4)]
        public bool ShowSummary { get; set; } = true;


        /// <summary>
        /// Call to action text.
        /// </summary>
        [TextInputComponent(
            Label = "{$dancinggoat.featuredarticlewidget.ctatext.label$}",
            ExplanationText = "{$dancinggoat.featuredarticlewidget.ctatext.explanation$}",
            Order = 5)]
        public string CallToActionText { get; set; } = "Read more";
    }


    /// <summary>
    /// Article selection mode enum.
    /// </summary>
    public enum ArticleSelectionModeEnum
    {
        /// <summary>
        /// Most recent article.
        /// </summary>
        [Description("Most recent article")]
        MostRecent,


        /// <summary>
        /// Specific article.
        /// </summary>
        [Description("Specific article")]
        Specific
    }
}
```

C#
**FeaturedArticleWidgetViewComponent.cs**
Copy
```
using System;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;

using CMS.ContentEngine;
using CMS.DataEngine;
using CMS.Websites;

using DancingGoat.Models;
using DancingGoat.Widgets;

using Kentico.Content.Web.Mvc;
using Kentico.PageBuilder.Web.Mvc;

using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.ViewComponents;

[assembly: RegisterWidget(
    FeaturedArticleWidgetViewComponent.IDENTIFIER,
    typeof(FeaturedArticleWidgetViewComponent),
    "{$dancinggoat.featuredarticlewidget.title$}",
    typeof(FeaturedArticleWidgetProperties),
    Description = "{$dancinggoat.featuredarticlewidget.description$}",
    IconClass = "icon-newspaper")]

namespace DancingGoat.Widgets
{
    /// <summary>
    /// Controller for featured article widget.
    /// </summary>
    public class FeaturedArticleWidgetViewComponent : ViewComponent
    {
        /// <summary>
        /// Widget identifier.
        /// </summary>
        public const string IDENTIFIER = "DancingGoat.General.FeaturedArticleWidget";

        private const string PLACEHOLDER_IMAGE_PATH = "/Content/Images/404.jpg";

        private readonly IContentRetriever contentRetriever;


        /// <summary>
        /// Creates an instance of <see cref="FeaturedArticleWidgetViewComponent"/> class.
        /// </summary>
        /// <param name="contentRetriever">Content retriever.</param>
        public FeaturedArticleWidgetViewComponent(IContentRetriever contentRetriever)
        {
            this.contentRetriever = contentRetriever;
        }


        public async Task<ViewViewComponentResult> InvokeAsync(FeaturedArticleWidgetProperties properties, CancellationToken cancellationToken)
        {
            ArticlePage article = null;
            string errorMessage = null;

            // Determine which article to retrieve based on selection mode
            if (properties.ArticleSelectionMode == nameof(ArticleSelectionModeEnum.Specific))
            {
                var selectedArticle = properties.SelectedArticle?.FirstOrDefault();
                if (selectedArticle != null)
                {
                    article = await GetArticleByGuid(selectedArticle.Identifier, cancellationToken);
                    if (article == null)
                    {
                        errorMessage = "The selected article could not be found.";
                    }
                }
                else
                {
                    errorMessage = "Please select an article to display.";
                }
            }
            else
            {
                article = await GetMostRecentArticle(cancellationToken);
                if (article == null)
                {
                    errorMessage = "No articles are available to display.";
                }
            }

            var model = BuildViewModel(article, properties, errorMessage);

            return View("~/Components/Widgets/FeaturedArticleWidget/_FeaturedArticleWidget.cshtml", model);
        }


        private async Task<ArticlePage> GetArticleByGuid(Guid contentItemGuid, CancellationToken cancellationToken)
        {
            var articles = await contentRetriever.RetrievePages<ArticlePage>(
                new RetrievePagesParameters
                {
                    IncludeUrlPath = true,
                    LinkedItemsMaxLevel = 1
                },
                query => query
                    .Where(where => where.WhereEquals(nameof(IContentQueryDataContainer.ContentItemGUID), contentItemGuid))
                    .Columns(
                        nameof(ArticlePage.ArticleTitle),
                        nameof(ArticlePage.ArticlePageTeaser),
                        nameof(ArticlePage.ArticlePagePublishDate),
                        nameof(ArticlePage.ArticlePageSummary))
                    .UrlPathColumns(),
                new RetrievalCacheSettings($"WhereEquals_{nameof(IContentQueryDataContainer.ContentItemGUID)}_{contentItemGuid}"),
                cancellationToken
            );

            return articles.FirstOrDefault();
        }


        private async Task<ArticlePage> GetMostRecentArticle(CancellationToken cancellationToken)
        {
            var articles = await contentRetriever.RetrievePages<ArticlePage>(
                new RetrievePagesParameters
                {
                    IncludeUrlPath = true,
                    LinkedItemsMaxLevel = 1
                },
                query => query
                    .OrderBy(OrderByColumn.Desc(nameof(ArticlePage.ArticlePagePublishDate)))
                    .TopN(1)
                    .Columns(
                        nameof(ArticlePage.ArticleTitle),
                        nameof(ArticlePage.ArticlePageTeaser),
                        nameof(ArticlePage.ArticlePagePublishDate),
                        nameof(ArticlePage.ArticlePageSummary))
                    .UrlPathColumns(),
                new RetrievalCacheSettings($"OrderBy_{nameof(ArticlePage.ArticlePagePublishDate)}_{nameof(OrderByColumn.Desc)}_TopN_1"),
                cancellationToken
            );

            return articles.FirstOrDefault();
        }


        private FeaturedArticleWidgetViewModel BuildViewModel(
            ArticlePage article,
            FeaturedArticleWidgetProperties properties,
            string errorMessage)
        {
            var model = new FeaturedArticleWidgetViewModel
            {
                ErrorMessage = errorMessage,
                CallToActionText = !string.IsNullOrWhiteSpace(properties.CallToActionText)
                    ? properties.CallToActionText
                    : "Read more"
            };

            if (article != null)
            {
                model.ArticleTitle = article.ArticleTitle;
                model.ArticleUrl = article.GetUrl()?.RelativePath ?? string.Empty;

                // Get teaser image or use placeholder
                var teaserImage = article.ArticlePageTeaser?.FirstOrDefault();
                model.TeaserImageUrl = teaserImage?.ImageFile?.Url ?? PLACEHOLDER_IMAGE_PATH;

                // Conditionally include date and summary based on widget properties
                if (properties.ShowDate)
                {
                    model.PublishDate = article.ArticlePagePublishDate;
                }

                if (properties.ShowSummary)
                {
                    model.Summary = article.ArticlePageSummary;
                }
            }

            return model;
        }
    }
}
```

C#
**FeaturedArticleWidgetViewModel.cs**
Copy
```
using System;

namespace DancingGoat.Widgets
{
    /// <summary>
    /// View model for featured article widget.
    /// </summary>
    public class FeaturedArticleWidgetViewModel
    {
        /// <summary>
        /// Article title.
        /// </summary>
        public string ArticleTitle { get; set; }


        /// <summary>
        /// Article URL.
        /// </summary>
        public string ArticleUrl { get; set; }


        /// <summary>
        /// Teaser image URL.
        /// </summary>
        public string TeaserImageUrl { get; set; }


        /// <summary>
        /// Article publish date (null if ShowDate is false).
        /// </summary>
        public DateTime? PublishDate { get; set; }


        /// <summary>
        /// Article summary (null/empty if ShowSummary is false).
        /// </summary>
        public string Summary { get; set; }


        /// <summary>
        /// Call to action text.
        /// </summary>
        public string CallToActionText { get; set; }


        /// <summary>
        /// Error message to display in edit mode.
        /// </summary>
        public string ErrorMessage { get; set; }
    }
}
```

Try using the [KentiCopilot repository](https://github.com/Kentico/xperience-by-kentico-kenticopilot/tree/main) today to speed up your widget creation process!
![]()
[]()[]()
