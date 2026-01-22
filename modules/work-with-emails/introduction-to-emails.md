---
source: https://docs.kentico.com/modules/work-with-emails/introduction-to-emails
scrape_date: 2026-01-22
---

Module: Work with emails
2 of 14 Pages
# Introduction to emails
**Email license requirements**
The license tier required for email features depends on the size of the [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) that you use:
  * Email microchannels – available for all license tiers
  * Standard size email channels – require the **Advanced** license tier


To learn more, see the description of the [Channel size](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels) property for email channels.
Xperience by Kentico allows you to prepare emails and edit their content directly in the administration interface.
## Prerequisites and limitations
To allow the system to send out emails, developers and administrators need to set up and configure:
  * One or more [email clients](/documentation/developers-and-admins/configuration/email-configuration) for sending emails (for example an SMTP server or SendGrid integration)
  * [Email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management)
  * [Content types](/documentation/developers-and-admins/development/content-types) for emails
  * Email templates – two different types of templates are available: 
    * [Macro-based email templates](/documentation/developers-and-admins/digital-marketing-setup/email-templates) (edited directly in the administration)
    * [Email Builder templates](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates) (prepared by developers within the project’s code) – only available for emails with the _Automation_ , _Regular_ and _Order status change_ purposes.
  * To send [regular emails to subscribers](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers), the [email marketing feature](/documentation/developers-and-admins/digital-marketing-setup/enable-email-subscriptions#enable-the-email-marketing-feature) must be enabled


Only emails with the [purposes](#email-purposes) described on this page can be created and edited in email channels. Other system emails are managed as [notifications](/documentation/developers-and-admins/configuration/notifications) (for example, user password reset and registration emails) or need to be created by developers.
## Email channels
Emails in Xperience are created and managed within [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management). A single project may contain multiple email channels, focusing on different content strategies, languages and audience engagement techniques. Each email channel has a unique _sending domain_ , which represents your company or brand, and is used in all _sender addresses_ for the channel’s emails.
Channels allow for a clean separation of your organization’s emails. Every email channel has **its own application** in the Xperience administration, where you can create and send emails.
[![Channel applications available in the Xperience application list](/docsassets/documentation/emails/Emails_Channel_Applications.png)](/docsassets/documentation/emails/Emails_Channel_Applications.png)
### Find emails
Email channel applications may contain a large number of emails. The search bar allows you to find emails with a name or content type matching the entered text.
You can also **Filter** the list of emails to display only selected content types or items containing specific tags.
[![Filtering in email channel applications](/docsassets/documentation/emails/Emails_Filtering.png)](/docsassets/documentation/emails/Emails_Filtering.png)
## Emails and templates
Emails created in Xperience consist of two content layers:
  * [Email templates](/documentation/developers-and-admins/digital-marketing-setup/email-templates) – define the overall layout of emails and add fixed content, such as a header or footer. You can reuse a single template for any number of emails. Two different types of email templates are available:
    * [Macro-based email templates](/documentation/developers-and-admins/digital-marketing-setup/email-templates) – edited directly in the administration. These templates contain macros that serve as placeholders for the fields of email content types, as well as system elements, such as a regular email’s unsubscribe link URL. Every email content type has a set of allowed email templates that can be selected.
    * [Email Builder templates](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates) – prepared and registered by developers within the project’s code. Allow content editors to compose emails using configurable components in the [Email Builder visual interface](/modules/work-with-emails/create-emails-in-email-builder). Only available for emails with the _Automation_ , _Regular_ and _Order status change_ purposes.
  * Emails – every email is based on a selected email template and content type. Email content is added into fields predefined by the selected content type, and then displayed within the template. Additionally, every email has system fields for setting standard email properties, such as the subject and sender information.


[![Example of an email, highlighting the content provided by the email template and the content of the email’s fields](/docsassets/documentation/emails/Email_Structure.png)](/docsassets/documentation/emails/Email_Structure.png)
## Email purposes
Every email is created with one of the following purposes:
  * **Regular** – emails sent as part of a regular series to subscribed recipients. See [Send regular emails to subscribers](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) for detailed information and instructions.
  * **Form autoresponder** – sent automatically to visitors after they submit a [form](/documentation/business-users/digital-marketing/forms). Also used to set up email marketing subscription when a double opt-in link is added to the content. Can be sent as part of an [automation process](/documentation/business-users/digital-marketing/automation) that uses a form submission trigger.
  * **Confirmation** – confirmation messages sent to users after they subscribe or unsubscribe from email marketing.
  * **Automation** – sent out by an [automation process](/documentation/business-users/digital-marketing/automation). Cannot be sent manually.
  * **Order status change** – emails automatically sent to customers when [commerce orders](/documentation/business-users/manage-commerce-stores) are created or moved between [statuses](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses).


Currently, the [Email Builder visual interface](/modules/work-with-emails/create-emails-in-email-builder) is only supported for emails with the _Regular_ , _Automation_ or _Order status change_ purposes.
[ Previous page ](/modules/work-with-emails)
2 of 14
[ Mark complete and continue ](/modules/work-with-emails/email-lifecycle)
![]()
[]()[]()
