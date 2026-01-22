---
source: https://docs.kentico.com/guides/development/upgrade-deep-dives/convert-child-pages-to-widgets
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Upgrade deep dives](/guides/development/upgrade-deep-dives)
  * Convert child pages to widget content 


# Convert child pages to widget content
In this technical deep dive, we’ll show you how to convert child pages from Kentico Xperience 13 (KX13) into flexible Page Builder widgets when upgrading to Xperience by Kentico (XbyK). You’ll learn how to use the [Kentico migration tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool) to transform child pages into reusable Content hub items and render them as widgets that editors can manage through Page Builder.
This gives content editors more control over page layout and structure while preserving all the original content.
## Before you start
This guide assumes you have:
  * Basic knowledge of C# and .NET framework concepts
  * Familiarity with the basics of [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) widgets in Xperience by Kentico and in Kentico Xperience 13
  * Basic understanding of the [KX13 to XbyK upgrade process](/guides/architecture/upgrade-from-kx13/upgrade-from-kx13-overview) using the Kentico Migration tool.


If you haven’t, we recommend following along with the [upgrade walkthrough](/guides/architecture/upgrade-from-kx13/upgrade-walkthrough) to experience the process of getting a migrated page working in Xperience by Kentico. This guide builds on top of the walkthrough, assuming you have a running KX13 Dancing Goat sample site and a target XbyK _DancingGoat_ solution with two projects: _DancingGoat.Web_ and _DancingGoat.Entities_.
## Understand the scenario and the solution
We’ll use the Dancing Goat _About us_ page as our example.
In the content tree, the page contains multiple child items of type _About Us section_ , which the _About us_ page loads and renders in its view. The page is not using Page Builder, so the editor can’t access the **Page** tab or modify the layout.
[![The ‘About us section’ content in administration](/docsassets/guides/convert-child-pages-to-widgets/about-us-sections-admin.png)](/docsassets/guides/convert-child-pages-to-widgets/about-us-sections-admin.png)
[![The ‘About us’ page](/docsassets/guides/convert-child-pages-to-widgets/about-us-sections-live-site.gif)](/docsassets/guides/convert-child-pages-to-widgets/about-us-sections-live-site.gif)
Let’s change that in our target XbyK instance. Instead of utilizing the .NET routing, controller and hardcoded view, we’ll use an existing page template with Page Builder set up, and display content in widgets.
Additionally, we’ll extract the content into the Content hub for possible future reuse.
To accomplish this transformation, we’ll configure the migration tool to convert the child pages into Content hub items instead of web pages.
Then we’ll create a custom director class that generates widgets on the parent page, with each widget referencing the corresponding converted content item.
After running the data migration, we’ll implement the widget code in the target XbyK instance to render the converted content through Page Builder.
## Convert the child pages into reusable content
Start by configuring the migration tool to convert your child pages into Content hub items. Add the child page class to `ConvertClassesToContentHub` in your migration tool’s `appsettings.json`:
JSON
**appsettings.json**
Copy
```
{
  "MigrationProtocolSettings": {
    "ConvertClassesToContentHub": [
      "DancingGoatCore.AboutUsSection"
    ]
  }
}
```

This configuration tells the migration tool to convert `AboutUsSection` pages into Content hub items instead of regular web pages.
## Create widget content referencing the reusable items
Now you need to tell the migration tool how to handle the pages in your content tree. Create a custom Director class in the _Migration.Tool.Extensions/CommunityMigrations_ folder to generate widgets on the parent page.
The Director class controls the conversion process by evaluating each node in your content tree and determining what should happen to it. For our scenario, we need to:
  * **Identify the parent page** that will host the widgets in XbyK (the _About us_ page)
  * **Override the page template** to use Page Builder instead of the original hardcoded view
  * **Convert child pages to widgets** that will display the converted content
  * **Set widget placement** by specifying where each widget should appear on the parent page
  * **Define widget properties** to hold the reusable content item reference and other configuration options


