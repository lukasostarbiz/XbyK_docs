# Create and edit forms
  * [ Copy page link ](documentation/business-users/digital-marketing/forms/create-and-edit-forms#) | [Get HelpService ID](documentation/business-users/digital-marketing/forms/create-and-edit-forms#)
Core MVC 5


[✖](documentation/business-users/digital-marketing/forms/create-and-edit-forms# "Close page link panel") [Copy to clipboard](documentation/business-users/digital-marketing/forms/create-and-edit-forms#)
You can create new forms in the **Forms** application:
  1. Open the **Forms** application.
  2. Select **New form**.


The system creates a new form and opens the **Form Builder**.
Form Builder is a graphical interface for designing forms in Xperience. Editors modify the overall form layout, add various types of fields, and adjust their position in the form. The resulting form appears as it will display on the live site.
[![Overview of the Form Builder UI](docsassets/documentation/create-and-edit-forms/FormBuilder_Overview.png)](https://docs.kentico.com/docsassets/documentation/create-and-edit-forms/FormBuilder_Overview.png)
Using Form Builder, editors can:
  * [Set form layout](documentation/business-users/digital-marketing/forms/create-and-edit-forms#set-form-layout)
  * [Add fields](documentation/business-users/digital-marketing/forms/create-and-edit-forms#add-fields)
  * [Move and reorder fields](documentation/business-users/digital-marketing/forms/create-and-edit-forms#move-and-reorder-fields)
  * [Delete fields](documentation/business-users/digital-marketing/forms/create-and-edit-forms#delete-fields)
  * [Set the submit button text](documentation/business-users/digital-marketing/forms/create-and-edit-forms#set-the-submit-button-text)


In addition to the _Form Builder_ interface, the system allows you to:
  * [Access form submissions collected from users](documentation/business-users/digital-marketing/forms/manage-form-submissions)
  * [Configure general form properties and behavior](documentation/business-users/digital-marketing/forms/create-and-edit-forms#configure-form-properties-and-behavior)
  * [Choose actions that occur after the form is submitted](documentation/business-users/digital-marketing/forms/create-and-edit-forms#actions-after-form-submission)
  * [Configure notifications about form submissions for administration users](documentation/business-users/digital-marketing/forms/create-and-edit-forms#form-submission-notifications)
  * [Set up autoresponders or advanced email automation after form submission](documentation/business-users/digital-marketing/forms/create-and-edit-forms#automation-and-autoresponders)
  * [Track usage of forms](documentation/business-users/digital-marketing/forms/create-and-edit-forms#track-usage-of-forms)
  * [Synchronize forms](documentation/business-users/digital-marketing/forms/create-and-edit-forms#synchronize-forms)
  * [Segment contacts based on form submissions](documentation/business-users/digital-marketing/forms/create-and-edit-forms#segment-contacts-based-on-form-submissions)


## Set form layout
The layout of a form is composed of sections. Each section contains one or more zones to which you can [add fields](documentation/business-users/digital-marketing/forms/create-and-edit-forms#add-fields).
The system provides a **Default** section that organizes fields in a basic single-column layout. The project’s developers may prepare additional types of sections that allow you to create more advanced form layouts.
To edit the layout of a form, add or adjust sections using gray UI elements:
  * **Add section** – select a gray  _plus_ button located on the left to insert a new section. The list of available section types depends on the implementation of your project.
  * **Move section** – hover over a section and drag it by the handle among the section buttons on the right.
  * **Change section type** – you can change the type of a section to adjust the form’s layout. Hover over the section you want to modify, select the _Change section type_ button, and select the section type you wish to use.
  * **Configure section** – opens a dialog where you can set values for properties that affect the content, appearance, or behavior of the section. The _Configure_ button is only available for sections with configurable properties. Check with your project’s administrators or developers for more information about the available section types and their properties.
  * **Delete section** – hover over the section you want to remove and select the delete button on the right. This also deletes all form fields in the section, which can lead to a loss of data in existing forms.


[![Form section management](docsassets/documentation/create-and-edit-forms/FormBuilder_Sections.png)](https://docs.kentico.com/docsassets/documentation/create-and-edit-forms/FormBuilder_Sections.png)
Changes made to sections are saved automatically. After creating the required form layout through sections, you can [move fields](documentation/business-users/digital-marketing/forms/create-and-edit-forms#move-and-reorder-fields) between the resulting zones.
## Add fields
Select the purple  _plus_ buttons in the form editing area to add new fields. The **Fields** dialog opens, which allows you to add fields from two categories:
  * **Featured** – offers fields frequently included in forms, preconfigured for easy and quick use. Many featured fields automatically map the submitted information to the [contact](documentation/business-users/digital-marketing/contact-management) representing the visitor in the system (for visitors who give [consent](documentation/developers-and-admins/data-protection/consent-management)). See the [contact attribute mapping](documentation/business-users/digital-marketing/forms/create-and-edit-forms#Createandeditforms-ContactMapping) section for more details.
  * **New field** – allows editors to select a basic field type and configure the field from scratch. Information collected by these fields is not automatically used by the system (unless [configured otherwise](documentation/business-users/digital-marketing/forms/create-and-edit-forms#Createandeditforms-ContactMapping)), but can be [accessed in the form’s submissions](documentation/business-users/digital-marketing/forms/manage-form-submissions).


[![Adding a field to a form](docsassets/documentation/create-and-edit-forms/FormBuilder_New_Field.png)](https://docs.kentico.com/docsassets/documentation/create-and-edit-forms/FormBuilder_New_Field.png)
The form saves automatically each time a new field is added. 
After creating a new field, we recommend setting an appropriate **Name** property based on the field’s purpose. By default, the _Name_ is generated based on the type of the selected field type. Changing the value later once the form starts collecting data can be problematic.
See [Field properties](documentation/business-users/digital-marketing/forms/create-and-edit-forms#field-properties) for details.
### Hide featured fields
If any of the featured fields are not relevant for your forms, you can hide them from the _Fields_ dialog that appears when adding new form fields:
  1. Switch to the **Featured fields** tab in the left-side navigation menu of the **Forms** application.
  2. Toggle the **Visibility in forms** switcher for the corresponding featured fields.


### Field properties
Fields in the form are configurable. You can change a field’s label, tooltip, default value, and other properties depending on the field type via the **properties dialog**.
  1. Select the field you wish to configure.
  2. Open the properties dialog by selecting configure (  
[![Configuring field properties](docsassets/documentation/create-and-edit-forms/FormBuilder_PropertiesDialog.png)](https://docs.kentico.com/docsassets/documentation/create-and-edit-forms/FormBuilder_PropertiesDialog.png)
  3. Select **Apply** to save your changes.


You can configure fields to behave as “smart fields”, which means they are only displayed on repeated views of a form as a replacement for other fields that the visitor has already filled in. For more information, see [Use smart fields in forms](documentation/business-users/digital-marketing/forms/use-smart-fields-in-forms).
### Field validation
Validation rules restrict which values can be submitted into form fields. For example, validation can limit the maximum number of characters or the maximum numerical value a user can enter into a field.
**Validation of empty values**
The system skips the evaluation of validation rules for form fields that are submitted with an unfilled value. If you wish to enforce user input for a particular field, mark it as **Required** in the field’s [properties dialog](documentation/business-users/digital-marketing/forms/create-and-edit-forms#field-properties).
To add validation rules to fields in Form Builder:
  1. Select a field.
  2. Open the properties dialog by selecting configure (
  3. Navigate down to the **Validation** section and select **Add rule**.
  4. Select a rule and **Continue**. 
     * The available validation rules depend on the type of the form field.  
[![Validation rules available for text fields by default](docsassets/documentation/create-and-edit-forms/FormBuilder_validation_rules.png)](https://docs.kentico.com/docsassets/documentation/create-and-edit-forms/FormBuilder_validation_rules.png)
  5. Set the validation criteria by filling in the rule’s properties, e.g., the maximum length of the text input.
  6. (Optional) Enter the **Error message** text. 
     * This text is displayed to users when the field’s value does not meet the rule’s requirements. If you leave the message empty, a default one will be generated based on the rule’s current configuration. You can check the generated error message text in the list of applied rules after you _Save_.
  7. **Save** to apply the rule to the field.  
[![Adding validation rules to a form field](docsassets/documentation/create-and-edit-forms/FormBuilder_validation.png)](https://docs.kentico.com/docsassets/documentation/create-and-edit-forms/FormBuilder_validation.png)
  8. Repeat the process if you wish to add multiple validation rules for a single field. 
     * When adding multiple validation rules, be careful not to create contradictory validation criteria that would prevent users from entering any value.
  9. Select **Apply** to save your changes.


The added validation requirements are now evaluated whenever a user submits the form. If the input is not valid, the form submission fails and the error message text appears next to the field. The user can adjust the value and submit the form again.
Xperience can use the information collected via forms to update [contacts](documentation/business-users/digital-marketing/contact-management) representing website visitors in the system. If a visitor submits a form, the system automatically transfers the entered values into the data of a matching contact (for visitors who give [consent](documentation/developers-and-admins/data-protection/consent-management)). You can also use the same approach to update the data of existing contacts when a visitor provides new information.
Mapping links a form field to a specific contact attribute. You control how form fields are mapped via the **Mapped to contact attribute** [field property](documentation/business-users/digital-marketing/forms/create-and-edit-forms#field-properties).
[![Mapping a form field to a contact attribute](docsassets/documentation/create-and-edit-forms/FormBuilder_FFtoCAmapping.png)](https://docs.kentico.com/docsassets/documentation/create-and-edit-forms/FormBuilder_FFtoCAmapping.png)
For example, if the form has a field where users enter their email address, you can choose this field for the **Email** contact attribute. When a user submits the form, the system automatically saves the field’s value as the email address of the contact representing the given user.
**Contact mapping and featured fields**
Certain _featured_ fields are preconfigured to map to the corresponding contact attribute (e.g., First name, Last name, Email). The mapping can be changed. You can view and modify the field’s configuration using the [properties dialog](documentation/business-users/digital-marketing/forms/create-and-edit-forms#field-properties).
**Contact email changes**
If you have the _Overwrite existing contact information with submitted data_ option enabled on the form’s [General tab](documentation/business-users/digital-marketing/forms/create-and-edit-forms#configure-form-properties-and-behavior), it is possible for visitors to change their contact information by submitting data into mapped fields. However, because a contact’s email address is its main identifier in Xperience, the system **does not allow the email to be changed** based on data submitted through forms.
If you have a form field mapped to the _Email_ contact attribute, and a visitor submits a different value than the associated contact’s current email address, all related field updates are performed for a different contact. Either an existing contact that matches the submitted email value is used, or a new contact is created.
In these cases, the given visitor’s associated contact remains unchanged, but all subsequent actions, such as logging of the _Form submission_ [activity](documentation/business-users/digital-marketing/contact-activities) and triggering of [automation processes](documentation/business-users/digital-marketing/automation), are done for the “other” contact that matches the submitted email address.
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
[![Submit button in the Form Builder](docsassets/documentation/create-and-edit-forms/form_submit_button.png)](https://docs.kentico.com/docsassets/documentation/create-and-edit-forms/form_submit_button.png)
The Form Builder allows editors to change the text of the submit button’s label:
  1. Hover over the submit button at the bottom of the form.
  2. Open the button’s properties dialog by selecting configure (
  3. Enter the desired text into the **Submit button text** property.
  4. Select **Apply** to save the change.


## Configure form properties and behavior
After you create a form or when editing an existing form, you can define the following properties by selecting **General** in the options panel on the right:
Property |  Description  
---|---  
Form name |  Used in the administration and when [adding forms](documentation/business-users/digital-marketing/forms/display-forms-on-pages) to pages via the **Forms** widget. New forms have an automatically generated name based on the current date and time, for example: _Form (2022-12-02_12:00)_  
Code name |  An identifier of the form. Used internally by the system and by developers. Changes of the form code name will break existing forms [placed onto pages](documentation/business-users/digital-marketing/forms/display-forms-on-pages) via the Form widget. You need to reselect the form in these widgets after the change.  
Table name |  The database table storing the collected submissions. Used by developers.  
Class name |  A globally unique identifier of the form. Used by developers. The class name is updated automatically whenever the form’s code name is changed. Such changes break any existing [code files generated for the form](documentation/developers-and-admins/api/generate-code-files-for-system-objects). Developers need to update or regenerate the code.  
Overwrite existing contact information with submitted data |  If selected, form submissions by known [contacts](documentation/business-users/digital-marketing/contact-management) update already collected contact information. If disabled, only empty contact attributes are filled.  
Select **Save** to confirm any changes.
## Actions after form submission
There are several possible actions that can occur after a user submits a form on the live site – _Display text_ , _Redirect to page_ , _Redirect to URL_. The action is configured when [placing individual forms](documentation/business-users/digital-marketing/forms/display-forms-on-pages) onto a website channel page.
## Form submission notifications
The system can be configured to send notifications to selected administration users whenever a visitor on the live site submits a particular form. See [Form submission notifications](documentation/developers-and-admins/configuration/notifications#form-submission-notifications).
## Automation and autoresponders
You can enable a basic form autoresponder or set up advanced email automation. This allows you to automatically send one or more email messages to every user who submits the form.
**Email field requirement**
To send autoresponder emails or automated email follow-ups, the form must contain a field that allows users to submit their email address. The autoresponder can only be enabled if a form field is [mapped](documentation/business-users/digital-marketing/forms/create-and-edit-forms#Createandeditforms-ContactMapping) to the **Email** contact attribute in the **Form Builder**.
### Autoresponders
When editing a form in the **Forms** application, select **Autoresponder** in the options panel on the right. To send a simple autoresponder email, select one of the following **Autoresponder** options:
  * **Select email** – sends an email prepared in an email channel application in the Xperience administration. This option is recommended for most scenarios. See [Emails](documentation/business-users/digital-marketing/emails) for more information.
  * **Custom** – for scenarios that require advanced or dynamic autoresponder content. Sends a [custom autoresponder](documentation/developers-and-admins/digital-marketing-setup/custom-form-autoresponder-emails) email prepared by developers, with any required content, including values that the user entered into the form. Note that the system does not [track statistics](documentation/business-users/digital-marketing/emails/track-email-statistics) for custom form autoresponder emails.


[![Form Autoresponder settings](docsassets/documentation/create-and-edit-forms/Forms_Autoresponder.png)](https://docs.kentico.com/docsassets/documentation/create-and-edit-forms/Forms_Autoresponder.png)
Select **Save** to confirm any changes.
### Automation processes
If you wish to set up a more advanced email scenario when a form is submitted, you can create an [automation process](documentation/business-users/digital-marketing/automation) in the **Forms** application via the **Automation** tab in the options panel on the right. This allows you to send a series of multiple emails, add waiting intervals, or send different emails based on conditions.
[![A form that triggers an automation process](docsassets/documentation/create-and-edit-forms/Forms_Automation.png)](https://docs.kentico.com/docsassets/documentation/create-and-edit-forms/Forms_Automation.png)
Once completed and enabled, the automation process is then triggered for any user who submits the form.
## Track usage of forms
The Content reuse locator feature helps you track where each form is [displayed](documentation/business-users/digital-marketing/forms/display-forms-on-pages). This can be useful if you are considering deleting a form or making significant changes, particularly if your project contains a large number of pages with forms.
  1. Open the **Forms** application. 
     * The **In use** column in the form listing indicates whether a form is displayed on at least one page.
  2. Select the form you want to track.
  3. Navigate to the **Used in** tab.


The tab displays a list of all pages that use this form in their [Page Builder content](documentation/business-users/website-content/widgets-and-page-builder) (via the _Form_ widget). You can expand the **Related items** column for the listed pages to view any content items or pages that reference the given page and use the form indirectly.
[![Viewing where a form is used](docsassets/documentation/create-and-edit-forms/Forms_Usage.png)](https://docs.kentico.com/docsassets/documentation/create-and-edit-forms/Forms_Usage.png)
**Tracking limitations**
Form usage tracking does **not include** pages that [render](documentation/developers-and-admins/development/builders/page-builder/render-widgets-in-code) the form directly in their code.
[Form submission notifications](documentation/developers-and-admins/configuration/notifications#form-submission-notifications) set up for the form are also not covered by the tracking.
To see a list of [automation processes](documentation/business-users/digital-marketing/automation) that are triggered by the form’s submission, switch to the **Form Builder** tab and select the **Automation** view mode.
## Synchronize forms
Xperience allows you to easily transfer forms and their [field configuration](documentation/business-users/digital-marketing/forms/create-and-edit-forms#add-fields) from one instance of Xperience by Kentico to another.
Currently, form synchronization does **not** include:
  * [Form submission data](documentation/business-users/digital-marketing/forms/manage-form-submissions)
  * [Autoresponder settings](documentation/business-users/digital-marketing/forms/create-and-edit-forms#autoresponders) of the form
  * [Automation processes](documentation/business-users/digital-marketing/forms/create-and-edit-forms#automation-processes) related to the form
  * [Form submission notifications](documentation/developers-and-admins/configuration/notifications#form-submission-notifications)
  * [Visibility configuration](documentation/business-users/digital-marketing/forms/create-and-edit-forms#hide-featured-fields) of featured form fields


For detailed information and instructions, see [Content sync](documentation/business-users/content-sync).
## Segment contacts based on form submissions
You can use forms to segment your contacts. For example, you can build a [contact group](documentation/business-users/digital-marketing/contact-groups) for visitors who have submitted the _Contact Us_ form.
[![Creating a contact group condition](docsassets/documentation/create-and-edit-forms/FormBuilder_ContactGroup.png)](https://docs.kentico.com/docsassets/documentation/create-and-edit-forms/FormBuilder_ContactGroup.png)