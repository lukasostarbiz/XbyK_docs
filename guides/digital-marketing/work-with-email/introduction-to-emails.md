---
source: https://docs.kentico.com/guides/digital-marketing/work-with-email/introduction-to-emails
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Digital marketing](/guides/digital-marketing)
  * [Work with email](/guides/digital-marketing/work-with-email)
  * Introduction to emails in Xperience 


# Introduction to emails in Xperience
Email marketing is one of the most powerful tools available, and it’s more important today than ever. Xperience by Kentico provides flexible, built-in tools for sending emails that reach your audience when it matters most.
This guide is your starting point for understanding how email marketing works in Xperience. If you’re new to the email channels in Xperience or just want clarity on how the system fits together, you’re in the right place.
As the first guide on this topic, it’s designed to give you a conceptual overview of **Xperience email marketing features** available today. You won’t be setting up emails just yet–but by the time you finish, you’ll understand:
  * Why email marketing is still a vital tool in today’s privacy-first digital landscape.
  * What types of emails you can send with Xperience, and what purposes different emails serves.
  * What needs to be in place before you can start sending emails (like channels, templates, and content types).
  * How you and your technical team work together to build and deliver emails.


Whether you’re planning a newsletter, a welcome message, or a follow-up email after a form submission, this guide gives you the foundation you need to plan and collaborate effectively. You’ll also see how these building blocks prepare you for future updates, such as campaign features and optimization tools that will make your email strategy even more powerful.
Let’s dive in.
## Why email still matters in the age of privacy
Modern marketers face a growing challenge: reaching users in a world of strict privacy laws like GDPR, reduced browser tracking, and cookie restrictions. That’s where email shines.
**Email is a direct, permission-based channel.** When a customer fills out a form or subscribes to your newsletter, they give you the green light to stay in touch. This creates a more reliable, respectful relationship different from experience created by some digital ads or third-party tracking, which can feel intrusive or invisible to users.
While SEO helps bring new people to your website, **email helps you build long-term relationships** with those who’ve already shown interest. You don’t have to worry about changing search algorithms or losing visibility. If someone’s opted in, your message lands straight in their inbox where they’re more likely to act on it.
[![Email delivery report in Xperience by Kentico](/docsassets/guides/introduction-to-emails/5_newsletters_stats.png)](/docsassets/guides/introduction-to-emails/5_newsletters_stats.png)
## Marketing emails
Marketing emails are messages that **marketers send directly from the Xperience administration interface** to communicate with customers or website visitors. Marketers use them to build relationships, share updates, promote services, or guide users through a journey, such as a newsletter, welcome message, or special offers.
### 1. Regular emails (newsletters & announcements)
These are your go-to emails for reaching customers and subscribers with updates, promotions, news, or educational content. Use Xperience regular emails for:
  * Monthly newsletters
  * One-time announcements (like a new service or feature)
  * Seasonal offers or invitations


You send these emails manually through the Xperience administration UI, either as one-offs or on a recurring schedule. They’re ideal for maintaining visibility and nurturing long-term relationships with people who’ve already shown interest in your brand.
_An example using the Kbank demo_ : You could send small business owners or consumers a monthly newsletter with financial tips, customer stories, and information about new business banking services.
[![Sample newsletter email in Kbank demo site](/docsassets/guides/introduction-to-emails/1_newsletters_newsletter_email.png)](/docsassets/guides/introduction-to-emails/1_newsletters_newsletter_email.png)
#### What you need to send a regular email
Before you can send a regular email, you need to have a recipient list. This is a critical piece of setup that tells the system who should receive your message.
There are two main ways to build this list:
  * **Organic subscriptions** : The most common method is through a form on your website. When someone fills it out (for example, to receive your newsletter), their contact is automatically added to your recipient list.
  * **Custom integrations** : Developers can help set up more advanced methods to create recipient lists, for example, importing contacts from an external system like a CRM. This is useful when you already have a base of qualified contacts and want to email them without manual copy-paste work.


Without a _recipient list_ , you won’t be able to send a regular email. The send button remains unavailable until a valid list is assigned.
[![Overview of a recipient list in Xperience by Kentico](/docsassets/guides/introduction-to-emails/4_newsletters_recipient_list.png)](/docsassets/guides/introduction-to-emails/4_newsletters_recipient_list.png)
Depending on your requirements and approach to marketing, you can:
  * Maintain multiple recipient lists, one for each type of email communication.
  * Keep a single recipient list containing all marketable contacts and use contact groups to target the audience for individual emails. 
    * Note: Using a shared recipient list means that unsubscription is also shared for all emails sent to the given list.
  * Combine the options above according to your needs.


**What’s the difference between a recipient list and a contact group?**
In Xperience, it’s important to distinguish between:
  * **Recipient lists** : Used specifically for email marketing. These define who receives your newsletters and announcements, without affecting segmentation.
  * **Contact groups** : Used for personalization and segmentation in general. While they can contain many of the same contacts, they live in a different context.


When you’re planning an email, start by choosing the target recipient list. You can then apply further filtering by selecting contact groups to which the recipients must or cannot belong.
### 2. Form autoresponders
Form autoresponders are automatic emails triggered when someone submits a form on your website. They’re often short and to the point, used to thank the person, confirm their submission, or deliver something they requested, like a PDF or event invite.
These emails create a sense of reliability and professionalism. Visitors know their request was received, and you can immediately follow up with useful next steps.
[![Sample autoresponder email on the Kbank demo site](/docsassets/guides/introduction-to-emails/2_newletters_form_autoresponder.png)](/docsassets/guides/introduction-to-emails/2_newletters_form_autoresponder.png)
_Example using the Kbank demo:_ When visitors request an account or a loan consultation, they immediately receive an email confirming the request. In real-life scenarios, this email can contain links to a checklist of documents they’ll need to prepare for the call or additional details about the banker that will be calling.
### Decide whether to send an autoresponder or automation
You can set up a form to send an autoresponder directly, without involving any automation. But if you want to create a more flexible workflow, like delaying the message, adding a second follow-up, or triggering other emails. You can also use the Marketing Automation app to handle autoresponders.
For example:
  1. A form submission could trigger an automation that sends the initial confirmation email right away.
  2. Then, a follow-up email gets sent two days later with extra resources.


