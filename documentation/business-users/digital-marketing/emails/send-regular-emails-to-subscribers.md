# Send regular emails to subscribers
  * [ Copy page link ](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#) | [Get HelpService ID](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#)
Core MVC 5


[✖](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers# "Close page link panel") [Copy to clipboard](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#)
**Email license requirements**
The license tier required for email features depends on the size of the [email channels](documentation/developers-and-admins/digital-marketing-setup/email-channel-management) that you use:
  * Email microchannels – available for all license tiers
  * Standard size email channels – require the **Advanced** license tier


To learn more, see the description of the [Channel size](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels) property for email channels.
Emails can be one of the most effective ways to promote your business or keep in touch with existing contacts.
Xperience by Kentico allows you to create and send regular emails to groups of subscribed recipients.
  1. [Set up a subscription process to build recipient lists](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#set-up-a-subscription-process)
  2. [Create and send emails](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#send-regular-emails)


## Prerequisites
To send regular emails to subscribers, developers and administrators need to set up and configure the following:
  * One or more [email clients](documentation/developers-and-admins/configuration/email-configuration) for sending emails (for example an SMTP server or SendGrid integration)
  * The [email marketing feature](documentation/developers-and-admins/digital-marketing-setup/enable-email-subscriptions#enable-the-email-marketing-feature), which adds handling of subscription and unsubscription requests
  * [Email channels](documentation/developers-and-admins/digital-marketing-setup/email-channel-management)
  * [Content types](documentation/developers-and-admins/development/content-types) for emails
  * Email templates – you can use two different types of templates for regular emails: 
    * [Macro-based email templates](documentation/developers-and-admins/digital-marketing-setup/email-templates) (edited directly in the administration)
    * [Email Builder templates](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components) (prepared and registered by developers as Razor components within the project’s code)


## Set up a subscription process
  1. [Prepare subscription pages](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#prepare-subscription-pages)
  2. [Create recipient lists](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-recipient-lists)
  3. [Add a subscription form](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#add-a-subscription-form)
  4. (Optional) [Create contact groups for recipient filtering](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-contact-groups-for-recipient-filtering)


### Prepare subscription pages
Start by adding the following subscription-related [pages](documentation/business-users/website-content) to your website:
  * “Thank you” page – displayed to users after they confirm their email subscription by clicking on a double opt-in link.
  * “Goodbye” page – displayed to users if they choose to stop receiving your emails by clicking an unsubscribe link.
Contact your site’s developers if you need help setting up [content types](documentation/developers-and-admins/development/content-types) for the pages, or ensuring that the content is displayed correctly on the website.


Add suitable content to the pages, depending on your site’s branding and audience.
### Create recipient lists
Contacts who decide to subscribe and receive your emails are stored within **recipient lists**. When sending out regular emails, marketers choose the target audience by selecting a recipient list.
You can either use a single recipient list for all email communication, or create multiple lists for different newsletters or email groups (each with its own separate subscription and unsubscription process). If you prefer to share a single recipient list for multiple types of email communication, you can use [contact groups to filter recipients](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-contact-groups-for-recipient-filtering) when sending regular emails.
To add a recipient list:
  1. Open the **Recipient lists** application.
  2. Select **New recipient list**.
  3. Enter appropriate names and optionally a _Description_.
  4. Select the **Thank you page** and **Goodbye page** in the **Approval** and **Unsubscribe settings** sections (see [Prepare subscription pages](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#prepare-subscription-pages)).
  5. (Optional) Enable the **Send (un)subscription confirmation** options. 
    1. Create suitable [emails](documentation/business-users/digital-marketing/emails) with the **Confirmation** purpose.
    2. Select the emails in the recipient list’s **Approval** and/or **Unsubscribe settings** sections.
  6. Select **Save**.


[![Viewing a recipient list](docsassets/documentation/send-regular-emails-to-subscribers/Recipient_list.png)](https://docs.kentico.com/docsassets/documentation/send-regular-emails-to-subscribers/Recipient_list.png)
The recipient list is now ready to collect subscribers. Continue by adding a subscription form to your website.
### Add a subscription form
To allow people to join your recipient lists, you need to prepare a subscription form with an autoresponder email:
  1. Create a suitable [email](documentation/business-users/digital-marketing/emails) with the **Form autoresponder** purpose. 
     * The email must use a [content type](documentation/developers-and-admins/development/content-types) that provides one or more **Rich text editor** fields.
  2. Edit the email in the **Content** view mode, and add a double opt-in link: 
    1. Place the cursor in an appropriate location within a rich text field in the email content.
    2. Select **Insert Double Opt-In Link** (
    3. Enter the **Link text**.
    4. **Select a recipient list** – choose the [recipient list](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-recipient-lists) where you want to add contacts after they subscribe.
    5. Select **Insert**.  
[![Adding a Double opt-in link to a form autoresponder email](docsassets/documentation/send-regular-emails-to-subscribers/Double_opt_in_link.png)](https://docs.kentico.com/docsassets/documentation/send-regular-emails-to-subscribers/Double_opt_in_link.png)
    6. **Save** and **Publish** the email.
**Edit double opt-in links**
You can change the text and recipient list set for double opt-in links at any time. Select the highlighted link in the email content editor, adjust the values, and select **Update**.
  3. [Create a form](documentation/business-users/digital-marketing/forms/create-and-edit-forms). 
    1. Make sure the form contains a field that allows users to submit their email address. The autoresponder can only be enabled if a form field is mapped to the **Email** contact attribute in the **Form Builder**.
    2. Open **Autoresponder** in the form’s options panel.
    3. In the **Select email** option, pick the autoresponder email containing a double opt-in link.
You can also send the autoresponder as part of a more complex [automation process](documentation/business-users/digital-marketing/automation). 
    1. Select **Save**.
  4. Place the form onto one of your website’s pages: 
     * For pages where [Page Builder](documentation/business-users/website-content/widgets-and-page-builder) is enabled, you can [add the form](documentation/business-users/digital-marketing/forms/display-forms-on-pages) using the **Form** widget.
     * To add the subscription form to a fixed part of your website, such as the shared footer, contact your project’s developers to [render the form as a stand-alone widget](documentation/developers-and-admins/development/builders/page-builder/render-widgets-in-code).


Visitors can now use the form to subscribe to emails. The subscription process consists of the following steps:
  1. A website visitor provides their email address and submits the form.
  2. The form sends an autoresponder email with a double opt-in link.
  3. The recipient clicks the link.
  4. The contact representing the visitor is added to the selected [recipient list](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-recipient-lists).
  5. The “Thank you” page selected for the recipient list is displayed to the user.
  6. If the recipient list has subscription confirmations enabled, the user also receives the selected confirmation email informing them about the subscription.


If you plan to use [automation processes](documentation/business-users/digital-marketing/automation) that send emails based on recipient lists, ensure that the process includes all necessary steps, such as double opt-in confirmations, before it is enabled. Automation processes are not a suitable approach for ongoing or indefinite email campaigns, as the process cannot be extended once a contact completes the sequence.
### Create contact groups for recipient filtering
When [sending regular emails](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#send-regular-emails), you can filter the recipients from your selected recipient list using [contact groups](documentation/business-users/digital-marketing/contact-groups). This allows you to target specific audience segments without creating and maintaining separate recipient lists.
  1. [Add contact groups](documentation/business-users/digital-marketing/contact-groups#set-up-dynamic-contact-groups) with appropriate conditions.
  2. Set the **Send only to recipients from contact groups** and/or **Exclude recipients from contact groups** properties before you send your [regular emails](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-regular-emails).


For example, you could use a recipient list together with contact groups in the following scenario:
  * Maintain a single recipient list containing all contacts who have done business with your company and subscribed to email communication.
  * Segment these contacts into contact groups based on criteria, such as engagement level, purchase history, or generated revenue (e.g., using [custom activities](documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities)).
  * Send separate versions of promotional emails about new products or services, with targeted content for each group of contacts.


**Avoid email duplication**
Be careful when setting up your contact group conditions. If you send different versions of the same email based on contact groups, a single contact may receive multiple copies if they fulfill the conditions of multiple groups. The system does **not perform frequency capping** to limit how many emails contacts receive over a given time period.
**Coordinate email sendout**
When sending separate variants of the same email to recipients from different contact groups, you can coordinate the delivery by scheduling the mailout at the same time in the future. Use the **Schedule for later** option in the **Review and send** dialog.
**Unsubscription when using a shared recipient list**
If you use a shared recipient list for different types of email communication, any recipients who [unsubscribe](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#unsubscription) will stop receiving all emails that you send via the given recipient list. This may or may not be desired behavior. Depending on your mailing requirements, you need to maintain and use an appropriate number of recipient lists.
## Send regular emails
Once you have established a [subscription process](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#set-up-a-subscription-process) on your website, you can start sending emails to contacts in your recipient lists.
### Prepare email templates
Start by creating an email template. You can use two different types of templates for regular emails:
  * [Macro-based email templates](documentation/developers-and-admins/digital-marketing-setup/email-templates) (edited directly in the administration)
  * [Email Builder templates](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components) (prepared and registered by developers as Razor components within the project’s code)


Email templates for regular emails typically need to contain an unsubscribe link. For macro-based templates, place an `{%unsubscribeurl%}` placeholder into the template’s **Source code**.
  * Position the placeholder into the `href` attribute of a suitable link tag, for example within the footer of your email template.
  * The placeholder resolves into an unsubscribe URL based on the unsubscribe settings of the [recipient list](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-recipient-lists) to which the resulting emails are sent.


HTML
**Unsubscribe link example**
Copy
```
<p>
    If you'd rather not receive future emails, <a href="{%unsubscribeurl%}">unsubscribe here</a>.
</p>
```

To learn how to add unsubscribe links to Email Builder templates, see [Regular email unsubscription links](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#regular-email-unsubscription-links).
Make sure the email template is allowed for the content types that you plan to use for your regular emails.
### Create regular emails
Repeat the following steps for every email “issue” that you wish to send out:
**Cloning emails**
If you already have one or more existing emails, you can save time by **Cloning** (
  1. Create an [email](documentation/business-users/digital-marketing/emails). 
     * Set the **Email purpose** to _Regular_.
     * Select a **Template** containing an unsubscribe link.
  2. Prepare the email’s content: 
     * You can fill in the email’s subject, preview text and other content fields in the **Content** view mode.
     * If using an Email Builder template, you can compose the email’s content using configurable components in the **Email Builder** view mode. For detailed information, see [Create emails in Email Builder](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder).
  3. Once the content is ready, select the **Continue** button (or **Properties** in the option panel on the right).
  4. Select a **Sender** (senders are predefined for the [email channel](documentation/developers-and-admins/digital-marketing-setup/email-channel-management)).
  5. In the **Send to** property, choose the [recipient list](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-recipient-lists) to which you want to send the email.
  6. (Optional) Configure additional recipient filtering based on [contact groups](documentation/business-users/digital-marketing/contact-groups): 
     * **Send only to recipients from contact groups** – limits email delivery to contacts who belong to at least one of the selected groups (in addition to belonging to the target recipient list).
     * **Exclude recipients from contact groups** – prevents email delivery to contacts who belong to any of the selected groups (even if they belong to the target recipient list and _Send only to_ contact groups).  
[![Configuring regular email properties](docsassets/documentation/send-regular-emails-to-subscribers/Email_Properties.png)](https://docs.kentico.com/docsassets/documentation/send-regular-emails-to-subscribers/Email_Properties.png)
  7. Select **Save**.
  8. If the email’s content type is under [workflow](documentation/developers-and-admins/configuration/workflows), you need to go through the workflow’s steps before the email becomes ready to be sent. Users who belong to roles with full control for the given workflow can directly send the email from any step. 
     * When you move the email to a custom workflow step with [workflow notifications](documentation/developers-and-admins/configuration/workflows#workflow-notifications) enabled, you can enter a comment about your action. The comment will be included in the notification email.  
[![Preparing the content of a regular email](docsassets/documentation/send-regular-emails-to-subscribers/Email_Send.png)](https://docs.kentico.com/docsassets/documentation/send-regular-emails-to-subscribers/Email_Send.png)
  9. To send the email, select **Review and send**. For emails under workflow, select **Change workflow step** and choose the **Schedule / Send** step.
  10. Choose one of the options: 
     * **Schedule for later** – choose a date and time in the future when the system will send the email.
     * **Send now**
  11. Select **Send**.


**Publishing linked content items**
If the email has fields linking to reusable content items or pages, you need to select **Continue** instead of _Send_. The cascade publishing dialog opens, showing all linked items that are currently not published. You can select any of the listed items to **immediately** publish them.
Carefully consider which items are ready to be published. If you cannot publish linked items immediately, you can open ([schedule](documentation/business-users/content-hub/content-items#scheduled-publishing) items to be published just before the email’s sendout time.
**Note** : The system cannot always publish all items at the exact scheduled time. If many items are scheduled to publish at once, some of them may be published with a short delay. To ensure that all linked content is ready before an email is sent, schedule linked items to publish at a slightly earlier time. This extra time is called a buffer period. The length of the buffer period depends on the total number of items scheduled to publish – 100 items may take several seconds, thousands of items may take a few minutes.
When an email is scheduled to be sent in the future, it becomes read-only and editing is not possible. If you need to make further changes, you have the following options:
  * Select **Edit email** in the upper right corner of the view to cancel the scheduled sendout and re-enable editing of the email content and properties.
  * If you only wish to adjust the sending date and time, expand the drop-down in the upper right corner and select **Reschedule**. The _Reschedule_ dialog allows you to adjust the date and time, or **Cancel the scheduled sendout** completely.


[![Rescheduling the sendout of a regular email](docsassets/documentation/send-regular-emails-to-subscribers/Email_Reschedule.png)](https://docs.kentico.com/docsassets/documentation/send-regular-emails-to-subscribers/Email_Reschedule.png)
When you send the email or the scheduled date and time is reached, the system sends the email to all contacts in the selected recipient list. Once the sending starts, the email’s editing interface becomes read-only and you can no longer make any changes to the content or properties.
**Email statistics**
Remember to review and evaluate the [statistics](documentation/business-users/digital-marketing/emails/track-email-statistics) tracked for sent emails, such as the number of delivered and opened emails or clicked links. Use the results to identify content that works best for your audience and optimize subsequent emails.
### Bounced email tracking
Bounced email tracking only works if it is configured for your application. Contact your site’s developers or see [Set up email tracking](documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking).
When an email cannot be delivered successfully, an automatic reply informing about the problem is returned ([bounced](https://en.wikipedia.org/wiki/Bounce_message)) back to the sender or another designated address. Xperience by Kentico tracks bounced emails and identifies addresses that do not correctly receive emails.
You can see the **Delivery rate** for individual regular emails by viewing the [email statistics](documentation/business-users/digital-marketing/emails/track-email-statistics). The delivery rate is calculated by subtracting the bounce count from the total number of sent emails.
If an email address reaches the allowed limit for temporary delivery problems (soft bounces) or if a permanent delivery problem is encountered (a hard bounce), the given contact switches to the **Bounced** status and is excluded when sending further emails. This helps keep your [recipient lists](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-recipient-lists) healthy and protects your sender reputation.
You can check the bounce status for recipients (contacts) in several locations within the administration interface:
  * **Contact management** – in the contact listing, as well as the profile of individual contacts
  * **Recipient lists** – in the _Overview_ and _Recipients_ listing of specific recipient lists


If necessary, marketers can **Reset bounces** for individual contacts from the **Email marketing** section of the contact’s profile in the **Contact management** application.
[![Information about email bounces in the profile of a contact](docsassets/documentation/send-regular-emails-to-subscribers/Contact_Profile_Email_Bounces.png)](https://docs.kentico.com/docsassets/documentation/send-regular-emails-to-subscribers/Contact_Profile_Email_Bounces.png)
### Unsubscription
Recipients of regular emails can choose to stop receiving your emails. The unsubscription process consists of the following steps:
  1. A recipient clicks an unsubscribe link in the email content.
  2. The “Goodbye” page selected for the recipient list is displayed to the user.
  3. If the recipient list has unsubscription confirmations enabled, the user also receives the selected confirmation email informing them about the unsubscription.


The given contact switches to the **Unsubscribed** email status in the [recipient list](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-recipient-lists) to which the email was sent. Marketers can still see the contact when viewing the recipient list in the administration, but the contact is no longer included when sending further emails to the recipient list.
You can review the unsubscribe rate and exact number of unsubscriptions in the [statistics](documentation/business-users/digital-marketing/emails/track-email-statistics) of individual regular emails.
**One-click unsubscribe support**
All emails with the _Regular_ purpose support one-click unsubscribe (based on [RFC 2369](https://tools.ietf.org/html/rfc2369) and [RFC 8058](https://tools.ietf.org/html/rfc8058)).
The system automatically includes the following headers when sending regular emails:
  * `List-Unsubscribe-Post`: _List-Unsubscribe=One-Click_
  * `List-Unsubscribe`: _< unsubscribe URL with the Service domain of the given [email channel](documentation/developers-and-admins/digital-marketing-setup/email-channel-management)>_


If a recipient uses one-click unsubscribe, the resulting request is handled in the same way as a standard unsubscribe link in email content.