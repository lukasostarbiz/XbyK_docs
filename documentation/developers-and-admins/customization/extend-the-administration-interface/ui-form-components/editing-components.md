# Editing components
  * [ Copy page link ](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components#) | [Get HelpService ID](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components#)
Core MVC 5


[✖](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components#)
[Admin UI](documentation/developers-and-admins/customization/extend-the-administration-interface/admin-ui-customization-model-overview), [Page Builder](documentation/developers-and-admins/development/builders/page-builder), and [Form Builder](documentation/developers-and-admins/development/builders/form-builder) components can expose customizable properties that allow users to adjust their functionality, appearance, behavior, or other attributes. The editing interface for such properties is provided by [UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components).
The documentation refers to UI form components used in this fashion as **editing components**.
Editing components can be used in:
  * Admin UI component configuration dialogs of
    * [Form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components)
    * [UI form component validation rules](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules)
    * [UI form component visibility conditions](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions)
    * [Model-based edit pages](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-pages-with-forms)
    * [Listing UI page filters](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template)
  * Page Builder component configuration dialogs of
    * [Widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties)
    * [Sections](documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder/section-properties)
    * [Personalization condition types](documentation/business-users/digital-marketing/widget-personalization)
  * Form Builder component configuration dialogs of
    * [Form components](documentation/developers-and-admins/development/builders/form-builder/form-components)
    * [Form sections](documentation/developers-and-admins/development/builders/form-builder/form-sections)


## Assign and configure editing components
**Editing component namespaces**
Unless specified otherwise, all classes and members mentioned in this section can be found in the following namespaces:
  * `Kentico.Xperience.Admin.Base.Forms`
  * `Kentico.Xperience.Admin.Base.FormAnnotations`


Do **not** use API from the _Kentico.Forms.Web.Mvc_ namespace, which contains some classes with matching names (e.g., `FormComponentAttribute`).
Editing components are assigned using **C# attributes** specific to each UI form component. Annotating a configuration property of one of the supported components with a form component attribute sets that component as the editor of the property’s input.
For example, to assign the text input form component as an editing component, use the `TextInputComponent` attribute.
C#
Copy
```
using Kentico.Xperience.Admin.Base.FormAnnotations;

...

// Assigns 'TextInputComponent' as the editing component of 'MyProperty'
[TextInputComponent(Label = "My property")]
public string MyProperty { get; set; }
```

The data type of the assigned editing component must match the data type of the property being annotated.
The property is now editable via the component’s configuration dialog.
### Form component attributes
See [Reference - Admin UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) for an overview of form components and corresponding component attributes provided by default Xperience installations.
If the provided set of components isn’t sufficient, you can develop [custom form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) suitable for your scenarios.
### Configure editing component properties
All component attributes allow you to configure a set of default properties, which you can use to adjust the resulting configuration interface. These properties are set using optional constructor parameters available within each attribute.
  * **Label** – the label text displayed in the property’s configuration interface.
  * **Tooltip** – text displayed for the property on mouse hover.
  * **ExplanationText** – a short message displayed under the property’s input field.
  * **Order** – the order in which the properties appear in the configuration interface.


Form components can also have their own properties, which differ based on the component used. See the [individual attributes and their constructors](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) for details.
### Configure components dynamically
You can use the API to dynamically configure the state of editing components. Such configuration is intended for advanced scenarios where statically configuring editing component properties, [visibility conditions](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions) and [validations rules](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) via attribute notation is not sufficient. A typical use case is creating dynamic data sources for selector components.
For more information, see [Configure editing component state](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components/configure-editing-component-state).
### Group components into categories
Editing components within individual configuration dialogs can be grouped into categories using the `FormCategory` attribute. The attribute must be applied to the model class. There is no limit on the number of categories that can be created per dialog.
The position of each category relative to other editing components and categories is determined by the `Order` property, which is shared with the order of other items in the form.
The attribute also allows you to set various metadata for each category including its label, tooltip, and whether it is collapsible. See the following code snippet for an example:
C#
**Example - grouping components into categories**
Copy
```
using Kentico.Xperience.Admin.Base.FormAnnotations;

using Kentico.PageBuilder.Web.Mvc;

[FormCategory(Label = "Main", Order = 1)]
[FormCategory(Label = "Optional", Order = 4, Collapsible = true, IsCollapsed = true)]
public class WidgetModelClass : IWidgetProperties
{
    [TextInputComponent(Label = "Title", Order = 2)]
    [RequiredValidationRule]
    public string Title { get; set; }

    [TextInputComponent(Label = "Main body", Order = 3)]
    [RequiredValidationRule]
    public string Text { get; set; }

    [TextInputComponent(Label = "Additional notes", Order = 5)]
    public string Notes { get; set; }
}
```

## Create editing component attributes for custom form components
Custom form components also need their corresponding attributes if you wish to use them as editing components.
Component attributes must inherit from the `FormComponentAttribute` class.
C#
Copy
```
using Kentico.Xperience.Admin.Base.FormAnnotations;

public class MyComponentAttribute : FormComponentAttribute
{
}
```

If you don’t require more than the [base set of configuration properties](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components#configure-editing-component-properties), the above definition is sufficient.
However, if your form component relies on additional configuration properties, mirror those in the attribute class (provided you want them configurable). The mirrored properties:
  * **must be named identically** to the properties you wish to configure.
  * **must be of the same type** as the corresponding form component property.


Properties that are required for the form component to function can be made mandatory via the attribute’s constructor.
C#
Copy
```
using Kentico.Xperience.Admin.Base.FormAnnotations;

public class MyComponentAttribute : FormComponentAttribute
{
    public string ConfigProp1 { get; set; }

    public string ConfigProp2 { get; set; }

    // Attribute constructor
    // Editing component is not assignable without providing 'configProp1'
    public MyComponentAttribute(string configProp1)
    {
        ConfigProp1 = configProp1;
    }
}
```

To indicate the relation between the attribute class and the corresponding form component, decorate the main form component class with `ComponentAttribute`.
C#
Copy
```
using Kentico.Xperience.Admin.Base.Forms;
using Kentico.Xperience.Admin.Base.FormAnnotations;

[ComponentAttribute(typeof(MyComponentAttribute))]
public class MyComponent : FormComponent<TProperties, TClientProperties, TType>
```

For the example above, all properties decorated with `MyComponentAttribute` use `MyComponent` as their editing component.