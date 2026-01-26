---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-marketing-setup/contact-configuration/contact-recognition-logic
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital marketing setup](/documentation/developers-and-admins/digital-marketing-setup)
  * [Contact configuration](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration)
  * Contact recognition logic 


# Contact recognition logic
On [websites](/documentation/developers-and-admins/configuration/website-channel-management), Xperience stores the current [contact](/documentation/business-users/digital-marketing/contact-management) persistently in the **CurrentContact** browser cookie. For visitors without the cookie, new anonymous contacts are always created and remain in the system unless they are automatically merged with an existing contact. Merging occurs if the **email address** value of the contact is updated and matches the address of an existing contact.
The system remembers contacts until their cookies are deleted or until a different contact is determined for the visitor.
On many websites, different people often connect from the same IP addresses (e.g., from company or municipal networks). To keep contacts separate in such scenarios, Xperience does NOT automatically assign contacts to anonymous visitors based on their _IP address_ or _Browser user agent_.
### Contact email collisions
Because a contact’s email address is its main identifier in Xperience, the system does not allow the email to be changed based on data submitted by users. This can happen in the following ways:
  * [Form submissions](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#map-form-submission-to-contact-attributes)
  * [Headless tracking Data input activities](/documentation/developers-and-admins/digital-marketing-setup/headless-tracking#log-activities)
  * [Cross-site tracking Data input activities](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking#data-input)


If the contact associated with a user already has an email address stored in Xperience, and it doesn’t match the newly submitted email value, all related activities and field updates are performed for a different contact. Either an existing contact that matches the submitted email value is used, or a new contact is created. In these cases, the user’s associated contact remains unchanged, but any subsequent actions, such as [automation processes](/documentation/business-users/digital-marketing/automation), are performed for the “other” contact that matches the submitted email address.
### Customize contact recognition
If the contact recognition logic described above does not suit your needs, developers can change it programmatically by creating and registering a custom implementation of the `ICurrentContactProvider` service:
  1. Open your Xperience project in Visual Studio.
  2. [Add a custom assembly](/documentation/developers-and-admins/customization/integrate-custom-code) (_Class Library_ project) to the solution or re-use an existing one.
  3. Create a class that implements the `ICurrentContactProvider` interface in the custom assembly.
  4. Implement your own logic for contact recognition.
C#
Copy
```
using System;

using CMS;
using CMS.Membership;
using CMS.ContactManagement;

[assembly: RegisterImplementation(typeof(ICurrentContactProvider), typeof(CustomCurrentContactProvider))]

/// <summary>
/// Summary description for CustomCurrentContactProvider
/// </summary>
public class CustomCurrentContactProvider : ICurrentContactProvider
{
    public ContactInfo GetCurrentContact()
    {
        // Implement your contact recognition logic
        throw new NotImplementedException();
    }

    public ContactInfo GetExistingContact()
    {
        // Implement your contact recognition logic
        throw new NotImplementedException();
    }

    public void SetCurrentContact(ContactInfo contact)
    {
        // Implement your logic for storing information about the current contact
        throw new NotImplementedException();
    }
}
```

  5. Register your service using the `RegisterImplementation` assembly attribute.


![]()
[]()[]()
