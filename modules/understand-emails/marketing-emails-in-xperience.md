---
source: https://docs.kentico.com/modules/understand-emails/marketing-emails-in-xperience
scrape_date: 2026-01-22
---

Module: Understand emails in Xperience
3 of 31 Pages
# Marketing emails
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
[ Previous page ](/modules/understand-emails/understand-emails-in-xperience)
3 of 31
[ Mark complete and continue ](/modules/understand-emails/notification-in-xperience)
![]()
[]()[]()
