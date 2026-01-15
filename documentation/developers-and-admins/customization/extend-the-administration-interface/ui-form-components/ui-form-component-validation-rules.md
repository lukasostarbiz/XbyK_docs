# UI form component validation rules
  * [ Copy page link ](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules#) | [Get HelpService ID](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules#)
Core MVC 5


[✖](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules#)
Validation rules verify that the data users enter in a form meets the standards you specify. 
A validation rule contains an expression that evaluates the values of one or more form fields, and returns the validation result. A form cannot be submitted until user input passes all validation rules assigned to individual input fields.
Each validation rule also carries an error message that is displayed to users when their input fails to pass the given rule’s validation logic.
Default Xperience installations come with a set of validation rules ready to be used immediately. Additionally, the admin UI customization framework allows you to define custom rules suitable for your use cases and scenarios.
## Define validation rules
**Validation rule namespaces**
Unless specified otherwise, all classes and members mentioned on this page can be found in the following namespaces:
  * `Kentico.Xperience.Admin.Base.Forms`
  * `Kentico.Xperience.Admin.Base.FormAnnotations`


Do **not** use classes from the `Kentico.Forms.Web.Mvc` namespace when working with UI form component validation rules. This namespace contains obsolete classes with matching names (e.g., `ValidationRule`, `RegisterFormValidationRule`), as well as classes for [Form Builder validation rules](documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules), which serve a different purpose.
In Xperience, each validation rule consists of a main class that contains the rule’s validation logic – implemented in the rule’s `Validate` method – and two properties classes – one to enable client-side validation and the other to allow for the rule’s configuration (via the [field editor](documentation/developers-and-admins/customization/field-editor) or [custom attributes](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules#assign-validation-rules-using-attributes)).
Validation rules are **strongly typed**. A rule defined for _decimal_ value types can only be applied to fields that use [form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) of the corresponding type. The type must match exactly, including [nullability](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-value-types) for value types.
A validation rule consists of:
  1. [Back end definition](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules#validation-rule-back-end) – used to define server-side validation logic and rule configuration. Located in the ASP.NET Core project.
  2. [TypeScript component](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules#validation-rule-front-end) – used for client-side validation. Located in JavaScript modules that plug into the React admin app.


Validation rule architecture is summarized by the following diagram:
[![Validation rule architecture](docsassets/documentation/ui-form-component-validation-rules/Architecture.png)](https://docs.kentico.com/docsassets/documentation/ui-form-component-validation-rules/Architecture.png)
### Validation rule back end
Validation rules must inherit from the `ValidationRule<TProperties, TClientProperties, TType>` base class, where
  * `TProperties` – holds properties required for the functionality of the rule. The class must inherit from the `ValidationRuleProperties` base class. When creating the class, you must also implement the `GetDescriptionText` method. The method must return a string containing the description of the validation rule. This information is displayed when assigning validation rules to fields via the [field editor](documentation/developers-and-admins/customization/field-editor).  
[![Validation rule description](docsassets/documentation/ui-form-component-validation-rules/RuleDescriptionText.png)](https://docs.kentico.com/docsassets/documentation/ui-form-component-validation-rules/RuleDescriptionText.png)
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

  * `TType` – the data type for which the rule is available (e.g., `string`, `int`, or custom types). For an overview of default data types registered in the system, see [Data type management](documentation/developers-and-admins/customization/field-editor/data-type-management). For rules applicable to all types, use object.
    * Based on the specified type, the rule is offered when adding validation to fields of the corresponding data type via the [field editor](documentation/developers-and-admins/customization/field-editor).
    * If you don’t need the rule available within the field editor, you can use any suitable data type as long as it matches the type of the annotated UI form component (identical to how form component registration functions).
    * The type must exactly match the data type of the annotated UI form component, including [nullability](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-value-types) for value types. To find the data types of the system’s default components, see [Reference - Admin UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).


When inheriting from the class, you need to implement the following abstract members:
  * `ClientRuleName` – property set to the name of the client TypeScript component used to provide client-side validation for the rule. You must provide a full name in the _{orgName}/{projectName}/componentName_ format, as specified when you set up your [custom admin JS module](documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development). Make sure the component is exported (visible to other modules). For example: _@acme/web-admin-custom/ValidationRuleComponent_
  * `Validate` – method that contains the rule’s validation logic. User input is stored in the method’s `value` parameter. Returns a `ValidationResult` object that contains the validation result and, optionally, a customized [error message](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules#validation-error-messages). The object’s constructor expects the following parameters: 
    * `isValid` – a boolean value indicating the result of the validation.
    * `errorMessage` – optional error message that can be returned from the server. See [Validation error messages](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules#validation-error-messages) for more information.
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
Validation rules support constructor [dependency injection](documentation/developers-and-admins/development/website-development-basics/dependency-injection) by default. You can use dependency injection to resolve instances of external services on which your rule depends.
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