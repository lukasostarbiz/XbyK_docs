---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Extend the administration interface](/documentation/developers-and-admins/customization/extend-the-administration-interface)
  * [UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components)
  * UI form component visibility conditions 


# UI form component visibility conditions
Visibility conditions allow you to dynamically hide or display fields in editing forms within the Xperience administration UI. These conditions can be used for:
  * Fields of [content types](/documentation/developers-and-admins/development/content-types) or [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas). In this case, visibility conditions are configured via the [field editor](/documentation/developers-and-admins/customization/field-editor) UI.
  * Inputs in model-based forms, which are used to configure admin UI, [Page Builder](/documentation/developers-and-admins/development/builders/page-builder), and [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) components. In this case, visibility conditions are assigned directly to the configuration properties via C# attribute notation (each property represents a field in the corresponding configuration dialog).


A visibility condition consists of logic that evaluates whether a given field is displayed – implemented in the condition’s `Evaluate` method – and, optionally, a set of configuration properties.
## Define visibility conditions
**Visibility condition namespaces**
Unless specified otherwise, all classes and members mentioned in this section can be found in the following namespaces:
  * `Kentico.Xperience.Admin.Base.Forms`
  * `Kentico.Xperience.Admin.Base.FormAnnotations`


Do **not** use API from the _Kentico.Forms.Web.Mvc_ namespace, which contains some classes with matching names (e.g., `VisibilityCondition`, `RegisterFormVisibilityCondition`).
Visibility conditions are defined and evaluated on the application’s back end (the ASP.NET Core project). Their architecture is summarized by the following diagram:
[![Visibility condition architecture overview](/docsassets/documentation/ui-form-component-visibility-conditions/Architecture_overview.png)](/docsassets/documentation/ui-form-component-visibility-conditions/Architecture_overview.png)
The visibility of an input is determined using:
  * contextual information from the system (e.g., about the current user)
  * other inputs in the form
  * completely custom logic


