---
source: https://docs.kentico.com/documentation/business-users/email-queue
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Business users](/documentation/business-users)
  * Email queue 


# Email queue
Some Xperience features utilize email messages as part of their functionality, such as password reset emails for [users](/documentation/developers-and-admins/configuration/users/user-management).
**Prerequisite**
To allow the system to send out emails, developers need to set up and configure an email client (for example an SMTP server or SendGrid integration). For more information, see [Email configuration](/documentation/developers-and-admins/configuration/email-configuration).
Outgoing emails from Xperience are first placed into a queue before they are mailed out. The queue reduces the risk of losing emails due to errors.
Users can work with the email queue in the **Email queue** application of the Xperience administration on the **Email queue** tab.
[![Viewing the email queue](/docsassets/documentation/email-queue/Email_Queue.png)](/docsassets/documentation/email-queue/Email_Queue.png)
Select emails in the queue to view their details and content. If there are any emails whose sending failed, you can manually resend or delete them.
## Sent emails archive
If archival of emails is enabled, you can view the **Sent emails** tab in the **Email queue** application. Contact your site’s administrator or developer if you wish to enable email archiving or change the number of days for which sent emails remain in the archive.
Emails are displayed on the _Sent emails_ tab after they are successfully sent out from Xperience. This does not always guarantee that the email has been delivered to the recipient. In some cases, delivery may depend on additional services that perform the actual mailing, for example when using SendGrid integration.
![]()
[]()[]()
