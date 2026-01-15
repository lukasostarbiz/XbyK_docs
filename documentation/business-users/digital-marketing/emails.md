# Emails
  * [ Copy page link ](documentation/business-users/digital-marketing/emails#) | [Get HelpService ID](documentation/business-users/digital-marketing/emails#)
Core MVC 5


[✖](documentation/business-users/digital-marketing/emails# "Close page link panel") [Copy to clipboard](documentation/business-users/digital-marketing/emails#)
**Email license requirements**
The license tier required for email features depends on the size of the [email channels](documentation/developers-and-admins/digital-marketing-setup/email-channel-management) that you use:
  * Email microchannels – available for all license tiers
  * Standard size email channels – require the **Advanced** license tier


To learn more, see the description of the [Channel size](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels) property for email channels.
Xperience by Kentico allows you to prepare emails and edit their content directly in the administration interface.
## Prerequisites and limitations
To allow the system to send out emails, developers and administrators need to set up and configure:
  * One or more [email clients](documentation/developers-and-admins/configuration/email-configuration) for sending emails (for example an SMTP server or SendGrid integration)
  * [Email channels](documentation/developers-and-admins/digital-marketing-setup/email-channel-management)
  * [Content types](documentation/developers-and-admins/development/content-types) for emails
  * Email templates – two different types of templates are available: 
    * [Macro-based email templates](documentation/developers-and-admins/digital-marketing-setup/email-templates) (edited directly in the administration)
    * [Email Builder templates](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates) (prepared by developers within the project’s code) – only available for emails with the _Automation_ , _Regular_ and _Order status change_ purposes.
  * To send [regular emails to subscribers](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers), the [email marketing feature](documentation/developers-and-admins/digital-marketing-setup/enable-email-subscriptions#enable-the-email-marketing-feature) must be enabled


Only emails with the [purposes](documentation/business-users/digital-marketing/emails#email-purposes) described on this page can be created and edited in email channels. Other system emails are managed as [notifications](documentation/developers-and-admins/configuration/notifications) (for example, user password reset and registration emails) or need to be created by developers.
## Email channels
Emails in Xperience are created and managed within [email channels](documentation/developers-and-admins/digital-marketing-setup/email-channel-management). A single project may contain multiple email channels, focusing on different content strategies, languages and audience engagement techniques. Each email channel has a unique _sending domain_ , which represents your company or brand, and is used in all _sender addresses_ for the channel’s emails.
Channels allow for a clean separation of your organization’s emails. Every email channel has **its own application** in the Xperience administration, where you can create and send emails.
[![Channel applications available in the Xperience application list](docsassets/documentation/emails/Emails_Channel_Applications.png)](https://docs.kentico.com/docsassets/documentation/emails/Emails_Channel_Applications.png)
### Find emails
Email channel applications may contain a large number of emails. The search bar allows you to find emails with a name or content type matching the entered text.
You can also **Filter** the list of emails to display only selected content types or items containing specific tags.
[![Filtering in email channel applications](docsassets/documentation/emails/Emails_Filtering.png)](https://docs.kentico.com/docsassets/documentation/emails/Emails_Filtering.png)
## Emails and templates
Emails created in Xperience consist of two content layers:
  * [Email templates](documentation/developers-and-admins/digital-marketing-setup/email-templates) – define the overall layout of emails and add fixed content, such as a header or footer. You can reuse a single template for any number of emails. Two different types of email templates are available:
    * [Macro-based email templates](documentation/developers-and-admins/digital-marketing-setup/email-templates) – edited directly in the administration. These templates contain macros that serve as placeholders for the fields of email content types, as well as system elements, such as a regular email’s unsubscribe link URL. Every email content type has a set of allowed email templates that can be selected.
    * [Email Builder templates](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates) – prepared and registered by developers within the project’s code. Allow content editors to compose emails using configurable components in the [Email Builder visual interface](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder). Only available for emails with the _Automation_ , _Regular_ and _Order status change_ purposes.
  * [Emails](documentation/business-users/digital-marketing/emails#create-emails) – every email is based on a selected email template and content type. Email content is added into fields predefined by the selected content type, and then displayed within the template. Additionally, every email has system fields for setting standard email properties, such as the subject and sender information.


[![Example of an email, highlighting the content provided by the email template and the content of the email’s fields](docsassets/documentation/emails/Email_Structure.png)](https://docs.kentico.com/docsassets/documentation/emails/Email_Structure.png)
## Email purposes
Every email is created with one of the following purposes:
  * **Regular** – emails sent as part of a regular series to subscribed recipients. See [Send regular emails to subscribers](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) for detailed information and instructions.
  * **Form autoresponder** – sent automatically to visitors after they submit a [form](documentation/business-users/digital-marketing/forms). Also used to set up email marketing subscription when a double opt-in link is added to the content. Can be sent as part of an [automation process](documentation/business-users/digital-marketing/automation) that uses a form submission trigger.
  * **Confirmation** – confirmation messages sent to users after they subscribe or unsubscribe from email marketing.
  * **Automation** – sent out by an [automation process](documentation/business-users/digital-marketing/automation). Cannot be sent manually.
  * **Order status change** – emails automatically sent to customers when [commerce orders](documentation/business-users/manage-commerce-stores) are created or moved between [statuses](documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses).


Currently, the [Email Builder visual interface](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder) is only supported for emails with the _Regular_ , _Automation_ or _Order status change_ purposes.
## Create emails
  1. Open an email channel application in Xperience.
  2. Select **New email**.
  3. Fill in the email properties:
     * **Name** – the name of the email displayed in Xperience (for example when selecting a form autoresponder email).
     * **Email purpose** – select the email’s purpose (see [Email purposes](documentation/business-users/digital-marketing/emails#email-purposes)). To learn more about creating regular emails, see [Send regular emails to subscribers](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers).
     * **Content type** – select the [content type](documentation/developers-and-admins/development/content-types) for your email.
  4. Select **Continue**.
  5. Choose a suitable email template ([Email Builder](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates) or [macro-based](documentation/developers-and-admins/digital-marketing-setup/email-templates)) to set the email’s layout and fixed content.
  6. Select **Create**.
  7. Prepare the email’s content:
     * In the **Content** view mode, fill in the email’s fields:
       * **Subject** – the email’s subject line.
       * **Preview text** – the brief text that recipients see in their inbox after the email sender information and the subject line. We recommend keeping the preview text between 40 and 100 characters (email clients only show a limited number of characters). The preview text only applies if it is included in the selected _Email template_ by your developers:
         * For macro-based email templates, the email body must contain a suitably placed `{%previewtext%}` [placeholder](documentation/developers-and-admins/digital-marketing-setup/email-templates#email-template-macros).
         * For Email Builder templates, see [Email preview text support](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#email-preview-text-support) for more information.
       * Other email content fields are available depending on the selected content type. Typically, there will be one or more fields where you can use the [Rich text editor](documentation/business-users/rich-text-editor) to create the email content.
       * In many cases, emails will have fields that allow you to add content by selecting **reusable content items** (blocks of text, images, etc.). See [Content hub](documentation/business-users/content-hub) to learn more.  
[![Editing email content](docsassets/documentation/emails/Email_Content.png)](https://docs.kentico.com/docsassets/documentation/emails/Email_Content.png)
**Secured content items**
When selecting reusable content items, note that secured items with the **Requires authentication** flag enabled are not included in emails. If you send an email containing secured items, the corresponding content will be missing in the email. 
Xperience by Kentico integrates AI features that allow you to generate content suggestions for email subjects and content fields. To learn how to get access and use the AI features, see [Artificial intelligence features](documentation/developers-and-admins/configuration/aira-configuration). 
     * If using an Email Builder template, you can compose the email’s content using configurable components in the [Email Builder view mode](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder).
**Email language**
All emails within an email channel use the same [language](documentation/developers-and-admins/configuration/languages). If you wish to send emails in another language, you need to switch to a different email channel. Your project’s administrators need to prepare an email channel for every language that you wish to use. 
  8. Select **Save** and then **Continue**.
  9. In the **Properties** panel, select a **Sender** (senders are predefined for the [email channel](documentation/developers-and-admins/digital-marketing-setup/email-channel-management)).
  10. Select **Save** in the **Properties** panel.


When you create a new email, it is not immediately available for sending. To continue, you need to adjust the email’s workflow status.
### Email lifecycle
When viewing an email in an email channel application, you can find the current status at the end of the administration breadcrumbs navigation, and the available action in the upper right corner of the view.
[![Email workflow status and actions](docsassets/documentation/emails/Email_Workflow.png)](https://docs.kentico.com/docsassets/documentation/emails/Email_Workflow.png)
Emails with the **Form autoresponder** , **Confirmation** , **Automation** or **Order status change** [purpose](documentation/business-users/digital-marketing/emails#email-purposes) can have one of the following statuses:
  * **Draft (Initial)** – new emails that have never been published. 
    * Cannot be sent until you use the **Publish** action to move the email to the _Published_ status.
  * **Published** – emails that are ready to be sent. 
    * Displayed as read-only and cannot be edited. If you wish to modify the email, use the **Edit email** action to change the status to _Draft_.
  * **Draft (New version)** – emails that have already been published, but a new version is being edited. 
    * If the system needs to send an email while it is in the _Draft_ status (e.g., a form autoresponder), the previously published content and properties are used.
    * You can edit the email’s content or properties and **Save** the changes.
    * When the editing is complete, use the **Publish** action to overwrite the previously published content and move back to the _Published_ status.
    * You can revert back to the previously published content at any time. Expand the available actions drop-down and select **Revert to published**.
  * **Custom workflow step** – emails can go through custom workflow steps before they are ready to be sent (“between” the _Draft_ and _Published_ status). To learn more about these steps, see [workflows](documentation/developers-and-admins/configuration/workflows). The email works as if in the _Draft_ status until the workflow step is changed to _Published_.


Emails with the **Regular** purpose are sent only once to a specific list of recipients, and **have a different workflow cycle**. See [Send regular emails to subscribers](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) to learn more.
**Linked content items and workflow**
In many cases, your emails will have fields or Email Builder component properties that allow you to add content by selecting **reusable content items** (blocks of text, images, etc.). Every linked content item has its own workflow status. If you send an email that contains unpublished content items, the corresponding content will be missing in the email.
When publishing an email with fields linking to reusable content items or pages, the cascade publishing dialog opens. You can view all linked items that are currently not in the _Published_ status. Select any of the listed items to publish them together with the email. See [Content hub](documentation/business-users/content-hub/content-items#cascade-publishing) to learn more.
### Edit emails
When editing an email in an email channel application, you can switch between view modes in the options panel on the right:
  * **Preview** – allows you to see 3 different views (_desktop_ , _mobile_ , _inbox_) of the email content as it will appear to recipients, including the fixed content from the email template. Any [personalized dynamic text](documentation/business-users/digital-marketing/emails#personalize-emails-with-dynamic-text) in the email appears as it would for a fake recipient named _John Doe_.
  * **Content** – allows you to edit the email content fields, as well as the email’s subject and preview text. While in the _Content_ view mode, you can expand the following panels: 
    * **Properties** – to adjust the email properties, i.e., the email’s template and sender information.
    * **Plain text** – to enter the plain text version of the email’s content. The plain text must combine content from all of the email’s fields, as well as static text contained in the template. Adding plain text content improves the deliverability of emails. Some recipients may prefer plain text emails, and certain email clients only accept plain text.
  * **Email Builder** – allows you to compose the email’s content using configurable components in a visual interface. Only available for emails with the _Automation_ , _Regular_ and _Order status change_ [purposes](documentation/business-users/digital-marketing/emails#email-purposes). For detailed information, see [Create emails in Email Builder](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder). While in the _Email Builder_ view mode, you can access the _Properties_ panel to adjust the email’s sender information.
  * **Statistics** – displays statistics tracked for the email, including the number of sent emails, email opens and clicked links. These statistics are anonymous, collected without connecting actions to individual recipients. See [Track email statistics](documentation/business-users/digital-marketing/emails/track-email-statistics).
  * **Contact activities** – displays actions performed by individual email recipients, specifically link clicks. Depending on your project’s [implementation](documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking/log-email-activities), email activities are only logged for recipients who agree to be tracked, for example based on [consents](documentation/developers-and-admins/data-protection/consent-management)). This can result in discrepancies between the number of logged activities and the email’s overall statistics, which are anonymously logged for all recipients.


### Personalize emails with dynamic text
Emails allow you to include personalized pieces of text based on information known about the recipient or data relevant to the email’s context.
You can add dynamic text while editing emails in an email channel application. Dynamic text is supported for text fields in **Content** view mode (fields that use a _Text input_ , _Text area_ or _Rich text editor_ form component in the [content type](documentation/developers-and-admins/development/content-types) configuration), and also in the **Plain text** dialog.
**Dynamic text not supported in Email Builder**
Dynamic text is not supported for the content of rich text editors or component properties in [Email Builder view mode](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder).
Add dynamic text by entering or copying placeholders into the appropriate location within email fields:
  * For most email [purposes](documentation/business-users/digital-marketing/emails#email-purposes), the following placeholders are available: 
    * **{{FirstName}}** – the recipient’s first name
    * **{{LastName}}** – the recipient’s last name
    * **{{Email}}** – the recipient’s email address
  * For _Order status change_ emails, the available placeholders are different, and instead allow you to access the data of the given customer’s [order](documentation/business-users/manage-commerce-stores).


Optionally, you can add a backup text to any placeholder in the following format: **{{FirstName, backup}}**. The backup text is displayed when the system doesn’t have the given value available for the recipient.
[![Email content with a dynamic text placeholder in the Preview text](docsassets/documentation/emails/Emails_Dynamic_Text_Placeholder.png)](https://docs.kentico.com/docsassets/documentation/emails/Emails_Dynamic_Text_Placeholder.png)
For **Rich text editor** fields, you can alternatively add dynamic text via the editor toolbar:
  1. Place the cursor to the desired location within a rich text field and select **Insert Dynamic Text** (
  2. Fill in the dialog: 
     * **Recipient attribute** – select the recipient attribute or order value that will be displayed.
     * **Backup text** – text displayed to recipients who do not have a value available for the selected attribute.
  3. Select **Insert**.


[![Email content with dynamic text in a rich text field](docsassets/documentation/emails/Emails_Dynamic_Text_RTE.png)](https://docs.kentico.com/docsassets/documentation/emails/Emails_Dynamic_Text_RTE.png)
The rich text field now contains a highlighted piece of dynamic text, showing the selected recipient attribute. You cannot modify dynamic text after it has been added. If you need to make adjustments, delete the text and add it again with the required values.
When sending emails to specific recipients, the system replaces dynamic text with information available for the recipient.
**Sources for dynamic text values**
When replacing dynamic text values for individual recipients, the system loads the information based on the email’s [purpose](documentation/business-users/digital-marketing/emails#email-purposes):
  * For _Regular_ and _Confirmation_ emails, the email attempts to get the values from the [contact](documentation/business-users/digital-marketing/contact-management) representing the recipient.
  * For _Form autoresponder_ emails, the values are loaded from the matching fields of the submitted form. If fields are not available for the _First_ or _Last name_ , the email attempts to get the values from the contact representing the recipient.
  * For _Automation_ emails, the values are loaded from the matching fields of the form whose submission triggered the [automation process](documentation/business-users/digital-marketing/automation). If the process isn’t triggered by a form submission or such fields are not available, the email attempts to get the values from the processed contact.
  * For _Order status change_ emails, the placeholders values are always loaded from the data of the related commerce [order](documentation/business-users/manage-commerce-stores).


### Send draft emails
You can preview and test your emails by sending a draft version to any address.
  1. Select the email in an email channel application.
  2. Make sure there are no unintended changes in the email’s content. The system automatically saves the email when sending a draft.
  3. In the **Content** view mode, select **Send draft** from the drop-down list in the upper right corner of the view.  
[![The action drop-down when editing an email](docsassets/documentation/emails/Emails_Send_Draft.png)](https://docs.kentico.com/docsassets/documentation/emails/Emails_Send_Draft.png)
  4. Enter the email addresses where you want to send the draft.
     * By default, the _To_ field is prefilled with the email address of your user account.
     * You can enter multiple email addresses separated by semicolons (;).
  5. Select **Send**.


Draft emails differ from normally sent emails in the following ways:
  * The email content is sent based on the latest edited status of the email, even if the changes are not published. Normally sent emails always contain the published version of the email content.
  * [Email statistics](documentation/business-users/digital-marketing/emails/track-email-statistics) are not tracked for drafts.
  * Because the recipients are not linked to contacts in the system, any [personalized dynamic text](documentation/business-users/digital-marketing/emails#personalize-emails-with-dynamic-text) in the email appears as it would for a fake recipient named _John Doe_.
  * Certain features, such as [unsubscription links](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers), are not functional in draft emails.


### Clone emails
When creating new emails, you can save time by reusing the content and settings of an existing email.
  1. In an email channel application, view the list of emails.
  2. Select the **Clone** (
  3. Select **Clone** in the confirmation dialog.


You can also **Clone** an email by using the drop-down list in the upper right corner in the **Content** view mode of the email you’d like to reuse.
The application creates a new email with the same content and properties as the original. Adjust the new email’s subject, content, sender, and any other properties as required.
### Send regular emails to subscribed recipients
_Regular_ emails provide additional options that allow you to send the email to contacts in a selected recipient list.
For detailed information about this scenario, see [Send regular emails to subscribers](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers).
### Send form autoresponder emails
After you create one or more _Form autoresponder_ emails, you can assign them to the autoresponder of a form:
  1. Open the **Forms** application in Xperience.
  2. Select the form in the list.
  3. Select **Autoresponder** in the options panel on the right.
  4. Pick an autoresponder email from the list.
  5. Select **Save**.


Whenever a user submits the form on your website, the system automatically sends them an email with the specified content and properties.
#### Set up automation processes
If you wish to set up a more advanced email scenario when a form is submitted, you can create an [automation process](documentation/business-users/digital-marketing/automation) in the **Automation** tab. This allows you to send a series of multiple emails, add waiting intervals, or send different emails based on conditions.
**Send email** steps in automation processes support the following email purposes:
  * **Form autoresponder** – for scenarios where the process is triggered by an [email subscription form](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers). Use the _Form autoresponder_ email to send the double opt-in link used to confirm the subscription. Typically, this is the first email sent by the automation process.
  * **Automation** – for all other emails sent as part of the automation process.


Once completed and enabled, the automation process is then triggered for any user who submits the form.