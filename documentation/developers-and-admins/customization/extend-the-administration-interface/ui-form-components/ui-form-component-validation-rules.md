---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Extend the administration interface](/documentation/developers-and-admins/customization/extend-the-administration-interface)
  * [UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components)
  * UI form component validation rules 


# UI form component validation rules
Validation rules verify that the data users enter in a form meets the standards you specify. 
A validation rule contains an expression that evaluates the values of one or more form fields, and returns the validation result. A form cannot be submitted until user input passes all validation rules assigned to individual input fields.
Each validation rule also carries an error message that is displayed to users when their input fails to pass the given rule’s validation logic.
Default Xperience installations come with a set of validation rules ready to be used immediately. Additionally, the admin UI customization framework allows you to define custom rules suitable for your use cases and scenarios.
## Define validation rules
**Validation rule namespaces**
Unless specified otherwise, all classes and members mentioned on this page can be found in the following namespaces:
  * `Kentico.Xperience.Admin.Base.Forms`
  * `Kentico.Xperience.Admin.Base.FormAnnotations`


Do **not** use classes from the `Kentico.Forms.Web.Mvc` namespace when working with UI form component validation rules. This namespace contains obsolete classes with matching names (e.g., `ValidationRule`, `RegisterFormValidationRule`), as well as classes for [Form Builder validation rules](/documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules), which serve a different purpose.
In Xperience, each validation rule consists of a main class that contains the rule’s validation logic – implemented in the rule’s `Validate` method – and two properties classes – one to enable client-side validation and the other to allow for the rule’s configuration (via the [field editor](/documentation/developers-and-admins/customization/field-editor) or [custom attributes](#assign-validation-rules-using-attributes)).
Validation rules are **strongly typed**. A rule defined for _decimal_ value types can only be applied to fields that use [form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) of the corresponding type. The type must match exactly, including [nullability](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-value-types) for value types.
A validation rule consists of:
  1. [Back end definition](#validation-rule-back-end) – used to define server-side validation logic and rule configuration. Located in the ASP.NET Core project.
  2. [TypeScript component](#validation-rule-front-end) – used for client-side validation. Located in JavaScript modules that plug into the React admin app.


Validation rule architecture is summarized by the following diagram:
[![Validation rule architecture](/docsassets/documentation/ui-form-component-validation-rules/Architecture.png)](/docsassets/documentation/ui-form-component-validation-rules/Architecture.png)
### Validation rule back end
Validation rules must inherit from the `ValidationRule<TProperties, TClientProperties, TType>` base class, where
  * `TProperties` – holds properties required for the functionality of the rule. The class must inherit from the `ValidationRuleProperties` base class. When creating the class, you must also implement the `GetDescriptionText` method. The method must return a string containing the description of the validation rule. This information is displayed when assigning validation rules to fields via the [field editor](/documentation/developers-and-admins/customization/field-editor).  
[![Validation rule description](/docsassets/documentation/ui-form-component-validation-rules/RuleDescriptionText.png)](/docsassets/documentation/ui-form-component-validation-rules/RuleDescriptionText.png)
C#
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

public class MyValidationRuleProperties : ValidationRuleProperties
{
    // Properties used to configure the rule

    // Sets validation rule description
    public override string GetDescriptionText(ILocalizationService localizationService)
            => $"Validates...";
}
```

  * `TClientProperties` – used to store and send validation rule metadata (validation status, error message) and properties to the client-side portion of the rule. Must inherit from the `ValidationRuleClientProperties` base class. Typically contains a subset of properties from `TProperties` suitable for use during client-side validation. If the validation rule doesn’t require any data on the client, this class can be omitted. Instead, make the validation rule main class inherit from `ValidationRule<TProperties, TType>`, which implicitly uses the `ValidationRuleClientProperties` base class.
    * This class serves purely as a data transfer object and should not contain additional logic.
C#
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

public class MyValidationRuleClientProperties : ValidationRuleClientProperties
{
    // Properties sent to the client
}
```

  * `TType` – the data type for which the rule is available (e.g., `string`, `int`, or custom types). For an overview of default data types registered in the system, see [Data type management](/documentation/developers-and-admins/customization/field-editor/data-type-management). For rules applicable to all types, use object.
    * Based on the specified type, the rule is offered when adding validation to fields of the corresponding data type via the [field editor](/documentation/developers-and-admins/customization/field-editor).
    * If you don’t need the rule available within the field editor, you can use any suitable data type as long as it matches the type of the annotated UI form component (identical to how form component registration functions).
    * The type must exactly match the data type of the annotated UI form component, including [nullability](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-value-types) for value types. To find the data types of the system’s default components, see [Reference - Admin UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).


When inheriting from the class, you need to implement the following abstract members:
  * `ClientRuleName` – property set to the name of the client TypeScript component used to provide client-side validation for the rule. You must provide a full name in the _{orgName}/{projectName}/componentName_ format, as specified when you set up your [custom admin JS module](/documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development). Make sure the component is exported (visible to other modules). For example: _@acme/web-admin-custom/ValidationRuleComponent_
  * `Validate` – method that contains the rule’s validation logic. User input is stored in the method’s `value` parameter. Returns a `ValidationResult` object that contains the validation result and, optionally, a customized [error message](#validation-error-messages). The object’s constructor expects the following parameters: 
    * `isValid` – a boolean value indicating the result of the validation.
    * `errorMessage` – optional error message that can be returned from the server. See [Validation error messages](#validation-error-messages) for more information.
  * `ConfigureClientProperties` – method that configures the properties object sent to the client component. 
    * Properties from the `TProperties` class are accessible via the `Properties` member. When `ConfigureClientProperties` is called, `TProperties` is populated with configured values.


C#
**Validation rule back end**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

public class MyValidationRule : ValidationRule<MyValidationRuleProperties, MyValidationRuleClientProperties, TType>
{
    public override string ClientRuleName => "@kentico/xperience-admin-base/ValidationRuleComponent";

    protected override Task ConfigureClientProperties(TClientProperties clientProperties)
    {
        // Place to map properties sent to the client

        return base.ConfigureClientProperties(clientProperties);
    }

    public override Task<ValidationResult> Validate(TType value, IFormFieldValueProvider formFieldValueProvider)
    {
        // Validation logic
        bool result = ...
        // 'value' contains the input submitted by the user

        if (result)
        {
            return ValidationResult.SuccessResult();
        }

        string errorMessage = "Error message...";
        return ValidationResult.FailResult(errorMessage);
    }
}
```

**Dependency injection support**
Validation rules support constructor [dependency injection](/documentation/developers-and-admins/development/website-development-basics/dependency-injection) by default. You can use dependency injection to resolve instances of external services on which your rule depends.
C#
**Using constructor DI to resolve an instance of ILocalizationService**
Copy
```
private readonly ILocalizationService localizationService;
...
public MyValidationRule(ILocalizationService localizationService)
{
    this.localizationService = localizationService;
}
```

* * *
**Access the current form context**
You can access the `FormContext` property of the `ValidationRule` base class to evaluate validation rules based on the context where the UI form is displayed. For example, you can limit a validation rule to be used only in the UI forms of content items and use the content item context:
C#
**Example - Evaluating the context of the current UI form**
Copy
```
var contentItemFormContext = FormContext as IContentItemFormContextBase;
if (contentItemFormContext == null)
{
    throw new InvalidOperationException("The validation rule can only be used in a content item form context.");
}

// Retrieves the ID of the currently edited content item
int contentItemId = contentItemFormContext.ItemId;
```

During server-side validation, each validation rule assigned to a field is evaluated independently. Errors are collected and sent to the client, where they can be accessed under `FormComponentProps.validationMessage`.
#### Validation error messages
Validation rules provide several ways to specify error messages. When more than one is set, the system uses a priority system to determine which one is displayed. The priority order is as follows: 
  1. Error message specified when returning a `ValidationResult` from the `Validate` method. Prioritized above all others.
  2. Error message specified using the `ErrorMessage` property of the rule’s `TProperties` class (defined in the `ValidationRuleProperties` base class). Prioritized above the default error message.
  3. A default error message specified using the `ValidationRule.DefaultErrorMessage` property. Used when no other error message is set. The property can be overridden from the derived validation rule class and used to set a suitable default error message.
C#
**Customizing the default error message**
Copy
```
protected override string DefaultErrorMessage => "My default error message";
```



Additionally, `ValidationRuleProperties.ErrorMessage` and `ValidationRule.DefaultErrorMessage` can use the `ValidationRule.ErrorMessageFormatter` property to format their output. The property stores a delegate that is used to format either `ErrorMessage` or `DefaultErrorMessage` before the string is sent to the client.
C#
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

public class MyValidationRule : ValidationRule<MyValidationRuleProperties, MyValidationRuleClientProperties, TType>
{
    protected override Func<string, string> ErrorMessageFormatter => (errorMessage) => String.Format(errorMessage, Properties.RuleConfiguration);
}
```

This approach can be useful if you need to reflect rule configuration (dynamically changing values) in preset strings.
Error messages returned directly via the `ValidationResult` object are considered formatted and localized – the formatter is not used on them. 
### Validation rule front end
The client part of a validation rule is a TypeScript implementation that ensures client-side validation.
Inherit from `ValidationRule<TValidationRuleProps, TType>`, where
  * `TValidationRuleProps` – a class that extends `ValidationRuleProps`. This class is the client-side mirror of the rule’s back end `TClientProperties` class and therefore needs to declare the same set of properties. If you aren’t sending additional properties to the client, the base `ValidationRuleProps` class can be used instead.
  * `TType` – the type of the input that is being validated (`string`, `int`, custom types).


and return an object with the following properties:
  * `isValid: boolean` – indicates the result of client-side validation.
  * `errorMessage: string | null` – the message to display in case validation failed.


JS
Copy
```
import { ValidationRule, ValidationRuleProps } from '@kentico/xperience-admin-base';

export interface MyValidationRuleProps extends ValidationRuleProps {
    readonly prop1: string
    readonly prop2: number
}

export const MyValidationRule: ValidationRule<MyValidationRuleProps, string> = (props, value) => {
    const validate = (prop1, prop2) => {
        let isValid = true;
        // Validation logic

        return isValid;
    };

    return { isValid: validate(props.prop1, props.prop2), errorMessage: props.errorMessage }
};
```

## Register validation rules
To register a validation rule, use the `RegisterFormValidationRule` assembly attribute. This attribute ensures the validation rule is recognized by the system and available for [use in the field editor](/documentation/developers-and-admins/customization/field-editor). When registering a rule, specify the following parameters:
  * **Identifier** – a unique `string` identifier of the validation rule. We recommend using a unique prefix in your identifiers to prevent conflicts when deploying components to other projects. For example, use prefixes matching your company’s name.
  * **ValidationRuleType** – the  _System.Type_ of the validation rule class.
  * **Name** – used to set the name of the validation rule. Displayed when adding validation rules via the field editor.
  * (Optional) **Description** – sets the validation rule description. Displayed when adding validation rules via the field editor.


The following code snippet demonstrates validation rule registration:
C#
**Validation rule registration**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

[assembly: RegisterFormValidationRule("Acme.Administration.MyVisibilityCondition", typeof(MyVisibilityCondition),
           "Rule name", "Rule description")]
```

The validation rule is now registered in the system. Users can include it when adding field validation via the field editor (e.g., when defining content types and other system objects).
## Validation rules dependent on other inputs
Validation rules can also depend on other inputs in the form.
To construct such rules, the `Validate` method provides an instance of `IFormFieldValueProvider`, which enables access to the values of all preceding form field inputs. For example, if a form comprises inputs **A** , **B** , **C** , and validation is currently running on **C** , `IFormFieldValueProvider` can be used to access the values of **A** and **B**. Therefore, when configuring and assigning such rules, it’s important to keep in mind that they can only depend on inputs that precede the input to which they are assigned. Note that for [editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components), the order of the inputs in the resulting forms can be adjusted via the `Order` property.
Values can be accessed via the following methods:
  * `Get(string fieldName)` – returns the value of the given field as `object`
  * `Get<TType>(string fieldName)` – returns the value of the given field as `TType`


where `fieldName` is the name of the dependent input, which must be provided when configuring the rule (via a configuration property).
## Assign validation rules using attributes
In addition to field input validation, the system supports using validation rules for dynamically created input dialogs of [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) and [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) components and other admin UI components:
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


Within this approach, validation rules are assigned via attribute notation to the properties of model classes defining individual component configuration dialogs. For example:
C#
**Assigning the 'Required' validation rule in a widget properties class**
Copy
```
using System.Collections.Generic;

using CMS.Websites;

using Kentico.Xperience.Admin.Base.FormAnnotations;
using Kentico.Xperience.Admin.Websites.FormAnnotations;
using Kentico.PageBuilder.Web.Mvc;

public class CafeCardProperties : IWidgetProperties
{
    [WebPageSelectorComponent(Label = "Cafe", Order = 1)]
    // Binds the 'Required' validation rule to the property.
    // The rule ensures that the widget configuration dialog generated based
    // on this class cannot be submitted until the field is populated.
    [RequiredValidationRule]
    public IEnumerable<WebPageRelatedItem> SelectedCafes { get; set; } = new List<WebPageRelatedItem>();
}
```

For this purpose, each validation rule can have a corresponding [attribute](https://docs.microsoft.com/en-us/dotnet/csharp/tutorials/attributes) that, when used to annotate a property of one of the supported components, binds the validation rule to that property’s input. The input dialog generated based on that model class cannot be submitted until user input passes all assigned validation rules.
A single property can have multiple validation attributes assigned. However, be mindful not to create contradictory rulesets that would make submission impossible.
Validation rule attribute classes must inherit from `ValidationRuleAttribute`.
Declare all mandatory and optional properties of the corresponding validation rule as properties of the attribute class. The properties **must be named identically** to their validation rule counterparts. The system uses the property names to pass and instantiate the values of the corresponding rule. Properties that are required for the rule to function can be forced via the attribute’s constructor.
C#
**Sample validation rule attribute**
Copy
```
using Kentico.Xperience.Admin.Base.FormAnnotations;

public class ValueIsBetweenValidationRuleAttribute : ValidationRuleAttribute
{
    // Property names must match the properties of the corresponding rule
    public int Min
    {
        get;
    }

    public int Max
    {
        get;
    }

    public ValueIsBetweenValidationRuleAttribute(int min, int max)
    {
        Min = min;
        Max = max;
    }
}
```

`ValidationRuleAttribute` also contains an `ErrorMessage` property that, when populated, overrides the error message provided by the corresponding validation rule. 
To indicate the relation between the attribute class and the corresponding validation rule, decorate the validation rule class with the `ValidationRuleAttribute` attribute.
C#
**Assigns ValueIsBetweenValidationRuleAttribute to ValueIsBetweenValidationRule**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;
using Kentico.Xperience.Admin.Base.FormAnnotations;

[ValidationRuleAttribute(typeof(ValueIsBetweenValidationRuleAttribute))]
public class ValueIsBetweenValidationRule : ValidationRule<...>
```

For the example above, every property decorated with `ValueIsBetweenValidationRuleAttribute` is assigned a `ValueIsBetweenValidationRule`. 
## Default validation rules
The following table gives an overview of validation rules provided with default Xperience installations. All validation rule attributes are located in the `Kentico.Xperience.Admin.Base.FormAnnotations` namespace.
Name |  Applicable type |  Attribute |  Description  
---|---|---|---  
Field comparison |  int  
decimal  
double  
string |  IntegerFieldComparisonValidationRule  
DecimalFieldComparisonValidationRule  
DoubleFieldComparisonValidationRule  
StringFieldComparisonValidationRule |  Compares the input’s value with another field of the **same data type**.  
Email format |  string |  EmailValidationRule |  Checks that the input is an email address. Can be configured to allow multi-email input separated by a semicolon.  
Identifier |  string |  IdentifierValidationRule |  Checks that the input contains a valid object identifier. Identifiers must begin with a letter or an underscore and can contain only underscores and alphanumeric characters.  
Maximum length |  string |  MaxLengthValidationRule |  Limits user input to the specified maximum length.  
Minimum length |  string |  MinLengthValidationRule |  Sets the minimum number of characters a field must contain before it can be submitted.  
Maximum value |  int  
decimal  
double |  MaximumIntegerValueValidationRule  
MaximumDecimalValueValidationRule  
MaximumDoubleValueValidationRule |  Prevents users from entering numbers larger than specified.  
Minimum value |  int  
decimal  
double |  MinimumIntegerValueValidationRule  
MinimumDecimalValueValidationRule  
MinimumDoubleValueValidationRule |  Prevents users from entering numbers smaller than specified.  
Url format |  string |  UrlValidationRule |  Checks if the input is a valid absolute URL. Additional properties of the attribute can be enabled to also allow the following types of URLs:
  * relative URLs starting with ~/
  * URL fragments starting with #
  * query string values starting with ?

  
Required value |  object (all data types) |  RequiredValidationRule |  Prevents from submitting empty fields.  
## Example
The following example demonstrates a validation rule that checks if a number belongs to a specified interval. The example implements the rule for form components of the _int?_ type:
C#
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

[assembly: RegisterFormValidationRule(ValueIsBetweenValidationRule.IDENTIFIER,
                                  typeof(ValueIsBetweenValidationRule),
                                  "Entered number is between X and Y",
                                  "Checks that the entered number belongs to a specified closed interval.")]

public class ValueIsBetweenValidationRule : ValidationRule<ValueIsBetweenValidationRuleProperties, ValueIsBetweenValidationRuleClientProperties, int?>
{
    internal const string IDENTIFIER = "Acme.Customizations.ValidationRules.ValueIsBetween";

    // Replace 'acme/web-admin-custom' with the name of your organization and project
    public override string ClientRuleName => "@acme/web-admin-custom/ValueIsBetweenValidationRule";

    public override Task<ValidationResult> Validate(int? value, IFormFieldValueProvider formFieldValueProvider)
    {
        if (value <= Properties.Max && value >= Properties.Min)
        {
            return ValidationResult.SuccessResult();
        }

        return ValidationResult.FailResult($"Enter a value between {Properties.Min} and {Properties.Max}.");
    }

    // Sends properties to the client for use in client-side validation
    protected override Task ConfigureClientProperties(ValueIsBetweenValidationRuleClientProperties clientProperties)
    {
        clientProperties.Min = Properties.Min;
        clientProperties.Max = Properties.Max;

        return base.ConfigureClientProperties(clientProperties);
    }
}
```

The rule’s properties class needs to hold two values – the _lower_ and _upper_ boundary of the range from which to accept user input:
C#
Copy
```
using Kentico.Xperience.Admin.Base.Forms;
using Kentico.Xperience.Admin.Base.FormAnnotations;

public class ValueIsBetweenValidationRuleProperties : ValidationRuleProperties
{
    [NumberInputComponent(Label = "Minimum")]
    public int Min { get; set; }

    [NumberInputComponent(Label = "Maximum")]
    public int Max { get; set; }

    // Overriding the 'ErrorMessage' property and not assigning an editing component hides it from the configuration dialog.
    // This is desirable since the rule returns an error message via the 'Validate' method --
    // anything entered under 'ErrorMessage' has lower priority and is ignored by the system.
    public override string ErrorMessage { get => base.ErrorMessage; set => base.ErrorMessage = value; }

    public override string GetDescriptionText(ILocalizationService localizationService) => $"Entered value is between Minimum and Maximum.";
}
```

For the rule’s client properties class, the client needs to know the same information as the back end to correctly evaluate the input. For this reason, the properties in this class need to mirror those of the back end configuration.
C#
**ValueIsBetween client properties class**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

public class ValueIsBetweenValidationRuleClientProperties : ValidationRuleClientProperties {
    public int Min { get; set; }

    public int Max { get; set; }
}
```

And the TypeScript code to enable client-side validation:
JS
Copy
```
import { ValidationRule, ValidationRuleProps } from '@kentico/xperience-admin-base';

interface ValueIsBetweenValidationRuleProps extends ValidationRuleProps {
    readonly max: number
    readonly min: number
}

export const ValueIsBetweenValidationRule: ValidationRule<ValueIsBetweenValidationRuleProps, number> = (props, value) => {
    const validate = (max: number, min: number) => {
        return min <= value && value <= max;
    };

    return { isValid: validate(props.max, props.min), errorMessage: props.errorMessage }
};
```

Finally, the corresponding attribute to enable usage in component model classes:
C#
Copy
```
using Kentico.Xperience.Admin.Base.FormAnnotations;

public class ValueIsBetweenValidationRuleAttribute : ValidationRuleAttribute
{
    public int Max
    {
        get;
    }

    public int Min
    {
        get;
    }

    public ValueIsBetweenValidationRuleAttribute(int max, int min)
    {
        Max = max;
        Min = min;
    }
}
```

With the attribute created, add `ValidationRuleAttribute` with the attribute type as the parameter to the validation rule’s main class to tie the attribute and validation rule together:
C#
Copy
```
using Kentico.Xperience.Admin.Base.Forms;
using Kentico.Xperience.Admin.Base.FormAnnotations;

[ValidationRuleAttribute(typeof(ValueIsBetweenValidationRuleAttribute))]
public class ValueIsBetweenValidationRule : ValidationRule<ValueIsBetweenValidationRuleProperties, ValueIsBetweenValidationRuleClientProperties, int?>
```

The validation rule is now available when adding validation to fields and model class [properties](#assign-validation-rules-using-attributes) with the _int?_ data type.
![]()
[]()[]()
