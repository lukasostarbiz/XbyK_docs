---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * [Page Builder](/documentation/developers-and-admins/development/builders/page-builder)
  * [Widgets for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder)
  * Widget properties 


# Widget properties
When developing [Page Builder widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder), you can define properties that allow content editors to adjust the widget content or behavior directly in the administration interface. Users interact with widget properties through the widget configuration dialog or inline editors, which you need to implement.
Use the following process to develop properties for a widget:
  1. [Create a model class that defines the widget properties](#create-property-models).
  2. Allow content editors to modify the widget properties: 
     * [Define the configuration dialog](#define-the-configuration-dialog)
     * [Implement inline editors](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties)


**Configuration dialog and inline editors**
You can combine the configuration dialog and inline editors in a single widget.
  1. [Handle the properties in the widget’s code](#handle-properties-in-widget-code).
  2. (Optional) [Add support for POST actions](#access-widget-properties-in-post-actions).


To see a scenario with full code samples which will guide you through the process of developing a simple widget with a property, visit [Example - Widget development](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/example-widget-development). For a detailed walkthrough showing how to create a more complex widget, see the [Define an advanced widgets](/guides/development/page-builder/define-advanced-widget) guide.
**Areas**
Widgets are designed as global components. Adding related files, such as property model classes, into Areas is not supported and may lead to unexpected behavior.
## Create property models
The properties of a widget must be defined within a model class that implements the IWidgetProperties interface (available in the `Kentico.PageBuilder.Web.Mvc` namespace).
Specify each widget property by creating a corresponding property in the model class. You can also set default values for the properties.
C#
Copy
```
public class CustomWidgetProperties : IWidgetProperties
{
    // Defines a property and sets its default value
    public int Number { get; set; } = 22;
}
```

You can use the `Newtonsoft.Json.JsonIgnore` attribute to exclude dynamically computed widget properties from database serialization.
We recommend storing widget property models in the  _~/Components/Widgets/ <widget name>_ folder together with other files required by the widget.
## Define the configuration dialog
The configuration dialog is a simple way to allow content editors to set values for widget properties. In the property model class, you need to define editing form components for widget properties that you want to make editable in the configuration dialog. You can use the system’s [default form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) or develop [custom form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components).
  1. Edit the widget’s property model class in your Core project.
  2. Define the visual interface of the configuration dialog:
     * Assign [editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) to desired properties.
C#
Copy
```
[TextInputComponent(Order = 0, Label = "Text")]
public string Text { get; set; }
```



Users can now click the **Configure** ([working with widgets in the administration interface](/documentation/business-users/website-content/widgets-and-page-builder). This opens the widget properties dialog, and the configured property values affect the appearance and functionality of the widget on the live site.
**Custom editing components with links to content items**
If you decorate any widget property with a [custom form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) or use an [inline editor](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties) that allows users to add links to content items (other than the default [combined content selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) and [rich text editor](/documentation/business-users/rich-text-editor)), you need to create a [custom reference extractor](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-reference-extractors) in order to enable [usage tracking](/documentation/business-users/content-hub/content-items#track-usage-of-content-items) in the widget property.
**Advanced property options**
You can add dynamic [visibility conditions and validation](/documentation/developers-and-admins/development/builders/builder-component-properties-visibility-and-validation) that restricts how and when properties are displayed in the widget configuration dialog.
## Handle properties in widget code
For properties to have an effect on a widget’s appearance or functionality, you need to retrieve their values and reflect them in the widget’s output code or logic. The required steps depend on the development approach used to create the widget (see [Widgets for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) for more information).
### Basic widgets
For basic widgets that consist only of a partial view file and a properties class, handle the property values in the widget’s partial view.
The view must use the generic `ComponentViewModel<TPropertyModel>` class as its model, with the appropriate property model class as the generic type parameter. The system ensures that property values configured for the currently processed widget are passed to the view. Retrieve the property values from the model’s `Properties` member, which returns an object of the specified property model class.
cshtml
**Example**
Copy
```
@using Kentico.PageBuilder.Web.Mvc

@model ComponentViewModel<CustomWidgetProperties>

<p>The value of the widget's 'Number' property is: @Model.Properties.Number</p>
```

### Widgets based on a view component
To work with widget properties in view components, the component’s `Invoke` or `InvokeAsync` method signature must declare the `ComponentViewModel<TPropertiesType>` parameter, with the appropriate property model class as the generic type.
C#
Copy
```
// The signature of a view component's InvokeAsync method for widgets with custom properties
public Task<IViewComponentResult> InvokeAsync(ComponentViewModel<CustomWidgetProperties> widgetProperties)
```

The widget’s properties are accessible via the Properties property of the ComponentViewModel parameter, which contains an object of the specified property model class. The property values are loaded from the current configuration of the processed widget.
C#
Copy
```
// Gets the value of a widget property from within a component's Invoke method
int propertyValue = widgetProperties.Properties.Number;
```

You can then adjust the flow of your logic based on the values of various properties, or pass them to the component’s view using an appropriate view model.
Do not directly pass the property model to your widget views. Passing data to views is the responsibility of the widget’s view model. For this reason, we strongly recommend keeping the models separate.
## Access widget properties in POST actions
Basic POST requests do not by default contain widget properties data. To access the properties of a widget during POST actions (in the [class](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) handling POST requests), you first need to include the properties into the data submitted by the corresponding HTML form in the widget’s output. Call the `Html.Kentico().ComponentPropertiesData` extension method (or its [Tag Helper](/documentation/developers-and-admins/development/reference-tag-helpers) equivalent) within the given form tag in your widget view.
cshtml
**Example**
Copy
```
using Kentico.Content.Web.Mvc

...

<form asp-controller="WidgetPostController" asp-action="HandlePost" method="post">
    ...

    @Html.Kentico().ComponentPropertiesData()

    <input type="submit" value="Submit" />
</form>
```

The method renders a hidden field that persists the widget’s current properties configuration.
On the server, access the widget’s properties in the corresponding controller class via the `IPageBuilderComponentPropertiesRetriever` service and its `Retrieve<TWidgetProperties>` method. Specify the widget’s properties class as the method’s generic parameter: 
C#
**Controller class handling the request**
Copy
```
// Contains an instance of the IPageBuilderComponentPropertiesRetriever service (e.g., obtained via dependency injection)
private readonly IPageBuilderComponentPropertiesRetriever componentPropertiesRetriever;

// Gets the properties of the widget as a strongly typed object
NumberWidgetProperties properties = componentPropertiesRetriever.Retrieve<NumberWidgetProperties>();
```

![]()
[]()[]()
