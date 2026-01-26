---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-marketing-setup/contact-configuration/map-custom-member-fields-to-contacts
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital marketing setup](/documentation/developers-and-admins/digital-marketing-setup)
  * [Contact configuration](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration)
  * Map custom member fields to contacts 


# Map custom member fields to contacts
When a visitor [registers](/documentation/developers-and-admins/development/registration-and-authentication) as a [member](/documentation/business-users/members), the system logs the _Member registration_ [activity](/documentation/business-users/digital-marketing/contact-activities). The activity is logged for a [contact](/documentation/business-users/digital-marketing/contact-management), not directly for the registered member.
As part of this process, the system maps the member data submitted during registration to the corresponding contact. By default, the member **email address** is mapped (for contacts with an empty value in their email field). In their initial state, members contain a basic set of fields required for simple authentication scenarios, and only the email address is relevant for contacts.
If you have [added custom fields to the member object](/documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects), for example a first and last name, you can extend the member-contact mapping behavior by creating and registering an `IMemberToContactMapper` implementation.
## Implement the custom member field mapper
We recommend extending the default implementation of the `IMemberToContactMapper` service using the [decorator customization pattern](/documentation/developers-and-admins/customization/decorate-system-services#decorate-services-via-dependency-injection). First map your custom member fields to the contact (without setting the `ContactInfo` object), and then call the default implementation to map the email address and save the contact details.
Start by preparing a separate project for custom classes in your Xperience solution:
  1. Open your solution in Visual Studio.
  2. [Add a custom assembly](/documentation/developers-and-admins/customization/integrate-custom-code) (_Class Library_ project) with class discovery enabled to the solution, or re-use an existing assembly.
  3. Reference the project from your Xperience website project.


Continue by implementing the custom class:
  1. Create a new custom class. For example, name the class `CustomMemberToContactMapper`.
  2. Make the class implement the `IMemberToContactMapper` interface.
  3. Add the `Map` method: 
     * Get any required data from the `MemberInfo` parameter, and set the values into the `ContactInfo` parameter.
     * Use the [decorator customization pattern](/documentation/developers-and-admins/customization/decorate-system-services#decorate-services-via-dependency-injection) to call the default implementation of the `Map` method. This ensures mapping of the member email address (if the contact’s email is empty), and then saves the `ContactInfo` object.
The default implementation does not save any changes made to the `MemberInfo` object. 
  4. Register the implementation using the `RegisterImplementation` assembly attribute.


### Example
The following code example demonstrates how to implement contact mapping of custom _FirstName_ and _LastName_ member fields.
C#
**Mapping custom member fields to contact fields**
Copy
```
using CMS;
using CMS.ContactManagement;
using CMS.Membership;

using Kentico.OnlineMarketing.Web.Mvc;

[assembly: RegisterImplementation(typeof(IMemberToContactMapper), typeof(CustomMemberToContactMapper))]

public class CustomMemberToContactMapper : IMemberToContactMapper
{
    // Stores the default implementation of the IMemberToContactMapper service
    private readonly IMemberToContactMapper memberToContactMapper;

    public CustomMemberToContactMapper(IMemberToContactMapper memberToContactMapper)
    {
        this.memberToContactMapper = memberToContactMapper;
    }

    /// Maps the member's first and last name to the contact
    public void Map(MemberInfo member, ContactInfo contact)
    {
        // Gets the values from the member and sets them into the corresponding contact fields
        contact.ContactFirstName = member.GetValue<string>("FirstName", null);
        contact.ContactLastName = member.GetValue<string>("LastName", null);

        /* Calls the default IMemberToContactMapper.Map implementation
           This ensures mapping of the member email address (if the contact's email is empty) and saves the `ContactInfo` object
           If you choose not to call the default implementation, you need to manually:
            - Map the MemberEmail field to the ContactEmail field
              We only recommend updating the contact email if the value is empty. Do not overwrite existing email address values.
            - Save the contact object (using the IInfoProvider<ContactInfo>.Set method)
        */
        memberToContactMapper.Map(member, contact);
    }
}
```

Now when a visitor submits your registration form, the values of the custom first name and last name member fields are automatically mapped to the contact for which the _Member registration_ activity is logged.
![]()
[]()[]()
