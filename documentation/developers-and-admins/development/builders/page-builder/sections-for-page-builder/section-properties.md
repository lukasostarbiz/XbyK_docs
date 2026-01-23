---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder/section-properties
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * [Page Builder](/documentation/developers-and-admins/development/builders/page-builder)
  * [Sections for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder)
  * Section properties 


# Section properties
When developing [Page Builder sections](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder), you can define properties that allow content editors to adjust the appearance or behavior of the sections in the administration interface. Users then interact with the section properties through section configuration dialogs.
Use the following process to develop properties for a section:
  1. [Create a model class that defines the section properties](#create-property-models)
  2. [Define the configuration dialog to allow content editors to modify the properties](#define-the-configuration-dialog)
  3. [Handle the properties in the section’s code](#handle-properties-in-section-code)


See the [Example](#example---develop-a-section-with-a-configurable-property) on this page for a scenario with full code samples.
## Create property models
The properties of a section must be defined within a model class that implements the `ISectionProperties` interface (available in the `Kentico.PageBuilder.Web.Mvc` namespace).
Specify each section property by creating a corresponding property in the model class. You can also set default values for the properties.
C#
**Example**
Copy
```
public class CustomSectionProperties : ISectionProperties
{
    // Defines a property and sets its default value
    public string Color{ get; set; } = "#FFF";
}
```

You can use the `Newtonsoft.Json.JsonIgnore` attribute to exclude dynamically computed section properties from database serialization.
We recommend storing section property models in a dedicated _< SectionName>_ folder together with other files required by the section.
## Define the configuration dialog
The configuration dialog is a simple way to allow content editors to set values for section properties. In the property model class, you need to define editing form components for section properties which you want to make editable in the configuration dialog. You can use the system’s [default form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) or develop [custom form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components).
  1. Edit the section’s property model class in your live site project.
  2. Define the visual interface of the configuration dialog by assigning appropriate [editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) to the configuration properties.
C#
**Example - Setting an editing component**
Copy
```
[TextInputComponent(Order = 0, Label = "Color")]
public string Color { get; set; } = "#FFF";
```



Users can now click the **Configure** ([working with sections in the administration interface](/documentation/business-users/website-content/widgets-and-page-builder). This opens the section properties dialog, and the configured property values affect the appearance and functionality of the section on the live site.
**Custom editing components with links to content items**
If you decorate any section property with a [custom form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) that allows users to add links to content items (other than the default [combined content selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) and [rich text editor](/documentation/business-users/rich-text-editor) ), you need to create a [custom reference extractor](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-reference-extractors) in order to enable [usage tracking](/documentation/business-users/content-hub/content-items#track-usage-of-content-items) in the property.
**Advanced property options**
You can add dynamic [visibility conditions and validation](/documentation/developers-and-admins/development/builders/builder-component-properties-visibility-and-validation) that restrict how and when properties are displayed in the section configuration dialog.
## Handle properties in section code
In order for properties to have an effect on a section’s appearance or functionality, you need to retrieve the property values and adjust the section’s output code or logic correspondingly. The required steps depend on the development approach used to create the section (see [Sections for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) for more information).
### Basic sections
For basic sections that consist only of a partial view file and a properties class, handle the property values in the section’s partial view.
The view must use the generic `ComponentViewModel<TPropertyModel>` class as its model, with the appropriate property model class as the generic type parameter. The system ensures that the property values configured for the currently processed section are passed to the view. Retrieve the property values from the model’s Properties member, which returns an object of the specified property model class.
cshtml
Copy
```
@addTagHelper Kentico.Content.Web.Mvc.WidgetZoneTagHelper, Kentico.Content.Web.Mvc

@using Kentico.PageBuilder.Web.Mvc

@model ComponentViewModel<CustomSectionProperties>

@* Shows a sample section with a background color specified in the section's configuration dialog *@
<div style="background-color: @Model.Properties.Color;">
    @* Renders a widget zone via the widget-zone Tag Helper *@
    <widget-zone />
</div>
```

### Sections based on a view component
To work with properties in your section view components, the component’s `Invoke` or `InvokeAsync` method signature must declare the `ComponentViewModel<TPropertiesType>` parameter, with the appropriate property model class as the generic type.
C#
Copy
```
// The signature of a view component's InvokeAsync method for sections with custom properties
public Task<IViewComponentResult> InvokeAsync(ComponentViewModel<CustomSectionProperties> sectionProperties)
```

The section’s properties are accessible via the `Properties` property of the `ComponentViewModel` parameter. The method returns an object of the specified property model class. The object’s property values are loaded from the current configuration of the processed section.
C#
Copy
```
// Gets the value of a section property from within a component's Invoke method
var propertyValue = sectionProperties.Properties.BackgroundColor;
```

You can then adjust the flow of your code based on the values of individual section properties, or pass them to the section’s view using an appropriate view model.
Do not directly pass the property model to your section views. We strongly recommend creating a separate view model class, which you can then use to pass data to the section view.
## Access section properties in POST actions
Basic POST requests do not by default contain section properties data. To access the properties of a section during POST actions (in the controller class handling POST requests), you first need to include the properties into the data submitted by the corresponding HTML form in the section’s output. Call the `Html.Kentico().ComponentPropertiesData` extension method (or its [Tag Helper](/documentation/developers-and-admins/development/reference-tag-helpers) equivalent) within the given form tag in your section view.
cshtml
**Example**
Copy
```
using Kentico.Content.Web.Mvc

...

<form asp-controller="SectionPostController" asp-action="HandlePost" method="post">
    ...

    @Html.Kentico().ComponentPropertiesData()

    <input type="submit" value="Submit" />
</form>
```

The method renders a hidden field that persists the section’s current properties configuration.
On the server, access the section’s properties in the corresponding controller class via the `IPageBuilderComponentPropertiesRetriever` service and its `Retrieve<TSectionProperties>` method. Specify the section’s properties class as the method’s generic parameter: 
C#
**Controller class handling the request**
Copy
```
// Contains an instance of the IPageBuilderComponentPropertiesRetriever service (e.g., obtained via dependency injection)
private readonly IPageBuilderComponentPropertiesRetriever componentPropertiesRetriever;

// Gets the properties of the section as a strongly typed object
MySectionProperties properties = componentPropertiesRetriever.Retrieve<MySectionProperties>();
```

## Example - Develop a section with a configurable property
The following section will guide you through a step-by-step process of developing a simple [Page Builder section](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) with a configurable property.
When finished, the section is displayed with a color background of your choice. The color is set via a section property and can be modified through a section configuration dialog. The color can be specified in any text format that is accepted by the [CSS background-color property](https://www.w3schools.com/cssref/pr_background-color.asp) (e.g. a HEX or RGB value).
### Property model
Create a properties model **CustomSectionProperties.cs** in the _~/Components/PageBuilder/Sections/CustomSection_ folder:
C#
Copy
```
using Kentico.Forms.Web.Mvc;
using Kentico.PageBuilder.Web.Mvc;

public class CustomSectionProperties : ISectionProperties
{
    // Defines a property and sets its default value
    // Assigns the default Xperience text input component, which allows users to enter
    // a string value for the property in the section's configuration dialog
    [TextInputComponent(Order = 0, Label = "Color")]
    public string Color { get; set; } = "#FFF";
}
```

### Partial view
Create a partial view **_CustomSection.cshtml** in the  _~/Components/PageBuilder/Sections/CustomSection_ folder:
cshtml
Copy
```
@addTagHelper Kentico.Content.Web.Mvc.WidgetZoneTagHelper, Kentico.Content.Web.Mvc

@using Kentico.PageBuilder.Web.Mvc

@model ComponentViewModel<CustomSectionProperties>

@* Shows a sample section with a background color specified in the section's configuration dialog *@
<div style="background-color: @Model.Properties.Color;">
    @* Renders a widget zone via the widget-zone Tag Helper *@
    <widget-zone />
</div>
```

### Section registration
Register the section into the system using the `RegisterSection` assembly attribute. We recommend adding a dedicated code file to your project for the purposes of component registration, for example named **ComponentRegister.cs**.
C#
Copy
```
[assembly: RegisterSection("MyCompany.Sections.CustomSection",
                           "Custom section",
                           typeof(CustomSectionProperties),
                           customViewName: "~/Components/PageBuilder/Sections/CustomSection/_CustomSection.cshtml",
                           IconClass = "icon-square")]
```

![]()
[]()[]()
