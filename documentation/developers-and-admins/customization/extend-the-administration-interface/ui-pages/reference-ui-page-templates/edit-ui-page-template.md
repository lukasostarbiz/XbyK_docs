---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Extend the administration interface](/documentation/developers-and-admins/customization/extend-the-administration-interface)
  * [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages)
  * [Reference - UI page templates](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates)
  * Edit UI page template 


# Edit UI page template
Edit pages generate forms that enable users to edit [object types](/documentation/developers-and-admins/customization/object-types) (_*Info_ objects).
[![Editing user details in the Users application](/docsassets/documentation/edit-ui-page-template/UserEdit.png)](/docsassets/documentation/edit-ui-page-template/UserEdit.png)
All edit pages inherit from the `InfoEditPage<TInfo>` base class, where `TInfo` is substituted for the **Info** class of the object type the page is used to edit.
C#
**Example - edit page implementation**
Copy
```
using CMS.Membership;

using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.Base.Forms;
using Kentico.Xperience.Admin.Base.UIPages;

// Registers the UI page
[assembly: UIPage(
    parentType: typeof(UserEditSection),
    slug: "edit",
    uiPageType: typeof(UserEdit),
    name: "Edit user",
    templateName: TemplateNames.EDIT,
    order: 1)]

public class UserEdit : InfoEditPage<UserInfo>
{
    // Property that holds the ID of the edited user.
    // Needs to be decorated with the PageParameter attribute to propagate
    // user ID from the request path to the configuration of the page.
    [PageParameter(typeof(IntPageModelBinder))]
    public override int ObjectId { get; set; }

    // Mandatory constructor providing required services via dependency injection
    public UserEdit(IFormComponentMapper formComponentMapper, IFormDataBinder formDataBinder)
             : base(formComponentMapper, formDataBinder)
    {
    }
}
```

From the code above, you can see that edit pages are parameterized by the identifier of the edited object. To get this information into the URL structure of the page, the system provides the `EditSectionPage` base class. The base class, in conjunction with URL parameterization (described in the _Page URLs and routing_ section on [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages)), is used to ensure correct URLs for edit pages:
C#
Copy
```
using CMS.Membership;

using Kentico.Xperience.Admin.Base;

// The 'PARAMETERIZED_SLUG' constant used in the registration notifies the system that the URL slug contributed by this page is parameterized
// Edit pages for objects of this type must be registered as children of this page
// The 'AddEditRowAction' extension method provided by the listing template ensures URLs for each row are correctly generated with corresponding object IDs
[assembly: UIPage(
    parentType: typeof(UserObjectListing),
    slug: PageParameterConstants.PARAMETERIZED_SLUG,
    uiPageType: typeof(UserEditSection),
    name: "Edit",
    templateName: TemplateNames.SECTION_LAYOUT,
    order: 0)]

// Edit page for user objects
public class UserEditSection : EditSectionPage
{
}
```

This results in the following URL structure:
`~/<ID_PARAMETERIZED_SLUG>/<EDIT_PAGE_SLUG>`
where _ID_PARAMETERIZED_SLUG_ contains the identifier of the edited object. The resulting URLs look like, e.g., the following: _~/57/edit_
## Access the currently edited info object
You can retrieve the currently edited object (identified by the ID slug in the page URL) via the `GetInfoObject` method.
## Assign editing forms
To specify which [UI form](/documentation/developers-and-admins/customization/object-types) the edit page displays, use the `UIFormName` property and assign the **Code name** of the desired UI form:
**Reserved UI form code names**
If the selected object type contains a UI form with the **edit** code name, that form gets used automatically without needing to be set via `UIFormName`. Other forms need to be set directly.
C#
Copy
```
public override Task ConfigurePage()
{
    // Assigns the 'officeedit' UI form to the page. The assignment is case-insensitive.
    PageConfiguration.UIFormName = "officeedit";

    return base.ConfigurePage();
}
```

## Configure the submit action
You can configure the submit action via `SubmitConfiguration`.
C#
**Configure the submit action**
Copy
```
public override async Task ConfigurePage()
{
    ...

    // Sets the submit action tooltip
    PageConfiguration.SubmitConfiguration.TooltipText = "Select me to save";
    // Determines if the submit action is visible
    PageConfiguration.SubmitConfiguration.Visible = false;
    // Sets the submit button label
    PageConfiguration.SubmitConfiguration.Label = "Save";
}
```

## Raise a confirmation prompt on submit
If `ConfirmationConfiguration` is provided within `SubmitConfiguration`, users are prompted to confirm their action when saving changes. 
C#
**Enable confirmation dialog display**
Copy
```
public override async Task ConfigurePage()
{
    ...

    // If set, the page displays a confirmation dialog when saving changes
    PageConfiguration.SubmitConfiguration.ConfirmationConfiguration = new ConfirmationConfiguration()
    {
        Title = "Dialog caption",
        Detail = "Dialog text",
        Button = "Button label",
        // Optionally renders a form within the dialog
        FormModel = typeof(ConfirmationDialogModel)
    };
}
```

