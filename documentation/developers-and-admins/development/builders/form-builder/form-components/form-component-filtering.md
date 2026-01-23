---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/form-builder/form-components/form-component-filtering
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * [Form Builder](/documentation/developers-and-admins/development/builders/form-builder)
  * [Form components](/documentation/developers-and-admins/development/builders/form-builder/form-components)
  * Form component filtering 


# Form component filtering
Form component filtering allows you to hide [form components](/documentation/developers-and-admins/development/builders/form-builder/form-components) from the Form Builder’s [user interface](/documentation/business-users/digital-marketing/forms). For example, if you develop a custom version of one of the system’s [default components](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components), you can help users choose the correct one by hiding the original.
## Creating filters
Form component filters are classes that implement the `IFormComponentFilter` interface and its `Filter` method. The method’s parameters give you access to the following objects:
  * `IEnumerable<FormComponentDefinition>` – a collection of definitions representing the available form components. Each contains metadata under which the form component was [registered into the system](/documentation/developers-and-admins/development/builders/form-builder/form-components), such as the _identifier_ , _name_ , or _description._
  * `FormComponentFilterContext` – contains contextual information about the form being edited via the Form Builder interface, such as the form code name. You can use the information to provide a different set of components for specific forms.


The `Filter` method needs to return the collection of the filtered form component definitions.
The following example filters all default Xperience form components from the Form Builder user interface. 
C#
Copy
```
using System.Linq;
using System.Collections.Generic;

using Kentico.Forms.Web.Mvc;
using Kentico.Forms.Web.Mvc.FormComponents;

namespace LearningKit.FormBuilder
{
    public class FormComponentsFilter : IFormComponentFilter
    {
        public IEnumerable<FormComponentDefinition> Filter(IEnumerable<FormComponentDefinition> formComponents, FormComponentFilterContext context)
        {
            // Filters out all Xperience form components from the Form Builder UI
            return formComponents.Where(component => !component.Identifier.StartsWith("Kentico"));
        }
    }
}
```

You can also filter out individual Xperience form components by matching their identifiers with the `IDENTIFIER` constant exposed by each form component class. See [Reference - Form Builder components](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components).
C#
Copy
```
using System.Linq;
using System.Collections.Generic;

using Kentico.Forms.Web.Mvc;
using Kentico.Forms.Web.Mvc.FormComponents;

namespace LearningKit.FormBuilder
{
    public class IndividualFormComponentsFilter : IFormComponentFilter
    {
        public IEnumerable<FormComponentDefinition> Filter(IEnumerable<FormComponentDefinition> formComponents, FormComponentFilterContext context)
        {
            // Filters specified form components
            return formComponents.Where(component => !GetComponentsToFilter().Contains(component.Identifier));
        }

        // A collection of form component identifiers to filter
        private IEnumerable<string> GetComponentsToFilter()
        {
            return new string[] { TextInputComponent.IDENTIFIER, TextAreaComponent.IDENTIFIER, USPhoneComponent.IDENTIFIER };
        }
    }
}
```

## Register filters
Register filters by adding them to the `FormBuilderFilters.FormComponentFilters` collection on [application start](/documentation/developers-and-admins/customization/run-code-on-application-startup).
Note that the filtering is cumulative. The process starts with the collection of all form components registered in the system. Each subsequently applied filter then works with the collection of form components returned by the previous filter:
C#
**Example - Register form component filters**
Copy
```
using CMS;
using CMS.DataEngine;

using Kentico.Forms.Web.Mvc;

[assembly: RegisterModule(typeof(FormComponentFilters))]

public class FormComponentFilters : Module
{
    // Module class constructor, inherits from the base constructor with the code name of the module as the parameter
    public FormComponentFilters() : base("FormComponentFilters")
    {
    }

    // Initializes the module. Called when the application starts.
    protected override void OnInit()
    {
        base.OnInit();

        RegisterFormComponentFilters();
    }

    private void RegisterFormComponentFilters()
    {
        // Selectively hides default system form components
        FormBuilderFilters.FormComponents.Add(new IndividualFormComponentsFilter());
        // Hides all default system form components
        FormBuilderFilters.FormComponents.Add(new FormComponentsFilter());
    }
}
```

### Register filters that use dependency injection
Form component filter classes support [dependency injection](/documentation/developers-and-admins/development/website-development-basics/dependency-injection) and their constructor can have parameters (e.g., instances of services registered in the project’s DI container).
Such filters must be registered into the `FormBuilderFilters.FormComponentFilters` collection using the `Add<FilterClassType>` method:
C#
**Example - Register form component filters**
Copy
```
using Kentico.Forms.Web.Mvc;

...

protected override void OnInit()
{
   base.OnInit();
   RegisterFormComponentFilters();
}

private void RegisterFormComponentFilters()
{
    // Selectively hides default system form components
    FormBuilderFilters.FormComponents.Add<IndividualFormComponentsFilter>();
    // Hides all default system form components
    FormBuilderFilters.FormComponents.Add<FormComponentsFilter>();
}
```

![]()
[]()[]()
