# Personalize emails
  * [ Copy page link ](modules/work-with-emails/personalize-emails#) | [Get HelpService ID](modules/work-with-emails/personalize-emails#)
Core MVC 5


[✖](modules/work-with-emails/personalize-emails# "Close page link panel") [Copy to clipboard](modules/work-with-emails/personalize-emails#)
Emails allow you to include personalized pieces of text based on information known about the recipient or data relevant to the email’s context.
You can add dynamic text while editing emails in an email channel application. Dynamic text is supported for text fields in **Content** view mode (fields that use a _Text input_ , _Text area_ or _Rich text editor_ form component in the [content type](documentation/developers-and-admins/development/content-types) configuration), and also in the **Plain text** dialog.
**Dynamic text not supported in Email Builder**
Dynamic text is not supported for the content of rich text editors or component properties in [Email Builder view mode](modules/work-with-emails/create-emails-in-email-builder).
Add dynamic text by entering or copying placeholders into the appropriate location within email fields:
  * For most email [purposes](modules/work-with-emails/introduction-to-emails), the following placeholders are available: 
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
When replacing dynamic text values for individual recipients, the system loads the information based on the email’s [purpose](modules/work-with-emails/introduction-to-emails):
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
  * [Email statistics](modules/work-with-emails/email-statistics-in-xperience) are not tracked for drafts.
  * Because the recipients are not linked to contacts in the system, any [personalized dynamic text](modules/work-with-emails/personalize-emails) in the email appears as it would for a fake recipient named _John Doe_.
  * Certain features, such as [unsubscription links](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers), are not functional in draft emails.


### Clone emails
When creating new emails, you can save time by reusing the content and settings of an existing email.
  1. In an email channel application, view the list of emails.
  2. Select the **Clone** (
  3. Select **Clone** in the confirmation dialog.


You can also **Clone** an email by using the drop-down list in the upper right corner in the **Content** view mode of the email you’d like to reuse.
The application creates a new email with the same content and properties as the original. Adjust the new email’s subject, content, sender, and any other properties as required.
[ Previous page ](modules/work-with-emails/email-lifecycle)
4 of 14
[ Mark complete and continue ](modules/work-with-emails/sending-newsletters-and-autoresponder-emails)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/work-with-emails/personalize-emails)
[](https://docs.kentico.com/modules/work-with-emails/personalize-emails)[](https://docs.kentico.com/modules/work-with-emails/personalize-emails)