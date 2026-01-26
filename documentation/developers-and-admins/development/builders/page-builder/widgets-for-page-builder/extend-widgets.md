---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/extend-widgets
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * [Page Builder](/documentation/developers-and-admins/development/builders/page-builder)
  * [Widgets for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder)
  * Extend widgets 


# Extend widgets
When developing [Page Builder widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder), you can extend an existing widget instead of creating a new one from scratch. The API provides a way to render a **“nested”** widget from the view code of another widget. An extended widget retains the full functionality of the original nested widget, but can introduce additions such as wrapping HTML code, new [configurable properties](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties) and additional custom logic.
Nesting is possible for widgets of any type, including the [default system widgets](/documentation/developers-and-admins/development/builders/page-builder/reference-default-page-builder-widgets), third-party widgets, or your own custom widgets. The only requirement is for the widget to be registered using the RegisterWidget assembly attribute (as described in [Widgets for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder)).
## Creating extended widgets
To create a widget that extends another widget via nesting:
  1. Start by following the [standard widget development](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) instructions.
  2. For widgets with [configurable properties](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties), choose one of the following approaches based on your requirements:
     * Use the property model class of the nested widget for your extended widget (either during registration or in the widget’s custom view component).  
– OR –
     * Create a new property model class that **inherits** from the nested widget’s property model. You can then add any properties that you need for your extended widget.
  3. In the partial view of the extended widget, find the location where you want to display the content of the nested widget, and call the `Html.Kentico().RenderNestedWidgetAsync` extension method (from the `Kentico.PageBuilder.Web.Mvc` namespace) or its [Tag Helper](/documentation/developers-and-admins/development/reference-tag-helpers) equivalent. The method takes the following parameters:
     * **`identifier`**– the _string_ identifier under which the nested widget was registered. For identifiers of the default system widgets, check [the system widget reference](/documentation/developers-and-admins/development/builders/page-builder/reference-default-page-builder-widgets).
     * **`properties`**– object containing the properties of the nested widget. Use the ComponentViewModel <TProperties> model class to make the system automatically pass the [properties](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties) to the widget view.


You now have a custom widget that provides the same content and functionality as the nested widget. Implement any required extended functionality, for example by adding HTML code before or after the nested widget.
If the nested widget has configurable properties, they are also available for the extended widget within the Page Builder interface (along with any additional properties defined in an inherited property model class).
## Example
The following example demonstrates how to use widget nesting to extend the system’s default **Form** widget. The sample extended widget adds an extra heading above the default form content, along with a custom property that allows editors to set the heading text.
  1. Create a widget property model class in the _~/Components/Widgets/ExtendedFormWidget_ folder, named **ExtendedFormWidgetProperties.cs**.
  2. Make the property model inherit from the `FormWidgetProperties` class (in the `Kentico.Forms.Web.Mvc.Wigets` namespace), which is the property model of the default form widget.
  3. Define a string property representing the text of the additional heading.
C#
Copy
```
using Kentico.Forms.Web.Mvc.Widgets;
using Kentico.Xperience.Admin.Base.FormAnnotations;

public class ExtendedFormWidgetProperties : FormWidgetProperties
{
    // Defines a property and sets its default value
    // Assigns the default Kentico text input component, which allows users to enter
    // a textual value for the property in the widget's configuration dialog
    [TextInputComponent(Order = 0, Label = "Heading text")]
    public string HeadingText { get; set; } = "Default";
}
```

  4. Create a partial view for the widget in the _~/Components/Widgets/ExtendedFormWidget_ folder, named **_ExtendedFormWidget.cshtml**.
  5. Call the `Html.Kentico().RenderNestedWidgetAsync` extension method to render the system’s default form widget.
cshtml
Copy
```
@using Kentico.Content.Web.Mvc
@using Kentico.PageBuilder.Web.Mvc
@using Kentico.Web.Mvc

@model ComponentViewModel<ExtendedFormWidgetProperties>
@* Configurable heading added above the default form widget *@
<h1>@Model.Properties.HeadingText</h1>

@* Renders the nested form widget *@
@await Html.Kentico().RenderNestedWidgetAsync(SystemComponentIdentifiers.FORM_WIDGET_IDENTIFIER, Model.Properties)
```

  6. Register the new widget into the system using the `RegisterWidget` assembly attribute. We recommend adding a dedicated file to your project’s _~/Components_ folder for the purposes of component registration, for example named **ComponentRegister.cs**.
C#
Copy
```
// Registers the 'Extended form' widget
[assembly: RegisterWidget("MySite.Widgets.ExtendedFormWidget",
                          "Extended form",
                          typeof(ExtendedFormWidgetProperties),
                          customViewName: "~/Components/Widgets/ExtendedFormWidget/_ExtendedFormWidget.cshtml",
                          IconClass = "icon-form")]
```



You now have a custom widget that works exactly like the default _Form_ widget, but with an additional heading. The text of the heading can be configured in the Page Builder interface through a new property.
![]()
[]()[]()
