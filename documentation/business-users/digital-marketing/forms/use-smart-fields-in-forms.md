---
source: https://docs.kentico.com/documentation/business-users/digital-marketing/forms/use-smart-fields-in-forms
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Business users](/documentation/business-users)
  * [Digital marketing](/documentation/business-users/digital-marketing)
  * [Forms](/documentation/business-users/digital-marketing/forms)
  * Use smart fields in forms 


# Use smart fields in forms
Smart fields are a feature that helps keep your [forms](/documentation/business-users/digital-marketing/forms) short and easy to complete. Forms with smart fields collect data gradually over multiple submissions. When a user views the form for the first time, only an initial set of “standard” fields is displayed. Smart fields appear on subsequent views of the same form, as replacements for already submitted fields.
For example, imagine that you need a large form to gather information from customers about their preferences and shopping patterns. If you show a form with 30 fields at once, many people will lose patience while filling in the form or completely ignore it. With smart fields, you can create a form that displays only one or two fields at a time and place it onto a page that customers frequently pass through. People are more likely to fill in the data in small chunks, and the form collects the information over time.
Similar functionality is also called “progressive fields,” “queued fields,” or “progressive forms.” Xperience documentation refers to forms containing smart fields as **_smart forms_**.
## Contact tracking requirement
To display smart fields correctly, Xperience needs to track:
  * which users have submitted a given form
  * which fields they have already filled


The system uses [contact tracking](/documentation/business-users/digital-marketing/contact-management) for this purpose. As a result, filtering and hiding smart fields only works for visitors tracked as contacts by the system.
Before you can use smart forms, your project’s developers need to:
  1. Configure [contact tracking](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration).
  2. Consider data protection regulations, such as the [GDPR](/documentation/developers-and-admins/data-protection/gdpr-compliance). If required, implement a way for visitors to give and revoke [tracking consent](/documentation/developers-and-admins/data-protection/consent-development).


For visitors **who don’t consent to be tracked as contacts** , forms always **display all fields** , including smart fields. In these cases, repeated submissions by the same visitor create separate records in the form data.
**Smart forms and contact merging**
If a contact [merges](/documentation/business-users/digital-marketing/contact-management) with a different contact, the system does NOT merge the data of form submissions from these contacts. In these cases, the smart form displays fields according to the data submitted by the contact left over after the merge.
## Build forms with smart fields
The following steps guide you through the process of creating a smart form:
  1. Open the **Forms** application.
  2. [Create or edit](/documentation/business-users/digital-marketing/forms/create-and-edit-forms) a form.
  3. Add several standard fields (featured or regular) to the form.
     * These are the fields that users see when viewing the form for the first time.
     * The number of fields displayed in the form always matches the number of standard fields (non-smart).
  4. Add additional fields and enable the **Smart field** option in their properties dialog.
     * These are the fields that users do not see initially. The form only displays them on subsequent views as replacements for other fields that a user has filled.
[![Set smart fields](/docsassets/documentation/use-smart-fields-in-forms/FormBuilder_SetAsSmart.png)](/docsassets/documentation/use-smart-fields-in-forms/FormBuilder_SetAsSmart.png)
  5. Make sure your fields are organized and ordered in a way that makes sense for a smart form:
     * You can imagine smart forms as a queue of fields. The order in the queue is the same as the order of fields in Form Builder (from top to bottom in the default layout).
     * Always place the form’s standard fields before the smart fields.
     * We recommend placing your most important smart fields first.
     * Avoid fields that only make sense when viewed together with another field. For this reason, [visibility conditions](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#field-visibility) may work inconsistently, since you cannot fully control which smart fields are displayed together.


The Form Builder interface displays the 
[![Smart fields displayed in the Form Builder](/docsassets/documentation/use-smart-fields-in-forms/FormBuilder_SmartFields.png)](/docsassets/documentation/use-smart-fields-in-forms/FormBuilder_SmartFields.png)
We recommend [displaying smart forms on pages](/documentation/business-users/digital-marketing/forms/display-forms-on-pages) that your website’s audience visits repeatedly.
After a visitor tracked as a contact submits the form, the system links the resulting form submission with that visitor. When the same visitor views the form again, smart fields replace the fields they have already filled in (according to the order set within Form Builder). If the visitor submits the form again, the system automatically updates the existing form submission. This process repeats until all fields have values. Once all fields are filled, the system no longer displays the smart form at all for the given visitor.
**Notes**
  * Smart fields cannot be **Required**. Because smart fields are not always displayed, making them required would prevent users from submitting the form.
  * [Visibility conditions](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#field-visibility) have limited functionality in forms with smart fields: 
    * Never use smart fields as the reference in the visibility conditions of standard fields, since you cannot control when the smart field will be displayed.
    * Visibility conditions of smart fields with references to other smart fields only work if the fields are displayed together. The results may be inconsistent, since you cannot guarantee that smart fields will be displayed in specific pairs.
    * When a field is hidden due to a visibility condition, it is replaced by the next item in the queue of smart fields.
  * If you add a **Consent agreement** field to a smart form (see [Consent management](/documentation/developers-and-admins/data-protection/consent-management)), do not configure the consent field as a smart field. You also need to ensure that the consent text covers all possible fields in the form, even though the visitor might see only a small subset when accepting the agreement.


![]()
[]()[]()
