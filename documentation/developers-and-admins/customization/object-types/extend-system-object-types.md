---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/object-types/extend-system-object-types
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Object types](/documentation/developers-and-admins/customization/object-types)
  * Extend system object types 


# Extend system object types
Xperience allows you to customize the data structure of certain system objects by modeling additional database columns to store custom data. This can typically be useful if you wish to gather data not collected by a particular out-of-the-box implementation or to add additional properties for the purposes of custom third-party integrations.
Follow these steps to extend a system object:
  1. Open the **Modules** application and select a system module.
  2. Select the **Classes** tab and select one of the [available classes](#customizable-data-classes).
  3. Switch to the **Database columns** tab and add required custom fields using the [field editor](/documentation/developers-and-admins/customization/field-editor).


**Unique names for custom fields**
We recommend including a unique prefix (e.g., your company name) in the **Field name** of any custom fields. The prefix ensures uniqueness for the field name, and prevents conflicts with system fields that may be added in the future.
You have adjusted the object’s database structure. The added columns respect the [configuration](/documentation/developers-and-admins/customization/object-types/object-type-configuration) of the extended object type. For example, the columns are automatically included in serialized data if the object [supports CI/CD](/documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types). However, the system does not automatically include the columns into editing forms in the administration. To enable administration users to work with the columns, you need to adjust the object’s [UI forms](#Extendsystemobjecttypes-UIForms). 
For more advanced customization scenarios, you can also build a completely [custom user interface](/documentation/developers-and-admins/customization/extend-the-administration-interface) capable of fully reflecting your requirements. You can find inspiration on the [Example – Offices management application](/documentation/developers-and-admins/customization/object-types/example-offices-management-application) page that guides you through the process of creating and registering an entirely new object in the system and building a corresponding management application in the admin UI. 
Moreover, to learn how to work with the added columns using the API, see [Access added columns via the API](#Extendsystemobjecttypes-API).
To make the added columns editable via the administration, you must also adjust the object’s **UI forms**. UI forms are system objects connected to individual classes. They store information describing editing forms for the object, such as the input [data type](/documentation/developers-and-admins/customization/field-editor/data-type-management) of form fields and assigned [UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components). Administration [edit pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template) then render an editing form based on the provided UI forms. 
Objects can have multiple UI forms, each shown in a different context as required. Creating new objects of a particular type can show a different set of form fields than the edit action, depending on user requirements. However, some extendable objects don’t have any UI forms defined – their editing interface is made to fit a specific application. In such cases, it isn’t possible to provide editing options for the added columns without a custom UI form displayed by a [custom interface](/documentation/developers-and-admins/customization/extend-the-administration-interface).
See the [Customizable data classes](#customizable-data-classes) section for the limitations of each extendable object.
To edit an object’s UI form:
  1. Select a system object you extended and switch to the **UI forms** tab.
  2. Select a UI form to edit or create a new one. 
     * The two most common UI forms are _Create_ and _Edit_. They are used either when creating a new object or editing an existing one.
  3. Select **New field**. The **Database column** selector opens. 
     * Use the selector to choose the added column. Data submitted via the created form field is persisted in the selected column.
  4. Add the new columns to the form using the [field editor](/documentation/developers-and-admins/customization/field-editor). 
     * If you are creating a new UI form for the object, pay attention to existing columns marked as **Required**. If not added to a form, these columns get set to their _Default value_ as configured on the _Database columns_ tab.
     * If you are editing one of the default UI forms, keep in mind that any changes to the optional settings (_Default value_ , _Field appearance_ settings, etc.) of one of the default fields may be **overwritten during any update**. Carefully consider if such changes are truly necessary, and if so, maintain documentation of the changes so that you can easily recover if a custom change is lost during an update.


The added columns now show in the modified UI form and are editable by administration users. 
**Custom UI forms**
If you created completely custom UI forms to display the added columns, you have the following options: 
  * (Recommended) Create a [UI page](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) using the [Edit](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates) page template and display the UI form using this page.
  * (Advanced) Replace the UI form on an existing page using a [UI page extender](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-extenders). Within the override of the `ConfigurePage` method, set the `UIFormName` property of the `PageConfiguration` object to the **Code name** of the new UI form. Carefully consider the impact this change may have on the system. For example, do not replace the form used when inviting new [users](/documentation/developers-and-admins/configuration/users/user-management) to the system (**Create** under _Membership → User_) with a form that doesn’t contain an email address field. Such a change would break the UI functionality, because the underlying implementation relies on the field being present.


To access the columns on the extended object via the API, use the `GetValue` and `SetValue` methods of the given `Info` class:
C#
**Access column values using the API**
Copy
```
// Gets the value from the specified column as 'object'
// You can use 'IConversionService' to convert the value to the desired type or use a custom conversion API
var columnValue = userInfoObject.GetValue("ColumnName");

// Sets the column value to the object
userInfoObject.SetValue("ColumnName", "value");
```

## Customizable data classes
This section lists customizable classes together with additional information or limitations.
Class |  Notes  
---|---  
Commerce module  
Customer  
Customer address  
Order  
Order address  
Order item  
Shopping cart |  Objects used when implementing a [checkout process](/documentation/developers-and-admins/digital-commerce-setup/checkout-process) for [digital commerce](/documentation/developers-and-admins/digital-commerce-setup) projects. These objects can be extended using the process described on this page.  
Contact management module  
Account |  Accounts are not supported in the current version of Xperience. We don’t recommend extending this class.  
Contact |  An object that represents a [contact](/documentation/business-users/digital-marketing/contact-management) in the system. This object can be extended using the process described on this page. If you wish to include custom fields in contact field selectors, you need to add the custom fields to the appropriate [UI form](#Extendsystemobjecttypes-UIForms):
  * **Contact edit**
    * _Contact has value in field_ [contact group](/documentation/business-users/digital-marketing/contact-groups) and [automation step](/documentation/business-users/digital-marketing/automation#conditions) conditions.
    * Setting [mappings of form fields to contact attributes](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#map-form-submission-to-contact-attributes).
  * **Set contact field value step**
    * _Set contact field value_ [automation step](/documentation/business-users/digital-marketing/automation#steps).

You also need to set a suitable **Field caption**. Fields with the _Date_ or _Date and time_ data type are not supported in contact field selectors for the **Contact edit** UI form. Additionally, the custom fields you add to this form will NOT render in the contact profile edit dialog. Currently, you cannot easily add fields to this dialog. See [our how-to guide](/guides/development/customizations-and-integrations/add-custom-contact-field) to learn how you can expose custom contact fields in the UI.  
Media libraries module  
Media library |  An object that represents a [media library](/documentation/business-users/media-libraries) in the system. This object can be extended using the process described on this page.  
Media file |  Represent media files managed by media libraries. A typical example of an object with a custom editing interface. You can work with added columns [via the API](#Extendsystemobjecttypes-API). However, editing added columns via the administration requires you to build a dedicated [editing interface](/documentation/developers-and-admins/customization/extend-the-administration-interface).  
Membership module  
User |  An object that represents a [user](/documentation/developers-and-admins/configuration/users/user-management) in the system. This object can be extended using the process described on this page.  
Member |  Represents visitors that register an account in the system. See [Add fields to member objects](/documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects).  
MemberExternalLogin |  Represents external provider information used when authenticating members. Multiple logins can be paired with a single account. See [External authentication](/documentation/developers-and-admins/development/registration-and-authentication/external-authentication).  
Data protection module  
Consent |  An object that represents a [consent agreement](/documentation/developers-and-admins/data-protection/consent-management) in the system. This object can be extended using the process described on this page.  
Consent archive |  An object that stores archived versions of consent agreements. This object can be extended using the process described on this page.  
Notifications module  
Notification email |  An object that represents a [notification email](/documentation/developers-and-admins/configuration/notifications) in the system. This object can be extended using the process described on this page.  
![]()
[]()[]()
