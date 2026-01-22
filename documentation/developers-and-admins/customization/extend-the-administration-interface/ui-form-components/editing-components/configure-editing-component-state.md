---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components/configure-editing-component-state
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Extend the administration interface](/documentation/developers-and-admins/customization/extend-the-administration-interface)
  * [UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components)
  * [Editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components)
  * Configure editing component state 


# Configure editing component state
When [defining editable properties](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) for [Admin UI](/documentation/developers-and-admins/customization/extend-the-administration-interface/admin-ui-customization-model-overview), [Page Builder](/documentation/developers-and-admins/development/builders/page-builder), or [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) components, you can use the API to configure the state of the assigned editing component.
This configuration is only intended for advanced scenarios where statically configuring component values is not sufficient. In most cases, you can directly set the component’s properties, visibility conditions and validation rules via the corresponding attribute notation. A typical use case is creating dynamic data sources for selector components, such as [DropDownComponent](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) or [RadioGroupComponent](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).
Component state is configured via `FormComponentConfigurator` classes that can run custom code at specific points in an editing component’s lifecycle. Configurators allow you to modify all aspects of the component: properties, assigned visibility conditions, validation rules, and default value.
Configurator classes must:
  1. Inherit from the `FormComponentConfigurator<TFormComponent>` base class.
Use the `FormComponentConfigurator` class from the `Kentico.Xperience.Admin.Base.Forms` namespace.
  2. Target a specific form component type via the generic `TFormComponent` parameter of the base class. For implementing the types of out-of-the-box components, see [Reference - Admin UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).
  3. Implement the `async` `Configure(FormComponent, IFormFieldValueProvider)` method.
     * The `FormComponent` parameter contains the instance of the configured component. Access and modify its `Properties`, `VisibilityConditions` or `ValidationRules` according to your configurator’s requirements.
     * `IFormFieldValueProvider` allows you to access the values of properties in the given dialog that **precede** the component being configured (according to the `Order` property that can be set when [assigning editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components)).