With this in mind, visibility conditions can be classified into two categories:
  * [Conditions without field dependencies](#visibility-conditions-without-field-dependencies) – determine visibility based solely on external or contextual information and custom logic.
  * [Conditions with field dependencies](#visibility-conditions-with-field-dependencies) – use other fields in the form to determine visibility.


### Visibility conditions without field dependencies
This type of visibility condition determines field visibility using information external to the form itself (e.g., information about the current user). 
Visibility conditions must inherit from the `VisibilityCondition` base class, and implement the `Evaluate` method. The method must return either `true` – indicating that the field is visible, or `false` – indicating that the field is hidden.
C#
**Visibility condition main class**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

public class MyVisibilityCondition : VisibilityCondition
{
    public override bool Evaluate(IFormFieldValueProvider formFieldValueProvider)
    {
        // Visibility evaluation
    }
}
```

Visibility conditions can have configuration properties that modify how the condition is evaluated (e.g., a “case sensitive” property for a condition that evaluates a text value). In this case, the condition class must inherit from the `VisibilityCondition<TProperties>` base class, where `TProperties` holds the configuration properties. This properties class must inherit from `VisibilityConditionProperties`.
Conditions intended for [use in the field editor](#register-visibility-conditions-into-the-field-editor) must have an [editing component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) assigned to properties in the `VisibilityConditionProperties` class. The editing components provide the editing UI for configuration properties in the field editor.
C#
**Visibility condition main class**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

public class MyVisibilityCondition : VisibilityCondition<MyVisibilityConditionProperties>
{
    public override bool Evaluate(IFormFieldValueProvider formFieldValueProvider)
    {
        // Visibility evaluation

        // The condition's properties can be accessed via 'VisibilityCondition<TProperties>.Properties'
        string value = Properties.StringProperty;
    }
}
```

C#
**Visibility condition properties class**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;
using Kentico.Xperience.Admin.Base.FormAnnotations;

public class MyVisibilityConditionProperties : VisibilityConditionProperties
{
    // Properties required for the functionality of the condition

    // Assign editing components to allow configuration of the properties in the field editor UI

    [NumberInputComponent(Label = "Number", Order = 1)]
    public int IntProperty { get; set; }

    [TextInputComponent(Label = "Text", Order = 2)]
    public string StringProperty { get; set; }
}
```

To make the condition usable, you need to do at least one of the following:
  * [Register the condition](#register-visibility-conditions-into-the-field-editor) to make it available in the [field editor](/documentation/developers-and-admins/customization/field-editor).
  * Create a corresponding attribute class, which can be used to [assign the condition to properties in model-based forms](#assign-visibility-conditions-using-attributes).


### Visibility conditions with field dependencies
This type of condition uses inputs from other fields in the form to determine a field’s visibility. 
To implement a visibility condition with field dependencies:
  1. [Create a regular visibility condition](#visibility-conditions-without-field-dependencies)
  2. [Add support for field dependencies](#add-support-for-field-dependencies)
  3. [Access field values when evaluating visibility](#access-field-values)
  4. Enable the condition for: 
     * [the field editor](#register-visibility-conditions-into-the-field-editor)
     * [model-based forms](#assign-visibility-conditions-using-attributes)


**Visibility condition performance**
Consider the performance impact of your code when implementing the evaluation logic for visibility conditions with field dependencies. Such conditions are re-evaluated upon every change in the value of the linked fields.
#### Add support for field dependencies
Conditions with dependencies need to locally store the names of the fields or model-based form properties on which they depend. The system uses this information to mark fields on which at least one visibility condition depends. A change in a marked field’s input triggers a re-evaluation of all related visibility conditions.
For this purpose, the `VisibilityCondition<TProperties>` base class contains the virtual `DependsOnFields` property, which holds the collection of field names as `IEnumerable<string>`. This property needs to be overridden in the class implementing the visibility condition and assigned a collection of field names on which the visibility evaluation depends. When [assigning the visibility condition using an attribute](#assign-visibility-conditions-using-attributes), the field names must be provided via configuration properties.
The Xperience API provides native support for conditions that depend on **one** other field via the following classes:
  * `VisibilityConditionWithDependencyProperties` – visibility condition properties class with one predefined property – `PropertyName`. The property holds the name of the field on which the visibility evaluation depends. 
C#
**Example - VisibilityConditionWithDependencyProperties usage**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

public class MyVisibilityConditionProperties : VisibilityConditionWithDependencyProperties {
    // Additional configuration properties for 'MyVisibilityCondition'
}

// The implementation of the 'VisibilityConditionWithDependencyProperties' class from the Xperience API
public class VisibilityConditionWithDependencyProperties : VisibilityConditionProperties
{
    public string PropertyName { get; set; }
}
```

  * `VisibilityConditionWithDependency<TProperties>` – extends the `VisibilityCondition<TProperties>` class with support for conditions that depend on a single other field. Intended to be used together with `VisibilityConditionWithDependencyProperties`. Ensures `DependsOnFields` is populated with the name of the dependency provided via `VisibilityConditionWithDependencyProperties.PropertyName`.
C#
**Example - VisibilityConditionWithDependency usage**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

public class MyVisibilityCondition : VisibilityConditionWithDependency<MyVisibilityConditionProperties>
{
}

// The implementation of the 'VisibilityConditionWithDependency<TProperties>' class from the Xperience API
public abstract class VisibilityConditionWithDependency<TProperties> : VisibilityCondition<TProperties>
        where TProperties : VisibilityConditionWithDependencyProperties, new()
{
     // Assigns the name of the property on which the condition depends to the collection
     public override IEnumerable<string> DependsOnFields => Enumerable.Repeat(Properties.PropertyName, 1);
}
```



You can extend this approach if you need to write conditions that depend on multiple other fields – override the `DependsOnFields` property in your visibility condition class, and add the names of the given fields to the collection.
#### Access field values
Visibility conditions can only depend on fields that precede the currently evaluated field in the given form. For example, if a form comprises inputs **A** , **B** , **C** a visibility condition assigned to **C** can only depend on fields **A** and **B**. Note that the field order in the resulting form can be influenced by the `Order` property specified when assigning [editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components).
To work with the values of fields on which the condition depends, the `Evaluate` method provides an instance of `IFormFieldValueProvider`. The provider enables access to the values of all preceding form field inputs. For example, if a form comprises inputs **A** , **B** , **C** and visibility is currently being evaluated on **C** , `IFormFieldValueProvider` can be used to access the values of **A** and **B**.
To get field values, use:
  * `TryGet<TType>(string fieldName, out var fieldValue)` – returns the value of the given field as `TType`


where `fieldName` is the name of the dependency from which to retrieve the value (provided via a configuration property – e.g., `VisibilityConditionWithDependencyProperties.PropertyName`).
C#
**Example - VisibilityConditionWithDependency usage**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

public class MyVisibilityCondition : VisibilityConditionWithDependency<MyVisibilityConditionProperties>
{
    public override bool Evaluate(IFormFieldValueProvider formFieldValueProvider)
    {
        // Gets the value of the field as object
        formFieldValueProvider.TryGet<string>(Properties.PropertyName, out var fieldValue);

        // Use the value in visibility evaluation...
    }
}

public class MyVisibilityConditionProperties : VisibilityConditionWithDependencyProperties
{
    // Additional configuration properties for 'MyVisibilityCondition'
}
```

## Register visibility conditions into the field editor
Use the `RegisterFormVisibilityCondition` assembly attribute to register visibility conditions. Registration is required for the condition to be available in the [field editor](/documentation/developers-and-admins/customization/field-editor). When registering a condition, specify the following parameters:
  * **Identifier** – a unique `string` identifier of the visibility condition. 
    * We recommend using a unique prefix in your identifiers to prevent conflicts when deploying components to other projects. For example, use prefixes matching your company’s name.
    * You can store the identifier within a constant in the visibility condition class.
  * **ConditionType** – the  _System.Type_ of the visibility condition class.
  * **Name** – sets the name displayed when adding visibility conditions via the field editor.


For [visibility conditions with field dependencies](#visibility-conditions-with-field-dependencies), you also need to set the **TargetFieldType** property of the `RegisterFormVisibilityCondition` attribute. The value must be a C# [data type](/documentation/developers-and-admins/customization/field-editor/data-type-management) that determines which type of fields are compatible as dependencies. For example, if `TargetFieldType` is set to `typeof(string)`, the registered condition becomes available if the dependency is on a _Text_ , _Long text_ or _Rich text (HTML)_ field.
C#
**Visibility condition registration**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

// Registers a custom visibility condition with a field dependency
[assembly: RegisterFormVisibilityCondition(
    identifier: "Acme.CustomVisibilityCondition",
    conditionType: typeof(CustomVisibilityCondition),
    name: "Condition name",
    // Sets a data type that limits which fields are compatible as dependencies for the condition
    TargetFieldType = typeof(string)
)]
```

The visibility condition is now registered in the system. Users can assign the condition when configuring the visibility of fields not marked as _Required_ via the field editor for content types and reusable field schemas. Each field can have **one** visibility condition.
## Assign visibility conditions using attributes
Visibility conditions are supported in dynamically created configuration dialogs of [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) and [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) components and other admin UI components:
  * Page Builder 
    * widget configuration and personalization
    * section configuration
    * page template configuration
  * Form Builder 
    * form component configuration
    * form section configuration
  * Admin UI 
    * form component configuration
    * validation rule configuration
    * visibility condition configuration


In these scenarios, visibility conditions are assigned using [attribute](https://docs.microsoft.com/en-us/dotnet/csharp/tutorials/attributes) notation to the properties of model classes defining the configuration dialogs of supported components. For example:
C#
**Example - visibility condition usage in widget properties**
Copy
```
using Kentico.PageBuilder.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;

public class BannerWidgetProperties : IWidgetProperties
{
    ...

    [CheckboxInputComponent(Label = "Show link", Order = 1)]
    public bool ShowLink { get; set; }

    [TextInputComponent(Label = "Link URL", Order = 2, WatermarkText = "Enter a URL...")]
    // The 'VisibleIfTrue' condition only shows link-related properties in the dialog if 'ShowLink' is selected
    [VisibleIfTrue(nameof(ShowLink))]
    public string LinkUrl { get; set; }

    [TextInputComponent(Label = "Link title", Order = 3)]
    // The 'VisibleIfTrue' condition only shows link-related properties in the dialog if 'ShowLink' is selected
    [VisibleIfTrue(nameof(ShowLink))]
    // The 'VisibleIfNotEmpty' condition hides 'LinkTitle' from the properties dialog until 'LinkUrl' is provided
    [VisibleIfNotEmpty(nameof(LinkUrl))]
    public string LinkTitle { get; set; } = String.Empty;
}
```

**Multiple visibility conditions**
A single property can have multiple visibility condition attributes assigned. The property is visible if **all** added visibility conditions are fulfilled.
However, be mindful not to create contradictory rulesets that could make the field permanently hidden.
### Create visibility condition attributes
Each visibility condition that you wish to assign to properties in code **must** have a corresponding [attribute](https://docs.microsoft.com/en-us/dotnet/csharp/tutorials/attributes). When the attribute is used to annotate a property of one of the supported components, the condition applies to that property’s input.
Visibility condition attribute classes must inherit from `VisibilityConditionAttribute` or derived classes:
  * `VisibilityConditionAttribute` – for rules that depend only on data that can be obtained contextually (e.g., a user’s assigned role).
  * `VisibilityConditionWithDependencyAttribute` – for rules that [depend on a single other field in the configuration dialog](#visibility-conditions-with-field-dependencies) (e.g., the example snippet above). The class extends the base `VisibilityConditionAttribute` class with a property that holds the name of the field on which the condition depends. You can adapt this pattern and add other properties to transfer field names for conditions with multiple dependencies.


Declare all mandatory and optional properties of the corresponding visibility condition as properties of the attribute class. The properties:
  * **must be named identically** to the properties in the visibility condition class. The system uses the property names to instantiate the corresponding condition.
  * **must be of the same type** as the corresponding property in the visibility condition.


Properties that are required for the condition to function can be forced via the attribute’s constructor.
C#
**Example - sample visibility condition attribute**
Copy
```
using Kentico.Xperience.Admin.Base.FormAnnotations;

public class ValueIsBetweenVisibilityConditionAttribute : VisibilityConditionWithDependencyAttribute
{
    public int Maximum { get; set; }

    public int Minimum { get; set; }

    // Use the attribute constructor to force properties required for the correct
    // functionality of the visibility condition
    public ValueIsBetweenVisibilityConditionAttribute(string propertyName,
              int max, int min) : base(propertyName)
    {
        Maximum = max;
        Minimum = min;
    }
}
```

To indicate the relation between the attribute class and the corresponding visibility condition, decorate the visibility condition class with the `VisibilityConditionAttribute` attribute.
C#
**Example - bind visibility condition and corresponding attribute**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

[VisibilityConditionAttribute(typeof(ValueIsBetweenVisibilityConditionAttribute))]
public class ValueIsBetweenVisibilityCondition : VisibilityConditionWithDependency<TProperties>
```

For the example above, every property decorated with `ValueIsBetweenVisibilityConditionAttribute` is assigned the `ValueIsBetweenVisibilityCondition` condition. 
## Default visibility conditions
By default, the system provides a set of visibility conditions for the [field editor](/documentation/developers-and-admins/customization/field-editor), which assess the value of another field (_Equal to_ , _Contains_ , _Empty_ , etc.). The available conditions depend on the data type of the target field.
Additionally, the Xperience API includes the following visibility conditions for model-based forms, which you can [assign to properties via attributes](#assign-visibility-conditions-using-attributes). All visibility condition attributes are located in the `Kentico.Xperience.Admin.Base.FormAnnotations` namespace.
Visibility condition |  Attribute |  Description  
---|---|---  
Visible if B empty |  VisibleIfEmptyAttribute |  The decorated property A is visible if property B is empty (an empty string, an empty collection, or `null`).   
Visible if B not empty |  VisibleIfNotEmptyAttribute |  The decorated property A is visible if property B is not empty.  
Visible if B equal to |  VisibleIfEqualToAttribute |  The decorated property A is visible if property B is equal to the specified string or object.  
Visible if B not equal to |  VisibleIfNotEqualToAttribute |  The decorated property A is visible if property B is not equal to the specified string or object.  
Visible if B true |  VisibleIfTrueAttribute |  The decorated property A is visible if property B is (boolean) `true`.  
Visible if B false |  VisibleIfFalseAttribute |  The decorated property A is visible if property B is (boolean) `false`.  
## Examples
### Is administrator condition
The following example demonstrates the implementation of a visibility condition that checks if the user viewing the editing form belongs to the system’s default _Administrator_ [role](/documentation/developers-and-admins/configuration/users/role-management). Such a condition doesn’t depend on any other fields, and doesn’t require any configuration properties. As a result, we only need to create a single class inheriting from `VisibilityCondition`.
C#
**Is admin visibility condition class**
Copy
```
using Kentico.Xperience.Admin.Base.Authentication;
using Kentico.Xperience.Admin.Base.Forms;

// Registers the visibility condition
[assembly: RegisterFormVisibilityCondition(
    identifier: IsAdminCondition.IDENTIFIER,
    conditionType: typeof(IsAdminCondition),
    name: "Is administrator"
)]

public class IsAdminCondition : VisibilityCondition
{
    internal const string IDENTIFIER = "IsAdminVisibilityCondition";

    private readonly IAuthenticatedUserAccessor userAccessor;

    public IsAdminCondition(IAuthenticatedUserAccessor userAccessor)
    {
        this.userAccessor = userAccessor;
    }

    public override bool Evaluate(IFormFieldValueProvider formFieldValueProvider)
    {
        // Gets the current user in the administration
        var currentUser = userAccessor.Get().Result;

        // Visible if the current user belongs to the 'administrator' system role
        return currentUser.IsAdministrator();
    }
}
```

The “Is administrator” condition is now available in the field editor for any fields not marked as _Required_. If you wish to use the condition for fields in model-based forms, you also need to [implement an appropriate attribute](#create-visibility-condition-attributes).
### Value is between condition
The following example shows how to implement a visibility condition that checks if the value entered into another field falls into the specified integer interval. Based on the result, the condition displays or hides its assigned field accordingly. The condition will be usable both in the field editor UI and in model-based forms via an attribute.
From the task definition, we know that we need to create a visibility condition that depends on **one** other field. To achieve this, we can use `VisibilityConditionWithDependency<TProperties>` and `VisibilityConditionWithDependencyProperties` provided by the Xperience API. The classes ensure our visibility condition gets correctly configured, leaving us to focus on the implementation of the `Evaluate` method and required configuration properties.
The condition’s properties class needs to store two values – the  _lower_ and _upper_ boundary of the accepted interval. If the input falls outside this interval, the field to which this condition is assigned is hidden. We’ll also decorate the properties with the `NumberInputComponent`[editing component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) attribute, which allows users to input a number when configuring the visibility condition in the field editor UI.
C#
**Properties class**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;
using Kentico.Xperience.Admin.Base.FormAnnotations;

public class ValueIsBetweenVisibilityConditionProperties : VisibilityConditionWithDependencyProperties
{
    [NumberInputComponent(Label = "Min", Order = 1)]
    public int Minimum { get; set; }

    [NumberInputComponent(Label = "Max", Order = 2)]
    public int Maximum { get; set; }
}
```

Within the condition’s main class, we implement the `Evaluate` method, access the value of the field on which the condition depends (using the field name provided when configuring the condition) via `IFormFieldValueProvider` and use it to evaluate the condition.
We’ll also add an `IDENTIFIER` constant for the condition and [register it into the field editor](#register-visibility-conditions-into-the-field-editor) using the `RegisterFormVisibilityCondition` attribute.
C#
**Visibility condition class**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

// Registers the custom visibility condition
[assembly: RegisterFormVisibilityCondition(
    identifier: ValueIsBetweenVisibilityCondition.IDENTIFIER,
    conditionType: typeof(ValueIsBetweenVisibilityCondition),
    name: "Value is between",
    TargetFieldType = typeof(int)
)]

public class ValueIsBetweenVisibilityCondition : VisibilityConditionWithDependency<ValueIsBetweenVisibilityConditionProperties>
{
    internal const string IDENTIFIER = "ValueIsBetweenVisibilityCondition";

    public override bool Evaluate(IFormFieldValueProvider formFieldValueProvider)
    {
        // Gets the value of the field on which the condition depends
        formFieldValueProvider.TryGet<int>(Properties.PropertyName, out var dependeeField);

        return (dependeeField >= Properties.Minimum) && (dependeeField <= Properties.Maximum);
    }
}
```

Now we need to create the corresponding attribute to enable [usage in component model classes](#assign-visibility-conditions-using-attributes).
C#
**ValueIsBetween attribute class**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;
using Kentico.Xperience.Admin.Base.FormAnnotations;

public class ValueIsBetweenVisibilityConditionAttribute : VisibilityConditionWithDependencyAttribute
{
    public int Maximum { get; set; }

    public int Minimum { get; set; }

    // The 'VisibilityConditionWithDependencyAttribute' base class enforces the field dependency via the constructor
    public ValueIsBetweenVisibilityConditionAttribute(string propertyName,
             int max, int min) : base(propertyName)
    {
        Maximum = max;
        Minimum = min;
    }
}
```

With the attribute created, add `VisibilityConditionAttribute` with the attribute type as the parameter to the condition’s main class to tie the attribute and visibility condition together.
C#
**Visibility condition and attribute relationship**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

[VisibilityConditionAttribute(typeof(ValueIsBetweenVisibilityConditionAttribute))]
public class ValueIsBetweenVisibilityCondition : VisibilityConditionWithDependency<ValueIsBetweenVisibilityConditionProperties>
```

The visibility condition is now ready to be used.
![]()
[]()[]()
