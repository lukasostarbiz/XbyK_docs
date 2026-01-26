---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-marketing-setup/contact-configuration/configure-custom-contact-field-empty-values
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital marketing setup](/documentation/developers-and-admins/digital-marketing-setup)
  * [Contact configuration](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration)
  * Configure custom contact field empty values 


# Configure custom contact field empty values
If you have [custom contact fields](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration#add-custom-fields-to-contacts) that use a [custom data type](/documentation/developers-and-admins/customization/field-editor/data-type-management#register-custom-data-types) and wish to use these fields with the _Contact field value is empty_ condition in [contact groups](/documentation/business-users/digital-marketing/contact-groups) or [automation processes](/documentation/business-users/digital-marketing/automation#conditions), you need to define what value is considered as empty for the data type. Otherwise the condition will not be evaluated correctly. You can configure the empty value for individual data types by extending the `ContactFieldEmptyValueProvider` using the [decorator pattern](/documentation/developers-and-admins/customization/decorate-system-services). Additionally, you can change the empty values provided by the system for the default contact fields.
## Define custom empty values
  1. Open your solution in Visual Studio.
  2. [Add a custom assembly](/documentation/developers-and-admins/customization/integrate-custom-code) (_Class Library_ project) with class discovery enabled to the solution, or re-use an existing assembly.
  3. Reference the project from your Xperience website project.


Continue by implementing the custom class:
  1. Create a new custom class. For example, name the class `CustomContactFieldEmptyValueProvider`.
  2. Make the class implement the `IContactFieldEmptyValueProvider` interface.
  3. Add the `Get` method: 
     * Use the [decorator customization pattern](/documentation/developers-and-admins/customization/decorate-system-services#decorate-services-via-dependency-injection) to call the default implementation of the `Get` method. This call returns a dictionary which maps the system data types to their empty value. Here, you can add the empty values of your custom data types or adjust the empty values of the system data types.
  4. Register the implementation using the `RegisterImplementation` assembly attribute.


C#
Copy
```
using CMS;
using CMS.ContactManagement;
using CMS.DataEngine;

[assembly: RegisterImplementation(typeof(IContactFieldEmptyValueProvider), typeof(CustomContactFieldEmptyValueProvider))]


public class CustomContactFieldEmptyValueProvider : IContactFieldEmptyValueProvider
{
    // Stores the default implementation of the IContactFieldEmptyValueProvider service
    private readonly IContactFieldEmptyValueProvider contactFieldEmptyValueProvider;

    // Stores the empty string value as an object array
    private static readonly object[] emptyString = { "" };

    public CustomContactFieldEmptyValueProvider(IContactFieldEmptyValueProvider contactFieldEmptyValueProvider)
    {
        this.contactFieldEmptyValueProvider = contactFieldEmptyValueProvider;
    }

    // Allows you to make adjustments to the empty value dictionary
    public Dictionary<string, object[]> Get()
    {
        // Calls the default IContactFieldEmptyValueProvider.Get implementation
        var result = contactFieldEmptyValueProvider.Get();

        // Sets a custom empty value for the custom data type with the "mydatatype" identifier
        result.Add("mydatatype", emptyString);

        // You can also rewrite the default empty values provided in the dictionary
        // Access the identifiers of the system data types via constants in the FieldDataType class
        result[FieldDataType.Text] = emptyString;

        return result;
    }
}
```

![]()
[]()[]()
