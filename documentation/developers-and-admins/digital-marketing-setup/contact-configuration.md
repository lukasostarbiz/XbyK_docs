---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-marketing-setup/contact-configuration
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital marketing setup](/documentation/developers-and-admins/digital-marketing-setup)
  * Contact configuration 


# Contact configuration
[Contacts](/documentation/business-users/digital-marketing/contact-management) in Xperience by Kentico represent people who view and interact with content through channels, e.g., visitors of a website. Each contact stores the visitor’s personal data and other marketing-related information.
**On websites** , contacts cover both anonymous visitors (identified by an HTTP cookie, depending on the [Default cookie level](/documentation/developers-and-admins/data-protection/cookies)) and users who identify themselves by submitting their name, email address and other data. The system automatically [tracks the activities](/documentation/business-users/digital-marketing/contact-activities) that contacts perform on the website and gathers data about contacts based on the actions and input of the associated visitors.
The collected [contact data](/documentation/business-users/digital-marketing/contact-management) allows marketers to analyze client behavior, optimize content, identify potential buyers, and perform other marketing‑related actions.
**Contacts and channels**
Contacts are global and shared across all content [channels](/documentation/developers-and-admins/configuration/website-channel-management). For example, if you use your Xperience application to run two websites, send messages through an email channel, and deliver content to an external application through a headless channel, visitors and email recipients from all channels will belong to the same pool of contacts.
## Contact tracking requirements
The contact tracking functionality in Xperience is **enabled by default**. However, due to [personal data protection](/documentation/developers-and-admins/data-protection) policies, contacts are only tracked for website channels with the **Default cookie level** set to _Visitor_ or _All_ , or if visitors give tracking consent and increase their cookie level. For more information, see [Consent development](/documentation/developers-and-admins/data-protection/consent-development).
## Get the current contact
The system uses the **CurrentContact** browser cookie as persistent storage for the current contact. By default, the storage is empty. However, once you ask for the current contact, the system sets the contact’s GUID (unique identifier) to every response cookie.
To get the current contact in the your application’s code, call the `ContactManagementContext.GetCurrentContact` method. The context class and method are available in the `CMS.ContactManagementont` namespace.
C#
Copy
```
using CMS.ContactManagement;

...

// Gets the current contact
ContactInfo currentContact = ContactManagementContext.GetCurrentContact();
```

If there is no contact related to the currently processed request, the `GetCurrentContact` method automatically creates and returns a new anonymous contact by default. You can disable the automatic creation of new contacts by calling the method with a `false` bool parameter.
**Contact management services and dependency injection**
If you wish to use the [dependency injection](/documentation/developers-and-admins/development/website-development-basics/dependency-injection) design pattern, you can create a custom service to provide the required contact management functionality. Call the methods of the `ContactManagementContext` class within the service’s implementation.
## Configure contact tracking
Depending on the project requirements, developers and administrators can adjust how the system recognizes contacts or processes contact data. This enables marketers to work with contacts more efficiently.
  * [Set up deletion of inactive contacts](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration/delete-inactive-contacts) (to reduce the required storage size and mitigate performance issues)
  * [Create custom contact recognition logic](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration/contact-recognition-logic)
  * [Change the logging interval for contact data updates](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities#activity-and-contact-update-processing) (contact updates are batch processed and logged at an interval together with activities)
  * [Map custom member fields to contacts](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration/map-custom-member-fields-to-contacts) (if your project allows visitors to [register as members](/documentation/developers-and-admins/development/registration-and-authentication) and you use [custom member fields](/documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects))


## Add custom fields to contacts
Custom fields can help you store and organize additional data about your contacts, for example, if you wish to integrate Xperience by Kentico with a third-party CRM.
To add custom contact fields, [extend](/documentation/developers-and-admins/customization/object-types/extend-system-object-types) the **Modules → Contact management → Classes → Contact management - Contact** system class.
**Include custom fields in contact field selectors**
When configuring the _Contact has value in field_ or _Contact field value is empty_ condition in [contact groups](/documentation/business-users/digital-marketing/contact-groups) or [automation](/documentation/business-users/digital-marketing/automation#conditions) or setting [mappings of form fields to contact attributes](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#map-form-submission-to-contact-attributes), the system provides selectors of contact fields.
To include your custom contact fields in these selectors, you need to [add the fields](/documentation/developers-and-admins/customization/object-types/extend-system-object-types#Extendsystemobjecttypes-UIForms) to the **Contact edit** UI form of the _Contact management - Contact_ class. Contact field selectors display custom fields with the text entered as the **Field caption** of the corresponding UI form field.
Note: Fields with the _Date_ or _Date and time_ data type are not supported in contact field selectors.
If your custom contact fields use a [custom data type](/documentation/developers-and-admins/customization/field-editor/data-type-management#register-custom-data-types), you need to ensure the empty value for this data type is configured when using a _Contact field value is empty_ [contact group](/documentation/business-users/digital-marketing/contact-groups) or [automation](/documentation/business-users/digital-marketing/automation#conditions) condition. You can [configure the empty value](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration/configure-custom-contact-field-empty-values) by extending the `IContactFieldEmptyValueProvider` service using the [decorator pattern](/documentation/developers-and-admins/customization/decorate-system-services). Additionally, you can change the empty values provided by the system for the default field data types.
![]()
[]()[]()
