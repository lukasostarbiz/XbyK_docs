# Form Builder validation rules
  * [ Copy page link ](documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules#) | [Get HelpService ID](documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules#)
Core MVC 5


[✖](documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules#)
Validation rules restrict the values that can be submitted into [form](documentation/business-users/digital-marketing/forms) fields. For example, validation can limit the maximum allowed text length or the highest number a user can enter into a field.
To learn how to add validation rules to fields in the Form Builder interface, see [Field validation](documentation/business-users/digital-marketing/forms/create-and-edit-forms#field-validation).
The system provides a basic set of validation rules by default, and also allows developers to define custom rules suitable for project-specific scenarios.
**Validation of empty values**
The system does not trigger validation rules for form fields that are submitted with an unfilled value. If you wish to enforce user input for a particular field, mark it as **Required** in the field’s [properties dialog](documentation/business-users/digital-marketing/forms/create-and-edit-forms#field-properties).
## Define custom validation rules
Classes related to Form Builder validation rules are located in the `Kentico.Forms.Web.Mvc` namespace.
The system also contains [validation rules for admin UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) with classes in the `Kentico.Xperience.Admin.Base.Forms` namespace. The two types of validations rules serve different purposes.
To develop a Form Builder validation rule, create the following:
  1. [Properties class](documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules#properties-class) – defines properties that parameterize the validation rule’s behavior and provides the configuration interface for the rule in the Form Builder UI.
  2. [Validation rule class](documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules#validation-rule-class) – contains the rule’s validation logic.


All validation rules must also be [registered](documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules#register-validation-rules) in the system.
Validation rules are **strongly typed**. For example, a rule defined for the `int?` type can only be applied to fields that use a [form component](documentation/developers-and-admins/development/builders/form-builder/form-components) of the corresponding type.
### Properties class
Properties provide configuration options that modify the behavior of the validation rule. For example, a validation rule that enforces a maximum number of characters would have a property that allows form editors to set the maximum text length.
  1. Create a class that inherits from `FormValidationRuleProperties`.
  2. Add a property to the class for each configuration option.
  3. Define the interface for the configuration options by decorating the properties with the attributes of corresponding [admin UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components). 
     * See [Editing components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) to learn more.


C#
Copy
```
using Kentico.Forms.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;

// Validation rule properties class with a text and integer configuration option
public class CustomFormValidationRuleProperties : FormValidationRuleProperties
{
    [TextInputComponent(Label = "Text value", Order = 0)]
    // Marked as required, so the rule cannot be added without setting a value for the property
    [RequiredValidationRule]
    public string RuleTextProperty { get; set; }

    [NumberInputComponent(Label = "Number value", Order = 1)]
    [RequiredValidationRule]
    public int RuleNumberProperty { get; set; }
}
```

### Validation rule class
The validation rule class contains the core validation logic that evaluates whether a submitted field value meets the rule’s requirements.
  1. Create a validation rule class that inherits from `SingleFieldFormValidationRule<TProperties, TValue>`. 
     * Substitute the `TProperties` generic with the type of the rule’s [properties class](documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules#properties-class).
     * Substitute the `TValue` generic with the `System.Type` of the [form components](documentation/developers-and-admins/development/builders/form-builder/form-components) for which the rule is applicable (e.g., basic data types such as `string` or `int`). Nullable types such as `int?` are supported. For example, the [default Form Builder form components](documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components) for numeric inputs use nullable types.
  2. Override the `Validate` method and implement the rule’s validation logic: 
     * The `value` parameter contains the submitted value of the field.
     * Return `true` to indicate that the value is valid or `false` if the validation fails.
  3. (Optional) Override the `GetDefaultErrorMessage` method to set the rule’s default error message. 
     * The default error message is used if another message is not entered when applying the rule to a specific form field.
     * The default error message text can be [localized](documentation/developers-and-admins/customization/admin-ui-localization) using `ILocalizationService`.
     * The error message (default or field-specific) is displayed to users when they attempt to submit an invalid value, and also in the list of applied validation rules when viewing field configuration in the Form Builder interface. For example: “The value must be between 1 and 100”.
C#
Copy
```
using Kentico.Forms.Web.Mvc;

using System.Threading;
using System.Threading.Tasks;

public class CustomFormValidationRule : SingleFieldFormValidationRule<CustomFormValidationRuleProperties, string>
{
    // Defines the rule's validation logic
    protected override Task<bool> Validate(string value, CancellationToken cancellationToken)
    {
        return Task.FromResult(true);
    }

    // Sets the error message used if one is not specified when applying the rule to a specific form field
    // Allows you to generate a message containing the specified rule property values
    public override Task<string> GetDefaultErrorMessage()
    {
        return Task.FromResult("Validation failed. Please adjust the value.");
    }
}
```

You can access the values of the rule’s configurable [properties](documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules#properties-class) through the `Properties` property of the validation rule base class. 
For a more complete implementation of a validation rule, see the [Example](documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules#example). 


## Register validation rules
To register a validation rule, use the `RegisterFormBuilderValidationRule` assembly attribute. This attribute ensures the validation rule is recognized by the system and available in the [Form Builder interface](documentation/business-users/digital-marketing/forms/create-and-edit-forms). When registering rules, specify the following parameters for the attribute:
  * **identifier** – a unique `string` identifier of the validation rule. We recommend using a unique prefix in your identifiers to prevent conflicts when deploying components to other projects. For example, use prefixes matching your company’s name.
  * **ruleType** – the `System.Type` of the [validation rule class](documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules#validation-rule-class).
  * **name** – the name displayed when selecting validation rules for form fields in the administration interface.
  * (Optional) **description** – a user-friendly description of the validation rule. Displayed when adding validation rules in the administration interface.


Both the **name** and the **description** parameters can be [localized](documentation/developers-and-admins/customization/admin-ui-localization) using resource string keys. Use a string value in format `{$my.resource.string.key$}`.
C#
**Validation rule registration example**
Copy
```
using Kentico.Forms.Web.Mvc;

// Registers the validation rule
[assembly: RegisterFormBuilderValidationRule(
    identifier: "Acme.CustomValidationRule",
    ruleType: typeof(CustomValidationRule),
    name: "Custom validation",
    description: "Contains custom validation logic.")]
```

The validation rule is now registered in the system. Users can include it when [configuring field validation](documentation/business-users/digital-marketing/forms/create-and-edit-forms#field-validation) on the **Form Builder** tab of the **Forms** application.
## Example
The following example demonstrates the implementation of a validation rule that checks if a submitted number lies within a specified closed interval. The example implements the rule for fields using a [form component](documentation/developers-and-admins/development/builders/form-builder/form-components) with the `int?` type:
First, create a properties class that allows users to configure the bounds of the interval when adding the validation rule:
C#
**Validation rule properties class**
Copy
```
using Kentico.Forms.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;

public class IntervalFormValidationRuleProperties : FormValidationRuleProperties
{
    // Property for setting the minimum value of the interval
    // Marked as required, so the rule cannot be added without setting a value
    // Must be at least 1 in this example
    [NumberInputComponent(Label = "Minimum value}", Order = 0)]
    [RequiredValidationRule]
    [MinimumIntegerValueValidationRule(1)]
    public int MinimumValue { get; set; }

    // Property for setting the maximum value of the interval
    // Marked as required, so the rule cannot be added without setting a value
    [NumberInputComponent(Label = "Maximum value", Order = 1)]
    [RequiredValidationRule]
    public int MaximumValue { get; set; }
}
```

Continue by creating the validation rule class with the required logic, together with the attribute that [registers](documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules#register-validation-rules) the rule into the system.
C#
**Validation rule class**
Copy
```
using Kentico.Forms.Web.Mvc;

using System.Threading;
using System.Threading.Tasks;

// Registers the validation rule
[assembly: RegisterFormBuilderValidationRule(
    identifier: "Acme.IntervalValidationRule",
    ruleType: typeof(IntervalFormValidationRule),
    name: "Interval validation",
    description: "Checks whether the input lies within the specified closed interval.")]

public class IntervalFormValidationRule :
    SingleFieldFormValidationRule<IntervalFormValidationRuleProperties, int?>
{
    // Defines the rule's validation logic
    protected override Task<bool> Validate(int? value, CancellationToken cancellationToken)
    {
        // Validation is not performed if the value is null
        // To enforce user input for a particular field, mark the field as 'Required' instead of using validation rules
        if (value is null)
        {
            return Task.FromResult(true);
        }

        // Returns true if the component's value lies between the specified bounds
        return Task.FromResult((Properties.MinimumValue <= value) && (value <= Properties.MaximumValue));
    }

    // Sets the error message used if one is not specified when applying the rule to a specific form field
    public override Task<string> GetDefaultErrorMessage()
    {
        return Task.FromResult($"The value must lie between [{Properties.MinimumValue};{Properties.MaximumValue}].");
    }
}
```

The rule is now available when configuring validation in the Form Builder interface for fields based on a form component with the `int?` type. When applied, the rule ensures that the given form can only be submitted if the field contains a number within the specified interval.