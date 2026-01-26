---
source: https://docs.kentico.com/modules/custom-settings/model-channel-specific-settings
scrape_date: 2026-01-26
---

Module: Custom modules: custom settings
5 of 9 Pages
# Model channel-specific settings
In the samples from the [earlier in this series](/modules/custom-settings), we created a basic module UI with standard listing, create, edit, and delete functionality.
Now, we will explore the process of:
  * Adding per-channel configuration to a custom module
  * Managing multiple levels of hierarchy
  * Using UI pages to ensure one-to-one relationships between objects


The samples here will add a website-channel-specific settings section to the module defined [earlier](/modules/custom-settings).
The new UI pages will allow designated users to select a [website channel](/documentation/developers-and-admins/configuration/website-channel-management) from a list and edit its settings. These include SEO-related settings and channel-specific code snippets.
Your browser does not support the video tag. 
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
Now, as we did [in the earlier example](/modules/custom-settings/define-module-in-xperience-ui#generate-code-files), generate code files for the new classes.
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
As in the [the earlier example](/modules/custom-settings) and the [cookie consent mapping guide](/guides/development/data-protection/map-consents-to-cookie-levels#generate-the-code), consider any changes you may need to make to the [object type configuration](/documentation/developers-and-admins/customization/object-types/object-type-configuration) of the classes.
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

[ Previous page ](/modules/custom-settings/access-custom-global-configurations)
5 of 9
[ Mark complete and continue ](/modules/custom-settings/build-channel-specific-settings-ui)
![]()
[]()[]()