## Assign configurators
Assign configurators to property editing components via the `FormComponentConfiguration` attribute (available in the `Kentico.Xperience.Admin.Base.FormAnnotations` namespace).
The attribute has several possible constructors for configurator assignment:
  * For configurators without any dependencies on the values of other components in the dialog: 
    * `FormComponentConfiguration(configuratorType)`
    * `FormComponentConfiguration(identifier)`
    * This configurator is invoked only during the initial load of the configuration dialog. See [Set the initial component state](#Configureeditingcomponentstate-initial).
  * For configurators with dependencies on the values of other components in the dialog: 
    * Configurators with a single dependency: 
      * `FormComponentConfiguration(configuratorType, dependencyFieldName)`
      * `FormComponentConfiguration(identifier, dependencyFieldName)`
    * Configurators with multiple dependencies: 
      * `FormComponentConfiguration(configuratorType, dependencyFieldNames)`
      * `FormComponentConfiguration(identifier, dependencyFieldNames)`
    * This configurator is invoked during the initial load of the configuration dialog and every time the value of the fields designated as dependencies change. See [Establish dependencies between fields](#Configureeditingcomponentstate-dependency).


**Differences between the`FormComponentConfiguration` constructors with `configuratorType` and `identifier` parameters**
If you want to be able to [deploy your project without the Xperience administration](/documentation/developers-and-admins/deployment/deploy-to-private-cloud/deploy-without-the-administration), you can only use the constructors with the `identifier` parameter when assigning configurators to any of the editing components in your live site project. Otherwise your live site code will maintain dependencies on the _Kentico.Xperience.Admin_ NuGet package. Before you can use the constructors with the `identifier` parameter, you need to register the form component configurator under the identifier via the `RegisterFormComponentConfigurator` assembly attribute.
When assigning configurators to editing components used in the administration or when you don’t need to completely separate the live site project from the administration, you can use the `FormComponentConfiguration` constructors with either the `configuratorType` or `identifier` parameter.
You can assign at most **one** component configurator per editing component.
Configurators assigned via `FormComponentConfiguration(configuratorType)` or `FormComponentConfiguration(identifier)` are invoked only on the initial load of the given configuration dialog. Use such configurators if you need to assign a dynamic value (variable) to a component’s properties, visibility conditions or validation rules. For fixed values, directly use the corresponding attribute notation.
To assign a configurator to a property:
  1. Edit the class containing the component’s properties (e.g., a [widget properties model class](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties)).
  2. Identify the property whose editing component you want to configure.
  3. Decorate the property with the `FormComponentConfiguration` attribute.
  4. In the attribute’s constructor, specify the desired configurator using one of the attributes’s properties: 
     * `configuratorType` – the underlying type of the class that implements the component configurator you want to use.
     * `identifier` – the string identifier under which the the component configurator is registered.


Form component configurators can be used to establish dependencies between fields within a dialog. A typical use case is a selector component that changes its available selection options based on the value of other fields. 
For fields with dependencies, the configurator is called during the initial load of the configuration dialog, and every time the value of any field marked as a dependency changes.
**Property order requirement**
Value dependencies between properties are restricted by the order in which the properties are displayed in the configuration dialog. A dependency can only be established with properties of a lower order (controlled by the `Order` property that can be set when [assigning editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components)).
To assign a configurator with field value dependencies to a property:
  1. Edit the class containing the component’s properties (e.g., a [widget properties model class](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties)).
  2. Identify the property whose editing component you want to configure.
  3. Decorate the property with the `FormComponentConfiguration` attribute.
  4. In the attribute’s constructor: 
    1. Specify the desired configurator using one of the attributes’s properties: 
       * `configuratorType` – the underlying type of the class that implements the component configurator you want to use.
       * `identifier` – the string identifier under which the the component configurator is registered.
    2. Specify the fields on which the configurator depends using one of the attributes’s properties: 
       * `dependencyFieldName` – the name of the property whose change in value causes the configurator to reconfigure the component (i.e., call the `Configure` method again). Use the `nameof` operator.
       * `dependencyFieldNames` – an array of names of properties whose change in value causes the configurator to reconfigure the component (i.e., call the `Configure` method again). To identify each property in the array, use the `nameof` operator.


The example below shows a sample implementation of a component configurator and its usage.
### Example
The following example shows how to create country and state selector fields.
The country selector uses an `IDropDownOptionsProvider` implementation to load the country data.
C#
**Example - country options provider**
Copy
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;

using CMS.Globalization;

using Kentico.Xperience.Admin.Base.FormAnnotations;

// Data provider for loading countries into a drop-down list
public class CountryDropDownOptionsProvider : IDropDownOptionsProvider
{
    private readonly IInfoProvider<CountryInfo> countryProvider;

    // Obtains required dependencies using constructor injection
    public CountryDropDownOptionsProvider(IInfoProvider<CountryInfo> countryProvider)
    {
        this.countryProvider = countryProvider;
    }

    public async Task<IEnumerable<DropDownOptionItem>> GetOptionItems()
    {
        // Retrieves all countries from the database
        // Transforms country data to the drop-down item format
        return (await countryProvider.Get().GetEnumerableTypedResultAsync())
                                           .Select(c => new DropDownOptionItem()
                                            {
                                                Value = c.CountryID.ToString(),
                                                Text = c.CountryDisplayName
                                            });
    }
}
```

The state selector uses a configurator to display states available for the selected country. The configurator takes the country selector as a dependency. Every time a user selects a different country, it fetches the corresponding states from the database. If the selected country is not subdivided into states, the configurator hides the field instead.
In this case, the configurator is registered to the system via the `RegisterFormComponentConfigurator` assembly attribute and later assigned to the state selector field using the configurator’s identifier.
C#
**Example - state configurator**
Copy
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;

using CMS.Globalization;

using Kentico.Xperience.Admin.Base.Forms;

// Registers the 'StateConfigurator' under the 'stateConfigurator' identifier
[assembly: RegisterFormComponentConfigurator("stateConfigurator", typeof(StateConfigurator))]

// Configurator for components of the 'DropDownComponent' type that populates the drop-down with
// a selection of all states based on the country selected in another field
public class StateConfigurator : FormComponentConfigurator<DropDownComponent>
{
    private readonly IInfoProvider<StateInfo> stateInfoProvider;

    // Obtains required dependencies using constructor injection
    public StateConfigurator(IInfoProvider<StateInfo> stateInfoProvider)
    {
        this.stateInfoProvider = stateInfoProvider;
    }

    public override async Task Configure(DropDownComponent formComponent, IFormFieldValueProvider formFieldValueProvider, CancellationToken cancellationToken)
    {
        // Gets the identifier of the country selected in the field marked as a dependency
        var countryId = GetCountryId(formFieldValueProvider);

        // If no country is selected (typically on initial page load), hides the state selector
        if (!countryId.HasValue)
        {
            HideField(formComponent);
            return;
        }

        // Gets all states for a given country
        var states = await stateInfoProvider.Get().WhereEquals("CountryID", countryId).GetEnumerableTypedResultAsync();

        // Hides the state selector component if subdivision into states doesn't exist for the selected country
        if (!states.Any())
        {
            HideField(formComponent);
        }
        else
        {
            // Populates the state selector with states that belong to the selected country
            var stateItems = states.Select(s => s.StateID + ";" + s.StateDisplayName);
            formComponent.Properties.Options = String.Join("\r\n", stateItems);
        }
    }

    private int? GetCountryId(IFormFieldValueProvider formFieldValueProvider)
    {
        if (formFieldValueProvider.TryGet<string>(nameof(MyWidgetProperties.Country), out var countryId))
        {
            if (!String.IsNullOrEmpty(countryId))
            {
                return Int32.Parse(countryId);
            }
        }
        return null;
    }

    private void HideField(DropDownComponent formComponent)
    {
        formComponent.VisibilityConditions.Add(new Invisible());
    }
}

// Custom visibility condition that is always false, used to dynamically hide fields
public class Invisible : VisibilityCondition
{
    public override bool Evaluate(IFormFieldValueProvider formFieldValueProvider)
    {
        return false;
    }
}
```

The following code assigns the country data provider and state configurator to [DropDownComponent](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) properties:
C#
**Example - country and state selector usage**
Copy
```
using Kentico.PageBuilder.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;

public class MyWidgetProperties : IWidgetProperties
{
    [DropDownComponent(Label = "Country", Order = 0, Tooltip = "Select your country", DataProviderType = typeof(CountryDropDownOptionsProvider))]
    public string Country { get; set; }

    [DropDownComponent(Label = "State", Order = 1)]
    // Assigns 'StateConfigurator' to the field using the configurator identifier and sets 'Country' as a dependency
    [FormComponentConfiguration("stateConfigurator", nameof(Country))]
    public string State { get; set; }
}
```

When the widget properties dialog is opened, the configurator ensures that the state field only shows when selecting a state for the country chosen via the **Country** property makes sense and populates the drop-down with relevant choices. In this example, `StateConfigurator.Configure` is called on the initial load of the dialog and every time a country is selected using the **Country** drop-down.
![]()
[]()[]()
