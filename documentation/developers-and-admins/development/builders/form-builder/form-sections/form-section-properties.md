---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/form-builder/form-sections/form-section-properties
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * [Form Builder](/documentation/developers-and-admins/development/builders/form-builder)
  * [Form sections](/documentation/developers-and-admins/development/builders/form-builder/form-sections)
  * Form section properties 


# Form section properties
When developing [form sections](/documentation/developers-and-admins/development/builders/form-builder/form-sections), you can define properties that allow editors to adjust the content or behavior of the sections directly in the Form Builder interface. Users interact with the section properties through section configuration dialogs.
Use the following process to develop properties for a section:
  1. [Create a model class that defines the section properties](#create-property-models)
  2. [Define the configuration dialog to allow form editors to modify the properties](#define-the-configuration-dialog)
  3. [Handle the properties in the section’s code](#handle-properties-in-section-code)
  4. [Specify the property model class in the section’s registration attribute](#register-sections-with-properties)


## Create property models
The properties of a form section must be defined within a model class that implements the `IFormSectionProperties` interface (available in the `Kentico.Forms.Web.Mvc` namespace).
Specify each section property by creating a corresponding property in the model class. You can also set default values for the properties.
C#
**Example**
Copy
```
public class TitledSectionProperties : IFormSectionProperties
{
    // Defines a title property and sets its default value
    public string Title { get; set; } = "General";
}
```

**Register sections with properties**
When [registering](/documentation/developers-and-admins/development/builders/form-builder/form-sections) form sections with properties, you additionally need to set the `PropertiesType` property of the `RegisterFormSection` attribute to the `System.Type` of the section’s property model class.
## Define the configuration dialog
The configuration dialog provides a simple way for form editors to set the values of section properties. In the property model class, you need to define [editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) for section properties which you want to make editable in the configuration dialog. 
  1. Edit the section’s property model class in your live site project.
  2. Define the visual interface of the configuration dialog:
     * Decorate the appropriate properties using the [editing component’s attribute](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components).
     * The attribute assigns and configures an [admin UI form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components), which is used as the input element for the given property in the configuration dialog. You can use the system’s [default UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) or develop [custom UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) depending on the type of input required.


C#
**Example - Setting an editing component**
Copy
```
using Kentico.Xperience.Admin.Base.FormAnnotations;

// Defines a title property whose value is edited via a text input in the section configuration dialog
[TextInputComponent(Label = "Title")]
public string Title { get; set; } = "General";
```

Users can now select the **Configure** ([working with sections in the Form Builder interface](/documentation/business-users/digital-marketing/forms). This opens the section properties dialog, and the configured property values affect the section’s content and functionality.
## Handle properties in section code
In order for properties to have an effect on a section’s content or functionality, you need to retrieve the property values and adjust the section’s output code or logic correspondingly. The required steps depend on the development approach used to create the section (see [Form sections](/documentation/developers-and-admins/development/builders/form-builder/form-sections) for more information).
### Basic sections
For basic sections implemented only as a partial view (without a view component) and a properties class, handle the property values directly in the section’s view.
The view must use the generic `FormSectionViewModel<TPropertyModel>` class as its model, with the appropriate property model class as the generic type parameter. The system ensures that the property values configured for the currently processed section are passed to the view. Retrieve the property values from the model’s `Properties` member, which returns an object of the specified property model class.
cshtml
**Example**
Copy
```
@using Kentico.Forms.Web.Mvc
@using Kentico.Web.Mvc

@model FormSectionViewModel<TitledSectionProperties>

@if (!string.IsNullOrEmpty(Model?.Properties?.Title))
{
    <h2>@Model.Properties.Title</h2>
}

<div>
    @await Html.Kentico().FormZoneAsync()
</div>
```

### Sections based on a view component
To work with properties in your form section view components, the component’s  _Invoke_ or  _InvokeAsync_ method signature must declare the `FormSectionViewModel<TPropertiesType>` parameter, with the appropriate property model class as the generic type.
The section’s properties are accessible via the `Properties` member of the `FormSectionViewModel` parameter, which returns an object of the specified property model class. The object’s property values are loaded from the current configuration of the processed section.
You can then adjust your code based on the values of individual section properties, or pass them to the section’s view using an appropriate view model.
C#
**Example**
Copy
```
using Microsoft.AspNetCore.Mvc;
using System.Threading.Tasks;

using Kentico.Forms.Web.Mvc;

public class TitledSectionViewComponent : ViewComponent
{
    public Task<IViewComponentResult> Invoke(FormSectionViewModel<TitledSectionProperties> sectionProperties)
    {
        // Prepares a view model object containing the section's Title property
        var viewModel = new TitledSectionViewModel
        {
            Title = sectionProperties.Properties.Title
        };

        return View("~/Components/FormSections/TitledSection/_TitledSection.cshtml", viewModel);
    }
}
```

## Register sections with properties
When [registering](/documentation/developers-and-admins/development/builders/form-builder/form-sections) form sections with properties, you additionally need to set the `PropertiesType` property of the `RegisterFormSection` attribute to the `System.Type` of the section’s property model class.
C#
**Example**
Copy
```
using Kentico.Forms.Web.Mvc;

[assembly: RegisterFormSection("MySections.TitledSection",
                               "Titled section",
                               customViewName: "~/Components/FormSections/TitledSection/_TitledSection.cshtml",
                               PropertiesType = typeof(TitledSectionProperties),
                               Description = "Organizes fields into a section with a configurable title.",
                               IconClass = "icon-square")]
```

C#
**Example - Section with a custom view component**
Copy
```
using Kentico.Forms.Web.Mvc;

[assembly: RegisterFormSection("MySections.TitledSection",
                                typeof(TitledSectionViewComponent),
                                "Titled section",
                                PropertiesType = typeof(TitledSectionProperties),
                                Description = "Organizes fields into a section with a configurable title.",
                                IconClass = "icon-square")]
```

![]()
[]()[]()
