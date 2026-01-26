---
source: https://docs.kentico.com/guides/development/customizations-and-integrations/add-channels-to-module
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Customizations and integrations](/guides/development/customizations-and-integrations)
  * Add channel-specific configuration to a module 


# Add channel-specific configuration to a module
In the samples from the [earlier in this series](/guides/development/customizations-and-integrations/create-basic-module), we created a basic module UI with standard listing, create, edit, and delete functionality.
Now, we will explore the process of:
  * Adding per-channel configuration to a custom module
  * Managing multiple levels of hierarchy
  * Using UI pages to ensure one-to-one relationships between objects


The samples here will add a website-channel-specific settings section to the module defined [earlier](/guides/development/customizations-and-integrations/create-basic-module).
The new UI pages will allow designated users to select a [website channel](/documentation/developers-and-admins/configuration/website-channel-management) from a list and edit its settings. These include SEO-related settings and channel-specific code snippets.
Your browser does not support the video tag. 
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](/documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 
  * Experience with [admin UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages).


The examples in this guide require that you either:
  * Have followed along with the samples from [the earlier guide](/guides/development/customizations-and-integrations/create-basic-module) that implement a custom module holding global settings.


or
  * Have an existing [UI application page](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-application-pages) under which you want to create a channel-specific UI.


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Design the classes
Before we define our UI pages themselves, we need to design the classes they will display.
If you plan to use [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) for a given class, make sure to include either a _Code name_ or _Guid_ field. Similarly, include a _Display name_ field if you want objects of the class to automatically display nicely in a listing.
If you’re following along with our example, create three new classes in the **Project settings** module:
  * [Web channel settings](#web-channel-settings), to represent the collection of all settings for a given web channel.
  * [SEO settings](#seo-settings), to hold the robots.txt specification that a given website channel provides to crawlers.
  * [Web channel snippet](#web-channel-snippet), to represent code snippets that you want to render to the channel’s website.


### Web channel settings
The _web channel settings_ class will have a one-to-one relationship with the _web channel_ class in Xperience by Kentico.
It will represent parent objects that group channel-specific settings together.
It needs a _display name_ column, so that objects display nicely on a listing page, and a _guid column_ for the sake of [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration).
Since objects of this class will serve as containers for other objects, and they can get their data directly from corresponding channels, they do not need an edit form for user input.
  1. **General** tab 
     * **Class name** : Web channel settings
     * **Namespace** : TrainingGuides
     * **Name** : WebChannelSettings
  2. **Database columns** tab 
    1. Channel settings display name 
       * **Field name** : WebChannelSettingsChannelDisplayName
       * **Data type** : Text
       * **Size** : 200
       * **Required** : True (Enabled)
    2. Channel settings channel ID 
       * **Field name** : WebChannelSettingsChannelID
       * **Data type** : Integer number
       * **Required** : True (Enabled)
       * **Default value** : 0
       * **Reference to** : Channel
       * **Reference type** : Required
    3. Channel settings guid 
       * **Field name** : WebChannelSettingsGUID
       * **Data type** : Unique identifier (GUID)
       * **Required** : True (Enabled)


### SEO settings
This class will represent a given web channel’s settings related to SEO.
Each of its additional database fields will represent the value of a setting. As a result, it has a one-to-one relationship with [web channel settings](#web-channel-settings), and by extension, [website channels](/documentation/developers-and-admins/configuration/website-channel-management).
For the sake of this example, it will contain a field for setting the content of the channel’s [robots.txt file](https://developers.google.com/search/docs/crawling-indexing/robots/intro).
For the sake of simplicity, this example is simple, but we recommend creating additional SEO setting fields in your project. For instance, you could add settings that configure automatic sitemap generation.
The SEO settings class also needs a _foreign key_ to the _Web channel settings_ class to represent that relationship, and a _guid_ for the sake of [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration).
Since SEO settings’ values are provided by users, the class also needs a _UI form_ to take their input.
  1. **General** tab 
     * **Class name** : SEO Settings
     * **Namespace** : TrainingGuides
     * **Name** : SeoSettings
  2. **Database columns** tab 
    1. Seo settings Robots.txt 
       * **Field name** : SeoSettingsRobots
       * **Data type** : Text
       * **Size** : 2000
       * **Required** : True (Enabled)
    2. Seo settings web channel settings ID 
       * **Field name** : SeoSettingsWebChannelSettingID
       * **Data type** : Integer number
       * **Required** : True (Enabled)
       * **Default value** : 0 
We will come back later to set the **Reference to** value, after the code files for the relevant classes have been generated. 
    3. Seo settings guid 
       * **Field name** : SeoSettingsGUID
       * **Data type** : Unique identifier (GUID)
       * **Required** : True (Enabled)
  3. UI forms 
    1. SEO settings edit 
      1. General 
         * **UI form name** : SEO settings edit
         * **Identifiers → Code name** : SeoSettingsEdit 
Leaving **Pre-fill code name automatically** checked will generate the same code name in this case. 
      2. Fields 
        1. Robots.txt 
           * **Database column** : SeoSettingsRobots
           * **Field caption** : Robots.txt
           * **Enabled** : True (Enabled)
           * **Form component** : Text area
           * **Minimum number of rows** : 3
           * **Maximum number of rows** : 100


### Web channel snippet
The Web channel snippet class will represent a code snippet associated with a given website channel.
Each channel can have many snippets, and the snippets can vary in type.
Alongside a text field to hold the code, the class needs an indicator field to indicate the type of code snippet (metadata, CSS, or Javascript).
It should also have a _display name_ for UI purposes, a _foreign key_ to the web channel settings class, and a _code name_ for easier code retrieval and [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration).
  1. General 
     * **Class name** : Web channel snippet
     * **Namespace** : TrainingGuides
     * **Name** : WebChannelSnippet
  2. Database columns 
    1. Snippet type 
       * **Field name** : WebChannelSnippetType
       * **Data type** : Text
       * **Size** : 200
       * **Required** : True (Enabled)
    2. Snippet code 
       * **Field name** : WebChannelSnippetCode
       * **Data type** : Text
       * **Size** : 2000
       * **Required** : True (Enabled)
    3. Snippet channel settings ID 
       * **Field name** : WebChannelSnippetWebChannelSettingsID
       * **Data type** : Integer number
       * **Required** : True (Enabled)
       * **Default value** : 0 
We will come back later to set the **Reference to** value, after the code files for the relevant classes have been generated. 
    4. Snippet display name 
       * **Field name** : WebChannelSnippetDisplayName
       * **Data type** : Text
       * **Size** : 200
       * **Required** : True (Enabled)
    5. Snippet code name 
       * **Field name** : WebChannelSnippetCodeName
       * **Data type** : Text
       * **Size** : 200
       * **Required** : True (Enabled)
  3. UI forms 
    1. Web channel snippet edit 
      1. General 
         * **UI form name** : Web channel snippet edit
         * **Identifiers → Code name** : WebChannelSnippetEdit
      2. Fields 
        1. Snippet display name 
           * **Database column** : WebChannelSnippetDisplayName
           * **Field caption** : Label
           * **Enabled** : True (Enabled)
           * **Form component** : Text input
        2. Snippet code name 
           * **Database column** : WebChannelSnippetCodeName
           * **Field caption** : Label
           * **Enabled** : True (Enabled)
           * **Form component** : Text input
        3. Snippet type 
           * **Database column** : WebChannelSnippetType
           * **Field caption** : Type
           * **Enabled** : True (Enabled)
           * **Text below the input** : Determines where on the page the snippet should be rendered.
           * **Form component** : Dropdown selector
           * **Data source** : 
Text
Copy
```
metadata;Metadata
css;CSS
javascript;Javascript
```

           * **Data source value separator** : ;
        4. Snippet code 
           * **Database column** : WebChannelSnippetCode
           * **Field caption** : Code
           * **Enabled** : True (Enabled)
           * **Text below the input** : Holds the code that you want to render. Make sure to include opening and closing tags if necessary.
           * **Form component** : Text area
           * **Minimum number of rows** : 3
           * **Maximum number of rows** : 50


## Prepare generated classes
### Generate code files
Now, as we did [in the earlier example](/guides/development/customizations-and-integrations/create-basic-module#generate-code-files), generate code files for the new classes.
See the [code generation documentation](/documentation/developers-and-admins/api/generate-code-files-for-system-objects#generate-code-files) for details about the available parameters.
If you have existing classes that were generated with different parameters, use either the `--include` or `--exclude` parameter to ensure that only the classes you’re currently working with are generated. Otherwise they may be overwritten.
For scenarios with multiple groups of classes generated with different parameters, we recommend using a scripting language like Powershell or Bash to create a reusable script that re-generates all code files according to your requirements.
If you’re following along with the example, you should be able to use something like this: 
CMD
Copy
```
dotnet run --no-build -- --kxp-codegen --type "Classes" --location "../TrainingGuides.Entities/{type}/{name}/" --with-provider-class "false"
```

### Override TYPEINFO definitions
As in the [the earlier example](/guides/development/customizations-and-integrations/create-basic-module) and the [cookie consent mapping guide](/guides/development/data-protection/map-consents-to-cookie-levels#generate-the-code), consider any changes you may need to make to the [object type configuration](/documentation/developers-and-admins/customization/object-types/object-type-configuration) of the classes.
You can use this property to control relationships and dependencies between classes, along with [CI/CD](/documentation/developers-and-admins/ci-cd) and [Caching](/documentation/developers-and-admins/development/caching) behavior.
See the [object type configuration document](/documentation/developers-and-admins/customization/object-types/object-type-configuration#type-info-properties) for more details about what you can configure.
Rather than editing a generated file directly, create a partial class to configure the class’s `TYPEINFO`.
If you’re following along with our example, go to the _~/Classes/Overrides_ folder of the _TrainingGuides.Entities_ project, or create it if it does not yet exist. Use partial classes to enable Continuous Integration for `WebChannelSettingsInfo`, `SeoSettingsInfo`, and `WebChannelSnippetInfo`.
Then, set `WebChannelSettingsInfo` as a child of `ChannelInfo`, the class that represents channels in Xperience by Kentico. This ensures that if the _Website channel_ associated with a _Web channel settings_ object is deleted, the settings are deleted too.
C#
**WebChannelSettingsInfoOverride.cs**
Copy
```
using CMS.ContentEngine;

namespace TrainingGuides.ProjectSettings;

public partial class WebChannelSettingsInfo
{
    static WebChannelSettingsInfo()
    {
        TYPEINFO.ContinuousIntegrationSettings.Enabled = true;
        TYPEINFO.ParentObjectType = ChannelInfo.OBJECT_TYPE;
        TYPEINFO.ParentIDColumn = nameof(WebChannelSettingsChannelID);
    }
}
```

Follow the same process to designate _web channel settings_ as the parent of _SEO settings_ and _web channel snippets_.
C#
**SeoSettingsInfoOverride.cs**
Copy
```
namespace TrainingGuides.ProjectSettings;

public partial class SeoSettingsInfo
{
    static SeoSettingsInfo()
    {
        TYPEINFO.ContinuousIntegrationSettings.Enabled = true;
        TYPEINFO.ParentObjectType = WebChannelSettingsInfo.OBJECT_TYPE;
        TYPEINFO.ParentIDColumn = nameof(SeoSettingsWebChannelSettingID);
    }
}
```

C#
**WebChannelSnippetInfoOverride.cs**
Copy
```
namespace TrainingGuides.ProjectSettings;

public partial class WebChannelSnippetInfo
{
    static WebChannelSnippetInfo()
    {
        TYPEINFO.ContinuousIntegrationSettings.Enabled = true;
        TYPEINFO.ParentObjectType = WebChannelSettingsInfo.OBJECT_TYPE;
        TYPEINFO.ParentIDColumn = nameof(WebChannelSnippetWebChannelSettingsID);
    }
}
```

### Save the references
Once the generated classes exist, the admin UI allows you to specify references pointing to them.
  1. Rebuild the application and access the **Database columns** tab of any custom class with a foreign key to another custom class.
  2. Set the **Reference to** and **Reference type** properties of the column, and click **Save**.
  3. Re-run the code generation command you used in the [Generate code files](#generate-code-files) step. This will re-generate the classes, now including this foreign key reference in their TYPEINFO definition.


These references act like foreign keys in some ways, but they do not represent real foreign key constraints in the database.
You can add foreign key constraints manually, but you will need to account for them during operations like [restoring data from CI/CD](/documentation/developers-and-admins/ci-cd/ci-cd-database-migration-scripts) and [updating the system](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects).
For our example, you’ll want to set the reference properties of the following columns:
  1. **SEO settings** class 
    1. Seo settings web channel settings ID 
       * **Field name** : SeoSettingsWebChannelSettingID
       * **Data type** : Integer number
       * **Required** : True (Enabled)
       * **Default value** : 0
       * **Reference to** : ObjectType.trainingguides_webchannelsettings
       * **Reference type** : Required
  2. **Web channel snippet** class 
    1. Snippet channel settings ID 
       * **Field name** : WebChannelSnippetWebChannelSettingsID
       * **Data type** : Integer number
       * **Required** : True (Enabled)
       * **Default value** : 0
       * **Reference to** : ObjectType.trainingguides_webchannelsettings
       * **Reference type** : Required


The re-generated code will have a new `ObjectDependency` defined in the TYPEINFO declaration.
C#
**WebChannelSnippetInfo.generated.cs**
Copy
```
...
public static readonly ObjectTypeInfo TYPEINFO = new ObjectTypeInfo(typeof(IInfoProvider<WebChannelSnippetInfo>), OBJECT_TYPE, "TrainingGuides.WebChannelSnippet", "WebChannelSnippetID", null, null, "WebChannelSnippetCodeName", "WebChannelSnippetDisplayName", null, null, null)
{
    TouchCacheDependencies = true,
    DependsOn = new List<ObjectDependency>()
    {
        new ObjectDependency("WebChannelSnippetWebChannelSettingsID", "trainingguides.webchannelsettings", ObjectDependencyEnum.Required),
    },
};
...
```

## Create an automatic listing page
[![screenshot of channel settings list](/docsassets/guides/add-channels-to-module/ChannelSettingsList.png)](/docsassets/guides/add-channels-to-module/ChannelSettingsList.png)
In the previous example from this series, we saw a [listing page that displayed _global settings key_ objects](/guides/development/customizations-and-integrations/create-basic-module#define-the-listing-page). Because these global settings were created and managed by users, the listing page just needed to display the objects, with links and buttons.
However, in situations where you want to enforce one-to-one relationships, you can use the landing page to execute code that ensures the existence of the required objects. [UI pages in Xperience by Kentico](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) support dependency injection, so you should be able to use whichever services and options you need to properly set up data before the page loads.
For our example, let’s use the listing page to make sure that for each web channel, a _web channel settings_ object exists, and that each one has an _SEO settings_ child.
Start by creating a listing page similar to the [example from earlier in this seires](/guides/development/customizations-and-integrations/create-basic-module#define-the-listing-page), but with no delete button; Xperience will handle deletions automatically thanks to our class overrides that established the relationship between Web channel settings and their corresponding channels.
You only need one column in the `ColumnConfigurations`, showing the display name of the channel. Editors will arrive at this page to see a list of channel names. Then they can edit the settings of whichever channel they click on.
C#
**WebChannelSettingsListingPage.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using TrainingGuides.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings.WebChannelSettings;
using TrainingGuides.Admin.ProjectSettings;
using CMS.DataEngine;
using CMS.ContentEngine;

[assembly: UIPage(
    parentType: typeof(ProjectSettingsApplication),
    slug: "channel-settings",
    uiPageType: typeof(WebChannelSettingsListingPage),
    name: "Channel settings",
    templateName: TemplateNames.LISTING,
    order: 0)]

namespace TrainingGuides.Admin.ProjectSettings.WebChannelSettings;
public class WebChannelSettingsListingPage : ListingPage
{
    protected override string ObjectType => WebChannelSettingsInfo.OBJECT_TYPE;

    public override async Task ConfigurePage()
    {
        PageConfiguration.ColumnConfigurations
                     .AddColumn(nameof(
                        WebChannelSettingsInfo.WebChannelSettingsChannelDisplayName), "Channel");

        PageConfiguration.AddEditRowAction<WebChannelSettingsEditSection>();

        await base.ConfigurePage();
    }
}
```

Next, add code that checks whether there is a `WebChannelSettingsInfo` object corresponding to each channel, and creates a new one if not.
It should check each `WebChannelSettingsInfo` object, ensuring that its display name matches the corresponding channel in case the channel is modified, and that it has a related `SeoSettingsInfo` object.
C#
**WebChannelSettingsListingPage.cs**
Copy
```
...
namespace TrainingGuides.Admin.ProjectSettings;
public class WebChannelSettingsListingPage : ListingPage
{
    private readonly IInfoProvider<ChannelInfo> channelInfoProvider;
    private readonly IInfoProvider<WebChannelSettingsInfo> webChannelSettingsInfoProvider;
    private readonly IInfoProvider<SeoSettingsInfo> seoSettingsInfoProvider;

    protected override string ObjectType => WebChannelSettingsInfo.OBJECT_TYPE;

    public WebChannelSettingsListingPage(
        IInfoProvider<ChannelInfo> channelInfoProvider,
        IInfoProvider<WebChannelSettingsInfo> webChannelSettingsInfoProvider,
        IInfoProvider<SeoSettingsInfo> seoSettingsInfoProvider) : base()
    {
        this.channelInfoProvider = channelInfoProvider;
        this.webChannelSettingsInfoProvider = webChannelSettingsInfoProvider;
        this.seoSettingsInfoProvider = seoSettingsInfoProvider;

        EnsureSettingsListData();
    }

    ...

    //Creates necessary settings objects if they do not exist, and ensures proper channel names.
    private void EnsureSettingsListData()
    {
        var channels = channelInfoProvider.Get();
        var webChannelSettings = webChannelSettingsInfoProvider.Get().ToList();
        var seoSettings = seoSettingsInfoProvider.Get().ToList();

        foreach (var channel in channels)
        {
            var currentChannelSettings = webChannelSettings.Where(setting => setting.WebChannelSettingsChannelID.Equals(channel.ChannelID)).ToList();

            EnsureChannelSetting(currentChannelSettings, channel);

            var currentChannelSetting = currentChannelSettings.FirstOrDefault();

            EnsureChannelSettingDisplayName(channel, currentChannelSetting);

            EnsureSeoSettings(currentChannelSetting, seoSettings);
        }
    }

    //Creates a new WebChannelSettingsInfo for the provided channel if none exists.
    private void EnsureChannelSetting(List<WebChannelSettingsInfo> currentChannelSettings, ChannelInfo channel)
    {
        if (currentChannelSettings.Count() == 0)
        {
            var newSetting = new WebChannelSettingsInfo
            {
                WebChannelSettingsChannelID = channel.ChannelID,
                WebChannelSettingsChannelDisplayName = channel.ChannelDisplayName,
            };
            webChannelSettingsInfoProvider.Set(newSetting);
            currentChannelSettings.Add(newSetting);
        }
    }

    //Updates display name of provided WebChannelSettingsInfo to match its channel if they are different.
    private void EnsureChannelSettingDisplayName(ChannelInfo channel, WebChannelSettingsInfo? currentChannelSetting)
    {
        if (currentChannelSetting != null && !channel.ChannelDisplayName.Equals(currentChannelSetting.WebChannelSettingsChannelDisplayName))
        {
            currentChannelSetting.WebChannelSettingsChannelDisplayName = channel.ChannelDisplayName;
            webChannelSettingsInfoProvider.Set(currentChannelSetting);
        }
    }

    //Creates a new SeoSettingsInfo object for the provided WebChannelSettingsInfoif none exists.
    private void EnsureSeoSettings(WebChannelSettingsInfo? currentChannelSetting, IEnumerable<SeoSettingsInfo> seoSettings)
    {
        int? webChannelSettingsId = currentChannelSetting?.WebChannelSettingsID;

        var currentSeoSettings = seoSettings
            .Where(setting => setting.SeoSettingsWebChannelSettingID == webChannelSettingsId).ToList();

        if (currentSeoSettings.Count() == 0 && webChannelSettingsId != null)
        {
            var newSeoSetting = new SeoSettingsInfo
            {
                SeoSettingsWebChannelSettingID = (int)webChannelSettingsId,
                SeoSettingsRobots = string.Empty,
            };

            seoSettingsInfoProvider.Set(newSeoSetting);
        }
    }
}
```

You can find the complete file [in the finished branch of the _Training guides repository_](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Admin/Pages/ProjectSettings/WebChannelSettings/WebChannelSettingsListingPage.cs) for reference.
You may notice how the above code does not check for and delete settings objects with no parent. Thanks to the parent-child relationship [we set earlier](#override-typeinfo-definitions), settings are deleted automatically along with their channel.
## Edit child objects as part of a parent
When objects have parent-child relationships, you may want to edit one or more child objects when a user selects a parent from the listing page, rather than directly editing the parent itself. In our example, the _Web channel settings_ don’t have any meaningful properties for users to edit, so instead, so when they choose a channel, we want to display the _SEO settings_ edit form instead.
This will involve a few adjustments compared to previous examples, which involved _listing_ and _edit_ pages that worked with the same object type.
Luckily, the type of an `InfoEditPage<T>` does not need to match the type of the `EditSectionPage<T>` registered as its `parentType`.
Compared to the previous examples, we need an extra bound parameter to retrieve the ID of the parent object. Then we can use its value in our child pages. For editing child objects with one-to-one relationships (like our example’s parent _Web channels settings_ and child _SEO settings_), override the abstract `ObjectID` property inherited from `InfoEditPage<T>`, and use the parent’s ID to retrieve the appropriate child.
The [next section](#create-a-sub-section-for-multiple-child-objects) wil cover one-to-many relationships, where there may be mutiple child objects per parent.
For example, several _Web channel snippets_ may relate to one _Web channel settings_ object.
For editing pages, Xperience automatically propagates the display name of a saved object to the breadcrumbs and navigation, so you’ll need to override the `GetSubmitSuccessResponse` method to keep the name of the parent object on display.
**See the difference**
The behavior of the navigation with and without the `GetSubmitSuccessResponse` override is best demonstrated visually.
_Before:_
Your browser does not support the video tag. 
_After:_
Your browser does not support the video tag. 
If you’re following along with our example, start by making an edit section for `WebChannelSettingsInfo`. It should be a child of the listing page from earlier.
C#
**WebChannelSettingsEditSection.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using TrainingGuides.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings.WebChannelSettings;

[assembly: UIPage(
    parentType: typeof(WebChannelSettingsListingPage),
    slug: PageParameterConstants.PARAMETERIZED_SLUG,
    uiPageType: typeof(WebChannelSettingsEditSection),
    name: "Edit",
    templateName: TemplateNames.SECTION_LAYOUT,
    order: 0)]

namespace TrainingGuides.Admin.ProjectSettings.WebChannelSettings;

public class WebChannelSettingsEditSection : EditSectionPage<WebChannelSettingsInfo>
{
}
```

Then add an edit page for the `SeoSettingsInfo` child of the edited `WebChannelSettingsInfo` object.
Bind the parent’s `WebChannelSettingsId` as a parameter, and use it to retrieve the corresponding `SeoSettingsInfo` in the accessor of `ObjectId`.
C#
**SeoSettingsEditPage.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using TrainingGuides.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings.WebChannelSettings;
using Kentico.Xperience.Admin.Base.Forms;
using CMS.DataEngine;

[assembly: UIPage(
    parentType: typeof(WebChannelSettingsEditSection),
    slug: "edit",
    uiPageType: typeof(SeoSettingsEditPage),
    name: "SEO settings",
    templateName: TemplateNames.EDIT,
    order: 0)]

namespace TrainingGuides.Admin.ProjectSettings.WebChannelSettings;

public class SeoSettingsEditPage : InfoEditPage<SeoSettingsInfo>
{
    private readonly IInfoProvider<SeoSettingsInfo> seoSettingsInfoProvider;
    private readonly IInfoProvider<WebChannelSettingsInfo> webChannelSettingsInfoProvider;

    [PageParameter(typeof(IntPageModelBinder))]
    public int WebChannelSettingsId { get; set; }

    [PageParameter(typeof(IntPageModelBinder))]
    public override int ObjectId
    {
        // Retrieves the SEO settings ID based on the WebChannelSettingsId.
        get => seoSettingsInfoProvider.Get()
                .WhereEquals(nameof(SeoSettingsInfo.SeoSettingsWebChannelSettingID), WebChannelSettingsId)
                .FirstOrDefault()?
                .SeoSettingsID ?? 0;
        // The UI will try to set the ObjectID to value of the parent WebChannelSettingsId, so we have to ignore that.
        set { }
    }

    public SeoSettingsEditPage(IFormComponentMapper formComponentMapper,
        IFormDataBinder formDataBinder,
        IInfoProvider<SeoSettingsInfo> seoSettingsInfoProvider,
        IInfoProvider<WebChannelSettingsInfo> webChannelSettingsInfoProvider)
             : base(formComponentMapper, formDataBinder)
    {
        this.seoSettingsInfoProvider = seoSettingsInfoProvider;
        this.webChannelSettingsInfoProvider = webChannelSettingsInfoProvider;
    }

    public override Task ConfigurePage()
    {
        PageConfiguration.UIFormName = "seosettingsedit";
        return base.ConfigurePage();
    }
}
```

On the edit page, override `GetSubmitSuccessResponse` method. Set the `ObjectDisplayName` property of the `EditPageSuccessFormSubmissionResult` to the display name of the parent `WebChannelSettingsInfo`. This ensures that the breadcrumbs and navigation dispaly the correct hierarchy.
C#
**SeoSettingsEditPage.cs**
Copy
```
...
public class SeoSettingsEditPage : InfoEditPage<SeoSettingsInfo>
{

    ...

    private string WebChannelSettingsDisplayName =>
        webChannelSettingsInfoProvider
            .Get()
            .WhereEquals(nameof(WebChannelSettingsInfo.WebChannelSettingsID), WebChannelSettingsId)
            .FirstOrDefault()?
            .WebChannelSettingsChannelDisplayName ?? "Web channel settings";

    ...

    protected override async Task<ICommandResponse> GetSubmitSuccessResponse(SeoSettingsInfo savedInfoObject, ICollection<IFormItem> items)
    {
        var result = new EditPageSuccessFormSubmissionResult()
        {
            Items = await items.OnlyVisible().GetClientProperties(),
            ObjectDisplayName = WebChannelSettingsDisplayName,
            ObjectId = WebChannelSettingsId,
            RefetchAll = RefetchAll
        };

        return ResponseFrom(result).AddSuccessMessage(LocalizationService?.GetString("base.forms.saved"));
    }
}
```

You can see the completed file in the [finished branch of the _Training guides repository_](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Admin/Pages/ProjectSettings/WebChannelSettings/SeoSettingsEditPage.cs) for reference. Note that the code sample above is not localized for simplicity, but the repository version includes a localized version. For more guidance on how to localize your custom UI pages, see our [Admin UI localization documentation](/documentation/developers-and-admins/customization/admin-ui-localization).
If you build the project and navigate to **Project settings → Channel settings → Training guides pages** , you should see a UI like this, where you can enter a _robots.txt_ value for your website channel:
[![Screenshot of SEO settings](/docsassets/guides/add-channels-to-module/SeoSettings.png)](/docsassets/guides/add-channels-to-module/SeoSettings.png)
## Create a sub-section for multiple child objects
If you want to include a listing of child objects that users can manage within the edit section of a parent object, for example, giving each _Web channel settings_ object a listing of _Web channel snippet_ objects, the process is nearly identical to the standard module page approach from the [basic module from earlier in this series](/guides/development/customizations-and-integrations/create-basic-module#build-the-module-ui).
You can define a _Listing page_ as a child of the parent’s _Edit section_. This listing can have its own _Edit section_ and _Create page_ as children.
[![Screenshot of the UI page hierarchy](/docsassets/guides/add-channels-to-module/UITree.png)](/docsassets/guides/add-channels-to-module/UITree.png)
Then, you just need to bind the parent object’s ID, like you did [earlier](#edit-child-objects-as-part-of-a-parent), on any pages that need access to it. For example, you need the parent object’s ID to set the foreign key value on the create page and generate the URL for the edit page, and to filter which child objects are displayed on the listing page.
For our example, complete the following steps:
  1. Create a listing page for _Web channel snippet_ objects under the _Web channel settings_ edit section. 
Follow the same approach as [earlier](/guides/development/customizations-and-integrations/create-basic-module). 
  2. Bind `WebChannelSettingsId` and use it to add a `QueryModifier` to `PageConfiguration.QueryModifiers` in the `ConfigurePage` method.


C#
**WebChannelSnippetListingPage.cs**
Copy
```
using CMS.DataEngine;
using CMS.Membership;
using Kentico.Xperience.Admin.Base;
using TrainingGuides.Admin.ProjectSettings.WebChannelSettings;
using TrainingGuides.ProjectSettings;

[assembly: UIPage(
    parentType: typeof(WebChannelSettingsEditSection),
    slug: "snippets",
    uiPageType: typeof(WebChannelSnippetListingPage),
    name: "Channel snippets",
    templateName: TemplateNames.LISTING,
    order: 10)]

namespace TrainingGuides.Admin.ProjectSettings.WebChannelSettings;
public class WebChannelSnippetListingPage : ListingPage
{
    protected override string ObjectType => WebChannelSnippetInfo.OBJECT_TYPE;

    [PageParameter(typeof(IntPageModelBinder))]
    public int WebChannelSettingsId { get; set; }

    public override Task ConfigurePage()
    {
        PageConfiguration.ColumnConfigurations
            .AddColumn(nameof(WebChannelSnippetInfo.WebChannelSnippetDisplayName), "Snippet")
            .AddColumn(nameof(WebChannelSnippetInfo.WebChannelSnippetType), "Type");

        PageConfiguration.HeaderActions.AddLink<WebChannelSnippetCreatePage>(
            "New snippet",
            parameters: new PageParameterValues
                {
                    { typeof(WebChannelSettingsEditSection), WebChannelSettingsId }
                });

        PageConfiguration.AddEditRowAction<WebChannelSnippetEditSection>(
            parameters: new PageParameterValues
                {
                    { typeof(WebChannelSettingsEditSection), WebChannelSettingsId }
                });

        PageConfiguration.TableActions
            .AddDeleteAction(nameof(Delete));

        PageConfiguration.QueryModifiers
            .AddModifier((query, _) =>
            {
                return query.Where(new WhereCondition().WhereEquals(nameof(WebChannelSnippetInfo.WebChannelSnippetWebChannelSettingsID), WebChannelSettingsId));
            });

        return base.ConfigurePage();
    }

    [PageCommand(Permission = SystemPermissions.DELETE)]
    public override Task<ICommandResponse<RowActionResult>> Delete(int id) => base.Delete(id);
}
```

Next, add an edit section for code snippets and an edit page underneath it.
C#
**WebChannelSnippetEditSection.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using TrainingGuides.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings.WebChannelSettings;

[assembly: UIPage(
    parentType: typeof(WebChannelSnippetListingPage),
    slug: PageParameterConstants.PARAMETERIZED_SLUG,
    uiPageType: typeof(WebChannelSnippetEditSection),
    name: "Edit snippets",
    templateName: TemplateNames.SECTION_LAYOUT,
    order: 0)]

namespace TrainingGuides.Admin.ProjectSettings.WebChannelSettings;

public class WebChannelSnippetEditSection : EditSectionPage<WebChannelSnippetInfo>
{
}
```

C#
**WebChannelSnippetEditPage.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using TrainingGuides.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings.WebChannelSettings;
using Kentico.Xperience.Admin.Base.Forms;

[assembly: UIPage(
    parentType: typeof(WebChannelSnippetEditSection),
    slug: "edit",
    uiPageType: typeof(WebChannelSnippetEditPage),
    name: "Edit snippet",
    templateName: TemplateNames.EDIT,
    order: 0)]

namespace TrainingGuides.Admin.ProjectSettings.WebChannelSettings;

public class WebChannelSnippetEditPage : InfoEditPage<WebChannelSnippetInfo>
{
    [PageParameter(typeof(IntPageModelBinder), typeof(WebChannelSnippetEditSection))]
    public override int ObjectId { get; set; }

    public WebChannelSnippetEditPage(IFormComponentMapper formComponentMapper, IFormDataBinder formDataBinder)
             : base(formComponentMapper, formDataBinder)
    {
    }

    public override Task ConfigurePage()
    {
        PageConfiguration.UIFormName = "webchannelsnippetedit";
        return base.ConfigurePage();
    }
}
```

Finally, add a _create page_. Make sure to bind the `WebChannelSettingsID`, and add it as a URL parameter for when the edit page is displayed after creation.
Override the `FinalizeInfoObject` method to make sure that `WebChannelSnippetWebChannelSettingsID` is set to `WebChannelSettingsId`
C#
**WebChannelSnippetCreatePage.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using TrainingGuides.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings.WebChannelSettings;
using Kentico.Xperience.Admin.Base.Forms;

[assembly: UIPage(
    parentType: typeof(WebChannelSnippetListingPage),
    slug: "create",
    uiPageType: typeof(WebChannelSnippetCreatePage),
    name: "Create snippet",
    templateName: TemplateNames.EDIT,
    order: 20)]

namespace TrainingGuides.Admin.ProjectSettings.WebChannelSettings;

public class WebChannelSnippetCreatePage : CreatePage<WebChannelSnippetInfo, WebChannelSnippetEditSection>
{
    // Bind the parent object's ID
    [PageParameter(typeof(IntPageModelBinder), typeof(WebChannelSettingsEditSection))]
    public int WebChannelSettingsId { get; set; }

    public WebChannelSnippetCreatePage(IFormComponentMapper formComponentMapper,
        IFormDataBinder formDataBinder,
        IPageLinkGenerator pageLinkGenerator)
        : base(formComponentMapper, formDataBinder, pageLinkGenerator)
        {
        }

    public override Task ConfigurePage()
    {
        // Add the parent object's ID to the URL
        AdditionalLinkParameters.Add(new PageParameterValues
            {
                { typeof(WebChannelSettingsEditSection), WebChannelSettingsId}
            });
        PageConfiguration.UIFormName = "webchannelsnippetedit";
        return base.ConfigurePage();
    }

    // Ensure that the child object's foreign key is set
    protected override Task FinalizeInfoObject(WebChannelSnippetInfo infoObject, IFormFieldValueProvider fieldValueProvider, CancellationToken cancellationToken)
    {
        infoObject.WebChannelSnippetWebChannelSettingsID = WebChannelSettingsId;

        return base.FinalizeInfoObject(infoObject, fieldValueProvider, cancellationToken);
    }
}
```

Now you should have a new section in your **Project settings** application with SEO settings and code snippets for each channel.
## See the result
With these tools you should be able to create a hierarchical custom module structure with multiple layers, including one-to-one and one-to-many relationships. You can also create channel-specific subsections in the admin UI.
If you followed the example, try going to the **Channel management** application a few times to create and delete channels. Each time you navigate to **Project settings → Channel settings** , you’ll be able to see the available channel settings update accordingly, each with is own distinct _SEO settings_ and its own empty set of snippets.
Your browser does not support the video tag. 
## What’s next?
The [next guide in this series](/guides/development/customizations-and-integrations/access-custom-channel-specific-configurations) will expand upon this example, demonstrating how to put the channel-specific settings to use on your live site.
![]()
[]()[]()
