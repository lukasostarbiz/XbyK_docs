# Deliver content dynamically using smart folders
  * How-to| [ Copy page link ](guides/development/advanced-content/deliver-content-dynamically-with-smart-folders#) | [Get HelpService ID](guides/development/advanced-content/deliver-content-dynamically-with-smart-folders#) | This page is part of a module: [ Advanced content ](modules/advanced-content)
Core MVC 5


[✖](guides/development/advanced-content/deliver-content-dynamically-with-smart-folders# "Close page link panel") [Copy to clipboard](guides/development/advanced-content/deliver-content-dynamically-with-smart-folders#)
[Smart folders](documentation/business-users/content-hub/content-hub-folders#smart-folders) provide content editors with a high level of flexibility, allowing them to organize content and dynamically control its delivery in their channels.
Smart folders out-of-the-box do **not** allow for dynamic content delivery in [headless](documentation/developers-and-admins/configuration/headless-channel-management) and [email](documentation/developers-and-admins/digital-marketing-setup/email-channel-management) channels. Read more in our [documentation](documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-content-items-from-smart-folders).
Let’s explore how you, as a developer, can enable these flexible features for editors in a widget. We will build a simple Gallery widget that dynamically loads content from a smart folder of the editor’s choice.
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 
  * Experience with [Page Builder widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder), [taxonomies](documentation/developers-and-admins/configuration/taxonomies), [Content hub](documentation/business-users/content-hub) and [smart folders](documentation/business-users/content-hub/content-hub-folders#smart-folders) in Xperience by Kentico.


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Consider the scenario
Your client owns a vacation resort. On their website they want to feature several image galleries showcasing the rooms, the amenities, the surroundings and other categories of images. The editors need the flexibility to adjust which images and which categories of images they display, based on the season and marketing requirements.
To fulfill these requirements you are going to build a gallery widget that displays images from a smart folder of editor’s choice.
In your client’s system you have already created a _Gallery image_ content type. It contains an **Asset** to hold the image to display, and a **Category** of the image defined by a Taxonomy.
[![Gallery image content item in the Xperience administration](docsassets/guides/deliver-content-dynamically-with-smart-folders/gallery-image-content-type.png)](https://docs.kentico.com/docsassets/guides/deliver-content-dynamically-with-smart-folders/gallery-image-content-type.png)
The editors will organize _Gallery image_ content items into smart folders based on **Category** field.
Smart folders enable content editors to save complex content views for dynamic delivery across channels.
In this example, a smart folder will automatically update the Gallery widget on your client’s website, ensuring the desired images replace previous ones **without manual intervention**. This is what makes the smart folders such a powerful tool that’s worth considering in your solution’s content modeling.
[![Smart folder example in the Xperience administration](docsassets/guides/deliver-content-dynamically-with-smart-folders/smart-folders.png)](https://docs.kentico.com/docsassets/guides/deliver-content-dynamically-with-smart-folders/smart-folders.png)
Your job is to implement the widget that lets the editors select a specific smart folder and retrieve and display the appropriate _Gallery image_ content items.
Additionally, the client wants the option to decide how many content items to display and sort them by newest or oldest first.
The examples here assume your system already includes several **Gallery image** content items organized into smart folders based on the **Category**.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of our Training guides repository has all this data prepared. To follow along, we recommend starting there, or you can set up your own content type and data in your system similarly.
## Retrieve content from smart folder
Let’s start with implementing smart folder content retrieval in your project.
You can [retrieve items from a smart folder](documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-content-items-from-smart-folders) using [ContentRetriever API](documentation/developers-and-admins/api/content-item-api/content-retriever-api) and its `RetrieveContent` extension method with `InSmartFolder` parametrization.
In the `ContentItemRetrieverService` (located in _TrainingGuides.Web/Features/Shared/Services/_), create a generic `RetrieveReusableContentItemsFromSmartFolder` method that utilizes this method and retrieves data from a smart folder identified by its `Guid`. Let’s also add other parameters to allow more data filtering and ordering - `orderBy`, `topN` and `depth`, and an optional parameter to specify language of the content.
Remember that to be able to retrieve content from a smart folder, the folder has to have [_Dynamic content delivery_ enabled](documentation/business-users/content-hub/content-hub-folders#enable-content-delivery-for-smart-folders).
C#
**IContentItemRetrieverService.cs**
Copy
```
...
public interface IContentItemRetrieverService
{
    ...
    /// <summary>
    /// Retrieves reusable content items of specified type from specified smart folder.
    /// </summary>
    /// <param name="contentTypeName">Content type name of the content items the method should return</param>
    /// <param name="smartFolderGuid">Guid of the smart folder to retrieve the content items from</param>
    /// <param name="orderBy">Order the returned items ascending/descending</param>
    /// <param name="topN">Number of items to return</param>
    /// <param name="depth">The maximum level of recursively linked content items that should be included in the results. Default value is 1.</param>
    /// <param name="includeSecuredItems">If true, secured items will be included in the results.</param>
    /// <param name="languageName">The language to query. If null, the language will be inferred from the URL of the current request.</param>
    /// <returns>Enumerable collection of content items from the given smart folder</returns>
    Task<IEnumerable<T>> RetrieveReusableContentItemsFromSmartFolder<T>(
        Guid smartFolderGuid,
        OrderByOption orderBy,
        int topN = 20,
        int depth = 1,
        string? languageName = null)
        where T : IContentItemFieldsSource, new();
    ...
}
...
```

C#
**ContentItemRetrieverService.cs**
Copy
```
...
public class ContentItemRetrieverService : IContentItemRetrieverService
{
    ...
    /// <inheritdoc />
    public async Task<IEnumerable<T>> RetrieveReusableContentItemsFromSmartFolder<T>(
        Guid smartFolderGuid,
        OrderByOption orderBy,
        int topN = 20,
        int depth = 1,
        string? languageName = null)
        where T : IContentItemFieldsSource, new()
    {
        const string LAST_PUBLISHED_COLUMN_NAME = "ContentItemCommonDataLastPublishedWhen";

        var parameters = new RetrieveContentParameters
        {
            LinkedItemsMaxLevel = depth,
            LanguageName = languageName ?? preferredLanguageRetriever.Get(),
            IsForPreview = webSiteChannelContext.IsPreview
        };

        return await contentRetriever.RetrieveContent<T>(
            parameters,
            query => query
                .InSmartFolder(smartFolderGuid)
                .OrderBy(new OrderByColumn(
                    LAST_PUBLISHED_COLUMN_NAME,
                    orderBy.Equals(OrderByOption.NewestFirst) ? OrderDirection.Descending : OrderDirection.Ascending))
                .TopN(topN),
            RetrievalCacheSettings.CacheDisabled);
    }
    ...
}
...
 


```

## Build a widget with smart folder selector
Now let’s implement the _Gallery widget_. If you’re working in the Training guides repository, create a _Features/Gallery/Widgets/GalleryWidget_ folder in the _TrainingGuides.Web_ project for all the widget files.
### Widget properties
Define gallery widget properties in a `GalleryWidgetProperties` class. In this example, we allow editors to specify the smart folder, gallery title, number of images to display and a choice to order items by newest or by oldest first.
To let the editor select a smart folder, use the [SmartFolderSelectorComponent](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#smart-folder-selector).
Allow only items of type `GalleryImage` by implementing a new custom class `GalleryImageContentTypeFilter` that implements the `IContentTypesNameFilter` and assigning it in the `AllowedContentTypeIdentifiersFilter` option.
Smart folders can include items of multiple content types. Specifying the `AllowedContentTypeIdentifiersFilter` ensures that only the content types you define in the custom class will load from the specified smart folder.
C#
**GalleryWidgetProperties.cs**
Copy
```
using CMS.ContentEngine;
using Kentico.PageBuilder.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;
using TrainingGuides.Web.Features.Shared.OptionProviders;
using TrainingGuides.Web.Features.Shared.OptionProviders.OrderBy;

namespace TrainingGuides.Web.Features.Gallery.Widgets.GalleryWidget;

public class GalleryWidgetProperties : IWidgetProperties
{
    [SmartFolderSelectorComponent(
        AllowedContentTypeIdentifiersFilter = typeof(GalleryImageContentTypeFilter),
        Label = "Smart folder",
        ExplanationText = "Select smart folder containing Gallery images you wish to display",
        Order = 10)]
    public SmartFolderReference SmartFolder { get; set; } = new SmartFolderReference();

    [TextInputComponent(
        Label = "Title",
        Order = 20)]
    public string Title { get; set; } = "Gallery";

    [NumberInputComponent(
        Label = "Number of images to display",
        Order = 30)]
    public int TopN { get; set; } = 10;

    [DropDownComponent(
        Label = "Order images by",
        DataProviderType = typeof(DropdownEnumOptionProvider<OrderByOption>),
        Order = 40)]
    public string OrderBy { get; set; } = OrderByOption.NewestFirst.ToString();
}

// Filters the smart folders available in the selector to those that allow the 'GalleryImage' content type
public class GalleryImageContentTypeFilter : IContentTypesNameFilter
{
    IEnumerable<string> IContentTypesNameFilter.AllowedContentTypeNames => [GalleryImage.CONTENT_TYPE_NAME];
}
```

Add `OrderByOption` enumeration. We are utilizing the `DropdownEnumOptionProvider` to map the enumeration to a dropdown. See details in our [guide series about Page Builder](guides/development/page-builder/map-enum-to-dropdown).
C#
**TrainingGuides.Web/Features/Shared/OptionProviders/OrderBy /OrderByOption.cs**
Copy
```
using System.ComponentModel;

namespace TrainingGuides.Web.Features.Shared.OptionProviders.OrderBy;
public enum OrderByOption
{
    [Description("Newest first")]
    NewestFirst,
    [Description("Oldest first")]
    OldestFirst
}
```

### Widget view model
Next, create a `GalleryWidgetViewModel` class. If you are coding along using the Training guides repo, we recommend to make the class inherit from the [WidgetViewModel](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/main/src/TrainingGuides.Web/Features/Shared/Models/WidgetViewModel.cs) class for consistency, and to make sure your widget has the `IsMisconfigured` property override for error handling.
Our widget view model also needs to have a `Title` of the gallery and the list of images that will load from a smart folder.
C#
**GalleryWidgetViewModel.cs**
Copy
```
using TrainingGuides.Web.Features.Shared.Models;

namespace TrainingGuides.Web.Features.Gallery.Widgets.GalleryWidget;

public class GalleryWidgetViewModel : WidgetViewModel
{
    public string Title { get; set; } = string.Empty;
    public List<AssetViewModel> Images { get; set; } = [];
    public override bool IsMisconfigured => Images == null || Images.Count == 0;
}
```

### Widget view
The view of our Gallery widget is relatively simple.
First, handle the misconfigured state of the widget and display a user-friendly message to the editor. To display the message, this code sample utilizes the [ConfigureWidgetInstructionsTagHelper](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/Helpers/TagHelpers/ConfigureWidgetInstructionsTagHelper.cs) and the [PageBuilderContentTagHelper](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/Helpers/TagHelpers/PageBuilderContentTagHelper.cs) tag helpers which you can find in the finished branch of the Training guides repository.
Then, simply iterate through the images in our view model to display them in a grid.
In our code samples we purposely keep the widget view very simple, utilizing just a few Bootstrap classes to style. Feel free to add complexity and beautify the widget as you see fit or according to your requirements, e.g., by enlarging the image in a modal after a site visitor clicks on it.
C#
**GalleryWidget.cshtml**
Copy
```
@model TrainingGuides.Web.Features.Gallery.Widgets.GalleryWidget.GalleryWidgetViewModel?

@if (Model == null || Model.IsMisconfigured)
{
    <tg-page-builder-content>
        <div class="row justify-content-center">
            <div class="col-xl-11">
                <div class="row" data-gridify="3-columns">
                    <h3>Gallery</h3>
                    @if(Model?.Images?.Count == 0)
                    {
                        <tg-configure-widget-instructions message="No images found. Double-check your smart folder selection."/>
                    }
                    else
                    {
                        <tg-configure-widget-instructions/>
                    }
                </div>
            </div>
        </div>
    </tg-page-builder-content>

    return;
}
else
{
    <div class="row justify-content-center">
        <div class="col-xl-11">
            <h3>@Model.Title</h3>
            <div class="row row-cols-1 row-cols-sm-2 row-cols-lg-3 c-blog-grid mt-3 mb-4 mt-md-4">
                @foreach (var image in Model.Images!)
                {
                    <div class="col mt-4">
                        <div class="c-card bg-3">
                            <a href="@Url.Content(image.FilePath)" >
                                <div class="c-card_img">
                                    <img src="@Url.Content(image.FilePath)" alt="@image.AltText" class="img-fluid gallery-img" />
                                </div>
                            </a>
                        </div>
                    </div>
                }
            </div>
        </div>
    </div>
}
```

### Widget view component
Our widget view component needs four key parts:
  1. The `InvokeAsync` method that uses the widget properties to fill out the view model and return the view
  2. The `RetrieveGalleryImages` method that calls our `galleryImageRetriever` service to get the content items from selected smart folder.
  3. The `GetGalleryImageViewModel` method to map the retrieved data to the view model
  4. The widget registration


C#
**GalleryWidgetViewComponent.cs**
Copy
```
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.ViewComponents;
using Kentico.PageBuilder.Web.Mvc;
using TrainingGuides.Web.Features.Shared.OptionProviders.OrderBy;
using TrainingGuides.Web.Features.Shared.Services;
using TrainingGuides.Web.Features.Gallery.Widgets.GalleryWidget;
using TrainingGuides.Web.Features.Shared.Models;

[assembly:
    RegisterWidget(GalleryWidgetViewComponent.IDENTIFIER, typeof(GalleryWidgetViewComponent), "Gallery widget",
        typeof(GalleryWidgetProperties), Description = "Displays gallery of images from a smart folder", IconClass = "icon-pictures")]

namespace TrainingGuides.Web.Features.Gallery.Widgets.GalleryWidget;

public class GalleryWidgetViewComponent : ViewComponent
{
    public const string IDENTIFIER = "TrainingGuides.GalleryWidget";

    private readonly IContentItemRetrieverService contentItemRetrieverService;

    public GalleryWidgetViewComponent(IContentItemRetrieverService contentItemRetrieverService)
    {
        this.contentItemRetrieverService = contentItemRetrieverService;
    }

    public async Task<ViewViewComponentResult> InvokeAsync(GalleryWidgetProperties properties)
    {
        var model = new GalleryWidgetViewModel
        {
            Title = properties.Title
        };

        // retrieve the smart folder guid from the SmartFolderSelectorComponent
        var smartFolderGuid = properties.SmartFolder?.Identifier ?? Guid.Empty;
        var orderBy = properties.OrderBy.Equals(OrderByOption.OldestFirst.ToString())
            ? OrderByOption.OldestFirst
            : OrderByOption.NewestFirst;
        int topN = properties.TopN;

        // if the editor selected a smart folder, retrieve the Gallery image content items using the selected property values
        if (!smartFolderGuid.Equals(Guid.Empty))
        {
            var galleryImages = await RetrieveGalleryImages(smartFolderGuid, orderBy, topN);
            // map the images content to the view model
            model.Images = galleryImages.Select(GetGalleryImageViewModel).ToList();
        }

        return View("~/Features/Gallery/Widgets/GalleryWidget/GalleryWidget.cshtml", model);
    }

    // retrieves the content items from the smart folder
    private async Task<IEnumerable<GalleryImage>> RetrieveGalleryImages(Guid smartFolderGuid, OrderByOption orderBy, int topN) =>
        await contentItemRetrieverService.RetrieveReusableContentItemsFromSmartFolder<GalleryImage>(
            smartFolderGuid,
            orderBy,
            topN);

    // maps the data from the Content hub to the view model
    private AssetViewModel GetGalleryImageViewModel(GalleryImage galleryImage) =>
        AssetViewModel.GetViewModel(galleryImage.GalleryImageAsset.FirstOrDefault());
}
```

If your project has a dedicated class for component identifiers, add the new widget identifier into the class. In the Training guides repository this means the [`ComponentIdentifiers` class](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/ComponentIdentifiers.cs).
C#
**TrainingGuides.Web/ComponentIdentifiers.cs**
Copy
```
...
public static class Widgets
{
    ...
    public const string GALLERY = GalleryWidgetViewComponent.IDENTIFIER;
}
...
```

## See the result
Build and run your application. You should now be able to add and configure the new Gallery widget on your pages.
Your browser does not support the video tag. 
Play around with selecting different smart folders or changing the filter criteria in the existing ones and observe how the displayed website content dynamically changes.
## What’s next?
Using smart folders to dynamically display content in a widget is only one example of possibilities the smart folders open for editors. [Our content modeling guide](guides/architecture/content-modeling/content-modeling-guide/general-content-modeling-recommendations) explores another use case - utilizing smart folders to model navigation.
Feel free to explore our [Content hub documentation](documentation/business-users/content-hub/content-hub-folders#smart-folders) to learn more about smart folders from the editor’s perspective. You can also read about the [smart folders and Content hub folders](documentation/developers-and-admins/configuration/content-hub-configuration#enable-content-hub-folders) from the configuration point of view.