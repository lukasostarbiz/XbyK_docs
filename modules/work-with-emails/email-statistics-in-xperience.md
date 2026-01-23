---
source: https://docs.kentico.com/modules/work-with-emails/email-statistics-in-xperience
scrape_date: 2026-01-22
---

Module: Work with emails
12 of 14 Pages
# Email statistics in Xperience
**Email license requirements**
The license tier required for email features depends on the size of the [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) that you use:
  * Email microchannels – available for all license tiers
  * Standard size email channels – require the **Advanced** license tier


To learn more, see the description of the [Channel size](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels) property for email channels.
To get the most out of your [email communication](/modules/work-with-emails/introduction-to-emails), it is important to determine the effectiveness of individual emails and optimize them according to the results. To help you achieve this, the system tracks sent emails and monitors the reactions of recipients.
Xperience by Kentico provides the following types of analytics for emails:
  * [Statistics](#email-statistics) – data such as the number of sent emails, email opens and clicked links. These statistics are anonymous, collected without connecting actions to individual recipients (with the exception of bounced emails).
  * [Contact activities](/modules/work-with-emails/view-statistics-for-emails) – actions performed by individual email recipients, specifically link clicks. Depending on your project’s [implementation](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking/log-email-activities), email activities are only logged for recipients who agree to be tracked, for example based on [consents](/documentation/developers-and-admins/data-protection/consent-management)).


Because contact activities are not always logged for all recipients, you may observe discrepancies between the number of logged activities and the email’s overall statistics (which are anonymously logged for all recipients).
## Limitations and prerequisites
To track statistics and activities for emails, the application must be [properly configured](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking). If statistics or activities are not being logged for your emails, contact your project’s administrator or developers.
Tracking is only possible for emails created using an email channel application in Xperience. Statistics and activities are not tracked for draft emails.
If an email is deleted and later [restored](/documentation/business-users/recycle_bin#restore-deleted-items), the statistics and contact activities are no longer available for this email.
## Email statistics
### Sent emails
The most basic email statistic tracks how many times an email was sent. This statistic does not always guarantee that the email was actually delivered to the recipient. Only that the email was successfully sent from the Xperience platform, e.g., to an SMTP server or SendGrid for further mailing.
In certain cases, the same email can be sent multiple times to a single recipient, for example if a visitor submits a form with an autoresponder multiple times with the same email address. In these scenarios, each sending of the email is logged separately.
**Email sending statistics for SaaS projects**
To see more comprehensive email sending statistics, visit [Email metrics](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#email-metrics) in the [Xperience Portal](https://xperience-portal.com).
### Email opens
Email opens are an important metric, tracking the number of recipients who actually opened the email. Multiple opens by a single recipient are not counted, unless the email was sent multiple times to the same recipient.
The system tracks opens by embedding a small, invisible pixel image into the content of emails. When this image is requested from the server, the system counts the email as opened for the given recipient.
  * Emails are only counted as opened if the email client of the recipient allows the loading of images. As a result, the indicated number of opened emails may be slightly lower than the actual amount.
  * If the recipient clicks on a link contained in the email, the system counts the email as opened even if images are blocked.


**Apple Mail impact on open rate reliability**
On September 20, 2021, Apple launched their Mail Privacy Protection (MPP) feature. Xperience by Kentico cannot reliably track the open rate for recipients who use the **Apple Mail** app with the MPP feature enabled. This also includes users who use other email services, with their inbox linked to Apple Mail.
MPP anonymizes email tracking by automatically fetching emails, along with all contained images (including the tracking pixel). As a result, the system identifies emails as opened even if they remain unread in the user’s inbox.
Depending on your recipient demographics and the percentage of users with Apple Mail clients, Apple MPP may significantly impact the reliability of the open rate statistics. To mitigate this, we strongly recommend evaluating your emails based on [clicked links](#clicked-links).
### Clicked links
One of the best ways to measure the effectiveness of emails is to track when recipients click links in the content. The primary goal of most emails is to get recipients to click a prominent call to action link.
When sending out emails, the system converts all links in the content of the email (and its template) to tracking links. When a recipient clicks a tracking link, the system logs a record of the event, and then redirects the user to the link’s original target.
Currently, the reports in Xperience by Kentico provide the following link click data for each email:
  * **Click rate** – the percentage of recipients who clicked at least one link within the content of the email.
  * **Unique clicks** – the number of recipients who clicked at least one link.


Separate click data for individual links is not available in the anonymous email statistics. However, you can also track link clicks as [email activities](/modules/work-with-emails/view-statistics-for-emails), which contain information about individual recipients and links.
[Unsubscribe links](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) in emails of the **Regular** type are excluded from tracking.
### Delivered emails
**Note** : Delivered email statistics are only available for [regular emails](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) sent to subscribed recipients.
When an email cannot be delivered successfully, an automatic reply informing about the problem is returned ([bounced](https://en.wikipedia.org/wiki/Bounce_message)) back to the sender or another designated address. Tracking bounced emails allows the system to identify addresses that do not correctly receive emails, which helps keep your recipient lists healthy and protects your sender reputation.
If an email address reaches the allowed limit for temporary delivery problems (soft bounces) or if a permanent delivery problem is encountered (a hard bounce), the given contact switches to the **Bounced** status and is excluded when sending further email. If necessary, marketers can **Reset bounces** for individual contacts from the **Email marketing** section of the contact’s profile in the **Contact management** application.
The delivery rate for an email is calculated by subtracting the bounce count from the total number of sent emails.
### Unsubscribe rate
**Note** : Unsubscribe rate statistics are only available for [regular emails](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) sent to subscribed recipients.
Most types of email tracking focus on “positive” actions, such as email opens and clicked links. However, it is also important to know which emails cause a negative reaction among your recipients.
The unsubscribe rate statistic shows the percentage and exact number of recipients who used the email’s [unsubscribe link](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers), and will no longer receive emails sent to the given recipient list. The system calculates the unsubscribe rate from the number of successfully delivered emails.
If an email has a high unsubscription rate, focus on identifying the problems. You can then make improvements in the content of future emails. or take other steps to recover the relationship with your recipients.
### Spam reports
**Note** : Spam report statistics are only available for [regular emails](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) sent to subscribed recipients.
Only available when using [SendGrid](/documentation/developers-and-admins/configuration/email-configuration) to send emails.
The spam reports statistic shows how many recipients marked the email as spam in their email client. Xperience by Kentico loads the spam report statistics from SendGrid – see [Spam Reports](https://docs.sendgrid.com/glossary/spam-reports) for more information.
[ Previous page ](/modules/work-with-emails/create-emails-in-email-builder)
12 of 14
[ Mark complete and continue ](/modules/work-with-emails/view-statistics-for-emails)
![]()
[]()[]()
