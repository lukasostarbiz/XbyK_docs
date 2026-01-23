---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/form-builder/form-sections
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * [Form Builder](/documentation/developers-and-admins/development/builders/form-builder)
  * Form sections 


# Form sections
The layout of forms composed via the Xperience [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) is based on elements called **sections**. Each form section defines a block of HTML code containing one or more zones. These zones can then hold any number of form fields (based on [form components](/documentation/developers-and-admins/development/builders/form-builder/form-components)).
When [creating forms in the Form Builder interface](/documentation/business-users/digital-marketing/forms) of the _Forms_ application, editors first add or adjust the sections, and then add individual fields into the resulting zones.
The system provides a **Default** form section that organizes fields in a basic single-column layout (one zone). The default section is automatically added to new forms, and in cases where an editor removes the last section from a form. If you wish to use more advanced layouts for fields in your forms, you need to develop and register your own form sections.
**Form submit button**
The submit button of a form is rendered after the last section (when [displaying forms](/documentation/business-users/digital-marketing/forms/display-forms-on-pages) on the website using the _Form_ widget). If you need to adjust the position of the submit button, use appropriate CSS styling in your site’s stylesheet.
## Develop form sections
Form sections are implemented as either:
  * a partial view that contains form zones  
– OR –
  * an ASP.NET Core [view component](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/view-components). Recommended for more complex sections that require non-trivial interactions with the application’s business layer.


