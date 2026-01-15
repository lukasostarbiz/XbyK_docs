# Configure form properties and after-submission behavior
  * [ Copy page link ](modules/contact-management/properties-and-after-submission-behavior#) | [Get HelpService ID](modules/contact-management/properties-and-after-submission-behavior#)
Core MVC 5


[✖](modules/contact-management/properties-and-after-submission-behavior# "Close page link panel") [Copy to clipboard](modules/contact-management/properties-and-after-submission-behavior#)
After you create a form or when editing an existing form, you can define the following properties by selecting **General** in the options panel on the right:
Property |  Description  
---|---  
Form name |  Used in the administration and when [adding forms](modules/contact-management/display-forms-on-pages) to pages via the **Forms** widget. New forms have an automatically generated name based on the current date and time, for example: _Form (2022-12-02_12:00)_  
Code name |  An identifier of the form. Used internally by the system and by developers. Changes of the form code name will break existing forms [placed onto pages](modules/contact-management/display-forms-on-pages) via the Form widget. You need to reselect the form in these widgets after the change.  
Table name |  The database table storing the collected submissions. Used by developers.  
Class name |  A globally unique identifier of the form. Used by developers. The class name is updated automatically whenever the form’s code name is changed. Such changes break any existing [code files generated for the form](documentation/developers-and-admins/api/generate-code-files-for-system-objects). Developers need to update or regenerate the code.  
Overwrite existing contact information with submitted data |  If selected, form submissions by known [contacts](modules/contact-management/contact-management) update already collected contact information. If disabled, only empty contact attributes are filled.  
Select **Save** to confirm any changes.
## Actions after form submission
There are several possible actions that can occur after a user submits a form on the live site – _Display text_ , _Redirect to page_ , _Redirect to URL_. The action is configured when [placing individual forms](modules/contact-management/display-forms-on-pages) onto a website channel page.
## Form submission notifications
The system can be configured to send notifications to selected administration users whenever a visitor on the live site submits a particular form. See [Form submission notifications](documentation/developers-and-admins/configuration/notifications#form-submission-notifications).
## Automation and autoresponders
You can enable a basic form autoresponder or set up advanced email automation. This allows you to automatically send one or more email messages to every user who submits the form.
**Email field requirement**
To send autoresponder emails or automated email follow-ups, the form must contain a field that allows users to submit their email address. The autoresponder can only be enabled if a form field is [mapped](modules/contact-management/create-and-edit-forms) to the **Email** contact attribute in the **Form Builder**.
### Autoresponders
When editing a form in the **Forms** application, select **Autoresponder** in the options panel on the right. To send a simple autoresponder email, select one of the following **Autoresponder** options:
  * **Select email** – sends an email prepared in an email channel application in the Xperience administration. This option is recommended for most scenarios. See [Emails](documentation/business-users/digital-marketing/emails) for more information.
  * **Custom** – for scenarios that require advanced or dynamic autoresponder content. Sends a [custom autoresponder](documentation/developers-and-admins/digital-marketing-setup/custom-form-autoresponder-emails) email prepared by developers, with any required content, including values that the user entered into the form. Note that the system does not [track statistics](documentation/business-users/digital-marketing/emails/track-email-statistics) for custom form autoresponder emails.


[![Form Autoresponder settings](docsassets/documentation/create-and-edit-forms/Forms_Autoresponder.png)](https://docs.kentico.com/docsassets/documentation/create-and-edit-forms/Forms_Autoresponder.png)
Select **Save** to confirm any changes.
### Automation processes
If you wish to set up a more advanced email scenario when a form is submitted, you can create an [automation process](modules/contact-management/analyze-and-customize-automation) in the **Forms** application via the **Automation** tab in the options panel on the right. This allows you to send a series of multiple emails, add waiting intervals, or send different emails based on conditions.
[![A form that triggers an automation process](docsassets/documentation/create-and-edit-forms/Forms_Automation.png)](https://docs.kentico.com/docsassets/documentation/create-and-edit-forms/Forms_Automation.png)
Once completed and enabled, the automation process is then triggered for any user who submits the form.
## Track usage of forms
The Content reuse locator feature helps you track where each form is [displayed](modules/contact-management/display-forms-on-pages). This can be useful if you are considering deleting a form or making significant changes, particularly if your project contains a large number of pages with forms.
  1. Open the **Forms** application. 
     * The **In use** column in the form listing indicates whether a form is displayed on at least one page.
  2. Select the form you want to track.
  3. Navigate to the **Used in** tab.


The tab displays a list of all pages that use this form in their [Page Builder content](documentation/business-users/website-content/widgets-and-page-builder) (via the _Form_ widget). You can expand the **Related items** column for the listed pages to view any content items or pages that reference the given page and use the form indirectly.
[![Viewing where a form is used](docsassets/documentation/create-and-edit-forms/Forms_Usage.png)](https://docs.kentico.com/docsassets/documentation/create-and-edit-forms/Forms_Usage.png)
**Tracking limitations**
Form usage tracking does **not include** pages that [render](documentation/developers-and-admins/development/builders/page-builder/render-widgets-in-code) the form directly in their code.
[Form submission notifications](documentation/developers-and-admins/configuration/notifications#form-submission-notifications) set up for the form are also not covered by the tracking.
To see a list of [automation processes](modules/contact-management/analyze-and-customize-automation) that are triggered by the form’s submission, switch to the **Form Builder** tab and select the **Automation** view mode.
## Synchronize forms
Xperience allows you to easily transfer forms and their [field configuration](modules/contact-management/create-and-edit-forms) from one instance of Xperience by Kentico to another.
Currently, form synchronization does **not** include:
  * [Form submission data](modules/contact-management/manage-form-submissions)
  * [Autoresponder settings](modules/contact-management/properties-and-after-submission-behavior#autoresponders) of the form
  * [Automation processes](modules/contact-management/properties-and-after-submission-behavior#automation-processes) related to the form
  * [Form submission notifications](documentation/developers-and-admins/configuration/notifications#form-submission-notifications)
  * [Visibility configuration](modules/contact-management/create-and-edit-forms#hide-featured-fields) of featured form fields


For detailed information and instructions, see [Content sync](documentation/business-users/content-sync).
## Segment contacts based on form submissions
You can use forms to segment your contacts. For example, you can build a [contact group](modules/contact-management/contact-groups) for visitors who have submitted the _Contact Us_ form.
[![Creating a contact group condition](docsassets/documentation/create-and-edit-forms/FormBuilder_ContactGroup.png)](https://docs.kentico.com/docsassets/documentation/create-and-edit-forms/FormBuilder_ContactGroup.png)
[ Previous page ](modules/contact-management/create-and-edit-forms)
30 of 44
[ Mark complete and continue ](modules/contact-management/use-smart-fields-in-forms)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/contact-management/properties-and-after-submission-behavior)
[](https://docs.kentico.com/modules/contact-management/properties-and-after-submission-behavior)[](https://docs.kentico.com/modules/contact-management/properties-and-after-submission-behavior)