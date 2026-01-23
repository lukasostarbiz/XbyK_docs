---
source: https://docs.kentico.com/modules/understand-emails/how-emails-work-behind-the-scenes
scrape_date: 2026-01-22
---

Module: Understand emails in Xperience
5 of 31 Pages
# How emails work behind the scenes
Developers and system admins need to implement a few foundational pieces to make email marketing work in Xperience. You won’t need to configure these yourself; Ath the same time, once if you understand how they work, you can make smarter requests and collaborate better with your technical team.
### 1. Email channels: starting point for every marketing email
Before you can send any marketing email from the Xperience administration UI, you need an email channel. This is a required part of the process that defines:
  * The sender information (from name and address).
  * Delivery method (SendGrid or another custom SMPT server, etc.).
  * Channel settings, such as sending limits or contact lists.


If you don’t see a way to send emails, ask your developer or administrator whether they’ve set up an email channel. You might find out that they’ve already set everything up, but forgot to provide your with correct permissions to access the channel.
[![Marketing emails channel settings in Kbank demo site](/docsassets/guides/introduction-to-emails/11_newsletters_channel_settings.png)](/docsassets/guides/introduction-to-emails/11_newsletters_channel_settings.png)
### 2. Email content types: structure that holds every message
Every email you send in Xperience is based on a content type. Developers prepare these email types and define fields your emails will have.
In the following screenshot, you can see different email content types available in the Kbank demo site.
[![Different email content types in Kbank demo site](/docsassets/guides/introduction-to-emails/7_newsletters_email_content_types.png)](/docsassets/guides/introduction-to-emails/7_newsletters_email_content_types.png)
By default, all email content types include email-specific fields, such as:
  * **Subject** : the email’s headline in the inbox.
  * **Preview text** : the summary that appears next to or below the subject in most email clients.


Your team can also extend these content types with custom fields, for example, a headline, a CTA label, or a featured image. This makes it easy to reuse content and ensures consistency across emails.
### 3. Email templates define how your your emails look and work
Xperience supports two main types of templates that define how emails are displayed:
#### Macro-based email templates
These templates are editable directly in the admin UI and use macros to insert values from the email content type. If your content type has a “Headline” field, the template can display it dynamically.
They’re especially helpful when using structured content and keeping formatting consistent.
[![Structured email fields used in macro-based email templates](/docsassets/guides/introduction-to-emails/8_newsletters_email_macro_template.png)](/docsassets/guides/introduction-to-emails/8_newsletters_email_macro_template.png)
#### Email Builder (drag-and-drop) templates
These are built using the **Email Builder** , a visual editor designed for marketers. Developers prepare a template with editable zones, and you can drag content blocks into the layout without needing any HTML knowledge.
[![Newsletter email with Email builder in the Dancing Goat demo site](/docsassets/guides/introduction-to-emails/10_newsletters_dancing_goat_email_builder.png)](/docsassets/guides/introduction-to-emails/10_newsletters_dancing_goat_email_builder.png)
**Best of both worlds** : Many Xperience solutions combine structured fields (subject and header) with Email Builder sections for flexible layout and content. You get control where you need it, and consistency where it matters.
## Licensing considerations
All Xperience projects support small-scale email sending through an [email microchannel](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management). If you plan to run larger campaigns or higher volumes, you’ll need the Advanced license, which unlocks more standard-size channels.
[ Previous page ](/modules/understand-emails/notification-in-xperience)
5 of 31
[ Mark complete and continue ](/modules/understand-emails/understand-structured-emails)
![]()
[]()[]()