**Note** : Form sections are designed to be used in the global scope and their code files must be placed in the application root of your Core project (not in an [Area](https://docs.microsoft.com/en-us/aspnet/core/mvc/controllers/areas)). Creating sections in Areas may lead to unexpected behavior.
In both cases, you can develop sections with properties, which allow editors to adjust the section content or behavior directly in the Form Builder interface. For sections with configurable properties, you need to create an additional model class that represents the section properties. For information about this more advanced scenario, see [Form section properties](/documentation/developers-and-admins/development/builders/form-builder/form-sections/form-section-properties).
### Implement form section views
Create partial views with the required formatting. Use the `HtmlHelper.Kentico().FormZoneAsync()` extension method to add zones where form fields can be placed. Alternatively, you can use the equivalent `<form-zone />` [Tag Helper](/documentation/developers-and-admins/development/reference-tag-helpers) element.
We recommend placing form section view files into your project’s _~/Components/FormSections_ folder. Add a subfolder for specific sections that consist of multiple files.
cshtml
**Example - form section view**
Copy
```
@using Kentico.Web.Mvc
@using Kentico.Forms.Web.Mvc

<div>
    <div style="float:left; width:50%;">
        @await Html.Kentico().FormZoneAsync()
    </div>
    <div style="float:left; width:50%;">
        @await Html.Kentico().FormZoneAsync()
    </div>
    <div style="clear:both;" />
</div>
```

When a user [changes the section type](/documentation/business-users/digital-marketing/forms) in the Form Builder interface, components (form fields) are moved to the new section’s zones automatically based on the order in which the zones are specified in the section code. This may require additional work from editors to get the desired form layout. To ensure that form components are moved to correct zones when the section type is changed:
  1. Add an identifier to each zone.
  2. Use the same identifier for corresponding zones within all of your form sections.


When a section type is changed, the system moves form components to matching named zones.
[![Transfer of widgets between section types](/docsassets/documentation/form-sections/named-zones.png)](/docsassets/documentation/form-sections/named-zones.png)
cshtml
**Example - Named form zone**
Copy
```
@await Html.Kentico().FormZoneAsync("main-zone")
```

### Implement form section view components
The basic implementation of a section consists of only a partial view (and possibly a [properties](/documentation/developers-and-admins/development/builders/form-builder/form-sections/form-section-properties) class). If you need additional advanced logic (for example to execute operations not suitable for views), you can develop sections based on ASP.NET Core [view components](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/view-components). Such sections consist of a view component class and the corresponding partial view it renders.
Use the following process to implement sections based on a view component:
  1. Create a [view component class](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/view-components) for the section.
     * We recommend storing form section view components in the _~/Components/FormSections/ <SectionName>_ folder together with other files required by the section.
  2. Implement the component’s `Invoke` or `InvokeAsync` method (both synchronous and asynchronous approaches are supported, this choice depends solely on your requirements).
     * The system invokes the view component when the section is inserted via the Form B
     * uilder. For sections with [custom properties](/documentation/developers-and-admins/development/builders/form-builder/form-sections/form-section-properties), you must declare the `FormSectionViewModel` parameter with the section’s properties class as the **generic parameter** .
C#
Copy
```
// The signature of a view component's InvokeAsync method for sections without custom properties
public async Task<IViewComponentResult> InvokeAsync()

// The signature of a view component's InvokeAsync method for sections with custom properties
public async Task<IViewComponentResult> InvokeAsync(FormSectionViewModel<TSectionPropertiesClass> sectionProperties)
```

  3. Create view model classes to pass any required data to the partial view. For sections with properties, you can directly pass the `FormSectionViewModel` .
     * We recommend storing section models in the  _~/Components/FormSections/ <SectionName>_ folder together with other files required by the section.
  4. Prepare the [partial view](#implement-form-section-views) that defines the section’s layout.
  5. In the return statement of the view component’s `Invoke` method, add the full relative path to the section’s partial view.
C#
**Example**
Copy
```
using Microsoft.AspNetCore.Mvc;
using System.Threading.Tasks;

using Kentico.Forms.Web.Mvc;

public class MySectionViewComponent : ViewComponent
{
    public Task<IViewComponentResult> Invoke(FormSectionViewModel<MySectionProperties> sectionProperties)
    {
        return View("~/Components/FormSections/MySection/_MySection.cshtml", sectionProperties);
    }
}
```

  6. [Register the section](#register-form-sections) into the system.


When the section is inserted using the Form Builder, the system invokes the corresponding view component and renders its partial view. Using this approach, you can decouple business and view-layer code, maintaining separation of concerns.
## Register form sections
Every section needs to be registered before it becomes available in the Form Builder. Register sections by adding the `RegisterFormSection` assembly attribute (available in the `Kentico.Forms.Web.Mvc` namespace).
To register sections represented as a single view file without an additional business logic class, we recommend adding the assembly attributes to a dedicated code file. For example, you can create a class named `FormBuilderComponentRegister` in your project and use it to register your form sections. Specify the following attribute parameters:
  * `Identifier` – a _string_ identifier of the form section. We recommend using sufficiently unique identifiers to avoid potential conflicts with other third-party sections, for example using a company name prefix.
  * `Name` – the display name of the form section. Displayed when [selecting sections](/documentation/business-users/digital-marketing/forms) in the Form Builder interface.
  * `CustomViewName` – specifies the name and location of the view that defines the section’s output. If not set, the system searches for a corresponding `_<Identifier>.cshtml` view in the  _~/Views/Shared/Sections_ folder (any period characters ‘.’ in the identifier are replaced by underscores ‘_’).


C#
**Example**
Copy
```
using Kentico.Forms.Web.Mvc;

[assembly: RegisterFormSection("LearningKit.FormSections.ThreeColumns",
                                "Three columns",
                                customViewName: "~/Components/FormSections/ThreeColumns/_ThreeColumns.cshtml",
                                Description = "Organizes fields into three equal-width columns side-by-side.",
                                IconClass = "icon-l-cols-3")]
```

For [sections based on a view component](#implement-form-section-view-components) , you can add the assembly attribute directly into the view component code file. In this case, specify the following attribute parameters:
  * `Identifier` _–_ a _string_ identifier of the form section. We recommend using sufficiently unique identifiers to avoid potential conflicts with other third-party sections, for example using a company name prefix.
  * `ViewComponentType` – the `System.Type` of the section’s view component class.
  * `Name` – the name of the form section. Displayed when [selecting sections](/documentation/business-users/digital-marketing/forms) in the Form Builder interface.


C#
**Example**
Copy
```
using Kentico.Forms.Web.Mvc;

[assembly: RegisterFormSection("LearningKit.FormSections.MySection",
                                typeof(MySectionViewComponent),
                                "My section",
                                PropertiesType = typeof(MySectionProperties),
                                Description = "Organizes fields into a section with a configurable title.",
                                IconClass = "icon-square")]
```

When registering any type of section, you can also set the following attribute properties:
  * `PropertiesType` – required for [sections with properties](/documentation/developers-and-admins/development/builders/form-builder/form-sections/form-section-properties). Specifies the `System.Type` of the section’s property model class.
  * (Optional) `Description` – a description of the form section. Displayed as a tooltip when selecting sections the Form Builder interface.
  * (Optional) `IconClass` – the font-icon assigned to the form section. Displayed when selecting sections the Form Builder interface. For a list of font icons available by default in the system, see the [Icon list](http://devnet.kentico.com/docs/icon-list/index.html) .


The section is now available in the [Form Builder interface](/documentation/business-users/digital-marketing/forms).
## Add scripts and styles for form sections
To add JavaScript and CSS styles required by your form sections, we recommend placing script and stylesheet files into sub-folders under:
  * **_~/wwwroot/FormBuilder/Public/ <SectionName>_** – scripts and styles intended for the live site.
  * **_~/wwwroot/FormBuilder/Admin/ <SectionName>_**– scripts and styles intended for the administration interface (when working with the section in the Fage Builder editing interface). Note that the system already attempts to enforce a unified look and feel for components rendered in the Form Builder interface. See [Form components](/documentation/developers-and-admins/development/builders/form-builder/form-components) and the `GetEditorHtmlAttributes` extension method.


You can use sub-folders that match the identifiers of individual components, or a _Shared_ sub-folder for assets used by multiple components. Note that this recommendation only applies when using the default configuration of the bundling support provided by Xperience and may be different for your project. See [Bundle static assets of builder components](/documentation/developers-and-admins/development/builders/bundle-static-assets-of-builder-components).
**CSS notes**
  * Only use the specified directories to add **basic styles** that are required for the section to render correctly. Any **site-specific styles** that finalize the live site design should be handled separately within the given site’s main stylesheet.
  * To avoid potential conflicts between styles from other third-party components, we recommend adding a unique prefix to your CSS classes and identifiers (e.g., `#CompanyName-mid-button`), or use similar measures to ensure their uniqueness.


### Initialize component scripts
In many cases, you will need to initialize your scripts from the views of form sections (for example if you need to call a function on page load or register an event listener). For most types of page or element events, you can use [HTML Event Attributes](https://www.w3schools.com/tags/ref_eventattributes.asp) of elements in your views.
For scripts that you want to run on page load, you need to consider the following:
  * The bundles containing your main scripts are added at the end of the HTML document’s body tag, so they are not available in the section code during the page load process. A solution is to run the initialization script during the [DOMContentLoaded](https://developer.mozilla.org/en-US/docs/Web/Events/DOMContentLoaded) event.
  * Components may be added dynamically after the page is loaded. In this case, the _DOMContentLoaded_ event has already occurred and will not fire again.


For example, the following script demonstrates how to reliably call a custom function on page load:
JS
Copy
```
if (document.readyState === "loading") {
    // Calls the function during the 'DOMContentLoaded' event, after the HTML document has been completely loaded
    document.addEventListener("DOMContentLoaded", function () {
        customFunction();
    });
} else {
    // Calls the function directly in cases where the component is rendered dynamically after 'DOMContentLoaded' has occurred
    customFunction();
}
```

This approach ensures that the initialization script runs correctly when the form section is displayed on the live site, as well as in the Form Builder interface.
**Note** : Apart from initialization code, avoid linking or executing scripts directly within form section views – this could lead to duplicated scripts for forms that contain multiple sections of the same type, or on pages with multiple forms.
![]()
[]()[]()
