# Create an Email Builder widget for articles
  * How-to| [ Copy page link ](guides/development/email-marketing/create-an-email-widget-for-articles#) | [Get HelpService ID](guides/development/email-marketing/create-an-email-widget-for-articles#)
Core MVC 5


[✖](guides/development/email-marketing/create-an-email-widget-for-articles# "Close page link panel") [Copy to clipboard](guides/development/email-marketing/create-an-email-widget-for-articles#)
[Email Builder](documentation/developers-and-admins/development/builders/email-builder) in Xperience by Kentico allows marketers to design emails using a drag-and-drop interface. By creating custom widgets, developers can extend the functionality of Email Builder to meet specific project requirements.
Let’s explore how to create an [Email Builder widget](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#widgets) for displaying articles. The example here uses tools and follows conventions from our [Xperience Email Builder Starter Kit](https://github.com/Kentico/xperience-by-kentico-email-builder-starter-kit).
## Before you start
If you haven’t already integrated the Email Builder Starter Kit into your project, you can see a step-by-step example [in this guide](guides/development/email-marketing/use-email-builder-starter-kit).
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * Familiarity with [builders in Xperience by Kentico](documentation/developers-and-admins/development/builders)
  * Familiarity with [ASP.NET Razor components](https://learn.microsoft.com/en-us/aspnet/core/blazor/components/), [Blazor development best practices](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#blazor-development-best-practices), and [MJML markup](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#mjml-markup).
  * A running instance of Xperience by Kentico, preferably [30.11.1](documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide have only been tested in [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-9/overview).
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Configure allowed content types
To ensure the widget can select only articles, start by configuring the allowed content types.
For this example, we want our widget to be portable to other solutions, so we’ll go through extra steps to create options for developers to configure during startup, and use them in a filter.
This way, any project with some kind of _article_ , _blog post_ , or _news_ -like content type can configure the widget with minimal code changes. Then, we’ll go through an example using the **TrainingGuides.ArticlePage** content type from the [Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides).
If your widget does not need to be shared with other projects, you can simply hard-code the available content types in the content item selector that we will use later on.
### Define options for allowed content types
Start by adding a new file called **TrainingGuidesEmailBuilderOptions.cs** in the _~/Features/Shared/EmailBuilder_ folder. This options class will allow developers to specify the content types marketers can select in the widget properties.
C#
**TrainingGuidesEmailBuilderOptions.cs**
Copy
```
namespace TrainingGuides.Web.Features.Shared.EmailBuilder;

public class TrainingGuidesEmailBuilderOptions
{
    public IEnumerable<string> AllowedArticleContentTypes { get; set; } = Array.Empty<string>();
}
```

### Register the options
Add the options to the `AddTrainingGuidesOptions` method in **~/ServiceCollectionExtensions.cs** , found in the root of the _TrainingGuides.Web_ project. If this method does not exist, create it.
C#
**ServiceCollectionExtensions.cs**
Copy
```
...
public static void AddTrainingGuidesOptions(this IServiceCollection services)
{
    ...
    services.Configure<TrainingGuidesEmailBuilderOptions>(options =>
    {
        options.AllowedArticleContentTypes = [ArticlePage.CONTENT_TYPE_NAME];
    });
    ...
}
...
```

Then, call this method from **Program.cs** immediately after `AddTrainingGuidesServices`.
C#
**Program.cs**
Copy
```
...
builder.Services.AddTrainingGuidesServices();
builder.Services.AddTrainingGuidesOptions();
...
 


```

## Implement a content type filter
Next, let’s create a filter that uses the `TrainingGuidesEmailBuilderOptions` to restrict the widget’s selectable content types.
Add a file called **ArticleContentTypesFilter.cs** in the _~/Features/Articles/EmailWidgets_ folder.
Then, implement the `IContentTypesFilter` interface. Use dependency injection to resolve the options we configured during startup.
Use the provided codenames to find the GUIDs of the corresponding data classes and assign them to the `AllowedContentTypeIdentifiers` property.
C#
**ArticleContentTypesFilter.cs**
Copy
```
using CMS.DataEngine;
using Kentico.Xperience.Admin.Base.FormAnnotations;
using Microsoft.Extensions.Options;
using TrainingGuides.Web.Features.Shared.EmailBuilder;

namespace TrainingGuides.Web.Features.Articles.EmailWidgets;

/// <summary>
/// Article content types filter.
/// </summary>
internal sealed class ArticleContentTypesFilter : IContentTypesFilter
{
    /// <summary>
    /// Content type GUID identifiers allowed for <see cref="ArticleWidget"/>.
    /// </summary>
    public IEnumerable<Guid> AllowedContentTypeIdentifiers { get; }

    /// <summary>
    /// Article content types filter.
    /// </summary>
    /// <param name="trainingGuidesEmailBuilderOptions">The email builder options, configured at startup.</param>
    public ArticleContentTypesFilter(IOptions<TrainingGuidesEmailBuilderOptions> trainingGuidesEmailBuilderOptions)
    {
        var codeNames = trainingGuidesEmailBuilderOptions.Value.AllowedArticleContentTypes;

        AllowedContentTypeIdentifiers = DataClassInfoProvider.ProviderObject.Get()
            .WhereIn(nameof(DataClassInfo.ClassName), codeNames)
            .Column(nameof(DataClassInfo.ClassGUID))
            .GetListResult<Guid>();
    }
}
```

## Define the widget properties
Now, we need widget properties, where marketers can enter data to configure the widget.
For the sake of simplicity in this example, let’s use a single property that allows users to select an article. We should be able to gather all the information the widget needs from the selected article.
Create a file called **ArticleEmailWidgetProperties.cs** in the _~/Features/Articles/EmailWidgets_ folder:
Remember to assign the content type filter from earlier to the selector.
C#
**ArticleEmailWidgetProperties.cs**
Copy
```
using CMS.ContentEngine;
using Kentico.EmailBuilder.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;

namespace TrainingGuides.Web.Features.Articles.EmailWidgets;

/// <summary>
/// Configurable properties of the <see cref="ContentWidget"/>.
/// </summary>
public sealed class ArticleEmailWidgetProperties : IEmailWidgetProperties
{
    [ContentItemSelectorComponent(
        contentTypeFilter: typeof(ArticleContentTypesFilter),
        Label = "Select article",
        ExplanationTextAsHtml = true,
        ExplanationText = "The widget will display the content from the selected article.",
        Order = 10,
        MaximumItems = 1)]
    public IEnumerable<ContentItemReference> ArticlePage { get; set; } = [];
}
```

You can find details about the various form controls available for Email Builder component properties in [the documentation](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).
## Define the widget model
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
These types exist to show the process of handling content model expansion over time in the [advanced content module](modules/advanced-content).
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

## Build the widget Razor component
Now we’ve completed all the setup, and it’s time to implement the actual razor component that represents the widget.
### Create the code-behind
Start by creating a file called **ArticleEmailWidget.razor.cs** in the _~/Features/Articles/EmailWidgets_ folder. Use the mapper to populate the widget model.
Include properties to represent the `ArticleWidgetViewModel` the widget will use and the `ArticleEmailWidgetProperties` that marketers will configure.
Then use the model mapper from earlier to populate the model based on the selected article page.
C#
**ArticleEmailWidget.razor.cs**
Copy
```
using Microsoft.AspNetCore.Components;
using TrainingGuides.Web.Features.Articles.EmailWidgets;
using Kentico.EmailBuilder.Web.Mvc;
using Kentico.Xperience.Mjml.StarterKit.Rcl.Mapping;

[assembly: RegisterEmailWidget(
    identifier: ArticleEmailWidget.IDENTIFIER,
    name: "Article",
    componentType: typeof(ArticleEmailWidget),
    PropertiesType = typeof(ArticleEmailWidgetProperties),
    IconClass = "icon-l-list-img-article",
    Description = "Displays an article teaser link with an image, summary, and title."
    )]

namespace TrainingGuides.Web.Features.Articles.EmailWidgets;

public partial class ArticleEmailWidget : ComponentBase
{
    /// <summary>
    /// The component identifier.
    /// </summary>
    public const string IDENTIFIER = $"TrainingGuides.{nameof(ArticleEmailWidget)}";

    [Inject]
    private IComponentModelMapper<ArticleEmailWidgetModel> ArticleComponentModelMapper { get; set; } = default!;

    [Inject]
    private IEmailContextAccessor EmailContextAccessor { get; set; } = default!;

    /// <summary>
    /// The widget properties.
    /// </summary>
    [Parameter]
    public ArticleEmailWidgetProperties Properties { get; set; } = new();

    public ArticleEmailWidgetModel Model { get; set; } = new();

    /// <inheritdoc />
    protected override async Task OnInitializedAsync()
    {
        var itemGuid = Properties.ArticlePage.FirstOrDefault()?.Identifier;

        string languageName = EmailContextAccessor.GetContext().LanguageName;
        // Using the mapper here means you do not need to change this code when reusing this widget in a different project.
        Model = await ArticleComponentModelMapper.Map(itemGuid ?? Guid.Empty, languageName);
    }
}
```

### Design the widget front-end
With the code-behind in place, we can move on to the widget’s display.
[Email builder components](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components) also support HTML markup, though we recommend MJML as it automatically handles the messy conditional markup required for handling diverse email clients.
Add a file called **ArticleEmailWidget.razor** in the same folder and use MJML markup to define the widget’s appearance:
cshtml
**ArticleEmailWidget.razor**
Copy
```
<mj-image src="@Model.ArticleTeaserImage.ImageUrl" alt="@Model.ArticleTeaserImage.AltText"></mj-image>
<mj-text>
    <h1>@Model.ArticleTitle</h1>
    <p>
        <em>@Model.ArticleSummary</em>
    </p>
</mj-text>
<mj-button href="@Model.ArticleUrl">
    Read More
</mj-button>
```

Keep in mind that MJML generates a lot of markup. Overuse of complex elements can lead to issues with certain email clients and spam filters. We recommend keeping widgets simple.
## See the results
Rebuild your project and create a new **Basic email** in the **Training guides emails** channel. On the **Email builder** tab of the email, you can now display article previews that link to the full articles in your website channel.
Your browser does not support the video tag. 
## Looking forward
For simplicity, this example included the widget’s files directly in the _TrainingGuides.Web_ project.
To take full advantage of the portability that we built into the _Article_ email widget, consider moving the widget’s files to a separate RCL (Razor Class Library) project, which you can copy into other solutions or create a NuGet package from.
You can use this project to build a whole library of widgets that you can quickly implement for each of your clients.
Your RCL should include:
  * The widget’s Razor component 
    * `ArticleEmailWidget.razor`
    * `ArticleEmailWidget.razor.cs`
  * The widget’s model and properties 
    * `ArticleEmailWidgetModel`
    * `ArticleEmailWidgetProperties.cs`
  * The content types filter 
    * `ArticleContentTypesFilter.cs`
  * The options to configure the widget at startup 
    * `TrainingGuidesEmailBuilderOptions.cs`


**If you intend to make your library into a NuGet package** , you also need to include the files from the [Kentico.Xperience.Mjml.StarterKit.Rcl](https://github.com/Kentico/xperience-by-kentico-email-builder-starter-kit/tree/main/src/Kentico.Xperience.Mjml.StarterKit.Rcl) library of the Email Builder Starter Kit. It may be easier to create your own fork of the repository and add your widgets to this project.
Then, you only need these steps for each project where you use the widget:
  1. Implement the model mapper class.
  2. Register the model mapper as an implementation of `IComponentModelMapper<TModel>` during startup.
  3. Configure the options class during startup.


If your agency uses standardized content types for common scenarios, you’ll be able to copy-paste the code for each of these steps with minimal edits.
To see more examples of Email Builder components in action, we recommend checking out those used on the [Community Portal](https://github.com/Kentico/community-portal/tree/v30.5.1.6/src/Kentico.Community.Portal.Web/Components/EmailBuilder).
Here, you can see the code that makes up [community.kentico.com](https://community.kentico.com/), our site for the Xperience by Kentico developer community that features many helpful resources.
## What’s next
To see an example of an Email Builder template that displays content from a richly structured email content type, continue on to [the next guide in this series](guides/development/email-marketing/display-structured-content-email-builder).