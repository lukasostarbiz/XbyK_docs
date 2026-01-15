# Example - Offices management application
  * [ Copy page link ](documentation/developers-and-admins/customization/object-types/example-offices-management-application#) | [Get HelpService ID](documentation/developers-and-admins/customization/object-types/example-offices-management-application#)
Core MVC 5


[✖](documentation/developers-and-admins/customization/object-types/example-offices-management-application# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/customization/object-types/example-offices-management-application#)
This page contains step-by-step instructions showing how to implement a custom application for the Xperience administration, which allows management of manage offices. When working with the application, users:
  * Create new office objects
  * Assign users registered in the system as workers
  * Create listings of open positions for individual offices


Developers can then [display the modeled data](documentation/developers-and-admins/development/content-retrieval#retrieve-general-object-data) on the website, for example when building the site’s company overview or hiring sections.
The implementation makes use of custom [object types](documentation/developers-and-admins/customization/object-types) to define required database entities and the [admin UI customization framework](documentation/developers-and-admins/customization/extend-the-administration-interface) to build an editing interface for administration users. For this reason, you should already be familiar with these concepts (though relevant links are provided throughout the text).
This page is separated into multiple sections, each building upon the functionality implemented previously:
  1. [Prepare a class library for administration-related customizations](documentation/developers-and-admins/customization/object-types/example-offices-management-application#ExampleOfficesmanagementapplication-ClassLibrary)
  2. Define the custom Office object type and build a basic editing interface 
    1. [Define the Office object type](documentation/developers-and-admins/customization/object-types/example-offices-management-application#define-the-office-object-type)
    2. [Build a basic editing interface for the Office object type](documentation/developers-and-admins/customization/object-types/example-offices-management-application#build-a-basic-editing-interface-for-the-office-object-type)
  3. [Add the option to define open positions for individual offices via a child object type](documentation/developers-and-admins/customization/object-types/example-offices-management-application#ExampleOfficesmanagementapplication-ChildClass)
  4. [Link users registered in the system to individual offices via a binding object type](documentation/developers-and-admins/customization/object-types/example-offices-management-application#ExampleOfficesmanagementapplication-BindingClass)


We recommend storing all administration interface-related customizations in a dedicated class library project. This approach brings benefits such as separability for [specific types of deployments](documentation/developers-and-admins/deployment/deploy-to-private-cloud/deploy-without-the-administration). 
  1. Create a [new class library project](documentation/developers-and-admins/customization/integrate-custom-code) in your Xperience solution.
     * The example on this page uses a fictitious _Acme_ company to ensure the uniqueness of various object identifiers and namespace declarations.
     * For example, name the class library: _Acme.Web.Admin_
  2. Add the **Kentico.Xperience.Admin** package to the project.
  3. [Enable class discovery](documentation/developers-and-admins/customization/integrate-custom-code#enable-class-discovery) for the assembly via the `AssemblyDiscoverable` attribute. 
C#
**AssemblyAttributes.cs**
Copy
```
using CMS;

[assembly:AssemblyDiscoverable]
```

  4. Reference the project from your main Xperience project.


## Define the Office object type
### Create a new module to host the Office object type
  1. Open the **Modules** application.
  2. Select **New module** and fill in: 
    1. **Module name** : Acme
    2. **Identifiers → Code name** : AcmeModule
  3. **Save** the changes.


### Create and model the Office object type
  1. Open the **Modules** application and select the **Acme** module from the listing.
  2. Switch to the **Classes** tab and select **New class**.
  3. Enter the following values: 
    1. **Class name** : Office
    2. **Code name**
      1. **Namespace** : Acme
      2. **Name** : Office
  4. Select **Save**.


The system creates the data class and its corresponding database table: _Acme_Office_
Continue by modeling the database entity:
  1. Switch to the **Database columns** tab of the **Office** class (available after the class is created).
  2. Using the [field editor](documentation/developers-and-admins/customization/field-editor), add the following fields: 
     * Field 1: 
       * **Field name** : OfficeType
       * **Data type** : Integer number
       * **Required** : No (unselected)
       * **Default value** : 0
     * Field 2: 
       * **Field name** : OfficeDisplayName
       * **Data type** : Text
       * **Required** : Yes (checked)
  3. **Save** your changes after each field.


### Generate code files for the Office object type
  1. Shut down the application.
  2. Run the following command from the root directory of your project. When prompted for confirmation, press `Y`:
CMD
Copy
```
dotnet run --no-build -- --kxp-codegen --type "Classes" --include "Acme.*"
```

  3. Move the generated code files for classes with the `Acme` namespace from the _Classes\Acme\Office_ directory under your project’s root to the custom class library. Alternatively, you can use the `--location` [parameter](documentation/developers-and-admins/api/generate-code-files-for-system-objects) to set a custom path that targets the class library. Make sure the classes do not remain under your main web project, otherwise you will encounter errors when working with the office object types.
  4. Relaunch the site using `dotnet run`.


With the code files created, the last step required to prepare the object type is to define the _create_ and _edit_ UI forms that enable basic manipulation of office objects via custom admin UI pages.
### Define the create UI form
  1. Open the **Modules** application, edit the **Acme** module and select the **Office** class.
  2. Switch to the **UI forms** tab and select **New UI form**.
  3. Enter the following values: 
    1. **UI form name** : Office create
    2. **Identifiers → Code name** : create
  4. Select **Save**.


With the UI form created, define its fields:
  1. Switch to the **Fields** tab.
  2. Add the following fields using the field editor: 
     * Field 1: 
       * **Database column** : OfficeDisplayName
       * **Field caption** : Office name
       * **Form component:** Text input
       * **Watermark:** Office name
     * Field 2: 
       * **Database column** : OfficeType
       * **Field caption** : Office type
       * **Form component** : Number input
  3. **Save** your changes after each field.


### Define the edit UI form
  1. Open the **Modules** application, edit the **Acme** module and select the **Office** class.
  2. Switch to the **UI forms** tab and select **New UI form**.
  3. Enter the following values: 
    1. **UI form name** : Office edit
    2. **Identifiers → Code name** : edit
  4. Select **Save**.


With the UI form created, define its fields:
  1. Switch to the **Fields** tab.
  2. Add the following fields using the field editor: 
     * Field 1: 
       * **Database column** : OfficeDisplayName
       * **Field caption** : Office name
       * **Form component:** Text input
       * **Watermark:** Office name
     * Field 2: 
       * **Database column** : OfficeType
       * **Field caption** : Office type
       * **Form component** : Number input
  3. **Save** your changes after each field.


This concludes the basic configuration of the Office object type. The next step details how to build a simple application for the administration interface where users can create and edit Office objects.
## Build a basic editing interface for the Office object type
This section covers the implementation of the **Office management** application for the admin UI.
### Application page
The [application page](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-application-pages) serves as an entry point to the application. It contains the application registration and also registers a custom category under which the application is displayed in the administration.
  1. Create a new folder structure in the custom class library: _UIPages/OfficeManagement_
     * The _UIPages_ folder groups all custom UI page implementations
     * The _OfficeManagement_ folder groups all implementations that belong to the OfficeManagement application
  2. Under the _OfficeManagement_ folder, create a new class **OfficeManagementApplication.cs** and copy the following code:


C#
**Office management application page - OfficeManagementApplication.cs**
Copy
```
using Kentico.Xperience.Admin.Base;

using Acme.Web.Admin.UIPages.OfficeManagement;

// Registers a new category for custom admin UI applications
[assembly: UICategory(
    codeName: OfficeManagementApplication.CUSTOM_CATEGORY,
    name: "Custom applications",
    icon: Icons.CustomElement,
    order: 400)]

// Registers a new application under the 'Custom applications' category
[assembly: UIApplication(
    identifier: OfficeManagementApplication.IDENTIFIER,
    type: typeof(OfficeManagementApplication),
    slug: "officeManagment",
    name: "Office management",
    category: OfficeManagementApplication.CUSTOM_CATEGORY,
    icon: Icons.Apple,
    templateName: TemplateNames.SECTION_LAYOUT)]

namespace Acme.Web.Admin.UIPages.OfficeManagement
{
    // 'ApplicationPage' base class ensures automatic redirection to the first child page of 'OfficeManagementApplication'
    // The ApplicationPage itself is never actually accessed and only serves as an entry point
    public class OfficeManagementApplication : ApplicationPage
    {
        // Unique identifier of the application
        public const string IDENTIFIER = "Acme.Web.Application.OfficeManagement";

        public const string CUSTOM_CATEGORY = "acme.web.admin.category";
    }
}
```

### Office listing
The listing page displays all existing offices. The page is based on the [listing UI page template](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template) provided by the API. To add the listing page:
  1. Under the _OfficeManagement_ folder, create a new class **OfficeListing.cs** and copy the following code:


C#
**Office listing page - OfficeListing.cs**
Copy
```
using System.Threading.Tasks;

using Kentico.Xperience.Admin.Base;

using AcmeModule;
using Acme.Web.Admin.UIPages.OfficeManagement;

// Registers the page into the system under 'OfficeManagementApplication'
[assembly: UIPage(
    parentType: typeof(OfficeManagementApplication),
    slug: "list",
    uiPageType: typeof(OfficeListing),
    name: "Offices",
    templateName: TemplateNames.LISTING,
    order: UIPageOrder.First)]

namespace Acme.Web.Admin.UIPages.OfficeManagement
{
    public class OfficeListing : ListingPage
    {
        // Sets the object type to list
        // The code generated for the Office object type atomatically contains the referenced property
        protected override string ObjectType => OfficeInfo.OBJECT_TYPE;

        // Configures the listing page
        public override Task ConfigurePage()
        {
            PageConfiguration.HeaderActions.AddLink<OfficeCreate>("New office");

            PageConfiguration.ColumnConfigurations.AddColumn(nameof(OfficeInfo.OfficeDisplayName), "Office name");

            // Generates URLs with appropriate object identifiers
            PageConfiguration.AddEditRowAction<OfficeEdit>();

            return base.ConfigurePage();
        }
    }
}
```

### Create page
The create page enables users to create new office objects. The page is based on the [create UI page template](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template) provided by the API. To add the create page:
Under the _OfficeManagement_ folder, create a new class **OfficeCreate.cs** and copy the following code:
C#
**Office create page - OfficeCreate.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.Base.Forms;

using AcmeModule;
using Acme.Web.Admin.UIPages.OfficeManagement;

// Registers the page into the system under 'OfficeListing'
[assembly: UIPage(
    parentType: typeof(OfficeListing),
    slug: "create",
    uiPageType: typeof(OfficeCreate),
    name: "OfficeCreate",
    templateName: TemplateNames.EDIT,
    order: 200)]

namespace Acme.Web.Admin.UIPages.OfficeManagement
{
    public class OfficeCreate : CreatePage<OfficeInfo, OfficeEditSection>
    {
        public OfficeCreate(IFormComponentMapper mapper, IFormDataBinder binder, IPageLinkGenerator generator)
            : base(mapper, binder, generator)
        {
        }
    }
}
```

**Reserved UI form code names**
The create page automatically uses the UI form with the **create** code name, which you added for the _Office_ class. In other scenarios where you do not follow the _create_ code name convention for the UI form, you would need to override the `ConfigurePage` method and set the code name into the `PageConfiguration.UIFormName` property.
### Edit page
The create page enables users to create new office objects. The page is based on the [edit UI page template](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template) provided by the API. To add the create page:
  1. Create a new folder under _OfficeManagement_ called _OfficeEdit_.
  2. Under the _OfficeEdit_ folder, create a new class **OfficeEditSection.cs** and copy the following code:
C#
**Office edit section**
Copy
```
using Kentico.Xperience.Admin.Base;

using AcmeModule;
using Acme.Web.Admin.UIPages.OfficeManagement;

[assembly: UIPage(
    parentType: typeof(OfficeListing),
    slug: PageParameterConstants.PARAMETERIZED_SLUG,
    uiPageType: typeof(OfficeEditSection),
    name: "Edit section for office objects",
    templateName: TemplateNames.SECTION_LAYOUT,
    order: 300)]

namespace Acme.Web.Admin.UIPages.OfficeManagement
{
    // Provides a URL slug with the identifier of the selected office object
    public class OfficeEditSection : EditSectionPage<OfficeInfo>
    {
    }
}
```

  3. Under the _OfficeEdit_ folder, create a new class **OfficeEdit.cs** and copy the following code:
C#
**Office edit page**
Copy
```
using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.Base.Forms;

using AcmeModule;
using Acme.Web.Admin.UIPages.OfficeManagement;

// Registers the page under the 'edit section' of the office object
// Ensures the identifier of an office object in the URL
[assembly: UIPage(
    parentType: typeof(OfficeEditSection),
    slug: "edit",
    uiPageType: typeof(OfficeEdit),
    name: "Edit an office",
    templateName: TemplateNames.EDIT,
    order: UIPageOrder.First)]

namespace Acme.Web.Admin.UIPages.OfficeManagement
{
    public class OfficeEdit : InfoEditPage<OfficeInfo>
    {
        public OfficeEdit(IFormComponentMapper formComponentMapper, IFormDataBinder formDataBinder)
            : base(formComponentMapper, formDataBinder)
        {
        }

        // Consumes the identifier provided by the parent page
        // Identifies the Office instance to edit
        [PageParameter(typeof(IntPageModelBinder))]
        public override int ObjectId { get; set; }

    }
}
```

**Reserved UI form code names**
The edit page automatically uses the UI form with the **edit** code name, which you added for the _Office_ class. In other scenarios where you do not follow the _edit_ code name convention for the UI form, you would need to override the `ConfigurePage` method and set the code name into the `PageConfiguration.UIFormName` property. 


### Final page hierarchy
The following diagram shows the final UI page hierarchy of the **Office management** application:
[![Office management application UI page hierarchy](docsassets/documentation/example-offices-management-application/OfficeManagementBasicHierarchy.png)](https://docs.kentico.com/docsassets/documentation/example-offices-management-application/OfficeManagementBasicHierarchy.png)
The application, when accessed and populated with sample data, displays the following:
[![Application demonstration](docsassets/documentation/example-offices-management-application/OfficeManagementBasicScreenshot.png)](https://docs.kentico.com/docsassets/documentation/example-offices-management-application/OfficeManagementBasicScreenshot.png)
## Add a child class tracking open job positions
**Prerequisites**
This example extends the Office management application created in the following sections:
  1. [Define the Office object type](documentation/developers-and-admins/customization/object-types/example-offices-management-application#define-the-office-object-type)
  2. [Build a basic editing interface for the Office object type](documentation/developers-and-admins/customization/object-types/example-offices-management-application#build-a-basic-editing-interface-for-the-office-object-type)


In this section, we extend the Office management application with the ability to create and list _job openings_ available for specific offices. Similar to Office objects, job openings are represented by a custom **OfficeJob** object type that has a [parent-child relationship](documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships) with a specific office.
### Create the OfficeJob object type
  1. Edit the **Acme** module in the **Modules** application.
  2. Switch the **Classes** tab and select **New class**.
  3. Fill in the following values: 
    1. **Class name** : Office job opening
    2. **Code name**
       * **Namespace** : Acme
       * **Name** : OfficeJob
  4. **Save** the changes.


The system creates the data class and its corresponding database table: _Acme_OfficeJob_
Continue by modeling the database entity:
  1. Switch to the **Database columns** tab of the **OfficeJob** class (available after the class is created).
  2. Using the [field editor](documentation/developers-and-admins/customization/field-editor), add the following fields: 
     * Field 1: 
       * **Field name** : OfficeJobDisplayName
       * **Data type** : Text
       * **Required** : Yes (checked)
     * Field 2: 
       * **Field name** : OfficeJobOpeningCount
       * **Data type** : Integer number
       * **Required** : Yes (checked)
     * Field 3: 
       * **Field name** : OfficeJobParentID
       * **Data type** : Integer number
       * **Required** : Yes (checked)
  3. **Save** your changes after each field.


### Generate code files for the Office job object type
  1. Shut down the application.
  2. Run the following command from the root directory of your project. When prompted for confirmation, press `Y`:
CMD
Copy
```
dotnet run --no-build -- --kxp-codegen --type "Classes" --include "Acme.OfficeJob"
```

  3. Move the code files generated for the **OfficeJob** object type from the _Classes\Acme\Office_ directory under your project’s root to the custom class library. Alternatively, you can use the `--location` [parameter](documentation/developers-and-admins/api/generate-code-files-for-system-objects) to set a custom path that targets the class library housing the Office management application. Make sure the classes do not remain under your main web project, otherwise you will encounter errors when working with the office job object types.


### Set the OfficeJob object type as a child of the Office object type
  1. Edit **OfficeJobInfo.generated.cs**.
  2. Navigate to the `TYPEINFO` field in the class’s code.
  3. Set the parent-related parameters of the `ObjectTypeInfo` constructor (the last two parameters):
     * parentIdColumn: `"OfficeJobParentID"`
     * parentObjectType: `OfficeInfo.OBJECT_TYPE`
C#
**OfficeJobInfo object type info constructor**
Copy
```
public static ObjectTypeInfo TYPEINFO = new ObjectTypeInfo(
    providerType: typeof(OfficeJobInfoProvider),
    objectType: OBJECT_TYPE,
    objectClassName: "Acme.OfficeJob",
    idColumn: "OfficeJobID",
    timeStampColumn: null,
    guidColumn: null,
    codeNameColumn: null,
    displayNameColumn: "OfficeJobDisplayName",
    binaryColumn: null,
    parentIDColumn: "OfficeJobParentID",
    parentObjectType: OfficeInfo.OBJECT_TYPE)
{
    TouchCacheDependencies = true,
};
```

  4. Save the change and **Rebuild** the solution.
  5. Relaunch the site using `dotnet run`.


The **OfficeJob** object type is now registered as a child of the **Office** object type.
### Define the UI forms for the OfficeJob object type
#### New job opening
  1. Open the **Modules** application, edit the **Acme** module and select the **OfficeJob** class.
  2. Switch to the **UI forms** tab and select **New UI form**.
  3. Enter the following values: 
    1. **UI form name** : New job opening
    2. **Identifiers → Code name** : opening
  4. Select **Save**.


With the UI form created, define its fields:
  1. Switch to the **Fields** tab.
  2. Add the following fields using the field editor: 
     * Field 1: 
       * **Database column** : New field without database column
       * **Field name** : OfficeSelect
       * **Data type:** Text
       * **Field caption:** For office
       * **Form component** : Dropdown selector
     * Field 2: 
       * **Database column** : OfficeJobDisplayName
       * **Field caption:** Job title
       * **Form component** : Text input
     * Field 3: 
       * **Database column** : OfficeJobOpeningCount
       * **Field caption:** Number of openings
       * **Form component** : Number input
  3. **Save** your changes after each field.


#### Office job edit
  1. Open the **Modules** application, edit the **Acme** module and select the **Office job opening** class.
  2. Switch to the **UI forms** tab and select **New UI form**.
  3. Enter the following values: 
    1. **UI form name** : Office job edit
    2. **Identifiers → Code name** : edit
  4. Select **Save**.


With the UI form created, define its fields:
  1. Switch to the **Fields** tab.
  2. Add the following fields using the field editor: 
     * Field 1: 
       * **Database column** : OfficeJobDisplayName
       * **Field caption** : Job title
       * **Form component** : Text input
     * Field 2: 
       * **Database column** : OfficeJobOpeningCount
       * **Field caption** : Number of openings
       * **Form component** : Number input
     * Field 3: 
       * **Database column** : New field without database column
       * **Field name** : OfficeSelect
       * **Data type** : Text
       * **Field caption** : For office
       * **Form component** : Dropdown selector
  3. **Save** your changes after each field.


### Build an editing interface for open positions
The editing interface for the job listing consists of a listing of all open positions and a corresponding editing interface that allows users to create and edit postions.
#### Open position listing
  1. Create a new folder under _UIPages\OfficeManagement_ called _OpenPositions_.
  2. Under the _OpenPositions_ folder, create a new class **OpenPositionListing.cs** and copy the following code:
C#
**Listing of open positions - OpenPositionListing.cs**
Copy
```
using System.Threading.Tasks;

using Kentico.Xperience.Admin.Base;

using AcmeModule;
using Acme.Web.Admin.UIPages.OfficeManagement;

[assembly: UIPage(
    parentType: typeof(OfficeManagementApplication),
    slug: "openpositions",
    uiPageType: typeof(OpenPositionListing),
    name: "Open positions",
    templateName: TemplateNames.LISTING,
    order: 300)]

namespace Acme.Web.Admin.UIPages.OfficeManagement
{
    public class OpenPositionListing : ListingPage
    {
        protected override string ObjectType => OfficeJobInfo.OBJECT_TYPE;

        public override Task ConfigurePage()
        {
            PageConfiguration.HeaderActions.AddLink<OpenPositionCreate>("New open position");

            PageConfiguration.ColumnConfigurations.AddColumn(nameof(OfficeJobInfo.OfficeJobDisplayName), "Position")
                                                  .AddColumn(nameof(OfficeJobInfo.OfficeJobOpeningCount), "Number of slots")
                                                  .AddColumn(nameof(OfficeInfo.OfficeDisplayName), "Office");

            PageConfiguration.TableActions.AddDeleteAction(nameof(Delete));

            PageConfiguration.QueryModifiers
                                .AddModifier(query => query
                                     .Source(source => source.Join<OfficeInfo>(nameof(OfficeJobInfo.OfficeJobParentID), nameof(OfficeInfo.OfficeID)))
                                 );

            PageConfiguration.AddEditRowAction<OpenPositionEdit>();

            return base.ConfigurePage();
        }

        [PageCommand]
        public override Task<ICommandResponse<RowActionResult>> Delete(int id) => base.Delete(id);
    }
}
```



#### Open position create page
The create page enables users to create new job opening objects. The page is based on the [edit UI page template](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template) provided by the API. To add the create page:
  1. Under the _OpenPositions_ folder, create a new class **OpenPositionCreate.cs** and copy the following code:
C#
**Create a new open position - OpenPositionCreate.cs**
Copy
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;

using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.Base.Forms;
using Kentico.Xperience.Admin.Base.FormAnnotations;

using AcmeModule;
using Acme.Web.Admin.UIPages.OfficeManagement;

// Registers the page into the system under 'OpenPositionListing'
[assembly: UIPage(
    parentType: typeof(OpenPositionListing),
    slug: "create",
    uiPageType: typeof(OpenPositionCreate),
    name: "Open position create",
    templateName: TemplateNames.EDIT,
    order: UIPageOrder.First)]

namespace Acme.Web.Admin.UIPages.OfficeManagement
{
    public class OpenPositionCreate : CreatePage<OfficeJobInfo, OpenPositionEdit>
    {
        private readonly IOfficeInfoProvider officeInfoProvider;

        public OpenPositionCreate(IFormComponentMapper formComponentMapper,
                                  IFormDataBinder formDataBinder,
                                  IPageLinkGenerator pageLinkGenerator,
                                  IOfficeInfoProvider officeInfoProvider)
            : base(formComponentMapper, formDataBinder, pageLinkGenerator)
        {
            this.officeInfoProvider = officeInfoProvider;
        }

        public override Task ConfigurePage()
        {
            // Assigns the 'New job opening' UI form to the page
            PageConfiguration.UIFormName = "opening";

            return base.ConfigurePage();
        }

        // Populates the 'OfficeSelect' UI form field of the 'New job opening' UI form with existing offices
        protected override async Task<ICollection<IFormItem>> GetFormItems()
        {
            var items = await base.GetFormItems();

            var officeSelector = items.OfType<IFormComponent>()
                                      .FirstOrDefault(i => i.Name == "OfficeSelect") as DropDownComponent;

            officeSelector.Properties.DataProviderType = typeof(OfficesDropdownOptionsProvider);

            return items;
        }

        protected override Task FinalizeInfoObject(OfficeJobInfo infoObject, IFormFieldValueProvider fieldValueProvider, CancellationToken cancellationToken)
        {
            base.FinalizeInfoObject(infoObject, fieldValueProvider, cancellationToken);

            fieldValueProvider.TryGet<string>("OfficeSelect", out var value);
            infoObject.OfficeJobParentID = Int32.Parse(value);

            return Task.CompletedTask;
        }

        protected override Task<ICommandResponse> GetSubmitSuccessResponse(OfficeJobInfo savedInfoObject, ICollection<IFormItem> items)
        {
            string navigationUrl = pageLinkGenerator.GetPath<OpenPositionListing>();

            return Task.FromResult((ICommandResponse)NavigateTo(navigationUrl)
                          .AddSuccessMessage("Position created."));
        }
    }

    // Provides dropdown options for the UI form selector
    public class OfficesDropdownOptionsProvider : IDropDownOptionsProvider
    {
        private readonly IOfficeInfoProvider officeInfoProvider;

        public OfficesDropdownOptionsProvider(IOfficeInfoProvider officeInfoProvider)
        {
            this.officeInfoProvider = officeInfoProvider;
        }

        public async Task<IEnumerable<DropDownOptionItem>> GetOptionItems()
        {
            return (await officeInfoProvider.Get().GetEnumerableTypedResultAsync())
                                                    .OrderBy(x => x.OfficeDisplayName)
                                                    .Select(x => new DropDownOptionItem()
                                                                {
                                                                    Value = x.OfficeID.ToString(),
                                                                    Text = x.OfficeDisplayName
                                                                }
                                                    );
        }
    }
}
```



#### Open positions edit page
The edit page enables users to edit job opening objects. To add the edit page:
  1. Create a new folder under _OpenPositions_ called _OpenPositionsEdit_.
  2. Under the _OpenPositionsEdit_ folder, create a new class **OpenPositionEditSection.cs** and copy the following code:
C#
**Office edit section**
Copy
```
using Kentico.Xperience.Admin.Base;

using AcmeModule;
using Acme.Web.Admin.UIPages.OfficeManagement;

[assembly: UIPage(
    parentType: typeof(OpenPositionListing),
    slug: PageParameterConstants.PARAMETERIZED_SLUG,
    uiPageType: typeof(OpenPositionEditSection),
    name: "Open position edit section",
    templateName: TemplateNames.SECTION_LAYOUT,
    order: 600)]

namespace Acme.Web.Admin.UIPages.OfficeManagement
{
    public class OpenPositionEditSection : EditSectionPage<OfficeJobInfo>
    {
    }
}
```

  3. Under the _OpenPositionsEdit_ folder, create a new class **OpenPositionEdit.cs** and copy the following code:
C#
**Office edit page**
Copy
```
using System.Threading.Tasks;
using System.Collections.Generic;
using System.Linq;

using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.Base.Forms;

using AcmeModule;
using Acme.Web.Admin.UIPages.OfficeManagement;

[assembly: UIPage(
    parentType: typeof(OpenPositionEditSection),
    slug: "edit",
    uiPageType: typeof(OpenPositionEdit),
    name: "Edit position",
    templateName: TemplateNames.EDIT,
    order: UIPageOrder.First)]

namespace Acme.Web.Admin.UIPages.OfficeManagement
{
    public class OpenPositionEdit : InfoEditPage<OfficeJobInfo>
    {
        private readonly IOfficeInfoProvider officeInfoProvider;

        public OpenPositionEdit(IFormComponentMapper formComponentMapper,
                                IFormDataBinder formDataBinder,
                                IOfficeInfoProvider officeInfoProvider)
            : base(formComponentMapper, formDataBinder)
        {
            this.officeInfoProvider = officeInfoProvider;
        }

        [PageParameter(typeof(IntPageModelBinder), typeof(OpenPositionEditSection))]
        public override int ObjectId { get; set; }

        protected override async Task<ICollection<IFormItem>> GetFormItems()
        {
            var items = await base.GetFormItems();
            await SetOffice(items);

            return items;
        }

        protected override Task FinalizeInfoObject(OfficeJobInfo infoObject, IFormFieldValueProvider fieldValueProvider, CancellationToken cancellationToken)
        {
            base.FinalizeInfoObject(infoObject, fieldValueProvider, cancellationToken);

            fieldValueProvider.TryGet<string>("OfficeSelect", out var value);
            infoObject.OfficeJobParentID = Int32.Parse(value);

            return Task.CompletedTask;
        }

        private async Task SetOffice(ICollection<IFormItem> items)
        {
            var officeSelectField = items.OfType<IFormComponent>()
                                                .FirstOrDefault(i => i.Name == "OfficeSelect") as DropDownComponent;

            // Reuses 'OfficesDropdownOptionsProvider' from the 'OpenPositionCreate' UI page
            officeSelectField.Properties.DataProviderType = typeof(OfficesDropdownOptionsProvider);

            var infoObject = await GetInfoObject();

            officeSelectField.SetValue(infoObject.OfficeJobParentID.ToString());
        }
    }
}
```

**Reserved UI form code names**
The edit page automatically uses the UI form with the **edit** code name, which you added for the _OfficeJob_ class. In other scenarios where you do not follow the _edit_ code name convention for the UI form, you would need to override the `ConfigurePage` method and set the code name into the `PageConfiguration.UIFormName` property. 


This completes the editing interface for the OfficeJob object type.
### Final page hierarchy of the Office management application
The following diagram shows the final UI page hierarchy of the **Office management** application:
[![Office management application page hierarchy](docsassets/documentation/example-offices-management-application/OfficeManagementPositionsHierarchy.png)](https://docs.kentico.com/docsassets/documentation/example-offices-management-application/OfficeManagementPositionsHierarchy.png)
The application, when accessed on the _Open positions_ page and populated with sample data, displays the following:
[![Open positions management interface](docsassets/documentation/example-offices-management-application/OfficeManagementPositionsScreenshot.png)](https://docs.kentico.com/docsassets/documentation/example-offices-management-application/OfficeManagementPositionsScreenshot.png)
## Assign users from the system to offices via a binding object type
**Prerequisites**
This example extends the Office management application created in the following sections:
  1. [Define the Office object type](documentation/developers-and-admins/customization/object-types/example-offices-management-application#define-the-office-object-type)
  2. [Build a basic editing interface for the Office object type](documentation/developers-and-admins/customization/object-types/example-offices-management-application#build-a-basic-editing-interface-for-the-office-object-type)


The following example demonstrates how to create a binding class, including an editing interface. The sample binding class allows the creation of relationships between offices and [users](documentation/developers-and-admins/configuration/users/user-management). Each relationship indicates that a user is an employee of a given office.
### Create and model the binding object type
  1. Edit the **Acme** module in the **Modules** application.
  2. Switch the **Classes** tab and select **New class**.
  3. Fill in the following values: 
    1. **Class name** : Office user binding
    2. **Identifiers → Code name**
       * **Namespace:** Acme
       * **Name:** OfficeUser
  4. **Save** the changes.


The system creates the data class and its corresponding database table: _Acme_OfficeUser_
Continue by modeling the database entity:
  1. Switch to the **Database columns** tab of the **Office user binding** class (available after the class is created).
  2. Using the [field editor](documentation/developers-and-admins/customization/field-editor), add the following fields: 
     * Field 1: 
       * **Field name** : OfficeID
       * **Data type** : Integer number
       * **Required** : yes (checked)
       * **Reference to** : ObjectType.acme_office
       * **Reference type** : Binding
     * Field 2: 
       * **Field name** : AssignedUserID
       * **Data type** : Integer number
       * **Required** : yes (checked)
       * **Reference to** : User
       * **Reference type** : Binding
  3. **Save** your changes after each field.


### Generate code files for the Office-user object type
Generate the code required for the binding class’s API (including the type information):
  1. Shut down the application.
  2. Run the following command from the root directory of your project. When prompted for confirmation, press `Y`:
CMD
Copy
```
dotnet run --no-build -- --kxp-codegen --type "Classes" --include "Acme.OfficeUser"
```

  3. Move the code files generated for the **OfficeUser** object type from the _Classes\Acme\Office_ directory under your project’s root to the custom class library. Alternatively, you can use the `--location` [parameter](documentation/developers-and-admins/api/generate-code-files-for-system-objects) to set a custom path that targets the class library housing the Office management application. Make sure the classes do not remain under your main web project, otherwise you will encounter errors when working with office-user binding object type.


### Set the OfficeUser object type as a binding
  1. Edit **OfficeUserInfo.generated.cs**.
  2. Navigate to the `TYPEINFO` field in the class’s code.
  3. Add the `IsBinding` property to the initializer of the new `ObjectTypeInfo` object and set the value to `true`:
C#
Copy
```
public static ObjectTypeInfo TYPEINFO = new ObjectTypeInfo(
    providerType: typeof(OfficeUserInfoProvider),
    objectType: OBJECT_TYPE,
    objectClassName: "Acme.OfficeUser",
    idColumn: "OfficeUserID",
    timeStampColumn: null,
    guidColumn: null,
    codeNameColumn: null,
    displayNameColumn: null,
    binaryColumn: null,
    parentIDColumn: "OfficeID",
    parentObjectType: "acme.office")
{
    IsBinding = true,
    TouchCacheDependencies = true,
    DependsOn = new List<ObjectDependency>()
    {
        new ObjectDependency("AssignedUserID", "cms.user", ObjectDependencyEnum.Binding),
    },
};
```

  4. Save the change and **Rebuild** the solution.
  5. Relaunch the site using `dotnet run`.


The **OfficeUser** object type is now configured as a binding between the **Office** and **User** object types.
### Add foreign key constraints for the database table
The system does not provide automatic management of foreign keys on the database level. You need to manually set the added foreign key columns as foreign keys in the corresponding database tables. This is not required to ensure the correct functionality of foreign key columns. However, it is recommended to leverage the performance and data consistency advantages of foreign key constraints.
In this example, create foreign keys for the **OfficeID** and **UserID** columns in the **Acme_OfficeUser** table. Add relationships with the corresponding columns in the **Acme_Office** and **CMS_User** tables respectively. You can either use SQL Server Management Studio or execute an SQL script. See [Create Foreign Key Relationships](https://learn.microsoft.com/en-us/sql/relational-databases/tables/create-foreign-key-relationships) for more information.
### Build a user interface for the office-user binding
The following code samples show how to create a new page in the Office management application that allows users to assign workers to offices.
  1. Create a new folder under _OfficeEdit_ called _OfficeRelationships_.
  2. Under the _OfficeRelationships_ folder, create a new class **OfficeWorkers.cs** and copy the following code:
C#
**OfficeWorkers.cs**
Copy
```
using CMS.Membership;

using Kentico.Xperience.Admin.Base;

using AcmeModule;
using Acme.Web.Admin.UIPages.OfficeManagement;

// Registers the page under the 'edit section' of the office object
// Ensures the identifier of an office object in the URL
[assembly: UIPage(
    parentType: typeof(OfficeEditSection),
    slug: "workerslisting",
    uiPageType: typeof(OfficeWorkers),
    name: "Assigned workers",
    templateName: TemplateNames.BINDING,
    order: 200)]

namespace Acme.Web.Admin.UIPages.OfficeManagement
{
    // Lists all users assigned to the curently edited office and allows users to be added or removed
    public class OfficeWorkers : InfoBindingPage<OfficeUserInfo, UserInfo>
    {
        // Configures the ID of the edited binding object
        [PageParameter(typeof(IntPageModelBinder))]
        public override int EditedObjectId { get; set; }

        // Returns the name of the property that is used as the source in the binding object type
        protected override string SourceBindingColumn => nameof(OfficeUserInfo.OfficeID);

        // Returns the name of the property that is used as the target in the binding object type
        protected override string TargetBindingColumn => nameof(OfficeUserInfo.AssignedUserID);

        // Configures the listing of workers assigned to the office
        public override async Task ConfigurePage()
        {
            // Configures captions of pages and buttons
            PageConfiguration.ExistingBindingsListing.Caption = "Users assigned as workers";
            PageConfiguration.BindingSidePanelListing.Caption = "Add users as workers";
            PageConfiguration.AddBindingButtonText = "Add workers";
            PageConfiguration.SaveBindingsButtonText = "Confirm";

            // Adds a callout with information on top of the page
            PageConfiguration.ExistingBindingsListing.Callouts.Add(
                new CalloutConfiguration
                {
                    Type = CalloutType.QuickTip,
                    ContentAsHtml = true,
                    Headline = "This is a binding page",
                    Content = "Removing items from this page will not delete any users from the database."
                }
            );

            // Configures columns displayed in the listing of existing bindings
            PageConfiguration.ExistingBindingsListing.ColumnConfigurations
                .AddColumn(nameof(UserInfo.UserName), "User name", searchable: true, defaultSortDirection: SortTypeEnum.Asc);

            PageConfiguration.ExistingBindingsListing.QueryModifiers.AddModifier((objectQuery) =>
            {
                // Add a query modifier if necessary

                return objectQuery;
            });

            // Configures columns displayed in the listing of available object
            PageConfiguration.BindingSidePanelListing.ColumnConfigurations
                .AddColumn(nameof(UserInfo.UserName), "User name", searchable: true, defaultSortDirection: SortTypeEnum.Asc);

            PageConfiguration.BindingSidePanelListing.QueryModifiers.AddModifier((objectQuery) =>
            {
                // Add a query modifier if necessary

                return objectQuery;
            });

            await base.ConfigurePage();
        }
    }
}
```



### Final page hierarchy of the Office management application
The following diagram shows the final UI page hierarchy of the **Office management** application:
[![Office management final page hierarchy](docsassets/documentation/example-offices-management-application/OfficeManagementBindingHierarchy.png)](https://docs.kentico.com/docsassets/documentation/example-offices-management-application/OfficeManagementBindingHierarchy.png)
The application, when accessed on the _Assigned workers_ page and populated with sample data, displays the following:
[![Assigned workers page](docsassets/documentation/example-offices-management-application/OfficeManagementBindingScreenshot.png)](https://docs.kentico.com/docsassets/documentation/example-offices-management-application/OfficeManagementBindingScreenshot.png)