---
source: https://docs.kentico.com/modules/email-builder/set-up-widget-properties
scrape_date: 2026-01-26
---

Module: Email Builder
5 of 13 Pages
# Set up properties for an article widget
[Email Builder](/documentation/developers-and-admins/development/builders/email-builder) in Xperience by Kentico allows marketers to design emails using a drag-and-drop interface. By creating custom widgets, developers can extend the functionality of Email Builder to meet specific project requirements.
Let’s explore how to create an [Email Builder widget](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#widgets) for displaying articles. The example here uses tools and follows conventions from our [Xperience Email Builder Starter Kit](https://github.com/Kentico/xperience-by-kentico-email-builder-starter-kit).
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

You can find details about the various form controls available for Email Builder component properties in [the documentation](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).
[ Previous page ](/modules/email-builder/configure-the-starter-kit-and-email-builder)
5 of 13
[ Mark complete and continue ](/modules/email-builder/work-with-widget-model)
![]()
[]()[]()
