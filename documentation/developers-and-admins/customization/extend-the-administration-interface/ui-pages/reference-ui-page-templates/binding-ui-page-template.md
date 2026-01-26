---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/binding-ui-page-template
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Extend the administration interface](/documentation/developers-and-admins/customization/extend-the-administration-interface)
  * [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages)
  * [Reference - UI page templates](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates)
  * Binding UI page template 


# Binding UI page template
Binding UI pages allow users to manage bindings, which represent many-to-many [relationships](/documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships) between [object types](/documentation/developers-and-admins/customization/object-types) (_*Info_ class). For example, a typical example of a binding relationship is between roles and users – a user can be assigned to many roles, and a role can include many users.
[![Binding UI page template in the Channel management application](/docsassets/documentation/binding-ui-page-template/BindingUIPageExample.png)](/docsassets/documentation/binding-ui-page-template/BindingUIPageExample.png)
Like [edit UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template), binding pages are parameterized by the identifier of an edited object (one of the object types belonging to the relationship modeled by the binding). To get the currently edited object, binding pages must be registered under a parent UI page with the `EditSectionPage` base class. This base class, in conjunction with URL parameterization (described in the _Page URLs and routing_ section on [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages)), is used to ensure correct URLs for binding pages:
Binding pages are similar to listing pages, but offer some additional features that simplify the implementation of bindings. Binding pages consist of two [listing UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template):
  * `ExistingBindingsListing` – the main listing that displays items that have a binding with the currently edited object.
  * `BindingSidePanelListing` – an editing side panel with items that can be selected and bound to the currently edited object. This UI page is displayed when the “Add bindings” button is selected.


All binding pages must inherit from the `InfoBindingPage<TBindingObjectInfo, TTargetObjectInfo>` base class, where `TBindingObjectInfo` is the binding object type and `TTargetObjectInfo` is the object type of the selected object.
Additionally, you need to override the following abstract properties:
  * `EditedObjectId` – a property that holds the ID of the edited object. Needs to be decorated with the `PageParameter` attribute to propagate an object ID from the request path to the configuration of the page.
  * `SourceBindingColumn` – returns the name of the [field](/documentation/developers-and-admins/customization/field-editor) (database column) used as the source in the binding object type.
  * `TargetBindingColumn` – returns the name of the field used as the target in the binding object type.


C#
**Example - basic binding page code**
Copy
```
using CMS.Membership;

using Kentico.Xperience.Admin.Base;

public class OfficeWorkers : InfoBindingPage<OfficeUserInfo, UserInfo>
{

     // ID of the edited object
    [PageParameter(typeof(IntPageModelBinder))]
    public override int EditedObjectId { get; set; }

    // Name of the source column
    protected override string SourceBindingColumn => nameof(OfficeUserInfo.OfficeID);

    // Name of the target column
    protected override string TargetBindingColumn => nameof(OfficeUserInfo.AssignedUserID);

    // UI page configuration
    public override async Task ConfigurePage()
    {
        // ...
    }
}
```

**Tip** : To see a full-code example usage of the binding UI page and the context of the classes used on this page, visit [Example - Offices management application](/documentation/developers-and-admins/customization/object-types/example-offices-management-application).
## Configure the listing
The main listing page displays items that are already selected in a grid-like structure, one item per line, and one [field](/documentation/developers-and-admins/customization/field-editor) (database column) per column.
Specify the displayed item properties within an override of the `ConfigurePage` method.
  * Call the `AddColumn` method on `PageConfiguration.ExistingBindingsListing.ColumnConfigurations` and provide the name of the database column to be displayed.
  * Specify the text of the “Add bindings” button by modifying the `PageConfiguration.AddBindingButtonText` property.
  * (_Optional_) Call the `AddModifier` method on `PageConfiguration.ExistingBindingsListing.QueryModifiers` to modify the query used to retrieve displayed items.
  * (_Optional_) Specify a heading of the page by modifying the `PageConfiguration.ExistingBindingsListing.Caption` property.


C#
**Example - Existing bindings listing configuration**
Copy
```
using CMS.Membership;

using Kentico.Xperience.Admin.Base;

// ...

public override async Task ConfigurePage()
{
    // Columns
    PageConfiguration.ExistingBindingsListing.ColumnConfigurations
        .AddColumn(nameof(UserInfo.UserName), "User name", searchable: true, defaultSortDirection: SortTypeEnum.Asc);

    // Query modifier
    PageConfiguration.ExistingBindingsListing.QueryModifiers.AddModifier((objectQuery) =>
    {
         // Only displays user names starting with 'a'
        objectQuery.WhereLike(nameof(UserInfo.UserName), "a%");

        return objectQuery;
    });

    // Sets the caption for the 'Add items' button
    PageConfiguration.AddBindingButtonText = "Add workers";

    // Sets the heading of the page
    PageConfiguration.ExistingBindingsListing.Caption = "Add users as workers";
}
```

