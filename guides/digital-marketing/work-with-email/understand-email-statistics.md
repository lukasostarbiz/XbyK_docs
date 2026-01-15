# Understand Xperience email stats to spot what’s working
  * Concept| [ Copy page link ](guides/digital-marketing/work-with-email/understand-email-statistics#) | [Get HelpService ID](guides/digital-marketing/work-with-email/understand-email-statistics#)
Core MVC 5


[✖](guides/digital-marketing/work-with-email/understand-email-statistics# "Close page link panel") [Copy to clipboard](guides/digital-marketing/work-with-email/understand-email-statistics#)
When you send an email, your job isn’t finished once you click the _Send_ button. Every message is a chance to build trust. Your contacts permitted you to reach their inbox, and treating their trust with respect is essential.
Email statistics help you see how your message performed: how many people received it, who opened it, and whether they clicked any links. You will see what’s working and what’s not, and improve your future messages.
Xperience collects statistics only for emails sent through a configured [email channel](documentation/developers-and-admins/digital-marketing-setup/email-channel-management). _Draft_ emails aren’t tracked. That means you’ll see real data only after you publish and send an email to actual recipients.
Not seeing any stats? Here’s what to check:
  * Ask your administrator to confirm that the _email channel_ is correctly [configured](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels).
  * Make sure the [email sender](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#add-sender-addresses) address is valid and approved for sending.
  * Check that your [recipient list](guides/digital-marketing/work-with-email/create-a-recipient-list) includes real contacts – not test addresses or users who have opted out of communication.


Remember: Email stats only appear when messages are sent to trackable contacts through a working email channel.
## Analyze email performance in Xperience
Xperience provides a few places where you can check how your emails performed. These views help you understand _delivery rates_ in [statistics](documentation/business-users/digital-marketing/emails/track-email-statistics#email-statistics), your contact’s _engagement_ through [contact activities](documentation/business-users/digital-marketing/emails/track-email-statistics#contact-activities-for-emails), and _trends_ over time.
### View stats for individual emails in the admin interface
To see statistics for a **specific email** , open the **email channel** you sent it from in the Xperience administration. _Email channel_ is the technical foundation for sending emails. Depending on how your team sets them up, the channels will have business-friendly names. For example, the Kbank demo site has one _Marketing Emails_ channel.
The number of available channels can also vary based on your project’s license.
Choose your email and switch to the **Statistics** tab. You’ll find the most important metrics here, such as how many emails were sent, how many were opened, the overall delivery rate, how many clicks each email generated, and more.
[![Email statistics overview](docsassets/guides/understand-email-statistics/email-statistics-overview.png)](https://docs.kentico.com/docsassets/guides/understand-email-statistics/email-statistics-overview.png)
### Monitor overall email health in the Xperience Portal
If you’re working on a SaaS project, you can also view email performance in the [Email metrics](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#email-metrics) application under the **Monitoring** section in the [Xperience Portal](documentation/developers-and-admins/saas/xperience-portal). This view shows statistics collected directly from the [SendGrid platform](documentation/developers-and-admins/configuration/email-configuration#sendgrid-integration), which is Xperience’s built-in email delivery provider for [SaaS](guides/configuration/saas).
Who can access the **Email metrics** app?
Access to the [Email metrics](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#email-metrics) application in the _Portal_ is separate from access to the _email channel_ in the Xperience administration. Usually, only [users with access](documentation/developers-and-admins/saas/xperience-portal/reference-xperience-portal-user-roles) to the Xperience Portal, such as administrators, can view these SaaS-level statistics. If you don’t have access, ask your project admin to check your permissions.
You’ll see total counts for **delivered** , **bounced** , and **deferred** emails, along with the number of **unique** clicks. These metrics help you spot potential deliverability issues, understand how recipients interact with your emails, and find opportunities to improve campaign performance.
[![Monitoring emails in Xperience Portal](docsassets/guides/understand-email-statistics/email-saas-monitoring-emails.png)](https://docs.kentico.com/docsassets/guides/understand-email-statistics/email-saas-monitoring-emails.png)
Comparing email performance?
Use the **Statistics** tab to review the results of individual emails, then check the **Email metrics** app in the Portal to spot delivery patterns and engagement trends across multiple sends.
Can’t find stats for an email? Double-check that:
  * The email was sent through a configured email channel.
  * You’re looking at the correct [project environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments) in SaaS (usually called _production_).
  * The email isn’t in [Draft](documentation/business-users/digital-marketing/emails#email-lifecycle) state or wasn’t sent as a test message.


## Understand the key email metrics
Email [statistics](documentation/business-users/digital-marketing/emails/track-email-statistics#email-statistics) in Xperience show a few core metrics that help you quickly assess whether your message reached people and how they reacted. Xperience calculates them based on real-world email behaviors, but it helps to understand what’s tracked, and how.
### Sent emails
This shows how many times your email was [sent](documentation/business-users/digital-marketing/emails/track-email-statistics#sent-emails) to contacts. It confirms that the email left the Xperience’s [Email queue](documentation/business-users/email-queue)and was handed off to a delivery service, like an _SMTP_ server or _SendGrid_. It doesn’t mean the message was delivered, it could still bounce later.
[![Sent emails report in Xperience](docsassets/guides/understand-email-statistics/email-statistics-sent.png)](https://docs.kentico.com/docsassets/guides/understand-email-statistics/email-statistics-sent.png)
In some cases, Xperience can send the same email to the same recipient more than once. For example, if a visitor submits the same form multiple times , which triggers an [email autoresponder](guides/digital-marketing/work-with-email/introduction-to-emails#form-autoresponders), Xperience counts each send separately.
Check the **Sent** count to see the number of delivery attempts made, but use the **Delivered** count to measure actual inbox success.
### Delivered emails
The number of [regular emails](guides/digital-marketing/work-with-email/introduction-to-emails#regular-emails-newsletters-announcements) that reached inboxes. It subtracts **hard** and **soft** bounces (temporary or permanent failures) from the total sent. It is your [most reliable indicator](documentation/business-users/digital-marketing/emails/track-email-statistics#delivered-emails) that your contact received the email.
[![Delivery rate report in Xperience](docsassets/guides/understand-email-statistics/email-statistics-delivery.png)](https://docs.kentico.com/docsassets/guides/understand-email-statistics/email-statistics-delivery.png)
If an email address causes too many **soft bounces** (temporary issues) or any **hard bounce** (permanent issue), Xperience automatically marks the contact as _Bounced_ and excludes them from future sends. You can [reset this manually](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#bounced-email-tracking) for individual contacts in [Contact management](documentation/business-users/digital-marketing/contact-management) if needed. The report doesn’t show an empty box if no such event has occurred. That’s why the following screenshots shows only **hard bounces** box and not the _soft bounces_.
[![Hard bounces in Xperience email report](docsassets/guides/understand-email-statistics/email-statistics-hard-bounces.png)](https://docs.kentico.com/docsassets/guides/understand-email-statistics/email-statistics-hard-bounces.png)
A falling delivery rate often points to problems with list quality. Try cleaning up inactive or outdated contacts to improve results.
### Email opens
This tracks how many recipients opened your email. The system uses a small invisible image (called a [tracker pixel](https://en.wikipedia.org/wiki/Spy_pixel) or _tracking pixel_ ”*) that loads when the message is opened. If the recipient’s email client blocks images, the email open isn’t tracked, unless they also click a link.Then, Xperience counts this as an open.
[![Email open rate report in Xperience](docsassets/guides/understand-email-statistics/email-statistics-opens.png)](https://docs.kentico.com/docsassets/guides/understand-email-statistics/email-statistics-opens.png)
#### Privacy policies made email opens highly unreliable
Apple [Mail Privacy Protection (MPP)](guides/digital-marketing/work-with-email/litmus.com/blog/apple-mail-privacy-protection-for-marketers) preloads remote content, such as a _tracking pixel,_ even blocks it to prevent senders from knowing if emails have been read. Xperience (and other senders) register an “open” event even if the user never actually read the email, or sometimes not at all if the provider blocks opening the _tracking pixel_.
Though _Gmail_ or other third-party email clients don’t have an alternative to Apple MPP, their _enhanced privacy_ or _encryption features_ often turn off automatic image loading by default. This means that the **open tracking fails** as it requires explicit user action, which they might not make. These inbox tools, including Apple MPP, also mostly block or obfuscate IP-address locations, making the geographical data about email interactions imprecise.
In many cases _default behaviors_ of email tools artificially inflate or deflate your _email open_ statistics, which no longer represent real user engagement. As a result, you should shift your focus to more dependable metrics like **clicks** , **conversions** , or **replies**. Campaigns that rely on IP-based data, for example, the recipient’s location or open time, are also less effective because that information is now hidden.
Use _open rate_ to get a general sense of visibility, but don’t rely on it alone. Email clients and services may skew results, so compare _open rates_ with _clicked links_ to get a better picture.
### Clicked links
This is one of the most reliable signs of recipients engaging with your email. When you send the email, Xperience rewrites links in the email content into tracking links, which record clicks before redirecting users to the original target.
You can see:
  * **Click rate** – percentage of recipients who clicked at least one link.
  * **Unique clicks** – number of individual recipients who clicked.


[![Clicks in Xperience email report](docsassets/guides/understand-email-statistics/email-statistics-clicks.png)](https://docs.kentico.com/docsassets/guides/understand-email-statistics/email-statistics-clicks.png)
_Click data_ for specific links is only available in contact activities, not in the anonymous statistics you have available in the email channel’s _Statistics_.
Clicks are harder to fake than opens. Track click rate over time to measure whether your content motivates action.
Unsubscribe links are not tracked. This ensures compliance and makes the opt-out experience clean and “by the book”.
### Unsubscribe rate
The **unsubscribe** metric shows how many people clicked the opt-out link in a [regular email](guides/digital-marketing/work-with-email/introduction-to-emails#regular-emails-newsletters-announcements). Xperience automatically removes these contacts from the [recipient list](guides/digital-marketing/work-with-email/create-a-recipient-list) and they won’t receive future messages that you send to this recipient list.
[![Unsubscribes in Xperience email report](docsassets/guides/understand-email-statistics/email-statistics-unsubscribes.png)](https://docs.kentico.com/docsassets/guides/understand-email-statistics/email-statistics-unsubscribes.png)
Xperience calculates the **unsubscribe rate** based on the number of emails that were **successfully delivered**. A few _unsubscribes_ are normal. But if the number of unsubscribes starts rising, it’s a signal that something may not be working. It might mean the content you sent is irrelevant or you’re emailing too often.
Facing too many unsubscriptions?
  * Review emails with _high unsubscribe rates_. Look at your _subject line_ , _frequency_ , and whether you’ve sent relevant emails to relevant recipients. Try small changes in tone or timing to rebuild trust with your audience.
  * A higher unsubscribe rate doesn’t always mean failure. Remove disengaged users to keep your list clean.
  * Check how many recipients opted out of future emails after receiving this one. A small number is normal, but consistent spikes should prompt a review of your content and targeting.


### Spam reports
Spam reports show how many recipients marked your email as spam in their inbox. This metric is only available when you’re [using SendGrid](documentation/developers-and-admins/configuration/email-configuration#sendgrid-integration) to send emails, as is the case for SaaS in Xperience.
[![Spam reports in Xperience email report](docsassets/guides/understand-email-statistics/email-statistics-spam-reports.png)](https://docs.kentico.com/docsassets/guides/understand-email-statistics/email-statistics-spam-reports.png)
In Xperience, spam complaints from SendGrid are pulled into the system automatically. But if you’re running [a custom SMTP server](documentation/developers-and-admins/configuration/email-configuration) or a custom-tailored delivery setup that combines different email servers, developers may be able to implement their own version of spam reporting.
#### Leverage custom spam report implementations
Behind the scenes, all major email service providers (ESPs) offer tools that allow developers to access spam and complaint data programmatically, depending on the email provider’s features. These are sometimes referred to as _complaint APIs_ , _feedback loops_ , or simply _FBLs_. For example, Amazon SES uses [Amazon SNS](https://docs.aws.amazon.com/ses/latest/dg/monitor-sending-activity-using-notifications.html) to send automatic alerts when a user flags an email. [Mailgun](https://documentation.mailgun.com/docs/mailgun/api-reference/openapi-final/complaints), [Mailchimp](https://mailchimp.com/resources/avoid-spam-filters/), [Postmark](https://spamcheck.postmarkapp.com/doc/), or [Mailtrap](https://mailtrap.io/blog/email-feedback-loop/) all provide APIs that let developers track, fetch, and manage spam complaints. Some platforms, like [MxToolbox](https://mxtoolbox.com/c/products/features/complaints), even aggregate complaints from multiple inbox providers into one dashboard.
Even a few spam complaints can hurt your sender reputation and reduce future deliverability, so it’s critical to monitor this metric closely.
Too many complaints?
Review your opt-in process and email content if you’re running into repeated spam complaints. Developers can also explore spam reporting integrations with your email infrastructure to catch issues early.
## Find out who clicked and what they clicked
While overall statistics give you a snapshot of email performance, [contact activities](guides/digital-marketing/xperience-essentials/collect-visitor-data-essentials) help you zoom in and see what individual users did with your message. This view is especially useful if you want to target engaged [recipients](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-recipient-lists), build smarter [segments](documentation/business-users/digital-marketing/contact-groups), trigger follow-up actions in [automation](documentation/business-users/digital-marketing/automation), or improve [customer journeys](guides/customer-journeys).
To view activities of individual contacts for a specific email, open the email in the [email channel](documentation/developers-and-admins/digital-marketing-setup/email-channel-management) application and switch to the **Contact activities** view in the panel on the right. You’ll see a list of **recipients** who interacted with the **email** with their **activity** , such as a **click**. Xperience displays the exact **link** they clicked in the **Value** column.
Xperience tracks [email click](documentation/business-users/digital-marketing/emails/track-email-statistics) activities for contacts who have [consented to tracking](documentation/developers-and-admins/data-protection/consent-management).
Your project’s consent policy typically controls consents. If someone hasn’t [agreed to be tracked](guides/digital-marketing/xperience-essentials/manage-visitor-data-essentials#data-protection-and-gdpr), their activity won’t appear in the _Contact activities_. Their clicks will still be counted anonymously in the overall email statistics that serve as an aggregated overview.
If you compare total clicks in the statistics view with individual clicks in Contact activities, don’t be surprised if the numbers don’t match. Anonymous clicks and identified clicks are logged separately.
Once logged, contact activities become powerful data points you can use across the platform. You can [segment your audience](guides/digital-marketing/work-with-contacts/contact-segmentation-overview) using **Email click** conditions, personalize your digital marketing content on [websites](guides/digital-marketing/xperience-essentials/personalize-website-content-essentials) or in [emails](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder#personalize-widgets) on their behavior, or build [automated processes](documentation/business-users/digital-marketing/automation) that respond when a contact clicks in an email to access something important like a _pricing page_ , _demo registration form_ , or a _product features overview_.
Combine contact activities with [email widgets](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder#email-builder-components) to personalize the email content. If someone clicked a specific type of content in the past, you can tailor the following email they receive to show similar or complementary content.
Make sure your forms and subscriptions collect consent for tracking. Without consent, you’ll lose visibility into who’s engaging with your business and miss opportunities to act on those signals.
## Check these before jumping to conclusions
Email statistics in Xperience give you valuable insights, but only if you understand how and when Xperience collects the data. These quick tips will help you avoid common misunderstandings and make you more confident when you dive into your reports.
### Email stats don’t update instantly
Email statistics in Xperience refresh automatically, but not in real time. It can take **up to 30 minutes** for new data to appear, especially after sending an email blast.
If you don’t see expected results right after sending, give it some time or use the **Refresh** button to manually reload the latest data.
### Test sends and drafts don’t count
Xperience collects statistics only for emails sent through email channels. Sent _drafts_ or links clicked in email _previews_ won’t appear in your performance reports.
To confirm your setup is working, send a small live campaign to internal contacts first. You’ll see how your stats populate in a real-life scenario.
### Watch for high bounce or unsubscribe rates
A spike in bounces or unsubscribes often points to issues with contact quality or content relevance. These numbers affect both deliverability and audience trust.
If you see frequent _soft_ or _hard bounces_ , review your contact lists for outdated or invalid emails. For unsubscribes, revisit your _subject lines_ , email _frequency_ , and overall value your emails deliver to your readers.
## Consent affects what you can track
Some metrics – especially contact activities – depend on tracking consent. Without it, you’ll only see anonymous data, which limits personalization and automation options.
Make sure your forms and subscription points clearly request consent for tracking. Even a simple checkbox can help unlock richer insights later.
## Wrap up
_Email statistics_ help you understand how your messages perform and where to improve. When you learn what each metric means and how to read it correctly, you can spot what’s working, fix what’s not, and drive your marketing activities with data-informed decisions.
Focus on reliable signals like _clicks_ and _delivery rates_ , and use _contact activities_ to take personalized action when possible. Don’t forget to watch _bounces_ , _unsubscribes_ , and _spam reports_. They’ll tell you just as much as the other metrics that only show how good your email program is.
Most importantly, use the insights to improve your next email. Email statistics are there to help you improve, not just to fill out a report.