---
source: https://docs.kentico.com/modules/contact-management/create-and-edit-forms
scrape_date: 2026-01-22
---

Module: Contact management
29 of 44 Pages
# Create and edit forms
You can create new forms in the **Forms** application:
  1. Open the **Forms** application.
  2. Select **New form**.


The system creates a new form and opens the **Form Builder**.
Form Builder is a graphical interface for designing forms in Xperience. Editors modify the overall form layout, add various types of fields, and adjust their position in the form. The resulting form appears as it will display on the live site.
[![Overview of the Form Builder UI](/docsassets/documentation/create-and-edit-forms/FormBuilder_Overview.png)](/docsassets/documentation/create-and-edit-forms/FormBuilder_Overview.png)
Using Form Builder, editors can:
  * [Set form layout](#set-form-layout)
  * [Add fields](#add-fields)
  * [Move and reorder fields](#move-and-reorder-fields)
  * [Delete fields](#delete-fields)
  * [Set the submit button text](#set-the-submit-button-text)


In addition to the _Form Builder_ interface, the system allows you to:
  * [Access form submissions collected from users](/modules/contact-management/manage-form-submissions)
  * [Configure general form properties and behavior](/modules/contact-management/properties-and-after-submission-behavior)
  * [Choose actions that occur after the form is submitted](/modules/contact-management/properties-and-after-submission-behavior)
  * [Configure notifications about form submissions for administration users](/modules/contact-management/properties-and-after-submission-behavior)
  * [Set up autoresponders or advanced email automation after form submission](/modules/contact-management/properties-and-after-submission-behavior)
  * [Track usage of forms](/modules/contact-management/properties-and-after-submission-behavior)
  * [Synchronize forms](/modules/contact-management/properties-and-after-submission-behavior)
  * [Configure form properties and after-submission behavior](/modules/contact-management/properties-and-after-submission-behavior)


## Set form layout
The layout of a form is composed of sections. Each section contains one or more zones to which you can [add fields](#add-fields).
The system provides a **Default** section that organizes fields in a basic single-column layout. The project’s developers may prepare additional types of sections that allow you to create more advanced form layouts.
To edit the layout of a form, add or adjust sections using gray UI elements:
  * **Add section** – select a gray  _plus_ button located on the left to insert a new section. The list of available section types depends on the implementation of your project.
  * **Move section** – hover over a section and drag it by the handle among the section buttons on the right.
  * **Change section type** – you can change the type of a section to adjust the form’s layout. Hover over the section you want to modify, select the _Change section type_ button, and select the section type you wish to use.
  * **Configure section** – opens a dialog where you can set values for properties that affect the content, appearance, or behavior of the section. The _Configure_ button is only available for sections with configurable properties. Check with your project’s administrators or developers for more information about the available section types and their properties.
  * **Delete section** – hover over the section you want to remove and select the delete button on the right. This also deletes all form fields in the section, which can lead to a loss of data in existing forms.


[![Form section management](/docsassets/documentation/create-and-edit-forms/FormBuilder_Sections.png)](/docsassets/documentation/create-and-edit-forms/FormBuilder_Sections.png)
Changes made to sections are saved automatically. After creating the required form layout through sections, you can [move fields](#move-and-reorder-fields) between the resulting zones.
## Add fields
Select the purple  _plus_ buttons in the form editing area to add new fields. The **Fields** dialog opens, which allows you to add fields from two categories:
  * **Featured** – offers fields frequently included in forms, preconfigured for easy and quick use. Many featured fields automatically map the submitted information to the [contact](/modules/contact-management/contact-management) representing the visitor in the system (for visitors who give [consent](/documentation/developers-and-admins/data-protection/consent-management)). See the [contact attribute mapping](#Createandeditforms-ContactMapping) section for more details.
  * **New field** – allows editors to select a basic field type and configure the field from scratch. Information collected by these fields is not automatically used by the system (unless [configured otherwise](#Createandeditforms-ContactMapping)), but can be [accessed in the form’s submissions](/modules/contact-management/manage-form-submissions).


[![Adding a field to a form](/docsassets/documentation/create-and-edit-forms/FormBuilder_New_Field.png)](/docsassets/documentation/create-and-edit-forms/FormBuilder_New_Field.png)
The form saves automatically each time a new field is added. 
After creating a new field, we recommend setting an appropriate **Name** property based on the field’s purpose. By default, the _Name_ is generated based on the type of the selected field type. Changing the value later once the form starts collecting data can be problematic.
See [Field properties](#field-properties) for details.
### Hide featured fields
If any of the featured fields are not relevant for your forms, you can hide them from the _Fields_ dialog that appears when adding new form fields:
  1. Switch to the **Featured fields** tab in the left-side navigation menu of the **Forms** application.
  2. Toggle the **Visibility in forms** switcher for the corresponding featured fields.


### Field properties
Fields in the form are configurable. You can change a field’s label, tooltip, default value, and other properties depending on the field type via the **properties dialog**.
  1. Select the field you wish to configure.
  2. Open the properties dialog by selecting configure (  
[![Configuring field properties](/docsassets/documentation/create-and-edit-forms/FormBuilder_PropertiesDialog.png)](/docsassets/documentation/create-and-edit-forms/FormBuilder_PropertiesDialog.png)
  3. Select **Apply** to save your changes.


You can configure fields to behave as “smart fields”, which means they are only displayed on repeated views of a form as a replacement for other fields that the visitor has already filled in. For more information, see [Use smart fields in forms](/modules/contact-management/use-smart-fields-in-forms).
### Field validation
Validation rules restrict which values can be submitted into form fields. For example, validation can limit the maximum number of characters or the maximum numerical value a user can enter into a field.
**Validation of empty values**
The system skips the evaluation of validation rules for form fields that are submitted with an unfilled value. If you wish to enforce user input for a particular field, mark it as **Required** in the field’s [properties dialog](#field-properties).
To add validation rules to fields in Form Builder:
  1. Select a field.
  2. Open the properties dialog by selecting configure (
  3. Navigate down to the **Validation** section and select **Add rule**.
  4. Select a rule and **Continue**. 
     * The available validation rules depend on the type of the form field.  
[![Validation rules available for text fields by default](/docsassets/documentation/create-and-edit-forms/FormBuilder_validation_rules.png)](/docsassets/documentation/create-and-edit-forms/FormBuilder_validation_rules.png)
  5. Set the validation criteria by filling in the rule’s properties, e.g., the maximum length of the text input.
  6. (Optional) Enter the **Error message** text. 
     * This text is displayed to users when the field’s value does not meet the rule’s requirements. If you leave the message empty, a default one will be generated based on the rule’s current configuration. You can check the generated error message text in the list of applied rules after you _Save_.
  7. **Save** to apply the rule to the field.  
[![Adding validation rules to a form field](/docsassets/documentation/create-and-edit-forms/FormBuilder_validation.png)](/docsassets/documentation/create-and-edit-forms/FormBuilder_validation.png)
  8. Repeat the process if you wish to add multiple validation rules for a single field. 
     * When adding multiple validation rules, be careful not to create contradictory validation criteria that would prevent users from entering any value.
  9. Select **Apply** to save your changes.


The added validation requirements are now evaluated whenever a user submits the form. If the input is not valid, the form submission fails and the error message text appears next to the field. The user can adjust the value and submit the form again.
Xperience can use the information collected via forms to update [contacts](/modules/contact-management/contact-management) representing website visitors in the system. If a visitor submits a form, the system automatically transfers the entered values into the data of a matching contact (for visitors who give [consent](/documentation/developers-and-admins/data-protection/consent-management)). You can also use the same approach to update the data of existing contacts when a visitor provides new information.
Mapping links a form field to a specific contact attribute. You control how form fields are mapped via the **Mapped to contact attribute** [field property](#field-properties).
[![Mapping a form field to a contact attribute](/docsassets/documentation/create-and-edit-forms/FormBuilder_FFtoCAmapping.png)](/docsassets/documentation/create-and-edit-forms/FormBuilder_FFtoCAmapping.png)
For example, if the form has a field where users enter their email address, you can choose this field for the **Email** contact attribute. When a user submits the form, the system automatically saves the field’s value as the email address of the contact representing the given user.
**Contact mapping and featured fields**
Certain _featured_ fields are preconfigured to map to the corresponding contact attribute (e.g., First name, Last name, Email). The mapping can be changed. You can view and modify the field’s configuration using the [properties dialog](#field-properties).
**Contact email changes**
If you have the _Overwrite existing contact information with submitted data_ option enabled on the form’s [General tab](/modules/contact-management/properties-and-after-submission-behavior), it is possible for visitors to change their contact information by submitting data into mapped fields. However, because a contact’s email address is its main identifier in Xperience, the system **does not allow the email to be changed** based on data submitted through forms.
If you have a form field mapped to the _Email_ contact attribute, and a visitor submits a different value than the associated contact’s current email address, all related field updates are performed for a different contact. Either an existing contact that matches the submitted email value is used, or a new contact is created.
In these cases, the given visitor’s associated contact remains unchanged, but all subsequent actions, such as logging of the _Form submission_ [activity](/documentation/business-users/digital-marketing/contact-activities) and triggering of [automation processes](/modules/contact-management/analyze-and-customize-automation), are done for the “other” contact that matches the submitted email address.
## Move and reorder fields
To change the order of existing fields or move them between different zones in the form layout:
  1. Select a field in the form.
  2. Drag the field by the _handle_ (


## Delete fields
To delete fields from the form:
**Removing fields from forms with existing submissions**
Removing a field from forms with existing submissions also deletes all data the field gathered over its lifetime.
  1. Select the field you wish to remove.
  2. Select the delete (
  3. Confirm the removal via the popup dialog.


## Set the submit button text
Every form contains a button that allows users to submit the entered data. The submit button is positioned at the bottom of the form and cannot be moved.
[![Submit button in the Form Builder](/docsassets/documentation/create-and-edit-forms/form_submit_button.png)](/docsassets/documentation/create-and-edit-forms/form_submit_button.png)
The Form Builder allows editors to change the text of the submit button’s label:
  1. Hover over the submit button at the bottom of the form.
  2. Open the button’s properties dialog by selecting configure (
  3. Enter the desired text into the **Submit button text** property.
  4. Select **Apply** to save the change.


[ Previous page ](/modules/contact-management/forms)
29 of 44
[ Mark complete and continue ](/modules/contact-management/properties-and-after-submission-behavior)
![]()
[]()[]()