This makes it easy to turn a simple form submission into the start of a mini customer journey.
**Don’t forget to publish the email**
Before you can add an email as a form autoresponder directly or within an automation, the email must be published. You won’t see emails in Draft status while configuring the autoresponder behavior.
### 3. Automation emails
Automation emails are messages sent **automatically as part of a predefined workflow**. These emails are triggered by specific actions like customers joining a newsletter, submitting a form, or registering on the website, and help you deliver timely, relevant content without manual effort.
This is where you can start shaping personalized customer journeys. Instead of sending one email and hoping for the best, you can guide users based on what they do over time.
For example, hen a user registers on the website, you can create a simple welcome series:
  * Day 0: Welcome message with a brief overview of what they’ll receive.
  * Day 2: A helpful guide on choosing the right business account.
  * Day 7: A success story from an existing customer to build trust.


You can also send an email to newly registered members who haven’t yet subscribed to your newsletter.
[![Sample automation flow triggered by membership registration that sends an email to members who are not subscribed to Kbank demo newsletter.](/docsassets/guides/introduction-to-emails/9_newsletters_automation_flow.png)](/docsassets/guides/introduction-to-emails/9_newsletters_automation_flow.png)
#### Use automation thoughtfully
While automation can handle a variety of email scenarios, we don’t recommend using automation flows for sending long-running or large-scale newsletters.
Automation is best for:
  * Sending one or two follow-up messages.
  * Handling the initial onboarding of subscribers.
  * Responding to form submissions or trigger events.


For example, it’s a good idea to use automation to:
  1. Send a welcome email immediately after a user subscribes
  2. Follow up once to set expectations (“Here’s how often you’ll hear from us…”)


But after that, it’s more efficient and flexible to manage ongoing communications like monthly newsletters or product updates using regular emails that you send through an email channel.
This separation helps you keep your workflows lean and your messaging more targeted.
**Remember to publish the automation email first**
Just like with other types of emails, an **automation email** must be published before it can be added to an automation workflow. If the email is still in draft, it won’t appear in the automation builder, and the flow won’t run as expected.
### 4. Confirmation Emails
Used for subscription actions, these emails confirm that someone has successfully signed up or unsubscribed. They reinforce trust and provide clarity about the user’s choices.
Example: When someone joins your newsletter, they get a clear confirmation email to know they have subscribed and what to expect.
[![Sample subscription confirmation email in Kbank demo site](/docsassets/guides/introduction-to-emails/3_newsletters_confirmation_email.png)](/docsassets/guides/introduction-to-emails/3_newsletters_confirmation_email.png)
### 5. Order status change
You can configure Xperience to automatically send messages to customers whenever they [create an order](/documentation/business-users/manage-commerce-stores#manage-orders) or when their order’s [status changes](/documentation/business-users/manage-commerce-stores#change-order-status). Editors can [customize content](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses#create-customer-notification-emails) of these emails to match the brand’s tone and provide clear instructions and guide the customer through every [stage](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses#create-order-statuses) of the order process.
For example, a customer can receive an email that confirms their order was placed, another one when it has been shipped, and a final one once it has been delivered.
## Send notifications to your team
It’s easy to confuse notifications with marketing emails, but they’re totally different.
Notifications are internal system emails that alert your team when something happens like a new form submission or a system error. They’re not sent to customers, and they’re not part of your email marketing.
_Example_ : When a customer requests a business loan quote, a notification email alerts the sales team so they can follow up quickly. The email can contain all the data that the customer provided through the online form.
[![Sample notification send to editor team in Kbank demo site](/docsassets/guides/introduction-to-emails/6_newsletters_notification.png)](/docsassets/guides/introduction-to-emails/6_newsletters_notification.png)
## How emails work behind the scenes
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
## What’s next for email in Xperience?
While campaign-style emails and optimization and experimentation tools aren’t available yet, they’re on the roadmap for future updates. Once released, these new features will build on the same foundation you’re learning now. Which means that starting today, you’re ready for what comes next.
## Ready to try emails in Xperience?
The Kbank Business Banking demo site comes pre-loaded with sample data you can explore safely. To see how the email app works, try sending a form autoresponder or setting up a newsletter.
If you don’t have your demo site, request the [Xperience by Kentico - Business Tutorial Kbank demo site](https://www.kentico.com/services/training/training-website-requests) through a form at [Kentico website](https://www.kentico.com/services/training/training-website-requests). The demo site is not connected to an SMPT server, but you can use the [Email queue](/documentation/business-users/email-queue) application to see what your emails will look like.
If you’re just starting out in your Xperience application, focus on:
  * Checking with your admin if an **email channel** is set up.
  * Exploring the available **email content types**.
  * Trying out an **Email Builder** or **macro-based template** to get a feel for building content.


## Summary
Email marketing is thriving. With Xperience by Kentico, you get a flexible, privacy-respecting platform that helps you connect with real people, not just traffic numbers. Learn the tools, use the types of emails available today, and collaborate with your team to make the most of every message. You’ll boost engagement and be ready for the next level of email marketing as soon as it arrives.
![]()
[]()[]()
