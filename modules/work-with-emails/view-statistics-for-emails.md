---
source: https://docs.kentico.com/modules/work-with-emails/view-statistics-for-emails
scrape_date: 2026-01-26
---

Module: Work with emails
13 of 14 Pages
# View email statistics
You can view statistics for emails in email channel applications.
The list of emails provides an overview where you can compare the effectiveness of your emails according the [open](/modules/work-with-emails/email-statistics-in-xperience) and [click](/modules/work-with-emails/email-statistics-in-xperience) rate.
To view detailed statistics for a specific email, select the email in the appropriate email channel application and switch to the **Statistics** view mode in the options panel on the right. Here you can find an _Engagement funnel_ of the email’s recipients, showing how many emails were sent, and the number of unique opens and clicks.
You can also view sending statistics of emails in the [Email metrics](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#email-metrics) application in the [Xperience Portal](https://xperience-portal.com) (for projects [deployed to the Xperience SaaS](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment)).
[![Viewing the statistics of an email](/docsassets/documentation/track-email-statistics/Emails_Statistics.png)](/docsassets/documentation/track-email-statistics/Emails_Statistics.png)
**Statistic delay**
The reports do not display emails statistics in real time as they are logged. The system calculates logged email statistics periodically every 30 minutes, so there may be a delay between updates of the displayed statistics.
You can trigger the recalculation manually for individual emails by clicking the **Refresh** button in the top right of the **Statistics** view mode.
## Contact activities for emails
The system can track _Email click_ [activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities), which represent link clicks performed by individual email recipients. Depending on your project’s [implementation](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking/log-email-activities), email activities are only logged for recipients who agree to be tracked, for example based on [consents](/documentation/developers-and-admins/data-protection/consent-management).
**Comparing email activities and statistics**
Because contact activities are not always logged for all recipients, you may observe discrepancies between the number of logged activities and the email’s overall statistics (which are anonymously logged for all recipients).
To view logged activities for a specific email, select the email in the appropriate email channel application and switch to the **Contact activities** view mode in the options panel on the right. Here, you can see information about the contact (recipient) who performed each click. The **Value** column displays the URL of the clicked link.
[![Viewing contact activities for an email](/docsassets/documentation/track-email-statistics/Emails_activities.png)](/docsassets/documentation/track-email-statistics/Emails_activities.png)
Email activities allow you to:
  * [Segment contacts into groups](/documentation/business-users/digital-marketing/contact-groups) by using “Email click” conditions
  * [Personalize content](/documentation/developers-and-admins/digital-marketing-setup/content-personalization)
  * Make decisions based on the actions that your audience takes when viewing email channel content. Such decisions can be automated by including “Email click” conditions in [automation processes](/documentation/business-users/digital-marketing/automation).


You can also view email activities in the general list of logged [activities](/documentation/business-users/digital-marketing/contact-activities) in the _Contact management_ application.
[ Previous page ](/modules/work-with-emails/email-statistics-in-xperience)
13 of 14
[ Mark complete and continue ](/modules/work-with-emails/conclusion)
![]()
[]()[]()
