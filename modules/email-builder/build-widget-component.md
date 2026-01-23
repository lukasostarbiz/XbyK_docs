---
source: https://docs.kentico.com/modules/email-builder/build-widget-component
scrape_date: 2026-01-22
---

Module: Email Builder
7 of 13 Pages
# Build the widget Razor component
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
[Email builder components](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components) also support HTML markup, though we recommend MJML as it automatically handles the messy conditional markup required for handling diverse email clients.
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
[ Previous page ](/modules/email-builder/work-with-widget-model)
7 of 13
[ Mark complete and continue ](/modules/email-builder/design-structured-data-template)
![]()
[]()[]()