Our director class should process all nodes under the `/About-Us` path, including the parent page and all its children. We should evaluate each node and react based on its type and location in the tree.
**Widget properties mapping**
You can either map content to widget properties one-to-one, simply using `JObject.FromObject(contentItemProps)` (see an example is one of our [sample Director classes](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/cd841d529344a3fb375096ea27d66c6f35362592/Migration.Tool.Extensions/CommunityMigrations/SamplePageToWidgetDirector.cs#L31) ), or create a custom `JObject` for more control.
We’ll use the manual approach to link widgets to Content hub items and add an alignment property, since the original _About us_ page alternates between left-aligned and right-aligned layouts.
C#
**AboutUsSectionsToWidgetsDirector.cs**
Copy
```
using Migration.Tool.Source.Mappers.ContentItemMapperDirectives;
using Newtonsoft.Json.Linq;

namespace Migration.Tool.Extensions.CommunityMigrations;

public class AboutUsSectionsToWidgetsDirector : ContentItemDirectorBase
{
    public override void Direct(ContentItemSource source, IContentItemActionProvider options)
    {
        // Apply the conversions only to nodes under /About-Us (or your desired KX13 tree node)
        // The system will evaluate all following conditions for each tree node which node AliasPath starts with /About-Us
        // This includes the /About-Us node itself and all its child nodes (/About-Us/Our-philosophy, /About-Us/References, etc.)
        if (source.SourceNode!.NodeAliasPath.StartsWith("/About-Us"))
        {
            // Identify the KX13 tree node that will host the widget in XbyK
            if (source.SourceNode.NodeAliasPath == "/About-Us")
            {
                // Ensure this template exists in the XbyK target instance
                options.OverridePageTemplate("DancingGoat.LandingPageSingleColumn");
            }
            // Nodes to be converted to widgets. We will identify them by SourceClassName
            // In some cases we may have to specify more conditions here to match a specific node
            else if (source.SourceClassName == "DancingGoatCore.AboutUsSection")
            {
                // The widget identifier must match the one defined in the XbyK target project
                options.AsWidget("DancingGoat.Widgets.AboutUsSection", null, null, options =>
                {
                    // Determine where to embed the widget
                    options.Location
                        .OnAncestorPage(-1)
                        // The area has to match what's defined in the XbyK project template's view
                        .InEditableArea("top")
                        // The section name has to match what's defined in the XbyK project
                        .InSection("DancingGoat.SingleColumnSection")
                        .InFirstZone();

                    // Construct the widget's properties
                    options.Properties.Fill(true, (itemProps, reusableItemGuid, childGuids) =>
                    {
                        // Manual property configuration for more control
                        var widgetProps = new JObject();

                        // Link to the converted Content hub item
                        widgetProps["aboutUsSectionItem"] = LinkedItemPropertyValue(reusableItemGuid!.Value);
                        widgetProps["alignment"] = "ImageLeft";

                        return widgetProps;
                    });
                });
            }
            else
            {
                // Discard all other child nodes or handle them differently - based on your client's needs
                options.Drop();
            }
        }
        else
        {
            // Add any handling you want to apply to pages that are not under the /About-us node
        }
    }
}
```

Remember to register your `AboutUsSectionsToWidgetsDirector` in the `ServiceCollectionExtensions` class:
C#
**Migration.Tool.Extensions/ServiceCollectionExtensions.cs**
Copy
```
...
public static class ServiceCollectionExtensions
{
    public static IServiceCollection UseCustomizations(this IServiceCollection services)
    {
        ...

        services.AddTransient<ContentItemDirectorBase, AboutUsSectionsToWidgetsDirector>();
        ...
        return services;
    }
}
 


```

**Rebuild** your local copy of the Kentico Migration Tool and run the migration. For the director to take effect, you need to use the `--pages` parameter.
**Check your progress**
To check whether your _About us_ page migrated correctly, look at the migrated Page Builder JSON data, stored in the `[CMS_ContentItemCommonData].[ContentItemCommonDataVisualBuilderWidgets]` column of your target database:
SQL
**View the ‘About us’ page Page Builder data**
Copy
```
SELECT ContentItemCommonDataVisualBuilderWidgets FROM CMS_ContentItemCommonData
    WHERE ContentItemCommonDataGUID = '<GUID_OF_ABOUT_US_PAGE_FROM_KX13>'
```

If you’re following our example, you should see the JSON Page Builder data with three widgets like this: 
JSON
Copy
```
...
{
    "identifier": "6ba9525d-d011-451a-94a3-59f07146fdfc",
    "type": "DancingGoat.Widgets.AboutUsSection",
    "conditionType": null,
    "variants": [
        {
            "identifier": "6dcb30e3-e4a8-4531-9e01-0788b2c5b475",
            "name": null,
            "properties": {
                "aboutUsSectionItem": [
                    {
                        "identifier": "53eaf1f4-f714-4002-8b66-b1a1ae8a5d3c"
                    }
                ],
                "alignment": "ImageLeft"
            },
            "conditionTypeParameters": null
        }
    ]
}
...
```

## Build your new widget in target instance
After successful data migration, it’s time to implement the widget in your target XbyK instance.
### Generate code files
First, we need to [generate code files](/documentation/developers-and-admins/api/generate-code-files-for-system-objects#generate-code-files) for the newly generated ‘DancingGoatCore.AboutUsSection’ content type.
Run this command from the root of your _DancingGoat.Web_ project: 
CMD
Copy
```
dotnet run -- --kxp-codegen --type "ReusableContentTypes" --location "../DancingGoat.Entities/{type}/{name}" --include "DancingGoatCore.AboutUsSection"
```

If you see errors or class name conflicts after code file generation, check whether you have `AboutUsSection` code files in your _PageContentTypes_ folder. This would happen if you generated code files for page content types **before** running the migration.
To resolve this, delete the generated class under _PageContentTypes_ and keep the one under _ReusableContentTypes_ in your _DancingGoat.Entities_ project.
### Implement the widget
Then implement your widget with these files:
C#
**AboutUsSectionWidgetProperties.cs**
Copy
```
using System.Collections.Generic;
using CMS.ContentEngine;
using DancingGoatCore;
using Kentico.Components.Web.Mvc.FormComponents;
using Kentico.Forms.Web.Mvc;
using Kentico.PageBuilder.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;

namespace DancingGoat.Widgets
{
    /// <summary>
    /// About Us Section widget properties.
    /// </summary>
    public class AboutUsSectionWidgetProperties : IWidgetProperties
    {
        [ContentItemSelectorComponent(
            AboutUsSection.CONTENT_TYPE_NAME,
            Label = "About Us Section Item",
            ExplanationText = "Choose the About Us Section item to be displayed in the widget.",
            MaximumItems = 1,
            Order = 10)]
        public IEnumerable<ContentItemReference> AboutUsSectionItem { get; set; } = [];

        [DropDownComponent(
            Label = "Alignment",
            Options = "ImageLeft;Image Left\r\nImageRight;Image Right",
            Order = 20)]
        public string Alignment { get; set; } = "ImageLeft";
    }
}
```

C#
**AboutUsSectionWidgetViewComponent.cs**
Copy
```
using System.Linq;
using System.Threading.Tasks;
using DancingGoat.Widgets;
using DancingGoatCore;
using Kentico.Content.Web.Mvc;
using Kentico.PageBuilder.Web.Mvc;
using Microsoft.AspNetCore.Html;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.ViewComponents;

// Remember to register the new widget
[assembly: RegisterWidget(
    identifier: AboutUsSectionWidgetViewComponent.IDENTIFIER,
    viewComponentType: typeof(AboutUsSectionWidgetViewComponent),
    name: "About us section",
    propertiesType: typeof(AboutUsSectionWidgetProperties),
    Description = "Displays an About us section with heading, text, and image in different alignments.",
    IconClass = "icon-rectangle-o")]

namespace DancingGoat.Widgets
{
    /// <summary>
    /// View component for About Us Section widget
    /// </summary>
    public class AboutUsSectionWidgetViewComponent(IContentRetriever contentRetriever) : ViewComponent
    {
        /// <summary>
        /// Widget identifier.
        /// </summary>
        public const string IDENTIFIER = "DancingGoat.Widgets.AboutUsSection";

        public async Task<ViewViewComponentResult> InvokeAsync(AboutUsSectionWidgetProperties properties)
        {
            var model = new AboutUsSectionWidgetViewModel();

            if (properties.AboutUsSectionItem != null)
            {
                var parameters = new RetrieveContentParameters
                {
                    LinkedItemsMaxLevel = 3,
                };
                var aboutUsSection = (await contentRetriever.RetrieveContentByGuids<AboutUsSection>(
                    properties.AboutUsSectionItem
                        .Select(x => x.Identifier),
                    parameters)).FirstOrDefault();

                var image = (Attachment)aboutUsSection?.AboutUsSectionImage.FirstOrDefault();

                model = new AboutUsSectionWidgetViewModel
                {
                    Heading = aboutUsSection?.AboutUsSectionHeading ?? string.Empty,
                    Text = new HtmlString(aboutUsSection?.AboutUsSectionText ?? string.Empty),
                    ImagePath = image?.LegacyAttachmentAsset?.Url ?? string.Empty,
                    Alignment = properties.Alignment
                };
            }
            return View("~/Components/Widgets/AboutUsSectionWidget/_AboutUsSectionWidget.cshtml", model);
        }
    }
}
```

C#
**AboutUsSectionWidgetViewModel.cs**
Copy
```
using Microsoft.AspNetCore.Html;

namespace DancingGoat.Widgets
{
    /// <summary>
    /// View model for About Us Section widget.
    /// </summary>
    public class AboutUsSectionWidgetViewModel
    {
        public string Heading { get; set; }
        public HtmlString Text { get; set; }
        public string ImagePath { get; set; }
        public string Alignment { get; set; }
    }
}
```

cshtml
**_AboutUsSectionWidget.cshtml**
Copy
```
@using DancingGoat.Widgets

@model AboutUsSectionWidgetViewModel

@if (Model != null)
{
    <section class="row text-and-image">
        <h2 class="col-lg-12">@Model.Heading</h2>

        <div class="col-md-6 col-xl-8 @(Model.Alignment == "ImageRight" ? "col-md-push-6 col-xl-push-4" : "")">
            <div class="text-and-image-text @(Model.Alignment == "ImageRight" ? "text-right" : "")">
                @Model.Text
            </div>
        </div>
        <div class="col-md-6 col-xl-4 @(Model.Alignment == "ImageRight" ? "col-md-pull-6 col-xl-pull-8" : "")">
            @if (!string.IsNullOrEmpty(Model.ImagePath))
            {
                <img src="@Model.ImagePath" alt="@Model.Heading" title="@Model.Heading" class="img-responsive" />
            }
        </div>
    </section>
}
```

Build and run your target solution.
## See the result
Provided your target solution includes implementation for the _DancingGoat.LandingPageSingleColumn_ page template and the _DancingGoat.SingleColumnSection_ Page Builder section from Dancing Goat KX13, you should now be able to see your _About us_ page in the administration interface showing the migrated content in widgets:
Your browser does not support the video tag. 
You’ve converted rigid child pages into flexible Page Builder widgets giving editors full control over content layout and structure.
## What’s next?
For more migration scenarios, explore our other [upgrade deep dive guides](/guides/development/upgrade-deep-dives).
If you haven’t already, consider following along with the [upgrade walkthrough](/guides/architecture/upgrade-from-kx13/upgrade-walkthrough) to experience the process of getting a migrated page working in Xperience by Kentico from start to finish.
If you encounter any challenging scenarios during your own widget migrations, or if you have ideas for subjects we haven’t covered, don’t hesitate to reach out to us through the **Send us feedback** button at the bottom of this page.
![]()
[]()[]()
