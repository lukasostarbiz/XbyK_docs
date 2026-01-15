# Transform widget properties
  * How-to| [ Copy page link ](guides/development/upgrade-deep-dives/transform-widget-properties#) | [Get HelpService ID](guides/development/upgrade-deep-dives/transform-widget-properties#)
Core MVC 5


[✖](guides/development/upgrade-deep-dives/transform-widget-properties# "Close page link panel") [Copy to clipboard](guides/development/upgrade-deep-dives/transform-widget-properties#)
This technical deep dive shows you how to transform widget properties during an upgrade from Kentico Xperience 13 (KX13) to Xperience by Kentico (XbyK). You’ll learn how to migrate properties that use different UI controls, handle custom components, and enhance the editing experience by restructuring widget properties.
While the [Kentico migration tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool) handles most standard widget properties automatically, you often need to customize the migration process when working with complex property configurations or when modernizing widgets as part of the upgrade.
For comprehensive widget restructuring scenarios, see our [Migrate widget data as reusable content](guides/development/upgrade-deep-dives/migrate-widget-data-to-content-hub) page. This material focuses on property-level transformations.
## Before you start
This guide assumes you have:
  * Basic knowledge of C# and .NET framework concepts
  * Familiarity with the basics of [Page Builder](documentation/developers-and-admins/development/builders/page-builder) widgets in Xperience by Kentico and in Kentico Xperience 13
  * Basic understanding of the [KX13 to XbyK upgrade process](guides/architecture/upgrade-from-kx13/upgrade-from-kx13-overview) using the Kentico Migration tool.


If you haven’t, we recommend following along with the [upgrade walkthrough](guides/architecture/upgrade-from-kx13/upgrade-walkthrough) to experience the process of getting a migrated page working in Xperience by Kentico.
## Understand the mechanics
Before exploring specific transformation scenarios, it’s important to understand how widget property migration works. Generally, upgrading widget properties involves two key aspects: _data migration_ and _code migration_.
**Data migration** handles the transformation of existing widget property data from your KX13 database to the new XbyK structure. Most widget properties are straightforward to handle and migrate automatically as they map one-to-one between KX13 and XbyK (e.g., text fields) or have a default migration defined (e.g., pages, attachments/media files).
See our GitHub documentation for [how different property types map between versions](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#migration-details-for-specific-object-types) of our product.
For properties you wish to modify, you need to define your own custom widget property migration. You can do this for properties using out-of-the-box UI controls and custom controls alike.
**Code migration** involves updating your widget property classes to use XbyK’s new form component attributes and APIs. When you use [source instance API discovery](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#source-instance-api-discovery), you’ll need to adjust your widget properties to utilize the new controls. This is a straightforward and methodical process, where AI assistance can help – at least for the out-of-the-box UI controls.
If your widgets use any custom UI controls, you’ll need to re-implement them in the target solution.
Now let’s walk through concrete examples that demonstrate different types of property transformations.
## Transform properties to use a different UI form control
### Keeping the data type
Imagine you have a widget in your source KX13 instance with a simple text input property. During the usage of the widget it turned out that editors need to store a longer sentence or even a paragraph in this property.
[![Text input property in KX13 that needs transformation into a text area](docsassets/guides/transform-widget-properties/text-control-kx13.png)](https://docs.kentico.com/docsassets/guides/transform-widget-properties/text-control-kx13.png)
Let’s change the UI control from a _text input_ to a _text area_.
Since the data type remains the same (text), you don’t need to perform any data migration work.
When adjusting widget properties in the code of your target instance, simply use the `TextAreaComponent` attribute:
C#
**Source (KX13): HeroBannerWidgetProperties.cs**
Copy
```
[EditingComponent(TextInputComponent.IDENTIFIER,
    Label = "Content",
    Order = 1)]
public string Content { get; set; }
```

C#
**Target (XbyK): HeroBannerWidgetProperties.cs**
Copy
```
[TextAreaComponent(
    Label = "Content",
    MinRowsNumber = 10,
    Order = 20)]
public string Content { get; set; } = "";
```

The result will look seamless.
[![Showing a piece of widget properties configuration window in XbyK showcasing the migrated Content property.](docsassets/guides/transform-widget-properties/text-area-xbyk.png)](https://docs.kentico.com/docsassets/guides/transform-widget-properties/text-area-xbyk.png)
### Changing the data type
For a more complex scenario, consider a widget property that uses a page selector component in KX13:
[![KX13 Here Banner widget with a page selector property](docsassets/guides/transform-widget-properties/page-selector-kx13.png)](https://docs.kentico.com/docsassets/guides/transform-widget-properties/page-selector-kx13.png)
The migration tool already handles differences in the Page Builder data structure for page selector properties between KX13 and XbyK, with the default [page selector migration](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.Extensions/DefaultMigrations/WidgetPageSelectorMigration.cs). This default transformation will work for you if you’re aiming for a [lift-and-shift migration](guides/development/upgrade-deep-dives/upgrade-widgets-introduction#lift-and-shift-migration).
To use a different control for page selection, for example, the [combined content selector](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector), which is the Kentico-recommended practice, define a [custom widget property migration](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/4533d9be2da9e494057ae666ecd26976fe590116/Migration.Tool.Extensions/README.md#customize-widget-property-migrations).
First, examine the Page Builder data structures. Both use an array, but the _page selector_ in KX13 references pages by `nodeGuid`, while the _combined content selector_ in XbyK uses `Identifier` (a content item GUID).
JSON
**Page selector property data structure in KX13**
Copy
```
...
{
    ...
    "type": "Xperience.Widgets.HeroBannerWidget",
    "variants": [
        {
            "identifier": "d9146566-6bfc-4e62-b18c-87466e6f639c",
            "properties": {
                "title": "Who should take this course?",
                ...
                "ctaUrlInternal": [
                    {
                        "nodeGuid": "5f4f8058-44fa-46ec-be60-01b66d7ae63c"
                    }
                ],
                ...
            }
        }
    ]
}
...
 
 
 
 
 


```

JSON
**Combined content selector property data structure in XbyK**
Copy
```
...
{
    ...
    "type": "Xperience.Widgets.HeroBannerWidget",
    "variants": [
        {
            "identifier": "c9cd393b-8b2b-438a-9e1d-eba41be0960c",
            "name": null,
            "properties": {
                "title": "Who should take this course?",
                ...
                "ctaUrlInternal": [
                    {
                        "Identifier": "17053ede-cc2c-4430-bd75-168179780a52"
                    }
                ],
                "ctaTargetUrl": "https://kentico.com",
                ...
            },
            "conditionTypeParameters": null
        }
    ]
}
...
 
 
 
 
 


```

You can view the Page Builder JSON data by selecting from `CMS_Document.DocumentPageBuilderWidgets` in a KX13 database, and by selecting `CMS_ContentItemCommonData.ContentItemCommonDataVisualBuilderWidgets` in an XbyK database.
Let’s write the custom data migration to transform our widget JSON accordingly.
In the _Migration.Tool.Extensions_ project, create a new `WidgetPageSelectorToCombinedSelectorMigration` class file. We recommend organizing custom migrations separately from default ones, for example in a _CustomWidgetMigrations_ folder.
Define the class as shown below.
Read about the custom widget migration class structure in [our GitHub documentation](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.Extensions/README.md#customize-widget-property-migrations).
C#
**WidgetPageSelectorToCombinedSelectorMigration.cs**
Copy
```
using CMS.ContentEngine;
using CMS.Core;
using Microsoft.Extensions.Logging;
using Migration.Tool.Common.Enumerations;
using Migration.Tool.Common.Services;
using Migration.Tool.KXP.Api.Services.CmsClass;
using Migration.Tool.Source.Services.Model;
using Newtonsoft.Json.Linq;

public class WidgetPageSelectorToCombinedSelectorMigration(
    ISpoiledGuidContext spoiledGuidContext,
    ILogger<WidgetPageSelectorToCombinedSelectorMigration> logger) : IWidgetPropertyMigration
{
    private const string MigratedComponent = Kx13FormComponents.Kentico_PageSelector;

    // Set higher priority (lower number) than migrations you want to override
    public int Rank => 100;

    // This migration should happen only for Page selector properties
    public bool ShallMigrate(WidgetPropertyMigrationContext context, string propertyName)
        => MigratedComponent.Equals(context.EditingFormControlModel?.FormComponentIdentifier, StringComparison.InvariantCultureIgnoreCase);

    // Define the property migration
    public Task<WidgetPropertyMigrationResult> MigrateWidgetProperty(
        string key, JToken? value, WidgetPropertyMigrationContext context)
    {
        (int siteId, _) = context;

        // Read the KX13 value if it's not empty
        if (value?.ToObject<List<PageSelectorItem>>() is { Count: > 0 } items)
        {
            // Map each page selector object to a content item reference - the target data type
            var result = items.Select(pageSelectorItem => new ContentItemReference
            {
                // Retrieve the correct GUID of the migrated page item
                Identifier = spoiledGuidContext.EnsureNodeGuid(pageSelectorItem.NodeGuid, siteId)
            }).ToList();

            // Serialize and return the new structure
            var resultAsJToken = JToken.FromObject(result);
            return Task.FromResult(new WidgetPropertyMigrationResult(resultAsJToken));
        }
        else
        {
            logger.LogError("Failed to parse '{ComponentName}' json {Json}", MigratedComponent, value?.ToString() ?? "<null>");

            // Leave value as it is
            return Task.FromResult(new WidgetPropertyMigrationResult(value));
        }
    }
}
 


```

Pay attention to the `Rank` property. If you look at the default [Page selector migration](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.Extensions/DefaultMigrations/WidgetPageSelectorMigration.cs), you’ll see that its `ShallMigrate` function looks the same - it targets page selector properties.
To prioritize your custom migration over the default `WidgetPageSelectorMigration`, **set the`Rank` to a lower number.**
In general, system/default migrations are ranked 100,000 or higher, allowing plenty of space to prioritize custom classes.
Next, register your custom migration.
C#
**Migration.Tool.Extensions/ServiceCollectionExtensions.cs**
Copy
```
...
using Migration.Tool.Extensions.CustomWidgetMigrations;
namespace Migration.Tool.Extensions;

public static class ServiceCollectionExtensions
{
    public static IServiceCollection UseCustomizations(this IServiceCollection services)
    {
        ...
        services.AddTransient<IWidgetPropertyMigration, WidgetPageSelectorMigration>();
        services.AddTransient<IWidgetPropertyMigration, WidgetPageSelectorToCombinedSelectorMigration>();

        return services;
    }
}
 


```

**Rebuild the migration tool** for the changes to take effect.
When you run the data migration now, all KX13 page selector widget properties will migrate to _combined content selector_ properties in the target instance.
**Apply the migration to a subset of properties**
What if you want to use the custom migration only for specific properties or widgets, instead of across the board?
You have two options:
  1. Test for a specific `propertyName`, and specific `siteId` in the `ShallMigrate` method of the `WidgetPageSelectorToCombinedSelectorMigration`.
  2. Make the custom migration lower priority than the default by setting the `Rank` and call it solely for specific properties in a custom widget migration.
For example:
C#
**Example custom widget migration**
Copy
```
...
public class HeroBannerWidgetMigration(ILogger<HeroBannerWidgetMigration> logger) : IWidgetMigration
{
    public int Rank => 100;
    public const string SOURCE_WIDGET_IDENTIFIER = "Xperience.Widgets.HeroBannerWidget";
    public const int SOURCE_SITE_ID = 1;
    public Task<WidgetMigrationResult> MigrateWidget(WidgetIdentifier identifier, JToken? value, WidgetMigrationContext context)
    {
        value!["type"] = "Xperience.Widgets.HeroBannerWidget";

        var variants = (JArray)value!["variants"]!;
        var singleVariant = variants[0];
        singleVariant["properties"] = new JObject
        {
            ["title"] = singleVariant["properties"]!["title"],
            ["content"] = singleVariant["properties"]!["content"],
            ...
            ["ctaTargetPage"] = singleVariant["properties"]!["ctaUrlInternal"]
        };

        //For new properties, we must explicitly define property migration classes
        var propertyMigrations = new Dictionary<string, Type>
        {
            // Use the custom property widget migration
            ["ctaTargetPage"] = typeof(WidgetPageSelectorToCombinedSelectorMigration)
        };

        return Task.FromResult(new WidgetMigrationResult(value, propertyMigrations));
    }

    public bool ShallMigrate(WidgetMigrationContext context, WidgetIdentifier identifier) =>
        string.Equals(SOURCE_WIDGET_IDENTIFIER, identifier.TypeIdentifier, StringComparison.InvariantCultureIgnoreCase)
    && SOURCE_SITE_ID == context.SiteId;
}
 
 


```



Regardless of which approach you chose, let’s adjust the code in your target instance. In the widget properties file, decorate the migrated property with the `ContentItemSelectorComponent` attribute and adjust content retrieval logic:
C#
**The widget properties file**
Copy
```
...
[ContentItemSelectorComponent(
    Cafe.CONTENT_TYPE_NAME,
    Label = "Select page",
    ExplanationText = "Select a page to link to.",
    Order = 70)]
public IEnumerable<ContentItemReference> CtaTargetPage { get; set; } = new List<ContentItemReference>();
...
```

C#
**Retrieve the page URL to for a View Model**
Copy
```
private async Task InitializeModel(HeroBannerWidgetModel model, HeroBannerWidgetProperties properties,
        LandingPage document1)
{
    ...
    // Retrieve the web page based on the migrated content item's GUID
    var contentItemGuid = properties.CtaTargetPage.FirstOrDefault()?.Identifier ?? Guid.Empty;
    // Call a service method that retrieves the Webpage data
    var page = await RetrieveWebPageByContentItemGuid(contentItemGuid);
    // assign the page URL to the model to be rendered
    model.CtaUrl = page != null ? page.GetUrl().AbsoluteUrl : String.Empty;
    ...
}
```

The content retrieval is beyond the scope of this material. See an example implementation of the `RetrieveWebPageByContentItemGuid` method in our [Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/main/src/TrainingGuides.Web/Features/Shared/Services/ContentItemRetrieverService.cs).
## Upgrade properties with custom UI control
To migrate a property implemented with a custom control, you can also define a custom property migration. Then use `Rank` or direct property assignment in widget migration as described in the previous section.
Let’s look at an example. Consider a Hero banner widget in KX13 that uses a Cloudinary custom control, which allows editors to use images and media files stored in a connected Cloudinary account.
[![KX13 instance showing a Hero Banner widget with the Cloudinary control property](docsassets/guides/transform-widget-properties/custom-control.gif)](https://docs.kentico.com/docsassets/guides/transform-widget-properties/custom-control.gif)
The control stores the file path in Cloudinary within the widget property.
JSON
**An example of KX13 widget data with custom Cloudinary UI control**
Copy
```
...
{
    "identifier": "3209a0e7-6cdc-46c2-b738-f1bf01414cb4",
    "type": "Xperience.Widgets.HeroBannerWidget",
    "variants": [
        {
            "identifier": "45e0c03d-850e-4c77-bf71-076d609683e8",
            "properties": {
                "title": "Kentico Xperience Developer hub",
                "content": "The hub collects all the essential resources you need to know to develop websites in Kentico Xperience.",
                "logo": null,
                "image": "In-house photos/Working at computer/Kentico_010_mjtcjo",
                "ctaText": null,
                ...
            }
        }
    ]
}
...
 


```

You could decide to keep your data in Cloudinary and reimplement the custom control in your target instance. In that case no custom property migration would be necessary. But let’s look at a scenario where you want to migrate the referenced media to an out-of-the-box reusable content item (for example, _Legacy media file_) or to a custom reusable content item, then use an out-of-the-box combined content selector.
First define a custom `IWidgetPropertyMigration` as in the previous example.
For example:
C#
**WidgetCustomSelectorMigration.cs**
Copy
```
using CMS.Core;
using Microsoft.Extensions.Logging;
using Migration.Tool.KXP.Api.Services.CmsClass;
using Newtonsoft.Json.Linq;

public class WidgetCustomSelectorMigration(
    ILogger<WidgetCustomSelectorMigration> logger) : IWidgetPropertyMigration
{
    private const string MigratedComponent = "CloudinarySelectorComponent"; //The code name of your custom selector

    public int Rank => 100_002;

    public bool ShallMigrate(WidgetPropertyMigrationContext context, string propertyName)
        => MigratedComponent.Equals(context.EditingFormControlModel?.FormComponentIdentifier, StringComparison.InvariantCultureIgnoreCase);

    // Migrate the property to combined content selector with content item references
    public Task<WidgetPropertyMigrationResult> MigrateWidgetProperty(
        string key, JToken? value, WidgetPropertyMigrationContext context)
    {
        (int siteId, _) = context;

        var refsToMedia = new List<object>();
        if (value != null && !string.IsNullOrEmpty(value.ToString()))
        {
            refsToMedia.Add(CreateReusableContentItemFromCloudinary(value));
        }
        var resultAsJToken = JToken.FromObject(refsToMedia);
        return Task.FromResult(new WidgetPropertyMigrationResult(resultAsJToken));
    }

    private ContentItemReference CreateReusableContentItemFromCloudinary(JToken value)
    {
        // Retrieve the necessary data from the Cloudinary platform
        // code TODO ...
        // Create the content item based on the Cloudinary data
        // Reference the new content item GUID
        return new ContentItemReference { Identifier = <NEW-CONTENT-ITEM-GUID> };
    }
}
```

See guidance on how to create new reusable content items in our [migrating widget data to content hub](guides/development/upgrade-deep-dives/migrate-widget-data-to-content-hub#define-custom-widget-migration-logic) example.
**Remember to register your custom property migration class**. 
C#
**ServiceCollectionExtensions.cs**
Copy
```
...
services.AddTransient<IWidgetPropertyMigration, WidgetCustomSelectorMigration>();
...
```

If you migrated your property to an out-of-the-box combined content selector, decorate the property in your target instance and adjust content retrieval as shown in [the previous example](guides/development/upgrade-deep-dives/transform-widget-properties#combined-content-selector-property).
## Change property names and order
Sometimes you want to rename, reorder, or consolidate widget properties to improve the editing experience in your target solution. You might also want to perform small property data changes specific to this widget without reusing them elsewhere.
You can add this functionality to your custom widget migration class using simple value mapping.
Consider an example:
The _HeroBanner_ widget in this example has the following properties in source instance:
  * `Title` - the heading of the widget
  * `CtaText` - the label for the widget’s call-to-action button
  * `CtaTarget` - a dropdown selector with a value _‘_blank’_ to open target page in a new tab, or _‘_self’_ to open target page in the same tab
  * `CtaUrlInternal` - a page to navigate to when a visitor clicks the call-to-action button
  * `CtaUrlExternal` - an external URL to navigate to when a visitor clicks the call-to-action button


The widget’s code contains internal conditional logic to prioritize navigating to the **CtaUrlExternal** before **CtaUrlInternal** if it’s set.
[![Showing the old version of Hero Banner widget in the source instance](docsassets/guides/transform-widget-properties/hero-banner-old.png)](https://docs.kentico.com/docsassets/guides/transform-widget-properties/hero-banner-old.png)
The resulting _HeroBanner_ widget should feature an improved user experience for editors:
  * `Title` - stays the same
  * `CtaText` - stays the same
  * `CtaOpenInNewTab` - a new check box, if checked (true), the page or link will open in a new tab
  * `CtaTargetType` - a new a radio button group property; the editor can use this property to decide whether to reference a page or an external URL
  * `CtaTargetPage` - still holds the page reference, but displays conditionally, based on _CtaTargetType_
  * `CtaTargetUrl` - still holds the external absolute URL, but displays conditionally, based on _CtaTargetType_


[![Showing the new version of Hero Banner widget in the target instance](docsassets/guides/transform-widget-properties/hero-banner-new.gif)](https://docs.kentico.com/docsassets/guides/transform-widget-properties/hero-banner-new.gif)
Here’s the sample code for the custom migration and widget property file in the target XbyK instance:
C#
**HeroBannerWidgetMigration.cs**
Copy
```
using Microsoft.Extensions.Logging;
using Migration.Tool.KXP.Api.Services.CmsClass;
using Newtonsoft.Json.Linq;

namespace Migration.Tool.Extensions.CustomWidgetMigrations;

public class HeroBannerWidgetMigration(ILogger<HeroBannerWidgetMigration> logger) : IWidgetMigration
{
    public int Rank => 100;
    public const string SOURCE_WIDGET_IDENTIFIER = "Xperience.Widgets.HeroBannerWidget";
    public const int SOURCE_SITE_ID = 1; // Dancing goat site ID in the source instance
    public Task<WidgetMigrationResult> MigrateWidget(WidgetIdentifier identifier, JToken? value, WidgetMigrationContext context)
    {
        value!["type"] = "Xperience.Widgets.HeroBannerWidget";

        var variants = (JArray)value!["variants"]!;
        var singleVariant = variants[0];
        singleVariant["properties"] = new JObject
        {
            ["title"] = singleVariant["properties"]!["title"],
            ["ctaText"] = singleVariant["properties"]!["ctaText"],
            // Change the property name and convert to a boolean value
            ["ctaOpenInNewTab"] = CtaTargetToBool(singleVariant["properties"]!["ctaTarget"]),
            // Add property for better UX - to select between internal and external link in the target instance
            ["ctaTargetType"] = singleVariant["properties"]!["ctaUrlExternal"] != null && !string.IsNullOrEmpty(singleVariant["properties"]!["ctaUrlExternal"]!.ToString())
                ? "absolute" : "page",
            ["ctaTargetPage"] = singleVariant["properties"]!["ctaUrlInternal"],
            ["ctaTargetUrl"] = singleVariant["properties"]!["ctaUrlExternal"],
        };

        var propertyMigrations = new Dictionary<string, Type>
        { };

        return Task.FromResult(new WidgetMigrationResult(value, propertyMigrations));
    }

    public bool ShallMigrate(WidgetMigrationContext context, WidgetIdentifier identifier) =>
        string.Equals(SOURCE_WIDGET_IDENTIFIER, identifier.TypeIdentifier, StringComparison.InvariantCultureIgnoreCase)
    && SOURCE_SITE_ID == context.SiteId;

    // A simple method making a widget-specific property transformation that's not intended to be reused across widgets/solution
    private bool CtaTargetToBool(JToken? value) => value?.ToString() == "_blank";
}
```

C#
**HeroBannerWidgetProperties.cs**
Copy
```
using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using CMS.ContentEngine;
using CMS.Websites;
using DancingGoatCore;
using Kentico.Components.Web.Mvc.FormComponents;
using Kentico.Forms.Web.Mvc;
using Kentico.PageBuilder.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;
using Kentico.Xperience.Admin.Websites.FormAnnotations;

namespace Xperience.PageBuilder.Widgets
{
    public class HeroBannerWidgetProperties : IWidgetProperties
    {
        // The guide will point to the docs for widget properties
        // highlight adding the explanation text as a good practice
        [TextInputComponent(
            Label = "Title",
            ExplanationText = "The title is displayed as a heading at the top of the banner. Avoid ending the title with a period.",
            Order = 10)]
        [Required]
        public string Title { get; set; } = "";

        [TextInputComponent(
            Label = "Call-to-action Text",
            Order = 20)]
        public string CtaText { get; set; } = "";

        // Display a checkbox instead of a dropdown with technical values
        [CheckBoxComponent(
            Label = "Open in a new tab",
            Order = 30)]
        public bool CtaOpenInNewTab { get; set; } = false;

        //Conditionally show selector vs text input for a link
        [RadioGroupComponent(
            Label = "Call-to-action target type",
            Options = "page;Page\nabsolute;Absolute URL",
            Order = 35)]
        public string CtaTargetType { get; set; } = "page";

        [VisibleIfEqualTo(nameof(CtaTargetType), "page", StringComparison.OrdinalIgnoreCase)]
        [ContentItemSelectorComponent(
            Cafe.CONTENT_TYPE_NAME,
            Label = "Select page",
            ExplanationText = "Select a page to link to.",
            Order = 40)]
        public IEnumerable<ContentItemReference> CtaTargetPage { get; set; } = new List<ContentItemReference>();

        [VisibleIfEqualTo(nameof(CtaTargetType), "absolute", StringComparison.OrdinalIgnoreCase)]
        [TextInputComponent(
            Label = "Absolute URL",
            ExplanationText = "Enter a full URL, including http:// or https://",
            Order = 50)]
        public string CtaTargetUrl { get; set; } = "";
    }
}
```

You can set default values with simple property initialization, for example: 
C#
Copy
```
public string CtaTargetUrl { get; set; } = "";
```

## Migrate inline properties
Some of your widgets in KX13 contain so-called [inline properties](13/developing-websites/distributing-custom-builder-components#inline-property-editors), values which the editor can modify in Page Builder edit mode without visiting the widget configuration. A good example is the CTA button widget in the Dancing Goat sample site:
[![Editing an inline property of CTA button in KX13](docsassets/guides/transform-widget-properties/cta-button-inline-prop-kx13.gif)](https://docs.kentico.com/docsassets/guides/transform-widget-properties/cta-button-inline-prop-kx13.gif)
Inline properties with out-of-the-box data types migrate automatically. In this example, the property is of type Text.
The inline property needs no special decoration in the properties file. Here’s comparison of the CTA button widget properties file in KX13 and in XbyK:
C#
**CTAButtonWidgetProperties.cs - KX13 source instance**
Copy
```
using Kentico.Components.Web.Mvc.FormComponents;
using Kentico.Forms.Web.Mvc;
using Kentico.PageBuilder.Web.Mvc;

namespace DancingGoat.Widgets
{
    public class CTAButtonWidgetProperties : IWidgetProperties
    {
        // Inline property - button text
        public string Text { get; set; }

        [EditingComponent(UrlSelector.IDENTIFIER, Order = 1, Label = "Link URL")]
        [EditingComponentProperty(nameof(UrlSelectorProperties.Placeholder), "Please enter a URL or select a page...")]
        [EditingComponentProperty(nameof(UrlSelectorProperties.Tabs), ContentSelectorTabs.Page)]
        public string LinkUrl { get; set; }

        [EditingComponent(CheckBoxComponent.IDENTIFIER, Order = 2, Label = "Open in a new tab")]
        public bool OpenInNewTab { get; set; }
    }
}
 


```

C#
**CTAButtonWidgetProperties.cs - XbyK target instance**
Copy
```
using Kentico.Components.Web.Mvc.FormComponents;
using Kentico.Forms.Web.Mvc;
using Kentico.PageBuilder.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;

namespace DancingGoat.Widgets
{
    public class CTAButtonWidgetProperties : IWidgetProperties
    {
        // Inline property - button text
        public string Text { get; set; }

        [TextInputComponent(
        Label = "Link URL",
        ExplanationText = "Please enter a URL or select a page, for example, \"https://your-doma.in/contact-us#form\"",
        Order = 10)]
        public string LinkUrl { get; set; }

        [CheckBoxComponent(
        Label = "Open in a new tab",
        ExplanationText = "If enabled, the link will open in a new tab.",
        Order = 20)]
        public bool OpenInNewTab { get; set; }
    }
}
 


```

However, for inline editing to work properly in your XbyK instance, you have to implement the text editor partial view (or port it from the KX13 instance).
For example, let’s migrate the CTA button widget from the Dancing Goat sample site:
Copy the __TextEditor.cshtml_ view and _TextEditorViewModel.cs_ view model into your solution.
cshtml
**_TextEditor.cshtml**
Copy
```
@model DancingGoat.InlineEditors.TextEditorViewModel

@using (Html.Kentico().BeginInlineEditor("text-editor", Model.PropertyName,
    new
    {
        @class = "text-editor",
        contenteditable = "true",
        data_placeholder_text = Model.PlaceholderText
    }))
{
    @Model.Text
}
```

C#
**TextEditorViewModel.cs**
Copy
```
namespace DancingGoat.InlineEditors
{
    /// <summary>
    /// View model for Text editor.
    /// </summary>
    public sealed class TextEditorViewModel : InlineEditorViewModel
    {
        /// <summary>
        /// Editor text.
        /// </summary>
        public string Text { get; set; }

        /// <summary>
        /// Placeholder text.
        /// </summary>
        public string PlaceholderText { get; set; } = "Type your text";
    }
}
```

Render the text editor as a partial view in your CTA button widget view when the Page Builder is in edit mode:
cshtml
**CTAButtonWidget.cshtml**
Copy
```
@using DancingGoat.InlineEditors
@using DancingGoat.Widgets

@model ComponentViewModel<CTAButtonWidgetProperties>

<div class="clear center-text">
    @if (Context.Kentico().PageBuilder().GetMode() == PageBuilderMode.Edit)
    {
        <div class="btn btn-more">
            <partial name="~/Components/InlineEditors/TextEditor/_TextEditor.cshtml"
                     model="new TextEditorViewModel
                        {
                            PropertyName = nameof(CTAButtonWidgetProperties.Text),
                            Text = Model.Properties.Text,
                        }" />
        </div>
    }
    else
    {
        <a href="@(Model.Properties.LinkUrl ?? "#")" class="btn btn-more" @(Model.Properties.OpenInNewTab ? "target=_blank" : "")>
            @Model.Properties.Text
        </a>
    }
</div>
 
 
 
 
 
 


```

Now the CTA button widget in XbyK looks nearly identical to its KX13 counterpart:
[![Editing an inline property of CTA button in XbyK](docsassets/guides/transform-widget-properties/cta-button-inline-prop-xbyk.gif)](https://docs.kentico.com/docsassets/guides/transform-widget-properties/cta-button-inline-prop-xbyk.gif)
What about inline properties using custom data types?
Currently, the Kentico Migration Tool doesn’t support custom property migrations for inline properties. However, if your widget uses an inline property of a custom type, you can work with the JSON data in a [custom widget migration](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.Extensions/README.md#customize-widget-migrations).
## Improve editing experience
An upgrade provides an excellent opportunity to improve not only your content model, but also the robustness and editing experience of your Xperience solution.
Consider these tips when migrating your widget properties:
  * Use a [dropdown provider](guides/development/page-builder/map-enum-to-dropdown) to fill dropdowns with dynamic data instead of hardcoded values.
  * Add meaningful explanation texts.
  * Set meaningful default values.
  * Reevaluate control types, for example, checkbox versus dropdown.
  * Use [visibility conditions](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions).


## What’s next?
This guide focused on scenarios around widget properties. Check out our other [deep dive guides](guides/development/upgrade-deep-dives) for additional widget migration topics, migration strategies, and transforming widget data into reusable content items.
If you haven’t already, consider following along with the [upgrade walkthrough](guides/architecture/upgrade-from-kx13/upgrade-walkthrough) to experience the process of getting a migrated page working in Xperience by Kentico from start to finish.
If you encounter any challenging scenarios during your own widget migrations, or if you have ideas for subjects we haven’t covered, don’t hesitate to reach out to us through the **Send us feedback** button at the bottom of this page.