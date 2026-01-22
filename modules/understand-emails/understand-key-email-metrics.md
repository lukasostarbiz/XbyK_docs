---
source: https://docs.kentico.com/modules/understand-emails/understand-key-email-metrics
scrape_date: 2026-01-22
---

Module: Understand emails in Xperience
13 of 31 Pages
# Understand the key email metrics
Email [statistics](/documentation/business-users/digital-marketing/emails/track-email-statistics#email-statistics) in Xperience show a few core metrics that help you quickly assess whether your message reached people and how they reacted. Xperience calculates them based on real-world email behaviors, but it helps to understand what’s tracked, and how.
### Sent emails
This shows how many times your email was [sent](/documentation/business-users/digital-marketing/emails/track-email-statistics#sent-emails) to contacts. It confirms that the email left the Xperience’s [Email queue](/documentation/business-users/email-queue)and was handed off to a delivery service, like an _SMTP_ server or _SendGrid_. It doesn’t mean the message was delivered, it could still bounce later.
[![Sent emails report in Xperience](/docsassets/guides/understand-email-statistics/email-statistics-sent.png)](/docsassets/guides/understand-email-statistics/email-statistics-sent.png)
In some cases, Xperience can send the same email to the same recipient more than once. For example, if a visitor submits the same form multiple times , which triggers an [email autoresponder](/modules/understand-emails/marketing-emails-in-xperience#form-autoresponders), Xperience counts each send separately.
Check the **Sent** count to see the number of delivery attempts made, but use the **Delivered** count to measure actual inbox success.
### Delivered emails
The number of [regular emails](/modules/understand-emails/marketing-emails-in-xperience#regular-emails-newsletters-announcements) that reached inboxes. It subtracts **hard** and **soft** bounces (temporary or permanent failures) from the total sent. It is your [most reliable indicator](/documentation/business-users/digital-marketing/emails/track-email-statistics#delivered-emails) that your contact received the email.
[![Delivery rate report in Xperience](/docsassets/guides/understand-email-statistics/email-statistics-delivery.png)](/docsassets/guides/understand-email-statistics/email-statistics-delivery.png)
If an email address causes too many **soft bounces** (temporary issues) or any **hard bounce** (permanent issue), Xperience automatically marks the contact as _Bounced_ and excludes them from future sends. You can [reset this manually](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#bounced-email-tracking) for individual contacts in [Contact management](/documentation/business-users/digital-marketing/contact-management) if needed. The report doesn’t show an empty box if no such event has occurred. That’s why the following screenshots shows only **hard bounces** box and not the _soft bounces_.
[![Hard bounces in Xperience email report](/docsassets/guides/understand-email-statistics/email-statistics-hard-bounces.png)](/docsassets/guides/understand-email-statistics/email-statistics-hard-bounces.png)
A falling delivery rate often points to problems with list quality. Try cleaning up inactive or outdated contacts to improve results.
### Email opens
This tracks how many recipients opened your email. The system uses a small invisible image (called a [tracker pixel](https://en.wikipedia.org/wiki/Spy_pixel) or _tracking pixel_ ”*) that loads when the message is opened. If the recipient’s email client blocks images, the email open isn’t tracked, unless they also click a link.Then, Xperience counts this as an open.
[![Email open rate report in Xperience](/docsassets/guides/understand-email-statistics/email-statistics-opens.png)](/docsassets/guides/understand-email-statistics/email-statistics-opens.png)
#### Privacy policies made email opens highly unreliable
Apple [Mail Privacy Protection (MPP)](litmus.com/blog/apple-mail-privacy-protection-for-marketers) preloads remote content, such as a _tracking pixel,_ even blocks it to prevent senders from knowing if emails have been read. Xperience (and other senders) register an “open” event even if the user never actually read the email, or sometimes not at all if the provider blocks opening the _tracking pixel_.
Though _Gmail_ or other third-party email clients don’t have an alternative to Apple MPP, their _enhanced privacy_ or _encryption features_ often turn off automatic image loading by default. This means that the **open tracking fails** as it requires explicit user action, which they might not make. These inbox tools, including Apple MPP, also mostly block or obfuscate IP-address locations, making the geographical data about email interactions imprecise.
In many cases _default behaviors_ of email tools artificially inflate or deflate your _email open_ statistics, which no longer represent real user engagement. As a result, you should shift your focus to more dependable metrics like **clicks** , **conversions** , or **replies**. Campaigns that rely on IP-based data, for example, the recipient’s location or open time, are also less effective because that information is now hidden.
Use _open rate_ to get a general sense of visibility, but don’t rely on it alone. Email clients and services may skew results, so compare _open rates_ with _clicked links_ to get a better picture.
### Clicked links
This is one of the most reliable signs of recipients engaging with your email. When you send the email, Xperience rewrites links in the email content into tracking links, which record clicks before redirecting users to the original target.
You can see:
  * **Click rate** – percentage of recipients who clicked at least one link.
  * **Unique clicks** – number of individual recipients who clicked.


[![Clicks in Xperience email report](/docsassets/guides/understand-email-statistics/email-statistics-clicks.png)](/docsassets/guides/understand-email-statistics/email-statistics-clicks.png)
_Click data_ for specific links is only available in contact activities, not in the anonymous statistics you have available in the email channel’s _Statistics_.
Clicks are harder to fake than opens. Track click rate over time to measure whether your content motivates action.
Unsubscribe links are not tracked. This ensures compliance and makes the opt-out experience clean and “by the book”.
### Unsubscribe rate
The **unsubscribe** metric shows how many people clicked the opt-out link in a [regular email](/modules/understand-emails/marketing-emails-in-xperience#regular-emails-newsletters-announcements). Xperience automatically removes these contacts from the [recipient list](/guides/digital-marketing/work-with-email/create-a-recipient-list) and they won’t receive future messages that you send to this recipient list.
[![Unsubscribes in Xperience email report](/docsassets/guides/understand-email-statistics/email-statistics-unsubscribes.png)](/docsassets/guides/understand-email-statistics/email-statistics-unsubscribes.png)
Xperience calculates the **unsubscribe rate** based on the number of emails that were **successfully delivered**. A few _unsubscribes_ are normal. But if the number of unsubscribes starts rising, it’s a signal that something may not be working. It might mean the content you sent is irrelevant or you’re emailing too often.
Facing too many unsubscriptions?
  * Review emails with _high unsubscribe rates_. Look at your _subject line_ , _frequency_ , and whether you’ve sent relevant emails to relevant recipients. Try small changes in tone or timing to rebuild trust with your audience.
  * A higher unsubscribe rate doesn’t always mean failure. Remove disengaged users to keep your list clean.
  * Check how many recipients opted out of future emails after receiving this one. A small number is normal, but consistent spikes should prompt a review of your content and targeting.


### Spam reports
Spam reports show how many recipients marked your email as spam in their inbox. This metric is only available when you’re [using SendGrid](/documentation/developers-and-admins/configuration/email-configuration#sendgrid-integration) to send emails, as is the case for SaaS in Xperience.
[![Spam reports in Xperience email report](/docsassets/guides/understand-email-statistics/email-statistics-spam-reports.png)](/docsassets/guides/understand-email-statistics/email-statistics-spam-reports.png)
In Xperience, spam complaints from SendGrid are pulled into the system automatically. But if you’re running [a custom SMTP server](/documentation/developers-and-admins/configuration/email-configuration) or a custom-tailored delivery setup that combines different email servers, developers may be able to implement their own version of spam reporting.
#### Leverage custom spam report implementations
Behind the scenes, all major email service providers (ESPs) offer tools that allow developers to access spam and complaint data programmatically, depending on the email provider’s features. These are sometimes referred to as _complaint APIs_ , _feedback loops_ , or simply _FBLs_. For example, Amazon SES uses [Amazon SNS](https://docs.aws.amazon.com/ses/latest/dg/monitor-sending-activity-using-notifications.html) to send automatic alerts when a user flags an email. [Mailgun](https://documentation.mailgun.com/docs/mailgun/api-reference/openapi-final/complaints), [Mailchimp](https://mailchimp.com/resources/avoid-spam-filters/), [Postmark](https://spamcheck.postmarkapp.com/doc/), or [Mailtrap](https://mailtrap.io/blog/email-feedback-loop/) all provide APIs that let developers track, fetch, and manage spam complaints. Some platforms, like [MxToolbox](https://mxtoolbox.com/c/products/features/complaints), even aggregate complaints from multiple inbox providers into one dashboard.
Even a few spam complaints can hurt your sender reputation and reduce future deliverability, so it’s critical to monitor this metric closely.
Too many complaints?
Review your opt-in process and email content if you’re running into repeated spam complaints. Developers can also explore spam reporting integrations with your email infrastructure to catch issues early.
[ Previous page ](/modules/understand-emails/analyze-emails-in-xperience)
13 of 31
[ Mark complete and continue ](/modules/understand-emails/find-out-contact-activity-interactions)
![]()
[]()[]()
