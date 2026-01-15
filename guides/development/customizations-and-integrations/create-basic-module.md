# Create a basic module to hold custom settings
  * How-to| [ Copy page link ](guides/development/customizations-and-integrations/create-basic-module#) | [Get HelpService ID](guides/development/customizations-and-integrations/create-basic-module#)
Core MVC 5


[✖](guides/development/customizations-and-integrations/create-basic-module# "Close page link panel") [Copy to clipboard](guides/development/customizations-and-integrations/create-basic-module#)
A custom module is a common requirement in digital experience projects. It allows developers to empower certain users to configure the site in new ways that are not available out-of-the-box.
This is the start of a series that shows how to create a basic custom settings module. To start with, the module will hold custom settings keys and values. Administrators and editors with sufficient permissions in the UI will be able to create and adjust the values of these settings, which you can access from your code.
Your browser does not support the video tag. 
The custom settings we’ll create initially will be **global** and not associated with any specific channel. We will create custom **channel-specific settings** later in the series.
Some of the materials here are not exclusive, and may look familiar if you’ve already completed other modules.
## Before you start
This series requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


**Code samples**
You can find a project with completed, working versions of code samples from this series and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with our guides and learning modules.
The code samples in this series are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Define module and data class
The individual settings in our custom settings module (e.g., the Email notification sender from the video above) correspond to objects of a [custom class](documentation/developers-and-admins/customization/object-types).
Each class must belong to a **Module** – a container that groups related data classes. We recommend organizing your custom classes into separate modules for different features.
### Create custom module
  1. Navigate to your Xperience administration.
  2. [Create a new module](documentation/developers-and-admins/customization/object-types#create-a-module), entering these values: 
     * **Module name** : Project settings
     * **Identifiers → Code name** : TrainingGuides.ProjectSettings
Note, that in order to fill out the **Code name** manually, you need to uncheck the **Pre-fill the code name automatically** checkbox (checked by default).
We recommend including the namespace of your client or organization in the code name (_TrainingGuides_ in this example). It will help you easily differentiate the custom modules and their purpose.


### Create data class
  1. [Create new class](documentation/developers-and-admins/customization/object-types#define-and-model-the-data-class) within your new module. Use the following parameters:
     * **Class name** : Global settings key
     * **Namespace** : TrainingGuides
     * **Name** : GlobalSettingsKey
Now if you check the **CMS_Class** table of the database, you’ll see a row representing your new class. [![Select from a CMS_Class table showing the new TrainingGuides.GlobalSettingsKey class](docsassets/guides/create-basic-module/global-settings-class-in-db.png)](https://docs.kentico.com/docsassets/guides/create-basic-module/global-settings-class-in-db.png)
  2. [Specify **Database columns**](documentation/developers-and-admins/customization/object-types#define-and-model-the-data-class) for the class. Add columns with the following properties:
    1. The value of the setting, which you will access in code and used to influence functionality 
       * **Field name** : GlobalSettingsKeyValue
       * **Data type** : Text
       * **Size** : 1000
       * **Required** : True (Enabled)
    2. The code name of the setting, which you will use to find the setting and its value 
       * **Field name** : GlobalSettingsKeyName
       * **Data type** : Text
       * **Size** : 200
       * **Required** : True (Enabled)
    3. The display name of the setting, which provides a human-readable name for the listing page 
       * **Field name** : GlobalSettingsKeyDisplayName
       * **Data type** : Text
       * **Size** : 200
       * **Required** : True (Enabled)
    4. A note field, which tells editors how to use the setting and provides additional context 
       * **Field name** : GlobalSettingsKeyNote
       * **Data type** : Text
       * **Size** : 2000
       * **Required** : True (Enabled)
[![](docsassets/guides/create-basic-module/global-settings-db-columns.png)](https://docs.kentico.com/docsassets/guides/create-basic-module/global-settings-db-columns.png)
**Column roles designations**
Create fields whose names end in _DisplayName_ , _Name_ , and _LastModified_ with appropriate data types to automatically map them to the **Display name column** , **Code name column** , and **“Last modified” column** roles, respectively.
_Binary_ and _Guid_ fields will automatically map to the **GUID column** and **Binary column** of the class.
These settings are important for [code generation](documentation/developers-and-admins/api/generate-code-files-for-system-objects), which we will discuss [later on](guides/development/customizations-and-integrations/create-basic-module#generate-code-files).
Switch to the **Code** tab to see that your new fields have been automatically mapped:
[![Database columns ‘GlobalSettingsKeyDisplayName’ and ‘GlobalSettingsKeyName’ automatically mapped to ‘Display name column’ and ‘Code name column’ fields in the Code tab](docsassets/guides/create-basic-module/global-settings-colum-designation.png)](https://docs.kentico.com/docsassets/guides/create-basic-module/global-settings-colum-designation.png)
Note that you can always manually change the designations on the **Code**.


### Add a UI form for the class
To enable editors or administrators to create items of your class in the admin UI, you need a **UI form**.
  1. [Create a new edit form](documentation/developers-and-admins/customization/object-types#define-editing-forms) for your class: 
    1. **UI form name** : Global settings key edit
    2. **Identifiers → Code name** : GlobalSettingsKeyEdit 
In this case, you can simply leave **Pre-fill code name automatically** checked instead of typing the code name manually. The system-generated code name will be identical.
  2. Switch to the **Fields** tab in the left menu.
  3. Add a new UI field for each of your class database fields: 
    1. Click **New field**.
    2. Select a database column you want to map the field to. The available columns are sorted alphabetically.
    3. Fill out field settings as desired and hit **Save**.
For our example, let’s set the fields up as follows: 
     * _GlobalSettingsKeyDisplayName_
       * **Field caption** : Display name
       * **Enabled** : True (Enabled)
       * **Form component** : Text input
     * _GlobalSettingsKeyName_
       * **Field caption** : Settings key code name
       * **Enabled** : True (Enabled)
       * **Form component** : Text input
     * _GlobalSettingsKeyValue_
       * **Field caption** : Value
       * **Enabled** : True (Enabled)
       * **Form component** : Text area
       * **Minimum number of rows** : 3
       * **Maximum number of rows** : 5
     * _GlobalSettingsKeyNote_
       * **Field caption** : Notes
       * **Tooltip text** : Explain the purpose of this settings key.
       * **Enabled** : True (Enabled)
       * **Form component** : Text area
       * **Minimum number of rows** : 3
       * **Maximum number of rows** : 15


[![Global settings key edit form fields](docsassets/guides/create-basic-module/global-settings-edit-form.png)](https://docs.kentico.com/docsassets/guides/create-basic-module/global-settings-edit-form.png)
Your custom class and edit form are now ready.
## Generate code files
The next step toward accessing your class and its objects in your code in a strongly typed manner is to [generate code files for it](documentation/developers-and-admins/customization/object-types#generate-code-files).
**Dedicated info providers**
We recommend using the `--with-provider-class "false"` parameter to **avoid** generating a [dedicated provider](documentation/developers-and-admins/customization/customize-system-providers) for your class.
You should use a [generic provider](documentation/developers-and-admins/customization/customize-system-providers), based on `IInfoProvider<TInfo>` to access the objects of your class - see an example [later on in this series](guides/development/customizations-and-integrations/access-custom-global-configurations#map-configuration-to-the-options-model) .
This is the best practice **unless** you specifically need to retrieve your objects in a context where **dependency injection is not yet available**. An example is the `CookieLevelConsentMapping` class in [one of our Data protection guides](guides/development/data-protection/map-consents-to-cookie-levels).
**Double-check**
If you’ve previously generated classes that **do** require dedicated providers, use the `--include` or `--exclude` parameters to ensure that the new command does not overwrite them.
If you are working in the [Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides), you can use the following command. (Skip the `--exclude` parameter if your code doesn’t include the `CookieLevelConsentMapping` class.)
CMD
Copy
```
dotnet run --no-build -- --kxp-codegen --type "Classes" --location "../TrainingGuides.Entities/{type}/{name}/ " --exclude "trainingguides.cookielevelconsentmapping" --with-provider-class "false"
```

As a result, the system will generate a partial `GlobalSettingsKeyInfo` class under the namespace matching the module name (`TrainingGuides.ProjectSettings`) inside the _TrainingGuides.Entities_ project.
[![Generated Global settings key class](docsassets/guides/create-basic-module/global-settings-key-generated.png)](https://docs.kentico.com/docsassets/guides/create-basic-module/global-settings-key-generated.png)
Visit [our documentation page](documentation/developers-and-admins/api/generate-code-files-for-system-objects) for more information on the code generation parameters.
### Enable CI for the new class
If your project uses [Continuous integration](documentation/developers-and-admins/ci-cd/continuous-integration), you need to enable the feature for the newly generated `GlobalSettingsKeyInfo` class. You can achieve this by overriding the [object type configuration](documentation/developers-and-admins/customization/object-types/object-type-configuration) of the class.
Rather than editing the generated files directly, we recommend performing the configuration in **a new partial class file**. This way, you won’t accidentally overwrite your changes the next time you generate code files.
In the _TrainingGuides.Entities/Classes/Overrides_ folder create a partial `GlobalSettingsKeyInfo` class and override its `TYPEINFO` definition:
C#
**GlobalSettingsKeyInfoOverride.cs**
Copy
```
namespace TrainingGuides.ProjectSettings;

public partial class GlobalSettingsKeyInfo
{
    static GlobalSettingsKeyInfo()
    {
        TYPEINFO.ContinuousIntegrationSettings.Enabled = true;
    }
}
```

## Build the module UI
You have created a custom module and class representing custom settings. However, at this point, administrators or editors still cannot manage the settings from the administration dashboard.
Let’s develop some [UI pages](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) for them.
**Admin UI localization**
Note that the custom pages created in this example are not localized. For guidance on how to localize Admin UI for your solution and your custom UI pages, see our [Admin UI localization documentation](documentation/developers-and-admins/customization/admin-ui-localization). For an example implementation, check out the finished branch of our Training guides repository and look for the [SeoSettingsEditPage](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Admin/Pages/ProjectSettings/WebChannelSettings/SeoSettingsEditPage.cs).
The pages will have the following structure in the UI tree:
  * Under the **Configuration category** , we will have a **Project settings** application.
  * Under the application, there will be a listing of **Global settings**.
  * From the **Global settings** page, the user can: 
    * edit a specific setting - through the **Edit** section holding an **Edit global settings key** page.
    * create a new setting - through the **Create global settings key** page.


[![UI pages structure in the System -> UI tree](docsassets/guides/create-basic-module/global-settings-UI-tree.png)](https://docs.kentico.com/docsassets/guides/create-basic-module/global-settings-UI-tree.png)
### Create the application page
**Store customization files separately**
We recommend storing any files customizing the Xperience administration separate from the channel-specific files. In our [Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) and this series, we use the _TrainingGuides.Admin_ project for this purpose.
If you are using the main branch of our repository and your solution does not contain this project yet, you can follow the steps in the [_Set up the project_ section](guides/development/data-protection/map-consents-to-cookie-levels#set-up-the-project) of the cookie consent guide to define it.
If you are working with more of an enterprise-sized solution, our guide about [utilizing Clean architecture in Xperience projects](guides/development/get-started/use-abstraction-and-enterprise-level-architecture-in-xperience-projects) may be interesting to you.
  1. In your source code, navigate to your administration customization project (e.g., _TrainingGuides.Admin_).
  2. Create the following folder structure: _Pages/ProjectSettings_.
We recommend **matching the folder name** with the **name of your custom module**.
  3. In the _ProjectSettings_ folder, create a new class file, _ProjectSettingsApplication.cs_.
  4. Implement your custom [UI application page](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-application-pages):


C#
**ProjectSettingsApplication.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.Base.UIPages;
using TrainingGuides.Admin.ProjectSettings;

// Register the UI application pages - see a links and more details in the info box below.
[assembly: UIApplication(
    identifier: ProjectSettingsApplication.IDENTIFIER,
    type: typeof(ProjectSettingsApplication),
    slug: "project-settings",
    name: "Project settings",
    category: BaseApplicationCategories.CONFIGURATION,
    icon: Icons.BoxCogwheel,
    templateName: TemplateNames.SECTION_LAYOUT)]

namespace TrainingGuides.Admin.ProjectSettings;

// The class must inherit from the ApplicationPage base class.
public class ProjectSettingsApplication : ApplicationPage
{
    public const string IDENTIFIER = "TrainingGuides.ProjectSettingsApplication";
}
```

**Registration and category**
Read more about registration parameters [in our documentation](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-application-pages#register-ui-application-pages).
Each UI application page has to be registered under a [category](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-application-pages#ui-application-categories). In this example we use **Configuration** - one of the [pre-defined categories in Xperience](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-application-pages#predefined-categories).
Optionally, you can also [create and register your own custom categories](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-application-pages#ui-application-categories).
#### Permissions
Each application in the Xperience administration needs to declare the [set of permissions it actively evaluates](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-permission-checks#define-the-set-of-application-permissions).
Our custom module will support viewing, creating, deleting and updating instances of the GlobalSettingsKey class. Let’s add the default system permissions, VIEW, CREATE, DELETE and UPDATE, by decorating the `ProjectSettingsApplication` class with the `UIPermission` attributes.
Later on you can [further restrict actions (page commands) or UI pages access](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-permission-checks#require-permissions-to-access-ui-pages) within the application, using any permission from this set.
The permissions correspond with those assigned to roles in the [Role management](documentation/developers-and-admins/configuration/users/role-management) application.
You can also use any custom permissions that exist in your system or have an application page without any permission requirements. [Learn more about permissions](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-permission-checks).
C#
**[FRAGMENT] ProjectSettingsApplication.cs - permissions**
Copy
```
...
using CMS.Membership;
...
[UIPermission(SystemPermissions.VIEW)]
[UIPermission(SystemPermissions.CREATE)]
[UIPermission(SystemPermissions.DELETE)]
[UIPermission(SystemPermissions.UPDATE)]
public class ProjectSettingsApplication : ApplicationPage
...
```

See the [full ProjectSettingsApplication.cs file](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Admin/Pages/ProjectSettings/ProjectSettingsApplication.cs) in our Training guides repository for your reference.
Now if you run your solution, you should be able to see your new **Project settings** application in the Xperience administration under **Configuration**.
[![Project settings application tile in the Xperience administration](docsassets/guides/create-basic-module/project-settings-new-tile.png)](https://docs.kentico.com/docsassets/guides/create-basic-module/project-settings-new-tile.png)
### Define the listing page
Your application is now accessible in the Xperience administration, but if you open it, you will see an empty page right now.
Instead, we want to show a list of available project settings, with an option to perform basic CRUD operations (for the users with sufficient permissions).
Let’s create a [listing page](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template) and its underlying _edit_ and _create_ pages.
  1. Create a _ProjectSettings/GlobalSettings_ folder - let’s keep all our GlobalSettingsKey-related pages in one place.
  2. In the _GlobalSettings_ folder, create a new class file, _GlobalSettingsListingPage.cs_.
  3. Implement a UI page using the [Listing UI page template](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template). 
     * Register the page as a child of `ProjectSettingsApplication` created in [the previous section](guides/development/customizations-and-integrations/create-basic-module#create-the-application-page).
     * Decorate the `Delete` method override with the `PageCommand` attribute to restrict the action to users with permission to delete.
Learn more about [page commands and permissions in our documentation](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-permission-checks#evaluate-permissions-within-page-commands). 
C#
**GlobalSettingsListingPage.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using TrainingGuides.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings.GlobalSettings;
using CMS.Membership;

[assembly: UIPage(
    parentType: typeof(ProjectSettingsApplication),
    slug: "global-settings",
    uiPageType: typeof(GlobalSettingsListingPage),
    name: "Global settings",
    templateName: TemplateNames.LISTING,
    order: 0)]

namespace TrainingGuides.Admin.ProjectSettings.GlobalSettings;
// The class has to inherit from ListingPage.
public class GlobalSettingsListingPage : ListingPage
{
    protected override string ObjectType => GlobalSettingsKeyInfo.OBJECT_TYPE;

    public override async Task ConfigurePage()
    {
        // Determine which columns of your GlobalSettingsKeyInfo objects to show the user in the list view.
        PageConfiguration.ColumnConfigurations
                    .AddColumn(nameof(GlobalSettingsKeyInfo.GlobalSettingsKeyDisplayName), "Name")
                    .AddColumn(nameof(GlobalSettingsKeyInfo.GlobalSettingsKeyValue), "Value")
                    .AddColumn(nameof(GlobalSettingsKeyInfo.GlobalSettingsKeyNote), "Note")
                    .AddColumn(nameof(GlobalSettingsKeyInfo.GlobalSettingsKeyName), "Codename");

        // Add a "New settings" button at the top. This will navigate the user to a "create" page.
        // We will define it in the next section.
        PageConfiguration.HeaderActions.AddLink<GlobalSettingsCreatePage>("New setting");

        // Add an "Edit" row action. This will navigate the user to a "edit" section for a specific instance of GlobalSettingsKeyInfo object.
        // We will define it in the next section.
        PageConfiguration.AddEditRowAction<GlobalSettingsEditSection>();

        // Define a "Delete" action for the user with sufficient permissions to remove an existing setting.
        PageConfiguration.TableActions
            .AddDeleteAction(nameof(Delete));

        await base.ConfigurePage();
    }

    // Ensure only a user with permissions to delete can perform the delete action.
    [PageCommand(Permission = SystemPermissions.DELETE)]
    public override Task<ICommandResponse<RowActionResult>> Delete(int id) => base.Delete(id);
}
```



Read [more about listing page template](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template) and it’s options in our documentation.
If you copy-pasted the code sample above, note that the `GlobalSettingsListingPage` class references `GlobalSettingsCreatePage` and `GlobalSettingsEditSection` - classes that don’t exist yet. We will create them [in the next section](guides/development/customizations-and-integrations/create-basic-module#define-create-and-edit-pages).
If you wish, comment out the two lines of code for now. When you visit the **Project settings** application in your administration, you should see an empty listing page, similar to this:
[![Empty listing page](docsassets/guides/create-basic-module/global-settings-empty-listing.png)](https://docs.kentico.com/docsassets/guides/create-basic-module/global-settings-empty-listing.png)
### Define create and edit pages
Both create and edit pages for `GlobalSettingsKeyInfo` objects will utilize the `GlobalSettingsKeyEdit` UI form you created [earlier](guides/development/customizations-and-integrations/create-basic-module#add-a-ui-form-for-the-class).
#### Edit page
When the user goes to edit a specific instance of the _GlobalSettingsKey_ class, their URL should contain the ID of the object that the user is currently editing, e.g., _~/admin/project-settings/global-settings/2/edit_
We first need to create an _edit section page_ to ensure a desired tree structure and correct routing. Subsequently, we will make the edit page a child of the edit section page.
Find more details in the [first section of this documentation page](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template).
  1. In the _GlobalSettings_ folder, create a new class file, _GlobalSettingsEditSection.cs_.
  2. Implement the `GlobalSettingsEditSection` class, utilizing the [`PARAMETERIZED_SLUG` parameter](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages#parameterized-url-slugs).
C#
**GlobalSettingsEditSection.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using TrainingGuides.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings.GlobalSettings;

// Register the UI page as child of GlobalSettingsListingPage.
[assembly: UIPage(
    parentType: typeof(GlobalSettingsListingPage),
    slug: PageParameterConstants.PARAMETERIZED_SLUG,
    uiPageType: typeof(GlobalSettingsEditSection),
    name: "Edit",
    templateName: TemplateNames.SECTION_LAYOUT,
    order: 10)]

namespace TrainingGuides.Admin.ProjectSettings.GlobalSettings;

// Inherit from EditSectionPage and specify the type of object edited
public class GlobalSettingsEditSection : EditSectionPage<GlobalSettingsKeyInfo>
{ }
```

  3. In the _GlobalSettings_ folder, create a new class file, _GlobalSettingsEditPage.cs_.
  4. Implement your edit page:
C#
**GlobalSettingsEditPage.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using TrainingGuides.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings.GlobalSettings;
using Kentico.Xperience.Admin.Base.Forms;

// Register page as a child of GlobalSettingsEditSection
[assembly: UIPage(
    parentType: typeof(GlobalSettingsEditSection),
    slug: "edit",
    uiPageType: typeof(GlobalSettingsEditPage),
    name: "Edit global settings key",
    templateName: TemplateNames.EDIT,
    order: 0)]

namespace TrainingGuides.Admin.ProjectSettings.GlobalSettings;

public class GlobalSettingsEditPage : InfoEditPage<GlobalSettingsKeyInfo>
{
    [PageParameter(typeof(IntPageModelBinder))]
    public override int ObjectId { get; set; }

    public GlobalSettingsEditPage(IFormComponentMapper formComponentMapper,
        IFormDataBinder formDataBinder)
        : base(formComponentMapper, formDataBinder)
    {
    }

    public override Task ConfigurePage()
    {
        // Assign your 'GlobalSettingsKeyEdit' UI form code name to the page. The assignment is case-insensitive.
        PageConfiguration.UIFormName = "globalsettingskeyedit";
        return base.ConfigurePage();
    }
}
```



#### Create page
  1. In the _GlobalSettings_ folder, create a new class file, _GlobalSettingsCreatePage.cs_.
  2. Implement the [page using the `CreatePage` base class](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template#use-edit-pages-to-create-new-objects).
C#
**GlobalSettingsCreatePage.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using TrainingGuides.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings.GlobalSettings;
using Kentico.Xperience.Admin.Base.Forms;

// Register page as a child of GlobalSettingsListingPage created above.
[assembly: UIPage(
    parentType: typeof(GlobalSettingsListingPage),
    slug: "create",
    uiPageType: typeof(GlobalSettingsCreatePage),
    name: "Create global settings key",
    templateName: TemplateNames.EDIT,
    order: 20)]

namespace TrainingGuides.Admin.ProjectSettings.GlobalSettings;

// Specify the entity which this page will create (GlobalSettingsKeyInfo)
// and the page to redirect to after the entity has been cerated (GlobalSettingsEdit page)
public class GlobalSettingsCreatePage : CreatePage<GlobalSettingsKeyInfo, GlobalSettingsEditPage>
{
    public GlobalSettingsCreatePage(IFormComponentMapper formComponentMapper,
        IFormDataBinder formDataBinder,
        IPageLinkGenerator pageLinkGenerator)
        : base(formComponentMapper, formDataBinder, pageLinkGenerator)
    {
    }

    public override Task ConfigurePage()
    {
        // Assign your 'GlobalSettingsKeyEdit' UI form code name to the page. The assignment is case-insensitive.
        PageConfiguration.UIFormName = "globalsettingskeyedit";
        return base.ConfigurePage();
    }
}
```



Learn more about the [edit UI page template and it’s options here](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template).
To see another edit page example implementation take a look at our [_Add a custom field to the Contact profile_ guide](guides/development/customizations-and-integrations/add-custom-contact-field).
Build your solution and navigate to **System → UI tree** in the Xperience administration. If you have followed along with our example, you should be able to see your new pages in a UI tree structure:
[![New UI pages in the System → UI tree](docsassets/guides/create-basic-module/global-settings-key-UI-tree-final.png)](https://docs.kentico.com/docsassets/guides/create-basic-module/global-settings-key-UI-tree-final.png)
When you navigate to your new **Project settings** application, you should now be able to create, view, edit, and delete custom settings, like in this video:
Your browser does not support the video tag. 
If you still only see an empty listing page with no New setting button, make sure you uncommented the lines in the `GlobalSettingsListingPage`, referencing `GlobalSettingsCreatePage` and `GlobalSettingsEditSection`.
## Next steps
You have learned how to create a custom key-value settings module in Xperience. The [next guide in this series](guides/development/customizations-and-integrations/access-custom-global-configurations) will demonstrate how you can access your custom settings instances through the [ObjectQuery API](documentation/developers-and-admins/api/objectquery-api) and work with them in your code.