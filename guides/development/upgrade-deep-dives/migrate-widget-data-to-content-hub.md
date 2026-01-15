# Migrate widget data as reusable content
  * How-to| [ Copy page link ](guides/development/upgrade-deep-dives/migrate-widget-data-to-content-hub#) | [Get HelpService ID](guides/development/upgrade-deep-dives/migrate-widget-data-to-content-hub#)
Core MVC 5


[✖](guides/development/upgrade-deep-dives/migrate-widget-data-to-content-hub# "Close page link panel") [Copy to clipboard](guides/development/upgrade-deep-dives/migrate-widget-data-to-content-hub#)
In this technical deep dive, we will show you how to migrate widget data from Kentico Xperience 13 (KX13) into reusable content items when upgrading to Xperience by Kentico (XbyK). You will learn how to use the [Xperience by Kentico: Kentico migration tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool) together with **custom widget migrations** to transform widget data into reusable content items in Content hub, and reference those items from your migrated widgets in XbyK.
This material focuses on practical widget data migration and code adjustments, exploring the approach that combines Source item API discovery with targeted adjustments we talked about in our [Upgrade widgets from Kentico Xperience 13](guides/development/upgrade-deep-dives/upgrade-widgets-introduction).
## Before you start
This guide assumes you have:
  * Basic knowledge of C# and .NET framework concepts
  * Familiarity with the basics of [_Page Builder_](documentation/developers-and-admins/development/builders/page-builder) in Xperience by Kentico
  * Understanding and some experience with basic upgrade to XbyK using the Kentico Migration tool.


If you haven’t, we recommend following along with the [upgrade walkthrough](guides/architecture/upgrade-from-kx13/upgrade-walkthrough) to experience the process of getting a migrated page working in Xperience by Kentico.
In preparation to follow along with this guide, you need:
  * A running instance of Kentico Xperience 13 with the Dancing Goat template
  * An instance of the [migration tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool)
  * A compatible install of Xperience by Kentico


If you haven’t followed along with the walkthrough, complete the steps from its [environment setup page](guides/architecture/upgrade-from-kx13/upgrade-walkthrough/setup-your-environment) in preparation for this guide.
## Understand our migration scenario
For this example, we’ll be using the _Hero image_ widget from the KX13 Dancing Goat sample site.
[![Hero image widget example in the KX13 Dancing Goat instance](docsassets/guides/migrate-widget-data-to-content-hub/hero-image-KX13.jpg)](https://docs.kentico.com/docsassets/guides/migrate-widget-data-to-content-hub/hero-image-KX13.jpg)
Currently, the _Hero image_ widget in KX13 stores all its data directly within its property configuration, which lives in the _CMS_Document_ table for each page. It has these properties:
  * An **image** for the background
  * A **text** for the heading
  * A **button text** for the call-to-action
  * A **button target** URL
  * A **theme** selector for light or dark styling


Each widget instance requires editors to manually fill out all this information, and the content can’t be reused across different pages.
Together we’ll extract the content-related properties (`Text`, `ButtonText`, `ButtonTarget`) into reusable content items stored in the Content hub, while keeping the presentation properties (`Image`, `Theme`) in the widget itself. As a bonus, we’ll enhance the widget by adding a custom button text override option and a new “Open in new tab” property.
[![A visual representation of the hero widget migration](docsassets/guides/migrate-widget-data-to-content-hub/hero-image-widget-migration.png)](https://docs.kentico.com/docsassets/guides/migrate-widget-data-to-content-hub/hero-image-widget-migration.png)
## Map out the widget upgrade workflow
The Kentico migration tool allows you to migrate data iteratively, which we will take advantage of in this scenario. Here’s our roadmap:
  1. **Run the initial data migration** excluding the pages that contain _Hero image_ widgets.
  2. **Create the target content type** in your XbyK instance and generate its code files.
  3. **Build the custom migration logic** to transform your widget content.
  4. **Run the migration with our custom logic** to transform the widget data.
  5. **Adjust the widget code** in the target XbyK instance.


**Keep these migration best practices in mind:**
  * When checking your migrated widget data in the database, always look for GUIDs, not IDs. Numerical IDs will not necessarily match between KX13 and XbyK. They are generated fresh for each new object in a database.
  * Create database backups between successful migrations so you can roll back if needed.
  * To troubleshoot, you can enable detailed (trace) logging in the migration tool:


JSON
**Migration.Tool.CLI/appsettings.json**
Copy
```
...
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "System": "Warning",
      "Microsoft": "Warning",
      "Migration.Tool.Source.Services.VisualBuilderPatcher": "Trace",
      "Migration.Tool.Extensions.CustomWidgetMigrations": "Trace"
    },
...
 
 


```

## Migrate your site data without widget pages
Let’s start by migrating your site data while excluding the pages that contain our _Hero image_ widgets. You can approach this in two ways: either exclude pages entirely using migration command parameters, or exclude specific objects in the migration tool’s configuration file.
For our example, we’ll use the command parameter approach to migrate everything except pages:
CMD
**Initial migration excluding pages**
Copy
```
.\Migration.Tool.CLI.exe  migrate --sites --custom-modules --users --settings-keys
```

This gives us a clean foundation to work with. Later, we’ll run the migration again with our custom widget migration logic to handle all _Hero image_ widgets across the Dancing Goat site.
Before running any migration command, make sure you have:
  * Your source KX13 instance running
  * Your target XbyK instance stopped
  * [Source instance API discovery](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#source-instance-api-discovery) enabled in your migration configuration 
    * You’ll need this for the widget migration we’ll build later; see an example in [upgrade walkthrough](guides/architecture/upgrade-from-kx13/upgrade-walkthrough/migrate-data-and-binary-files#configuration).


After a successful migration, you should be able to run your target instance. When you sign in to the administration, you’ll see that the DancingGoatCore site has become a website channel without any pages.
## Set up the Hero reusable content type
Now we need to create the **Hero** reusable content type that will store our migrated widget content in the Content hub.
### Define the content type in administration
Start your target XbyK instance and navigate to the **Content types** application in the administration interface. Create your Hero content type with these settings:
  * **Content type name:** Hero
  * **Namespace:** DancingGoatCore
  * **Name:** Hero
  * **Icon:** Choose any icon you like
  * **Use for:** Reusable content (this is crucial—it makes the content available in the Content hub)
  * **Short code name:** Leave the auto-generated value


[![Hero reusable content type in the Xperience administration - general properties](docsassets/guides/migrate-widget-data-to-content-hub/hero-ct-1.png)](https://docs.kentico.com/docsassets/guides/migrate-widget-data-to-content-hub/hero-ct-1.png)
Add these three fields, each of the **Text** type, marked as **Required** and using the **Text input** form control:
  * **HeroHeading**
  * **HeroTarget**  

  * **HeroCallToAction**


[![Hero reusable content type in the Xperience administration - fields](docsassets/guides/migrate-widget-data-to-content-hub/hero-ct-2.png)](https://docs.kentico.com/docsassets/guides/migrate-widget-data-to-content-hub/hero-ct-2.png)
### Generate code files
Now let’s generate the strongly-typed C# classes for our new Hero content type. Make sure you have the DancingGoat.Entities project set up first—see our [upgrade walkthrough](guides/architecture/upgrade-from-kx13/upgrade-walkthrough/adjust-global-code#add-a-new-project-to-hold-generated-files) for details.
Run this command from your main project’s root directory (in our case, the _DancingGoat.Web_ folder):
CMD
**Generate Hero content type classes**
Copy
```
dotnet run --no-build -- --kxp-codegen --type "ReusableContentTypes" --namespace "DancingGoatCore" --location "../DancingGoat.Entities/{type}/{name}"
```

You’ll find the generated _Hero.generated.cs_ file in your _DancingGoat.Entities_ project under the _ReusableContentTypes_ folder. It should look like this:
C#
**Hero.generated.cs**
Copy
```
//--------------------------------------------------------------------------------------------------
// <auto-generated>
//
//     This code was generated by code generator tool.
//
//     To customize the code use your own partial class. For more info about how to use and customize
//     the generated code see the documentation at https://docs.xperience.io/.
//
// </auto-generated>
//--------------------------------------------------------------------------------------------------

using System;
using System.Collections.Generic;
using CMS.ContentEngine;

namespace DancingGoatCore
{
    /// <summary>
    /// Represents a content item of type <see cref="Hero"/>.
    /// </summary>
    [RegisterContentTypeMapping(CONTENT_TYPE_NAME)]
    public partial class Hero : IContentItemFieldsSource
    {
        /// <summary>
        /// Code name of the content type.
        /// </summary>
        public const string CONTENT_TYPE_NAME = "DancingGoatCore.Hero";


        /// <summary>
        /// Represents system properties for a content item.
        /// </summary>
        [SystemField]
        public ContentItemFields SystemFields { get; set; }


        /// <summary>
        /// HeroHeading.
        /// </summary>
        public string HeroHeading { get; set; }


        /// <summary>
        /// HeroTarget.
        /// </summary>
        public string HeroTarget { get; set; }


        /// <summary>
        /// HeroCallToAction.
        /// </summary>
        public string HeroCallToAction { get; set; }
    }
}
```

## Implement custom migration
Now comes the exciting part—let’s build the custom migration logic that will transform your widget data.
Before writing the code, it’s crucial to understand the widget data structure you’re working with. In KX13, widget configurations are stored as JSON in the `CMS_Document.DocumentPageBuilderWidgets` column. The migration tool provides the data as a `JToken` parameter in your migration method.
Examine the JSON structure by querying your KX13 database:
SQL
**Examine widget JSON structure in KX13**
Copy
```
SELECT DocumentPageBuilderWidgets FROM CMS_Document
    WHERE DocumentGUID = '<GUID_OF_COFFEE_SAMPLES_PAGE_IN_KX13>'
```

This way, you can see the exact JSON format, including how properties are nested within widget variants, which you’ll need to parse correctly in your migration code.
### Define custom widget migration logic
Next, in your forked or cloned migration tool repository, create a new file called _HeroImageWidgetMigration.cs_ in the _Migration.Tool.Extensions_ project under the _CustomWidgetMigrations_ folder.
This class will extract the text content from your KX13 widgets and create new Hero content items, while preserving the visual properties in the widget itself.
You can find [additional widget migration examples](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.Extensions/CommunityMigrations/SampleWidgetMigration.cs) in the migration tool repository.
C#
**HeroImageWidgetMigration.cs**
Copy
```
using CMS.ContentEngine;
using CMS.Core;
using CMS.DataEngine;
using Microsoft.Extensions.Logging;
using Migration.Tool.KXP.Api.Services.CmsClass;
using Newtonsoft.Json.Linq;

namespace Migration.Tool.Extensions.CustomWidgetMigrations;

public class HeroImageWidgetMigration(ILogger<HeroImageWidgetMigration> logger) : IWidgetMigration
{
    public int Rank => 100;
    public const string SOURCE_WIDGET_IDENTIFIER = "DancingGoat.LandingPage.HeroImage";
    public const int SOURCE_SITE_ID = 1; // Dancing goat site ID in the source instance
    public const string HERO_CONTENT_TYPE_NAME = "DancingGoatCore.Hero";

    public async Task<WidgetMigrationResult> MigrateWidget(WidgetIdentifier identifier, JToken? value, WidgetMigrationContext context)
    {
        // Recombine the properties for the desired variants
        var variants = (JArray)value!["variants"]!;
        var singleVariant = variants[0];

        // Map property values in the target widget
        var heroItemReference = await MigrateHeroWidgetProperty(singleVariant["properties"]);
        singleVariant["properties"] = new JObject
        {
            ["hero"] = heroItemReference,
            ["image"] = singleVariant["properties"]!["image"],
            ["theme"] = singleVariant["properties"]!["theme"],
            ["openInNewTab"] = JToken.FromObject(false) // default value for new property
        };

        // We don't need special widget property migrations in this case, so we can leave the dictionary empty
        var propertyMigrations = new Dictionary<string, Type> { };

        return new WidgetMigrationResult(value, propertyMigrations);
    }

    // Run this custom widget migration only for our Hero image widget and only for pages of the DancingGoatCore site
    public bool ShallMigrate(WidgetMigrationContext context, WidgetIdentifier identifier) =>
        string.Equals(SOURCE_WIDGET_IDENTIFIER, identifier.TypeIdentifier, StringComparison.InvariantCultureIgnoreCase)
    && SOURCE_SITE_ID == context.SiteId;

    private async Task<JToken?> MigrateHeroWidgetProperty(JToken? value)
    {
        if (value != null)
        {
            // Create the reusable content item of type Hero, which we have manually defined in the target instance
            var heroContentItem = await CreateHeroContentItem(value!);

            var widgetFieldValue = heroContentItem != null
                ? [heroContentItem]
                : Array.Empty<object>();

            // Return the array with the new content item reference as JSON
            return JToken.FromObject(widgetFieldValue);
        }
        else
        {
            logger.LogError("Failed to parse 'Hero' json {Json}", value?.ToString() ?? "<null>");

            // Leave value as it is
            return value;
        }
    }

    private async Task<ContentItemReference> CreateHeroContentItem(JToken value)
    {
        const string KENTICO_DEFAULT_WORKSPACE_NAME = "KenticoDefault"; // Value retrieved from the database
        const int GLOBAL_ADMINISTRATOR_USER_ID = 53; // ID of the Global Administrator user - check your database to get the correct ID
        const string ENGLISH_US_LANGUAGE = "en-US";

        // Extract properties from JSON
        var heroHeading = value["text"];
        var heroTarget = value["buttonTarget"];
        var heroCallToAction = value["buttonText"];

        var ciManager = Service.Resolve<IContentItemManagerFactory>().Create(GLOBAL_ADMINISTRATOR_USER_ID);

        var createContentItemParameters = new CreateContentItemParameters(
            contentTypeName: HERO_CONTENT_TYPE_NAME,
            name: $"MigratedHeroItem{Guid.NewGuid():N}",
            displayName: $"Hero item - {heroHeading?.ToString() ?? "<null>"}",
            languageName: ENGLISH_US_LANGUAGE,
            workspaceName: KENTICO_DEFAULT_WORKSPACE_NAME
        );

        // The property names have to match the manually created content type's field names in the administration
        var contentItemData = new ContentItemData();
        contentItemData.SetValue("HeroHeading", heroHeading?.ToString() ?? string.Empty);
        contentItemData.SetValue("HeroTarget", heroTarget?.ToString() ?? string.Empty);
        contentItemData.SetValue("HeroCallToAction", heroCallToAction?.ToString() ?? string.Empty);

        int itemId = await ciManager.Create(createContentItemParameters, contentItemData);

        if (itemId <= 0)
        {
            throw new Exception("Unable to create content item");
        }
        if (!await ciManager.TryPublish(itemId, ENGLISH_US_LANGUAGE))
        {
            throw new Exception("Could not publish Hero item");
        }
        return new ContentItemReference { Identifier = CMS.ContentEngine.Internal.ContentItemInfo.Provider.Get(itemId).ContentItemGUID };
    }
}
```

### Register your custom migration
Don’t forget to register your migration class in the migration tool service collection:
C#
**Migration.Tool.Extensions/ServiceCollectionExtensions.cs**
Copy
```
...
namespace Migration.Tool.Extensions;

public static class ServiceCollectionExtensions
{
    public static IServiceCollection UseCustomizations(this IServiceCollection services)
    {
        ...
        services.AddTransient<IWidgetPropertyMigration, WidgetPageSelectorMigration>();
        services.AddTransient<IWidgetMigration, HeroImageWidgetMigration>();
        ...
        return services;
    }
}
 


```

Last but not least, **rebuild your Kentico migration tool** so these changes take effect.
## Run the migration with widget transformation
Run the migration command again, this time including pages so our custom widget migration can work its magic. Make sure your _Source item API discovery_ is still enabled before proceeding.
If you’re following along, use this command; otherwise, adjust the parameters based on your project’s needs:
CMD
**Migrate pages with custom widget logic**
Copy
```
.\Migration.Tool.CLI.exe  migrate --page-types --pages --contact-management --forms --media-libraries --data-protection --custom-tables --members --categories --bypass-dependency-check
```

## Check your progress
Great! If everything went smoothly, you should see your transformed widget data in the target database. Let’s check the database to see what happened.
**Important:** When querying the database to verify your migration, always use the page GUID from your KX13 database, not the IDs. The IDs get regenerated during each migration session, but GUIDs remain consistent.
Run this SQL query to see your migrated widget data:
SQL
**Check migrated widget data**
Copy
```
SELECT ContentItemCommonDataVisualBuilderWidgets FROM CMS_ContentItemCommonData
    WHERE ContentItemCommonDataGUID = '<GUID_OF_COFFEE_SAMPLES_PAGE_FROM_KX13>'
```

You’ll see JSON that looks like this; notice how the hero content and image are now references to reusable content items:
JSON
**Migrated Hero image widget data**
Copy
```
{
  "editableAreas": [
    {
    ...
    "zones": [
        {
            "identifier": "e38e288c-c0c8-4b6e-9162-993d9e6bd45d",
            "name": null,
            "widgets": [
            {
                "identifier": "3c0f1821-4ac9-48b1-bd08-dde2b72b23d8",
                "type": "DancingGoat.LandingPage.HeroImage",
                "conditionType": null,
                "variants": [
                    {
                        "identifier": "301ba711-5a9d-4094-9765-9ce577b69746",
                        "name": null,
                        "properties": {
                            "hero": [
                                {
                                "Identifier": "d7aa7ef0-7799-403c-9c4d-1154b04b0388"
                                }
                            ],
                            "image": [
                                {
                                "Identifier": "79d9f4c0-6647-4163-8d45-24f59dd22303"
                                }
                            ],
                            "theme": "light",
                            "openInNewTab": false
                        },
                        "conditionTypeParameters": null
                    }
                ]
            },
            ...
            ]
        }]
    },
    ...
}
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 


```

## Adapt your widget implementation in the target instance
Now let’s adapt the widget code to work with our new content model. Before we start, make sure you have your Coffee sample page working in your target XbyK instance—you’ll need this to test the updated widget.
The original KX13 Dancing Goat holds the widget files in the _HeroImageWidget_ folder under _Components/Widgets_. You can either copy these files and modify them, or create them from scratch.
Here are the key changes we need to make for XbyK:
  * **Widget properties** - Reference the Hero reusable content item and handle our new properties.
The property names must match the names defined in the custom widget migration for the values to map correctly, _e.g., text - Text, buttonText - ButtonText, buttonTarget - ButtonTarget, theme - Theme_. 
  * **View model** - Adjust to reflect the new property structure.
  * **View component** - Use XbyK’s ContentRetriever API instead of KX13’s data access methods.
  * **View** - Remove inline editing in favor of configurable properties.


Here are the samples of what each file should look like:
C#
**HeroImageWidgetProperties.cs**
Copy
```
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using CMS.ContentEngine;
using DancingGoatCore;
using Kentico.Components.Web.Mvc.FormComponents;
using Kentico.Forms.Web.Mvc;
using Kentico.PageBuilder.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;
using Kentico.Xperience.Headless.Internal;

namespace DancingGoat.Widgets
{
    public class HeroImageWidgetProperties : IWidgetProperties
    {
        [ContentItemSelectorComponent(
            DancingGoatCore.Hero.CONTENT_TYPE_NAME,
            Label = "Select hero item",
            ExplanationText = "Choose the hero item to be displayed in the widget.",
            MaximumItems = 1,
            Order = 10)]
        public IEnumerable<ContentItemReference> Hero { get; set; } = [];

        [TextInputComponent(
            Label = "Custom call to action text",
            ExplanationText = "If empty, the default call to action text from the linked Hero item will be used.",
            Order = 20)]
        public string ButtonText { get; set; } = string.Empty;

        [CheckBoxComponent(
            Label = "Open link in a new tab",
            Order = 30)]
        public bool OpenLinkInNewTab { get; set; } = false;

        [ContentItemSelectorComponent(
            MediaFile.CONTENT_TYPE_NAME,
            Label = "Title image",
            ExplanationText = "Image to be displayed if the selected Hero item does not contain an image.",
            MaximumItems = 1,
            Order = 40)]
        public IEnumerable<ContentItemReference> Image { get; set; } = [];

        [DropDownComponent(
            Label = "Color scheme",
            Options = "light;Light\r\ndark;Dark",
            Order = 50)]
        public string Theme { get; set; } = "dark";
    }
}
```

In your own project, to feed data into a `DropDownComponent` control, we recommend using the dropdown provider, explained in [our Page Builder materials](guides/development/page-builder/map-enum-to-dropdown).
C#
**HeroImageWidgetViewModel.cs**
Copy
```
using CMS.MediaLibrary;
using DancingGoatCore;

namespace DancingGoat.Widgets
{
    public class HeroImageWidgetViewModel
    {
        public string Heading { get; set; }
        public string ImagePath { get; set; }
        public string Target { get; set; }
        public string CallToActionText { get; set; }
        public bool OpenLinkInNewTab { get; set; }
        public string Theme { get; set; }
    }
}
```

C#
**HeroImageWidgetViewComponent.cs**
Copy
```
using System;
using System.Linq;
using System.Threading.Tasks;
using CMS.MediaLibrary;

using DancingGoat.Models;
using DancingGoat.Widgets;
using DancingGoatCore;
using Kentico.Content.Web.Mvc;
using Kentico.PageBuilder.Web.Mvc;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.ViewComponents;

[assembly: RegisterWidget(
    HeroImageWidgetViewComponent.IDENTIFIER,
    typeof(HeroImageWidgetViewComponent),
    "Hero image",
    typeof(HeroImageWidgetProperties),
    Description = "Displays an image, text, and a CTA button.",
    IconClass = "icon-badge")]

namespace DancingGoat.Widgets
{
    /// <summary>
    /// Controller for hero image widget.
    /// </summary>
    public class HeroImageWidgetViewComponent(IContentRetriever contentRetriever) : ViewComponent
    {
        /// <summary>
        /// Widget identifier.
        /// </summary>
        public const string IDENTIFIER = "DancingGoat.LandingPage.HeroImage";

        public async Task<ViewViewComponentResult> InvokeAsync(HeroImageWidgetProperties properties)
        {
            // retrieve the data of the Hero reusable content item using ContentRetriever API
            var hero = (await contentRetriever.RetrieveContentByGuids<Hero>(properties.Hero.Select(x => x.Identifier)))
                .FirstOrDefault();

            // retrieve the image using ContentRetriever API
            var image = await contentRetriever.RetrieveContentByGuids<MediaFile>(properties.Image.Select(x => x.Identifier));
            var imagePath = image
                .FirstOrDefault()?.LegacyMediaFileAsset.Url;

            // fill out the view model
            var model = new HeroImageWidgetViewModel
            {
                Heading = hero?.HeroHeading,
                Target = hero?.HeroTarget,
                CallToActionText = string.IsNullOrEmpty(properties.ButtonText) ? hero?.HeroCallToAction : properties.ButtonText,
                OpenLinkInNewTab = properties.OpenLinkInNewTab,
                ImagePath = imagePath,
                Theme = properties.Theme
            };
            return View("~/Components/Widgets/HeroImageWidget/_HeroImageWidget.cshtml", model);
        }
    }
}
```

cshtml
**_HeroImageWidget.cshtml**
Copy
```
@using DancingGoat.InlineEditors
@using DancingGoat.Widgets

@model HeroImageWidgetViewModel

@if(@Model != null)
{
    string styleAttribute = null;
    if (!string.IsNullOrEmpty(Model.ImagePath))
    {
        styleAttribute = $"style=\"background-image: url('{Url.Content(Model.ImagePath)}');\"";
    }

    <section class="hero-image-section @Model.Theme" @Html.Raw(styleAttribute)>
        <div class="content">
            <div class="text">
                @Model.Heading
            </div>
            <a href="@Model.Target" class="btn" target="@(Model.OpenLinkInNewTab ? "_blank" : "")">
                @Model.CallToActionText
            </a>
        </div>
    </section>
}
```

See more details and guidance on best practices of defining Page Builder widgets and other components in our [Page Builder module](modules/page-builder).
Remember to add your new widget reference to the _ComponentIdentifiers_ class. This allows you to reference the widget in sections and templates without directly referencing the widget’s view component:
C#
**ComponentIdentifiers.cs**
Copy
```
using DancingGoat.Web.PageTemplates.LandingPages;

namespace DancingGoat
{
    public static class ComponentIdentifiers
    {
        // Widgets
        public const string CTA_BUTTON_WIDGET = "DancingGoat.General.CTAButtonWidget";
        public const string HERO_WIDGET = "DancingGoat.LandingPage.HeroImage";

        // Sections
        public const string SINGLE_COLUMN_SECTION = "DancingGoat.SingleColumnSection";
        ...

        // Page templates
        public const string LANDING_PAGE_SINGLE_COLUMN_TEMPLATE = LandingPageSingleColumnTemplate.IDENTIFIER;
        ...
    }
}
 


```

## See the result
Now build and run your target instance. When you look at your page in the **Page Builder** tab, you can see the migrated widget and its properties. Notice the new reusable content item reference.
Your browser does not support the video tag. 
Congratulations! You’ve successfully migrated your _Hero image_ widget data from KX13 to XbyK while modernizing your content architecture. You’ve transformed widget-specific content into reusable Content hub items that can be shared across pages, while maintaining visual control through widget properties.
## What’s next?
This guide focused on custom widget migrations for transforming widget data into reusable content items. For guidance on migrating widget properties and UI controls, check out our [Transform widget properties](guides/development/upgrade-deep-dives/transform-widget-properties) page.
You can find more advanced upgrade and data migration use cases in our other [Upgrade deep dive guides](guides/development/upgrade-deep-dives).
If you encounter any challenging scenarios during your own widget migrations, or if you have ideas for subjects we haven’t covered, don’t hesitate to reach out to us through the **Send us feedback** button at the bottom of this page.