`AddColumn` method:
  * **Caption** – sets a custom caption for the column heading. Defaults to the column name if not set.
  * **Sortable** – indicates whether the column can be used to sort the listing.
  * **DefaultSortDirection** – a `SortTypeEnum` value that determines the default order for properties in the column.
  * **Formatter** – sets the [formatter](https://docs.microsoft.com/en-us/dotnet/api/system.string.format) to use for the column values.
C#
**Example**
Copy
```
.AddColumn(nameof(UserInfo.UserCreated), formatter: (value, _) => String.Format("{0:MMMM dd, yyyy}", Convert.ToDateTime(value)))
```

  * **Visible** – default visibility state of the column. For hidden columns, the data is obtained from the server but hidden.
  * **Searchable** – indicates whether column values are searchable via a search box above the listing. If no columns are set as searchable, the search dialog is hidden.
  * **Localizable** – persisted values have localization macros resolved.


## Configure the editing side panel
The binding side panel listing displays selectable items of the object type that is the target of the binding. The items are displayed in a grid-like structure, one item per line, and one [field](/documentation/developers-and-admins/customization/field-editor) (database column) per column.
Specify the displayed item properties within an override of the `ConfigurePage` method.
  * Call the `AddColumn` method on `PageConfiguration.BindingSidePanelListing.ColumnConfigurations` and provide the name of the database column to be displayed. 
    * You can further configure each column by supplying [optional parameters](#BindingUIpagetemplate-addcolumn) to the `AddColumn` method.
  * Specify the text of the “Save bindings” button by modifying the `PageConfiguration.SaveBindingsButtonText` property.
  * (_Optional_) Call the `AddModifier` method on `PageConfiguration.BindingSidePanelListing.QueryModifiers` to modify the query used to retrieve displayed items.
  * (_Optional_) Specify a heading of the side panel by modifying the `PageConfiguration.BindingSidePanelListing.Caption` property.


C#
**Example - Editing side panel configuration**
Copy
```
using CMS.Membership;

using Kentico.Xperience.Admin.Base;

// ...

public override async Task ConfigurePage()
{
    // Columns
    PageConfiguration.BindingSidePanelListing.ColumnConfigurations
        .AddColumn(
            nameof(UserInfo.UserName),
            "User name",
            searchable: true,
            defaultSortDirection: SortTypeEnum.Asc);

    // Query modifier
    PageConfiguration.BindingSidePanelListing.QueryModifiers.AddModifier((objectQuery) =>
    {
        // Only displays user names starting with 'a'
        objectQuery.WhereLike(nameof(UserInfo.UserName), "a%");

        return objectQuery;
    });

    // Sets the caption for the side panel 'Add items' button
    PageConfiguration.SaveBindingsButtonText = "Add workers";

    // Sets the heading of the side panel
    PageConfiguration.BindingSidePanelListing.Caption = "Add users as workers";
}
```

## Reference – PageConfiguration property
The following table provides a reference of all template configuration options available via the `PageConfiguration` property.
Property |  Type |  Description  
---|---|---  
ExistingBindingsListing |  TListingConfiguration |  Configuration of the listing of selected items. For configuration options, see [Listing UI page template](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template). Adding listing filters via the `FilterFormModel` configuration option and mass actions via the `MassActions` configuration option is **not** supported.  
BindingSidePanelListing |  TListingConfiguration |  Configuration of the editing side panel. For configuration options, see [Listing UI page template](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template). Adding listing filters via the `FilterFormModel` configuration option and mass actions via the `MassActions` configuration option is **not** supported.  
AddBindingButtonText |  string |  Caption of the “Add bindings” button in the existing bindings listing.  
SaveBindingsButtonText |  string |  Caption of the “Save bindings” button in the editing side panel.  
## Register binding pages
Binding pages need to specify the `TemplateNames.BINDING` client template during [registration](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages).
C#
**Example - binding page registration**
Copy
```
[assembly: UIPage(
    parentType: typeof(OfficeEditSection),
    slug: "workerslisting",
    uiPageType: typeof(OfficeWorkers),
    name: "Assigned workers",
    templateName: TemplateNames.BINDING,
    order: 200)]
```

![]()
[]()[]()
