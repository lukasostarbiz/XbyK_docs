# Form components
  * [ Copy page link ](documentation/developers-and-admins/development/builders/form-builder/form-components#) | [Get HelpService ID](documentation/developers-and-admins/development/builders/form-builder/form-components#)
Core MVC 5


[✖](documentation/developers-and-admins/development/builders/form-builder/form-components# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/builders/form-builder/form-components#)
Form components are reusable elements that allow content creators and marketers to compose forms via the [Form Builder](documentation/developers-and-admins/development/builders/form-builder). Each component represents a specific form field, such as a text box for user input, a group of radio buttons, etc. Administration users are free to add, remove, or reorder individual form components and specify their properties via the [Form Builder interface](documentation/business-users/digital-marketing/forms) in the **Forms** application.
A form is a collection of form component instances, with each instance representing an individual form field.
In addition to form components included in Form Builder by default, you can implement custom form components suitable for specific use cases and project requirements.
This page covers how to:
  * [Develop form components](documentation/developers-and-admins/development/builders/form-builder/form-components#develop-form-components)
  * [Register form components](documentation/developers-and-admins/development/builders/form-builder/form-components#register-form-components)
  * [Add scripts and styles for form components](documentation/developers-and-admins/development/builders/form-builder/form-components#add-scripts-and-styles-for-form-components)


## Develop form components
Unless specified otherwise, all classes and members mentioned in this section are located in the `Kentico.Forms.Web.Mvc` namespace.
All form components need to inherit from the `FormComponent<TProperties, TValue>` class, where the `TProperties` generic parameter needs to be a class derived from `FormComponentProperties<TValue>`, and the `TValue` type needs to be identical for both classes. Each component also requires a corresponding view template that describes how its input elements are rendered. 
**Areas**
Form components are designed to be used in the global scope and their code files must be placed in the application root of your project (not in an [Area](https://docs.microsoft.com/en-us/aspnet/core/mvc/controllers/areas)). Creating components in Areas may lead to unexpected behavior.
Follow these steps to create a form component:
  1. Open your Xperience project in Visual Studio.
  2. Create a form component class that inherits from `FormComponent<TProperties, TValue>`.
     * This class defines properties used for model binding from the component’s input elements.
     * The `TValue` generic determines the data type of the form component’s overall value (e.g., a basic data type such as string, `int` or `bool`).
  3. Create a form component properties class that inherits from `FormComponentProperties<TValue>`.
     * This class is used to define the properties of the form component (i.e., the component’s underlying database column type, constraints, and other configuration options. See [Form component properties](documentation/developers-and-admins/development/builders/form-builder/form-components/form-component-properties)).
     * The `TValue` generic needs to match the data type of the corresponding form component.
We recommend storing the classes of form components under a dedicated _~/Models/FormComponents/ <ComponentName>_ folder.
  4. In the model class derived from `FormComponent<TProperties, TValue>`:
    1. Substitute the generic `TProperties` parameter with the type derived from `FormComponentProperties<TValue>`.
    2. Components can consist of multiple input elements whose values can be, as part of the component’s code, joined and saved as the resulting value. Define properties used to pass values from the form component’s input elements to Xperience for data binding, and decorate them with the `BindableProperty` attribute.
    3. Override the following methods: 
       * `GetValue` – gets the value of the form field instance passed from a view where the instance is rendered. Can be used to compose multiple bindable properties into a single value.
       * `SetValue` – sets the class’s properties based on the value of the underlying form field instance.
**Custom handling of server-side field value evaluation**
By default, Xperience monitors all input elements of form fields that possess dependencies requiring server-side evaluation. Whenever the system detects a change in the value of such inputs, it automatically submits the form to the server for evaluation.
You may want to prevent the automatic evaluation in form components that construct their resulting value from multiple inputs (via the `GetValue` method). In such components, it does not usually make sense to evaluate the component’s resulting value until after the values for all of the component’s partial inputs have been provided.
If you wish to prevent Xperience from monitoring a form component:
      1. Override the virtual `CustomAutopostHandling` property, and set it to `true`. This disables automatic evaluation of the inputs by Xperience.
      2. Manually submit the form for via `window.kentico.updatableFormHelper.updateForm(this.form)` within your JavaScript code or within an element’s [HTML event attribute](https://www.w3schools.com/tags/ref_eventattributes.asp) (most commonly `onchange`, or `onclick`). The form is submitted only when the function is executed, leaving it up to the component’s developer to ensure this happens at an opportune moment. For instance, after all the required values have been provided.
    4. (Optional) Override the virtual `LabelForPropertyName` member to specify which property the `for` attribute of the field’s `<label>` element targets. By default, the value of this property is set to the name of the first bindable property found within the form component class.
C#
Copy
```
public class CustomFormComponent : FormComponent<CustomFormComponentProperties, string>
{
    public const string IDENTIFIER = "CustomFormComponent";

    // Specifies the property is used for data binding by the Form Builder
    [BindableProperty]

    // Used to store the value of the input field of the component
    public string Value { get; set; }

    // Gets the value of the form field instance passed from a view where the instance is rendered
    public override string GetValue()
    {
        return Value;
    }

    // Sets the default value of the form field instance
    public override void SetValue(string value)
    {
        Value = value;
    }
}
```

  5. In the class derived from `FormComponentProperties<TValue>`:
     * Call the constructor of the base class and specify the data type of the underlying database column using the `CMS.DataEngine.FieldDataType` enumeration. For the list of available data types or to learn how to register custom data types suitable for your component, see [Data type management](documentation/developers-and-admins/customization/field-editor/data-type-management). 
The base class constructor also takes the following optional parameters:
       * `size` – sets the size of the corresponding component’s underlying database column.
       * `precision` – governs the number of digits floating point numbers stored in the database can contain. These parameters configure the underlying database column, and need to be specified when the data type of the form component requires it, for example:
         * Specify the `size` parameter when defining components with the `FieldDataType.Text` data type – `base(FieldDataType.Text, size: 200)`
         * Specify the `precision` parameter when defining components with the `FieldDataType.Decimal` data type – `base(FieldDataType.Decimal, precision: 10)`
     * _Override_ the `DefaultValue` property, used to `get` or `set` the default value of fields based on the form component.
       * Annotate the property with a suitable [Admin UI form component attribute](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) to specify the [editing interface](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) for the default value in the Form Builder properties dialog.
     * (Optional) Declare [additional properties](documentation/developers-and-admins/development/builders/form-builder/form-components/form-component-properties) the form component requires.
C#
Copy
```
// Sets an editing component for the DefaultValue property
[TextAreaComponent(Label = "Default value", Order = EditingComponentOrder.DEFAULT_VALUE)]
public override string DefaultValue
{
    get;
    set;
}

// Initializes a new instance of the CustomFormComponentProperties class and configures the underlying database field
public CustomFormComponentProperties()
    : base(FieldDataType.Text, size: 200)
{
}
```

  6. Create a new partial view that describes the appearance of the form component and place it in the _~/Views/Shared/FormComponents_ folder. Use a view name that matches the identifier assigned to the form component [upon its registration](documentation/developers-and-admins/development/builders/form-builder/form-components#register-form-components) and prefix it with the underscore (_‘_’_) character (to indicate a partial view according to MVC best practices).
You can place views into custom locations by specifying an optional parameter during the [component registration](documentation/developers-and-admins/development/builders/form-builder/form-components#register-form-components).
In the view:
    1. Retrieve the dictionary collection of system HTML attributes by calling the `ViewData.Kentico().GetEditorHtmlAttributes` extension method (from the `Kentico.Forms.Web.Mvc` namespace). You can modify the collection to include additional attributes the component requires, such as CSS classes and data attributes. Ensure the collection is included in each input element as certain features may not work otherwise.
    2. Specify input elements for the component. If possible, use extension methods provided by the framework’s `HtmlHelper` class to render individual inputs. The methods by default, provide an easy way to merge additional HTML attributes and ensure the inputs contain fully qualified names, facilitating model binding.
If you need to create more complex inputs, we recommend writing an extension method that handles the creation of input elements via the `TagBuilder` class, which simplifies the definition of additional attributes and handles attribute encoding by default. To generate fully qualified names and identifiers for model binding, use the `Html.NameFor` and `Html.IdFor` extension methods, respectively.
cshtml
**_CustomFormComponent.cshtml**
Copy
```
@using Kentico.Forms.Web.Mvc
@using Kentico.Web.Mvc

@* Gets a collection of system HTML attributes necessary for the correct functionality of the form component inputs *@
@{
    IDictionary<string, object> htmlAttributes = ViewData.Kentico().GetEditorHtmlAttributes();
}

@model LearningKit.FormBuilder.FormComponents.CustomFormComponent

@* Specifies additional HTML attributes of the form component. Checks for the existence of certain attributes in case
    the returned 'htmlAttributes' collection already contains them (e.g., when the component is rendered within
    the administration interface, where Xperience provides CSS classes to maintain the admin UI look and feel) *@
@{
    if (htmlAttributes.ContainsKey("class"))
    {
        htmlAttributes["class"] += " myclass";
    }
    else
    {
        htmlAttributes["class"] = "myclass";
    }
}

@* Renders the input element for the 'Value' property of the form component *@
@Html.TextBoxFor(m => m.Value, htmlAttributes)
```

  7. (Optional) Write any required CSS and JavaScript code the component requires for its functionality. Place the script and style files into the _~/Content/FormComponents/ <FormComponentIdentifier>_ folder. See [Add scripts and styles for form components](documentation/developers-and-admins/development/builders/form-builder/form-components#add-scripts-and-styles-for-form-components) for more information and recommended practices.


## Register form components
To register a form component, annotate the class derived from `FormComponent<TProperties, TValue>`with the `RegisterFormComponent` assembly attribute. This attribute ensures the component is recognized by the system and available for [use in the Form Builder](documentation/business-users/digital-marketing/forms). When registering the component, specify the following parameters:
  * `Identifier` – a `string` identifier of the form component. 
If you are planning to distribute your components, or are using components from third-party sources, consider specifying the identifier in a format that uniquely identifies your form component to avoid potential conflicts with identifier names. For example, use `CompanyName.ModuleName.ComponentName`.
  * `FormComponentType` – the `System.Type` of the form component class.
  * `Name` – sets the name of the form component. Displayed when [adding form fields](documentation/business-users/digital-marketing/forms/create-and-edit-forms) in the Form Builder.
  * (Optional) `Description`– sets the description of the form component. Displayed when adding form fields in the Form Builder.
  * (Optional) `IsAvailableInFormBuilderEditor` – determines if the form component can be added as a form field. `True` by default. If set to `false`, the component can only be added as an [editing component](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components).
  * (Optional) `ViewName` – specifies the name of the view used to display the component, for example _CustomComponent_. If not set, the system searches for a corresponding `_<Identifier>.cshtml` view in the  _~/Views/Shared/FormComponents/_ folder.
  * (Optional) `IconClass` – the font-icon assigned to the form component. Displayed in the component listing when adding new fields in the Form Builder. For a list of font icons available by default in the system, see the [Icon list](http://devnet.kentico.com/docs/icon-list/index.html).


You can see the registration of a sample form component of the `CustomFormComponent` type in the code snippet below:
C#
Copy
```
// Registers a form component for use in the Form Builder
[assembly: RegisterFormComponent(CustomFormComponent.IDENTIFIER, typeof(CustomFormComponent), "Custom component", Description = "This is a custom form component.", IconClass = "icon-newspaper")]
```

The form component is now created and registered in the system. Users can include it when composing forms [using the Form Builder interface](documentation/business-users/digital-marketing/forms/create-and-edit-forms).
## Add scripts and styles for form components
To add JavaScript and CSS styles required by your components, we recommend placing script and stylesheet files into sub-folders under:
  * **_~/wwwroot/FormBuilder/Public/_** – scripts intended for both the live site and administration. Styles intended for the live site.
  * **_~/wwwroot/FormBuilder/Admin/_** – scripts and styles intended for the administration interface. Note that the system already attempts to enforce a unified look and feel for components rendered in the Form Builder interface.


You can use sub-folders that match the identifiers of individual components, or a _Shared_ sub-folder for assets used by multiple components. Note that this recommendation only applies when using the default configuration of the bundling support provided by Xperience and may be different for your project. See [Bundle static assets of builder components](documentation/developers-and-admins/development/builders/bundle-static-assets-of-builder-components).
**CSS notes**
  * Only use the specified directories to add **basic styles** that are required for the widget to render correctly. Any **site-specific styles** that finalize the live site design of the widget should be handled separately within the given site’s main stylesheet.
  * To avoid potential conflicts between styles from other third-party components, we recommend adding a unique prefix to your CSS classes and identifiers (e.g., `#CompanyName-mid-button`), or use similar measures to ensure their uniqueness.


### Initializing component scripts
In many cases, you will need to initialize your scripts from the views of form components (for example if you need to call a function on page load or register an event listener). For most types of page or element events, you can use [HTML Event Attributes](https://www.w3schools.com/tags/ref_eventattributes.asp) of elements in your views.
For scripts that you want to run on page load, you need to consider the following:
  * The bundles containing your main scripts are added at the end of the HTML document’s body tag, so they are not available in the component code during the page load process. A solution is to run the initialization script during the [DOMContentLoaded](https://developer.mozilla.org/en-US/docs/Web/Events/DOMContentLoaded) event.
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

**Note** : Apart from initialization code, avoid linking or executing scripts directly within form component views – this could lead to duplicated scripts for forms that contain multiple fields based on the same component, or on pages with multiple forms.