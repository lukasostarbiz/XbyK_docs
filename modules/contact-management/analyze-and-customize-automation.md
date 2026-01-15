# Analyze and customize automation processes
  * [ Copy page link ](modules/contact-management/analyze-and-customize-automation#) | [Get HelpService ID](modules/contact-management/analyze-and-customize-automation#)
Core MVC 5


[✖](modules/contact-management/analyze-and-customize-automation# "Close page link panel") [Copy to clipboard](modules/contact-management/analyze-and-customize-automation#)
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Automation allows you to set up processes that dynamically interact with your audience ([contacts](modules/contact-management/contact-management)). Each process consists of steps that perform a specific marketing action, such as sending an [email](documentation/business-users/digital-marketing/emails). You design the steps within each process using a visual Automation Builder interface.
Once enabled, automation processes run for individual contacts who meet the trigger conditions. These contacts then move through the steps in the process.
In the current version, you can use automation to set up advanced email follow-ups when a [form is submitted](modules/contact-management/properties-and-after-submission-behavior#actions-after-form-submission) or when a visitor registers as a [member](modules/contact-management/members). With automation, you can send a series of multiple emails, add waiting intervals, or send different emails based on conditions.
## Create an automation process
  1. Open the **Automation** application in Xperience.
  2. Select **New process**.
  3. Fill in the following properties:
     * **Process name** – the name of the process displayed in Xperience.
     * **Process recurrence** – determines if the process is allowed to run repeatedly for the same contact. See [Automation process recurrence](modules/contact-management/analyze-and-customize-automation#automation-process-recurrence) to learn about the available options.
  4. **Save** the process. You will be redirected to the Automation Builder.
[![Empty automation process](docsassets/documentation/automation/Automation_Process.png)](https://docs.kentico.com/docsassets/documentation/automation/Automation_Process.png)
  5. Within the builder, you can edit the [steps](modules/contact-management/analyze-and-customize-automation#steps) of the automation process.


Once you finish designing the process, select **Enable**. This allows the process to trigger and run for contacts who meet the trigger conditions.
**Creating processes from forms**
You can also create automation processes directly from the _Forms_ application. This automatically sets the given form as the trigger for the process. See [Automation processes](modules/contact-management/properties-and-after-submission-behavior#automation-processes) in the forms documentation.
### Automation process recurrence
Automation processes can run repeatedly in the following ways:
  * **Always** – Runs whenever a contact meets the trigger conditions, even if the process is already running for the same contact. Use with caution, as this may result in unwanted actions, such as contacts receiving the same email multiple times.
  * **Only once** – Runs once when a contact meets the trigger conditions, and never repeats for the same contact.
  * **If not already running** – Runs whenever a contact meets the trigger conditions, but only if the process isn’t currently running for the same contact. Runs repeatedly if a contact meets the trigger conditions again after completing the process. For this type of recurrence to occur, the contact must be in the **Finish** step.


### Triggers
Triggers determine when the process runs for contacts. In the current version, the following types of triggers are available:
  * **Form** – Starts the process when a [form is submitted](modules/contact-management/properties-and-after-submission-behavior#actions-after-form-submission).
  * **Registration** – Starts the process after a visitor registers as a [member](modules/contact-management/members). The trigger occurs only after the member becomes active (enabled), for example after confirming the registration when using double opt-in via [email confirmation](documentation/developers-and-admins/development/registration-and-authentication/forms-authentication#email-confirmation). See [Member registration automation scenarios](modules/contact-management/member-registration-automation) for detailed information. This trigger type cannot be selected if the process already contains an _Email_ step configured to send an email with the _Form autoresponder_ purpose (such processes require a _Form_ trigger).
  * **Custom activity** – Starts the process after the selected [custom activity](documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities) is logged.


### Steps
An automation process can contain the following types of steps:
  * **Trigger** – Fixed first step that determines when the process runs. A process can only have one trigger. See [Triggers](modules/contact-management/analyze-and-customize-automation#triggers).
  * **Send email** – Sends an email from an email channel. The following [email purposes](documentation/business-users/digital-marketing/emails#email-purposes) are supported:
    * _Form autoresponder_ – for scenarios where the process is triggered by an [email subscription form](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers). Use a form autoresponder email to send the double opt-in link used to confirm the subscription. Typically, this is the first email sent by the automation process.
    * _Automation_ – for all other emails sent as part of the automation process.
  * **Wait** – Contacts remain in this step for a selected time interval or until a specific date, before continuing to the next step.
  * **Log custom activity** – Logs the selected [custom activity](documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities). Another automation process can be set up to trigger based on the logged activity.
  * **Set contact field value** – Allows you to change the value of the selected field for the processed contact. Currently, you can set a single fixed value for all contacts who pass through the step. For example, you can use this together with [custom fields](documentation/developers-and-admins/digital-marketing-setup/contact-configuration#add-custom-fields-to-contacts) to set a status flag for contacts.
**Customize the field selection**
The fields available for the step are taken from the _Set contact field value step_ [UI form](documentation/developers-and-admins/customization/object-types/extend-system-object-types) located under the _Contact management - Contact_ system class. If you want to set the values of [custom contact fields](documentation/developers-and-admins/digital-marketing-setup/contact-configuration#add-custom-fields-to-contacts) using this step, you need to add them to the UI form.
[Field editor](documentation/developers-and-admins/customization/field-editor) field settings are applied to the field value input shown in the step’s configuration, with these exceptions:
    * The default fields cannot be deleted from this UI form.
    * Marking fields as required or disabled is not supported and does not manifest in the automation step’s selector.
    * The order of the fields will not reflect in the step’s selector. The order is alphabetical by default.
    * AI features are not supported in this UI form.
    * Fields without a database column are not supported in this UI form.
    * **Condition** – Splits the process into true and false branches based on a [specified condition](modules/contact-management/analyze-and-customize-automation#conditions). [![An example of a condition path](docsassets/documentation/automation/Condition.png)](https://docs.kentico.com/docsassets/documentation/automation/Condition.png)
    * **Finish** – Represents the end of the process (either successful or not). A process may contain multiple _Finish_ steps. For processes with **If not already running** [recurrence](modules/contact-management/analyze-and-customize-automation#automation-process-recurrence), contacts must be in this step for the process to start again.


### Conditions
When building the condition for **Condition** steps within an automation process:
  * Use the search or select categories to find the appropriate condition.
  * Conditions are organized together within condition groups.
  * Select **Add condition group** to create multiple groups. A selector appears above the groups where you can choose if **Any** or **All** of the condition groups need to be fulfilled.
  * To add individual conditions to a group, select **Add another condition**.
  * For each group, you can select if **Any** or **All** of the conditions need to be fulfilled.
[![Building a condition](docsassets/documentation/automation/Multiple_Conditions.png)](https://docs.kentico.com/docsassets/documentation/automation/Multiple_Conditions.png)


When adding the _Contact field value is empty_ condition for a custom field which uses a custom data type, make sure your project’s developers have [set up the empty value for custom fields](documentation/developers-and-admins/digital-marketing-setup/contact-configuration/configure-custom-contact-field-empty-values).
## Automation process analytics
On the **Statistics** view of individual automation processes, you can view contact statistics for individual steps:
  * **Wait** steps.


The statistics are updated every 30 minutes. You can manually refresh the statistics on demand using the **Refresh** button.
[![Statistics of an automation process](docsassets/documentation/automation/Statistics.png)](https://docs.kentico.com/docsassets/documentation/automation/Statistics.png)
You can also view a list of contacts who triggered the process on the **Contacts** view. For each contact, the list provides additional information, such as the contact’s current step. A single contact may appear in the list multiple times if repeated runs are allowed by the [recurrence setting](modules/contact-management/analyze-and-customize-automation#automation-process-recurrence) of the process.
[ Previous page ](modules/contact-management/digital-marketing-automation-overview)
39 of 44
[ Mark complete and continue ](modules/contact-management/disable-or-modify-automation)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/contact-management/analyze-and-customize-automation)
[](https://docs.kentico.com/modules/contact-management/analyze-and-customize-automation)[](https://docs.kentico.com/modules/contact-management/analyze-and-customize-automation)