Additionally, the dialog can also display an arbitrary form. Typically, you would want to offer a multiple-choice selector that allows you to further control how the back end processes the submitted data.
The form must be defined within a dedicated model class and annotated with [Editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components). [Validation rules](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) are also supported.
C#
**Add forms to configuration dialogs**
Copy
```
using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.Base.FormAnnotations;

public class ConfirmationDialogFormModel
{
    [RequiredValidationRule]
    [RadioGroupComponent(Inline = false, Order = 1,
         Options = $"submit;Submit\r\nsubmitandlog;Submit and log\r\n")]
    public string ChooseOne { get; set; } = "option1";
}
```

To access the data during form submit, use `GetValidatedConfirmationModel<T>`. The method can only be called after the form model has been received from the client. Suitable places are the `FinalizeInfoObject`, and `SetFormData` methods (see [Lifecycle of edit and create UI pages](#lifecycle-of-edit-and-create-ui-pages)). 
C#
**Control back-end flow based on confirmation form data**
Copy
```
protected override async Task SetFormData(UserInfo infoObject, IFormFieldValueProvider fieldValueProvider)
{
    // The system binds the form from the confirmation dialog to the model <T>
    var confirmationDialogFormData = GetValidatedConfirmationModel<ConfirmationDialogFormModel>();

    // Decide the evaluation flow based on the submitted data
    if (string.Equals(confirmationDialogFormData.ChooseOne, "submitandlog", StringComparison.OrdinalIgnoreCase))
    {
        // Logs the submitted data
        // logger is an instance of ILogger<T> injected using dependency injection
        logger.LogInformation(new EventId(0, "SUBMIT"), $"Submitted data {confirmationDialogFormData}");
    }

    await base.SetFormData(infoObject, fieldValueProvider);
}
```

## Use edit pages to create new objects
You can use the edit page template to create new Xperience objects. Such pages must inherit from the `CreatePage<TInfo, TRedirectPage>` base class, where:
  * **TInfo** – the ***Info** object that represents the entity in the system (e.g., existing system object types such as `UserInfo`, or custom object types).
  * **TRedirectPage** – the page to which users get redirected after creating the object. 
    * By default, the system automatically appends the created object’s identifier to the redirect URL. As a result, you can only redirect to other edit pages that work with the created object (expect its identifier in their URL structure).
    * You can modify this behavior by overriding the `GetSubmitSuccessResponse` method. See [Change redirection behavior](#change-redirection-behavior).


C#
Copy
```
// UI page used to create user objects. After a user is created, redirects to the user edit page.
public class UserCreatePage : CreatePage<UserInfo, UserEdit>
{
    // Mandatory constructor providing required services via dependency injection
    public UserCreatePage(IFormComponentMapper formComponentMapper, IFormDataBinder formDataBinder, IPageLinkGenerator pageLinkGenerator)
            : base(formComponentMapper, formDataBinder, pageLinkGenerator)
    {
    }
}
```

### Assign editing forms to object create pages
To specify which form the create page displays, use the `UIFormName` property and assign the **Code name** of the desired UI form:
**Reserved UI form code names**
If the object type contains a UI form with the **create** code name, that form gets used automatically without needing to be set via `UIFormName`. Other forms need to be set directly.
C#
Copy
```
public override Task ConfigurePage()
{
    // Assigns the 'officecreate' UI form to the page. The assignment is case-insensitive.
    PageConfiguration.UIFormName = "officecreate";

    return base.ConfigurePage();
}
```

### Change redirection behavior
By default, the system automatically appends the created object’s identifier to the redirect URL. As a result, you can only redirect to other edit pages that work with the created object (expect its identifier in their URL structure). You can modify this behavior by overriding the `GetSubmitSuccessResponse` method. To generate URLs to other pages in the administration, use the `IPageLinkGenerator` service. See [Page URL generation](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages#generate-urls-to-other-administration-pages).
C#
**Change create page redirection behavior**
Copy
```
// Overrides the default redirection behavior with custom logic
protected override Task<ICommandResponse> GetSubmitSuccessResponse(OfficeUserInfo savedInfoObject, ICollection<IFormItem> items)
{
    string navigationUrl = pageLinkGenerator.GetPath<OfficeWorkers>(
        new PageParameterValues
            {
                { typeof(OfficeEditSection), ParentId }
            });

    return Task.FromResult((ICommandResponse)NavigateTo(navigationUrl));
}
```

If the object create page is located in a page structure with multiple parameterized URL slugs, you need to provide all but the ID of the currently created object via `AdditionalLinkParameters`. Add all parameters in the order they appear in the resulting URL.
C#
**Specify additional URL parameters**
Copy
```
// Gets the identifier of the parent from the URL (dictated by the UI page hierarchy)
[PageParameter(typeof(IntPageModelBinder), typeof(OfficeEditSection))]
public int ParentId { get; set; }

...

public override Task ConfigurePage()
{
    // Full URL when redirected: ~/admin/officeManagement/list/<parentID>/officeopenpositions/<createdObjectID>/edit
    AdditionalLinkParameters.Add(
        new PageParameterValues
            {
                { typeof(OfficeEditSection), ParentId }
            });

    return base.ConfigurePage();
}
```

## Reference - PageConfiguration
The following table provides a reference of all template configuration options available via the `PageConfiguration` property.
Property |  Type |  Description  
---|---|---  
BackLink |  string |  If set and the page is not displayed in a dialog, renders a back arrow above the form that redirects users to the specified page. Use `IPageLinkGenerator` to [generate links](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages#generate-urls-to-other-administration-pages) to other administration pages.  
ErrorMessage |  string |  If set, displays the specified error message instead of the editing form.  
Components |  ICollection<IFormComponentClientProperties> |  Contains the collection of [form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) properties required to instantiate the editing form on the client.  
SubmitConfiguration |  SubmitConfiguration |  Configures the submit action. See [Configure the submit action](#configure-the-submit-action) and [Raise a confirmation prompt on submit](#raise-a-confirmation-prompt-on-submit).  
EditMode |  FormEditMode |  Indicates whether the form can be edited and submitted:
  * `FormEditMode.Disabled` value disables submission
  * `FormEditMode.Default` enables submission
  * `FormEditMode.ReadOnly` disables submission, but the contents can be read easily

If some of the [form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) on the page don’t implement the ReadOnly state, they will be displayed in the Disabled state. When the `EditMode` value set for a [form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) differs from the one set in `PageConfiguration`, the component is rendered according to the more restrictive of the two, following this order of precedence:
  1. `FormEditMode.Disabled`
  2. `FormEditMode.ReadOnly`
  3. `FormEditMode.Default`

  
Headline |  string |  The page’s title.  
Callouts |  ICollection<CalloutConfiguration> |  Configures an optional message box element that can be displayed above the listing. See [Add callouts](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template#add-callouts) under the listing template section.  
## Register edit pages
Edit pages need to specify the `TemplateNames.EDIT` client template during [registration](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages).
C#
**Example edit page registration**
Copy
```
using Kentico.Xperience.Admin.Base;

[assembly: UIPage(
    parentType: typeof(UserEditSection),
    slug: "edit",
    uiPageType: typeof(UserObjectEdit),
    name: "Edit users",
    templateName: TemplateNames.EDIT,
    order: 0)]
```

## Client template – EDIT
Edit pages must use the `EDIT` client template whose properties are represented by `EditTemplateClientProperties` (_Kentico.Xperience.Admin.Base_) on the back end.
The template supports the following page commands:
  * **Change** – handles the form change command. The command is invoked whenever [visibility conditions](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions) or field value dependencies of the form need to be reevaluated and the form re-rendered (to display or hide form fields). The arguments submitted by the command to the server are represented by `FormChangeCommandArguments`. The response expected by the client is represented by `FormChangeResult`.
  * **Submit** – executed when the corresponding form is submitted from the client. The arguments the command sends to the server are represented by `FormSubmissionCommandArguments`. The response expected by the client is represented by `FormSubmissionResult`.


## Lifecycle of edit and create UI pages
The following diagram illustrates the lifecycle of edit and create pages, and their available customization points. Each step with a red border indicates a virtual member that can be overridden to customize the page’s behavior. 
[![Edit and create UI page lifecycle](/docsassets/documentation/edit-ui-page-template/EditPageLifecycle.png)](/docsassets/documentation/edit-ui-page-template/EditPageLifecycle.png)
### Configure page
The place to configure the behavior of the edit/create page. See the other topics on this page for details.
### GetFormItems
Provides a place to modify the initial configuration of individual form components that comprise the editing form displayed by the page before they get sent to the client. The default configuration is taken from the [editing form](/documentation/developers-and-admins/customization/object-types#define-editing-forms) assigned to the page.
### ConfigureTemplateProperties
Provides a place to modify page properties before they get sent to the client.
### FinalizeInfoObject
Called immediately after the submitted data is bound to the _Info_ object and before validation runs. Can be used to populate fields intentionally not editable as part of the form. For example, to configure [relationships](/documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships) between objects.
### SetFormData
Called when saving the edited _Info_ object to the database.
### GetSubmitSuccessResponse
Controls the page response upon a successful submit action. By default, edit pages display a “saved” prompt and create pages [redirect to the parent](#change-redirection-behavior).
![]()
[]()[]()
