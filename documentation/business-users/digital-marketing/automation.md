---
source: https://docs.kentico.com/documentation/business-users/digital-marketing/automation
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Business users](/documentation/business-users)
  * [Digital marketing](/documentation/business-users/digital-marketing)
  * Automation 


# Automation
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Automation allows you to set up processes that dynamically interact with your audience ([contacts](/documentation/business-users/digital-marketing/contact-management)). Each process consists of steps that perform a specific marketing action, such as sending an [email](/documentation/business-users/digital-marketing/emails). You design the steps within each process using a visual Automation Builder interface.
Once enabled, automation processes run for individual contacts who meet the trigger conditions. These contacts then move through the steps in the process.
In the current version, you can use automation to set up advanced email follow-ups when a [form is submitted](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#actions-after-form-submission) or when a visitor registers as a [member](/documentation/business-users/members). With automation, you can send a series of multiple emails, add waiting intervals, or send different emails based on conditions.
## Create an automation process
  1. Open the **Automation** application in Xperience.
  2. Select **New process**.
  3. Fill in the following properties:
     * **Process name** – the name of the process displayed in Xperience.
     * **Process recurrence** – determines if the process is allowed to run repeatedly for the same contact. See [Automation process recurrence](#automation-process-recurrence) to learn about the available options.
  4. **Save** the process. You will be redirected to the Automation Builder.
[![Empty automation process](/docsassets/documentation/automation/Automation_Process.png)](/docsassets/documentation/automation/Automation_Process.png)
  5. Within the builder, you can edit the [steps](#steps) of the automation process.


Once you finish designing the process, select **Enable**. This allows the process to trigger and run for contacts who meet the trigger conditions.
**Creating processes from forms**
You can also create automation processes directly from the _Forms_ application. This automatically sets the given form as the trigger for the process. See [Automation processes](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#automation-processes) in the forms documentation.
### Automation process recurrence
Automation processes can run repeatedly in the following ways:
  * **Always** – Runs whenever a contact meets the trigger conditions, even if the process is already running for the same contact. Use with caution, as this may result in unwanted actions, such as contacts receiving the same email multiple times.
  * **Only once** – Runs once when a contact meets the trigger conditions, and never repeats for the same contact.
  * **If not already running** – Runs whenever a contact meets the trigger conditions, but only if the process isn’t currently running for the same contact. Runs repeatedly if a contact meets the trigger conditions again after completing the process. For this type of recurrence to occur, the contact must be in the **Finish** step.


### Triggers
Triggers determine when the process runs for contacts. In the current version, the following types of triggers are available:
  * **Form** – Starts the process when a [form is submitted](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#actions-after-form-submission).
  * **Registration** – Starts the process after a visitor registers as a [member](/documentation/business-users/members). The trigger occurs only after the member becomes active (enabled), for example after confirming the registration when using double opt-in via [email confirmation](/documentation/developers-and-admins/development/registration-and-authentication/forms-authentication#email-confirmation). See [Member registration automation scenarios](#member-registration-automation-scenarios) for detailed information. This trigger type cannot be selected if the process already contains an _Email_ step configured to send an email with the _Form autoresponder_ purpose (such processes require a _Form_ trigger).
  * **Custom activity** – Starts the process after the selected [custom activity](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities) is logged.


### Steps
An automation process can contain the following types of steps:
  * **Trigger** – Fixed first step that determines when the process runs. A process can only have one trigger. See [Triggers](#triggers).
  * **Send email** – Sends an email from an email channel. The following [email purposes](/documentation/business-users/digital-marketing/emails#email-purposes) are supported:
    * _Form autoresponder_ – for scenarios where the process is triggered by an [email subscription form](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers). Use a form autoresponder email to send the double opt-in link used to confirm the subscription. Typically, this is the first email sent by the automation process.
    * _Automation_ – for all other emails sent as part of the automation process.
  * **Wait** – Contacts remain in this step for a selected time interval or until a specific date, before continuing to the next step.
  * **Log custom activity** – Logs the selected [custom activity](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities). Another automation process can be set up to trigger based on the logged activity.
  * **Set contact field value** – Allows you to change the value of the selected field for the processed contact. Currently, you can set a single fixed value for all contacts who pass through the step. For example, you can use this together with [custom fields](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration#add-custom-fields-to-contacts) to set a status flag for contacts.
**Customize the field selection**
The fields available for the step are taken from the _Set contact field value step_ [UI form](/documentation/developers-and-admins/customization/object-types/extend-system-object-types) located under the _Contact management - Contact_ system class. If you want to set the values of [custom contact fields](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration#add-custom-fields-to-contacts) using this step, you need to add them to the UI form.
[Field editor](/documentation/developers-and-admins/customization/field-editor) field settings are applied to the field value input shown in the step’s configuration, with these exceptions:
    * The default fields cannot be deleted from this UI form.
    * Marking fields as required or disabled is not supported and does not manifest in the automation step’s selector.
    * The order of the fields will not reflect in the step’s selector. The order is alphabetical by default.
    * AI features are not supported in this UI form.
    * Fields without a database column are not supported in this UI form.
    * **Condition** – Splits the process into true and false branches based on a [specified condition](#conditions). [![An example of a condition path](/docsassets/documentation/automation/Condition.png)](/docsassets/documentation/automation/Condition.png)
    * **Finish** – Represents the end of the process (either successful or not). A process may contain multiple _Finish_ steps. For processes with **If not already running** [recurrence](#automation-process-recurrence), contacts must be in this step for the process to start again.


### Conditions
When building the condition for **Condition** steps within an automation process:
  * Use the search or select categories to find the appropriate condition.
  * Conditions are organized together within condition groups.
  * Select **Add condition group** to create multiple groups. A selector appears above the groups where you can choose if **Any** or **All** of the condition groups need to be fulfilled.
  * To add individual conditions to a group, select **Add another condition**.
  * For each group, you can select if **Any** or **All** of the conditions need to be fulfilled.
[![Building a condition](/docsassets/documentation/automation/Multiple_Conditions.png)](/docsassets/documentation/automation/Multiple_Conditions.png)


When adding the _Contact field value is empty_ condition for a custom field which uses a custom data type, make sure your project’s developers have [set up the empty value for custom fields](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration/configure-custom-contact-field-empty-values).
## Automation process analytics
On the **Statistics** view of individual automation processes, you can view contact statistics for individual steps:
  * **Wait** steps.


The statistics are updated every 30 minutes. You can manually refresh the statistics on demand using the **Refresh** button.
[![Statistics of an automation process](/docsassets/documentation/automation/Statistics.png)](/docsassets/documentation/automation/Statistics.png)
You can also view a list of contacts who triggered the process on the **Contacts** view. For each contact, the list provides additional information, such as the contact’s current step. A single contact may appear in the list multiple times if repeated runs are allowed by the [recurrence setting](#automation-process-recurrence) of the process.
## Email scenarios for forms
A common scenario involves setting up a more complex email follow-up sequence after a [form submission](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#actions-after-form-submission), beyond a simple email response. For simple email scenarios, the standard form autoresponder can be used without designing a process in the Automation Builder.
With automation, you can send multiple emails as a follow-up to form submission, but the entire sequence must be predefined from the beginning, with each email sent by a separate step. You can also include wait steps or conditions between the email steps.
To comply with personal data protection regulations, you may need to implement conditions that check for some form of opt-in from the contact, such as a [consent](/documentation/developers-and-admins/data-protection/consent-management) or whether the contact is subscribed to a [recipient list](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-recipient-lists).
  * **Consent** – Can be obtained directly through the submitted form by including a _Consent agreement_ field, or more broadly (e.g., through a tracking/cookie consent banner implemented across a website).
  * **Recipient list** – The first email set by the process must use the _Form autoresponder_ [purpose](/documentation/business-users/digital-marketing/emails#email-purposes) and include a double opt-in link for the recipient list. A wait step should follow to give recipients time to complete the double opt-in process, after which conditions related to the recipient list can be used to control how the process sends further emails.


**Regular newsletter scenarios**
Automation is **not intended** for long-term newsletter distribution or regular email sending to a recipient list. In these cases, it’s difficult to predict how many email issues will be sent in the future. Currently, if an automation process is triggered by a subscription form submission for a recipient list, it can only contain a fixed number of email steps. Once a subscribed contact reaches the end of the process, there is no way to extend the process or add additional email issues.
You can [send regular emails to subscribers](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) manually.
### Multilingual emails
If you wish to send emails in multiple [languages](/documentation/developers-and-admins/configuration/languages) from an automation process, you need to prepare a separate form for each of the language variants. Each form requires its own automation process, which then sends emails from the corresponding language-specific [email channel](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management).
## Member registration automation scenarios
Processes with a _Registration_ trigger run when the _Member registration_ [activity](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities#activity-type-overview) is logged for a contact. This activity is logged only when the member becomes active (enabled), for example after confirming the registration when using double opt-in via [email confirmation](/documentation/developers-and-admins/development/registration-and-authentication/forms-authentication#email-confirmation).
### Member to contact mapping
Automation processes always run for a [contact](/documentation/business-users/digital-marketing/contact-management), not directly for the [member](/documentation/business-users/members) created during registration. The system maps the member to a contact based on the email address submitted during registration.
**Scenario** : A visitor registers after giving consent to be tracked as a contact
  * If the registration email matches the current contact’s email, the process is triggered for the current contact.
  * If the current contact does not have a known email address, the contact’s email is automatically updated to match the member registration email. The process is triggered for the current contact.
  * If the current contact has a known email address that doesn’t match the registration email, the process is triggered for: 
    * Another contact, if one exists with a matching email address.
    * A new untracked contact, which is automatically created with the member email if a matching contact doesn’t exist (based on [legitimate interest](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities#activity-logging-based-on-legitimate-interest)).


In the last case, the process runs for a different contact than the current contact. This means that any subsequent activities by the given visitor, such as page visits or clicks, are not logged for the contact in the automation process. Keep this in mind if the process uses [conditions](#conditions) that evaluate such activities.
**Scenario** : A visitor registers without giving consent to be tracked, so the current contact is unknown
  * If the registration email matches the email address of an existing contact, the process is triggered for this contact.
  * If a matching contact doesn’t exist, a new untracked contact with the registration email is automatically created (based on [legitimate interest](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities#activity-logging-based-on-legitimate-interest)). The process is triggered for this new contact.


In this scenario, the contact in the automation process is untracked, and not directly linked to the visitor. Subsequent activities by the visitor, such as page visits or clicks, are not logged at all. Keep this in mind if the process uses [conditions](#conditions) that evaluate such activities.
### Contact is member conditions
The _Contact is member_ [condition](#conditions) is only fulfilled for new members who have the [member registration activity](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities#activity-type-overview) logged. This activity cannot be logged retroactively. Pre-existing members who have no registration activity logged will not satisfy the condition.
If you have existing members who may have registered before the _Member registration_ activity became available, the condition will only work reliably in processes with the _Registration_ trigger.
Additionally, the condition requires members to exist and be active (enabled) at the time of the evaluation. If a member’s account was deleted or disabled after the member registration activity was logged, the condition is evaluated as false.
## Custom activity scenarios
You can set up an automation process to log a [custom activity](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities), which can then trigger another automation process using the logged activity as the trigger.
For example, when a user registers for an event, you can log a custom “Event registration” activity through various channels. Then, an automation process is triggered by the activity, which sends out event-related communications or feedback requests after the event ends.
The initial automation process can include a repeated condition, such as submitting a form or visiting a page, to check if the user progresses further in their registration process. If the user fails to progress in a timely manner, the process can end with a step which logs a custom activity such as “Ignored all reminders after registration and got stuck”. This would trigger a secondary process, which can initiate a re-engagement campaign.
Additionally, custom activities logged by automation processes can be used in [contact group](/documentation/business-users/digital-marketing/contact-groups) conditions, which enhances [personalization](/documentation/developers-and-admins/digital-marketing-setup/content-personalization) opportunities.
## Disable an automation process
To disable a process:
  1. Open the **Automation** application.
  2. Select the process you wish to disable from the list.
  3. On the **Automation Builder** view, select **Disable new triggers**.


The process will no longer trigger for new contacts. However, contacts already within the process will continue moving through steps until the process completes. This includes contacts in _Wait_ steps, so a process may perform actions (e.g., send emails) hours or even days after being disabled.
## Modify an automation process with active contacts
Versioning is currently not available for automation processes. If any steps are edited while contacts are actively progressing through the process, the contacts will follow the updated sequence of steps.
You cannot delete steps if the process is currently enabled, or has been enabled in the past and has logged contact statistics. At this point, there may be contacts progressing through the process, and deleting steps could result in unexpected behavior.
If you delete the entire automation process, the contact history is fully cleared, and no further steps will be performed for any contacts currently within the process.
If a contact reaches the last step in a process branch, which is not connected to another step (whether it is a **Finish** step or not), the process “finishes” and the contact becomes “stuck” in the given step. Even if you add another step to an enabled process later, the contact will **not continue**.
![]()
[]()[]()
