---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-marketing-setup/content-personalization/develop-personalization-condition-types
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital marketing setup](/documentation/developers-and-admins/digital-marketing-setup)
  * [Content personalization](/documentation/developers-and-admins/digital-marketing-setup/content-personalization)
  * Develop personalization condition types 


# Develop personalization condition types
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
After [developing widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) for Page Builder, you can enable content editors to personalize individual widgets. To set up personalization, you need to create the types of conditions based on which the widgets will be personalized.
The condition types may be of any kind or form, for example  _Current visitor belongs to contact group X_ , _Current visitor has recently bought product X_ , or  _Current date is between X and Y_. You can allow content editors to further adjust the conditions of specific widget variants by preparing properties and configuration dialogs for your condition types.
**Contact tracking**
We recommend setting up [tracking of contacts](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration) on your website. The tracking is required for most types of conditions that utilize Xperience digital marketing features and data.
**Example of condition type development**
For a full code sample of a personalization condition type, see [Example - Personalization condition type](/documentation/developers-and-admins/digital-marketing-setup/content-personalization/example-personalization-condition-type).
## Create condition types
Conditions types are designed as global components and therefore must be registered in the your project’s application root (not in an [Area](https://docs.microsoft.com/en-us/aspnet/core/mvc/controllers/areas)). Registering condition types in Areas may lead to unexpected behavior.
To define a new personalization condition type:
  1. Open your Xperience project in Visual Studio.
  2. Create a class that represents and evaluates the condition type.
     * Place the condition type class into a component folder together with any other related files, for example **~/Components/PageBuilder/PersonalizationConditions/ <condition type name>**.
     * The condition type class needs to: 
       * Inherit from the `ConditionType` base class (available in the `Kentico.PageBuilder.Web.Mvc.Personalization` namespace).
       * Override the `Evaluate` method, which determines whether the condition is met.
  3. Specify additional properties in the class, representing options that content editors can configure for conditions of the given type.
By default, the `ConditionType` base class contains the `VariantName` property that represents the name of the personalization variant. The base class implementation ensures that the value of the property is automatically displayed in the configuration dialog of personalization conditions. However, you can override the `VariantName` property if you wish to change the behavior or look of the property in the configuration dialog.
When transferring data to and from the configuration dialog, the system serializes objects of the condition type class into JSON format (using the _Newtonsoft.Json_ library).
You can use the `Newtonsoft.Json.JsonIgnore` attribute to exclude properties from the serialized data (for example dynamically computed properties).
  4. Define the visual interface of the condition type’s configuration dialog:
     * Decorate the specified properties using desired [editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components).
     * The attribute assigns and configures a form component, which is used as the input element for the given property.
C#
**Decorating condition type properties**
Copy
```
// Assigns the default Xperience text input component to the property
// Allows users to enter a text value for the given property in the configuration dialog
[TextInputComponent(Order = 0, Label = "Consent code name")]
public string ConsentCodeName { get; set; }
```

  5. [Register](#register-condition-types) the condition type.


[See a full example](/documentation/developers-and-admins/digital-marketing-setup/content-personalization/example-personalization-condition-type) that demonstrates how to develop a basic condition type.
Conditions of the given type work according to your implementation of the `Evaluate` method. The configuration dialog is generated automatically based on the editing components assigned to the class’s properties.
### Implement custom configuration dialogs
Custom configuration dialogs for personalization condition types are based on MVC architecture, composed of Model, View, and Controller elements. These elements define a configuration form which allows users to submit values for the condition type’s properties. By taking full control over the implementation, you can make the configuration dialog look and behave exactly as you need.
Place the configuration dialog classes into the condition type’s component folder, for example `~/Components/PageBuilder/PersonalizationConditions/<condition type name>`.
#### Model
Create a model class containing the properties that you need to transfer to and from the configuration dialog’s view.
C#
**Example**
Copy
```
using System.ComponentModel.DataAnnotations;

namespace MyProject.Components.PageBuilder.PersonalizationConditions.HasGivenConsent
{
    public class HasGivenConsentViewModel
    {
        [Required]
        [Display(Name = "Consent code name")]
        public string ConsentCodeName { get; set; }
    }
}
```

#### Partial view
Create a partial view for the configuration dialog’s interface. The view needs to contain a form element that posts data to the validation action of the controller.
cshtml
**Example**
Copy
```
@model MyProject.Components.PageBuilder.PersonalizationConditions.HasGivenConsent.HasGivenConsentViewModel

<form asp-controller="HasGivenConsent" asp-action="Validate">
    <label asp-for="ConsentCodeName"></label>
    <br />
    <input asp-for="ConsentCodeName" />
    <br />
    <span asp-validation-for="ConsentCodeName"></span>
</form>
```

#### Controller
Create a controller class. The controller needs to inherit from the `ConditionTypeController` class (available in the `Kentico.PageBuilder.Web.Mvc`namespace), with the condition type class as a generic parameter. The controller must contain the following actions:
  * `Index()` – POST action displaying the configuration form. The action must return the form’s HTML content, typically a partial view. To display values of the condition type parameters when editing the form, you need to get the parameters and set them when creating the view model object.
  * `Validate(<model>)` – POST action that receives the model of the configuration dialog as its parameter. Validate the model, and if successful, create an instance of the condition type class and serialize its data into JSON format by returning a new instance of the `ConditionTypeValidationResult` object. Upon unsuccessful validation or if any other error occurs, display the configuration dialog partial view with an appropriate error message.
The `VariantName` property that represents the name of the personalization variant is implemented in the `ConditionType` base class by default. Make sure this property has a set value before you return the data.
C#
**Example**
Copy
```
public class HasGivenConsentController : ConditionTypeController<HasGivenConsentConditionType>
{
    // Displays the configuration dialog
    [HttpPost]
    public ActionResult Index()
    {
        // Gets the condition's current configuration as an instance of the condition type class
        var conditionType = GetParameters();

        // Creates a view model object
        var viewModel = new HasGivenConsentViewModel
        {
            // Sets the consent code name obtained from the condition type parameters
            ConsentCodeName = conditionType.ConsentCodeName
        };

        // Displays the configuration dialog's view
        return PartialView("~/Components/PageBuilder/PersonalizationConditions/HasGivenConsent/_HasGivenConsentConfiguration.cshtml", viewModel);
    }

    // Submits the condition type parameters
    [HttpPost]
    public ActionResult Validate(HasGivenConsentViewModel model)
    {
        // Validates the model
        if (!ModelState.IsValid)
        {
            return PartialView("~/Components/PageBuilder/PersonalizationConditions/HasGivenConsent/_HasGivenConsentConfiguration.cshtml", model);
        }

        // Creates an object of the condition type class
        var parameters = new HasGivenConsentConditionType
        {
            ConsentCodeName = model.ConsentCodeName,
        };

        // Serializes the condition's configuration into JSON format and returns the data
        return new ConditionTypeValidationResult(parameters);
    }
}
```



## Register condition types
Register the condition type by adding an assembly attribute to the condition type class. Specify the following required attribute parameters:
  * _Identifier_ – the unique identifier of the condition type. We recommend using a unique prefix in your condition type identifiers to prevent conflicts when deploying condition types to other projects, for example matching your company’s name.
  * _Class type_ – the type (`System.Type`) of the condition type class.
  * _Display name_ – the name displayed in the condition type selector when personalizing widgets in the Xperience administration interface.
  * **(Required for condition types with a custom configuration dialog)**  _Controller type_ – the type (`System.Type`) of the controller class used to display the configuration dialog.


Additionally, you can specify the following optional attribute parameters:
  * `Description` – the description of the condition type displayed as a tooltip.
  * `IconClass` – the [font icon class](https://devnet.kentico.com/docs/icon-list/index.html) displayed in the condition type selector.
  * `Hint` – the text displayed as a hint above the condition type’s configuration dialog.


C#
**Example - Register a condition type with a custom dialog**
Copy
```
using Kentico.PageBuilder.Web.Mvc.Personalization;

[assembly: RegisterPersonalizationConditionType(
    identifier: "MyProject.Personalization.HasGivenConsentConditionTypeCustom",
    type: typeof(HasGivenConsentConditionTypeCustom),
    name: "Has given consent agreement (custom)",
    ControllerType = typeof(HasGivenConsentController),
    Description = "Evaluates whether the contact has given an agreement with a specified consent declaration.",
    IconClass = "icon-clipboard-checklist",
    Hint = "Enter the code name of a consent. The condition is fulfilled for visitors who have given an agreement with the given consent.")]
```

Once the condition type is registered, editors can select it when [personalizing widgets](/documentation/business-users/digital-marketing/widget-personalization) in a website channel application within the Xperience administration.
![]()
[]()[]()
