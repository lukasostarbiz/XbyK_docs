---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/builder-component-properties-visibility-and-validation
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * Builder component properties visibility and validation 


# Builder component properties visibility and validation
Similar to how you assign [editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) to Page, Email, or Form Builder component properties ([widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties), [sections](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder/section-properties), [personalization conditions](/documentation/developers-and-admins/digital-marketing-setup/content-personalization/develop-personalization-condition-types), etc.), the API also allows you to set validation rules and visibility conditions that apply to the corresponding configuration dialog. Properties can have multiple validation rules or visibility conditions assigned this way.
  * **Validation rules** are used to constrain user input to ensure that only data in the desired format are provided.
By default, the system provides a set of validation rules that covers some of the most common use cases – required input, email address, minimum and maximum text length. For the full list, see [Default validation rules](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules#default-validation-rules). In addition, the system allows you to develop [custom validation rules](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) suitable for your scenarios.
  * **Visibility conditions** control how and when properties are displayed to users in the configuration interface (dialog or panel).
By default, the system provides a set of conditions that determine visibility based on another property of the same component. For example, you can have a property setting a widget’s heading text and make it visible only when editors enable another checkbox property to actually display a widget heading. For the full list of visibility conditions provided by the system, see [Default visibility conditions](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions#default-visibility-conditions). In addition, the system allows you to develop [custom visibility conditions](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions) suitable for your scenarios.
Visibility conditions are restricted only to properties managed by editing components. They are NOT applicable to widget properties that users manage through an inline editor.


## Add validation rules
Use the following process to add validation rules to the properties of a component:
  1. Open your project in Visual Studio.
  2. Edit the class containing the component’s properties.
  3. Identify properties where validation is desirable.
  4. Decorate the properties with attributes that correspond to the desired validation rules. See [Default validation rules](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules#default-validation-rules). 
     * You can add multiple validation rule attributes to a single property.


The given component’s configuration dialog now validates user input according to the assigned validation rules.
C#
**Examples - validation rules**
Copy
```
using Kentico.Xperience.Admin.Base.FormAnnotations;

...

[NumberInputComponent(Order = 1, Label = "Age")]
[RequiredValidationRule]
public int Age { get; set; }

// Textbox property for entering a city value
[TextInputComponent(Order = 2, Label = "City")]
[MaxLengthValidationRule(20)]
public string City{ get; set; }
```

## Add visibility conditions
Use the following process to add visibility conditions to the properties of a component:
  1. Open your project in Visual Studio.
  2. Edit the class containing the component’s properties (property model, etc.).
  3. Identify the property that you want to hide or display based on a condition.
  4. Decorate the property using the attribute that corresponds to the desired visibility condition. See [Default visibility conditions](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions#default-visibility-conditions). 
     * You can add multiple visibility condition attributes to a single property. The property is visible if **all** added visibility conditions are fulfilled.
  5. Pass the name of the property on which the condition depends (use the [nameof](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/nameof) expression) into the attribute’s constructor.


The given component’s configuration dialog now displays properties based on the assigned visibility conditions.
**Property order requirement**
Visibility dependencies between properties are restricted by the order in which the properties are displayed in the configuration dialog. Properties can only depend on properties with a lower order, i.e. those that are shown before them in the dialog.
C#
**Examples - visibility conditions**
Copy
```
using System;
using Kentico.Xperience.Admin.Base.FormAnnotations;

...

// Checkbox property that controls whether the widget displays advanced properties
[CheckBoxComponent(Order = 0, Label = "Show advanced")]
public bool ShowAdvanced { get; set; }

// Textbox property for entering the widget's heading text
// Visible only if the widget's advanced properties are displayed
[TextInputComponent(Order = 1, Label = "Heading text")]
[VisibleIfTrue(nameof(ShowAdvanced))]
public string HeadingText { get; set; }

// Textbox property for entering a city value
[TextInputComponent(Order = 2, Label = "City")]
public string City{ get; set; }

// Checkbox property indicating whether the widget offers express shipping
// Visible only if the entered city is "New York" and the widget's advanced properties are displayed
[CheckBoxComponent(Order = 3, Label = "Offer express shipping")]
[VisibleIfTrue(nameof(ShowAdvanced))]
[VisibleIfEqualTo(nameof(City), "New York", StringComparison.OrdinalIgnoreCase)]
public bool ExpressShipping { get; set; }
```

![]()
[]()[]()
