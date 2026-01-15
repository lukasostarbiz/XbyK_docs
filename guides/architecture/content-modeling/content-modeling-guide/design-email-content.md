# Design email content
  * Concept| [ Copy page link ](guides/architecture/content-modeling/content-modeling-guide/design-email-content#) | [Get HelpService ID](guides/architecture/content-modeling/content-modeling-guide/design-email-content#) | This page is part of a module: [ Content modeling guide ](modules/content-modeling-guide)
Core MVC 5


[✖](guides/architecture/content-modeling/content-modeling-guide/design-email-content# "Close page link panel") [Copy to clipboard](guides/architecture/content-modeling/content-modeling-guide/design-email-content#)
## About this guide
This page is a part of the **Content modeling guide** which you should follow sequentially from [beginning to end](guides/architecture/content-modeling/content-modeling-guide). You can also follow this series in [Content modeling guide](modules/content-modeling-guide), which helps you keep track of your progress as you move through this sequential material.
**Email license requirements**
To create and send emails from Xperience administration, you need a license. The required license tier depends on the size of the [email channels](documentation/developers-and-admins/digital-marketing-setup/email-channel-management) you use:
  * Email microchannels – available for all license tiers
  * Standard-size email channels – require the **Advanced** license tier


Each _email channel_ represents a separate email campaign or newsletter email activity, similar to an [email feed](13/on-line-marketing-features/managing-your-on-line-marketing-features/email-marketing) you might know from Kentico Xperience 13. If you need to send multilingual emails, plan to create one channel for each language.
To learn more, see the description of the [Channel size](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels) property for email channels.
## Types of emails
Xperience by Kentico allows you to prepare [emails](documentation/business-users/digital-marketing/emails) and edit their content directly in the administration interface. All emails within the administration interface that editors manage are based on a [content type](documentation/developers-and-admins/development/content-types) and have a specific **email purpose**.
Xperience supports the following types of emails:
  * **Regular** – emails sent as part of a regular series to subscribed recipients. These emails serve to build a reliable communication channel with an engaged audience. See [Send regular emails to subscribers](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) for detailed information and instructions.
  * **Form autoresponder** – sent automatically to visitors after they submit a [form](documentation/business-users/digital-marketing/forms). These emails also [confirm email marketing subscription](documentation/developers-and-admins/digital-marketing-setup/enable-email-subscriptions) if their content includes [double opt-in link](guides/digital-marketing/xperience-essentials/email-marketing-essentials) the content.
  * **Confirmation** – [confirmation messages](guides/digital-marketing/work-with-email/create-newsletter-confirmation-and-unsubscription-emails) sent to users after they _subscribe_ or _unsubscribe_ from email marketing to provide immediate feedback.
  * **Automation** – sent out by an [automation process](documentation/business-users/digital-marketing/automation). These emails cannot be sent manually.
  * **Notification** – represent emails sent to administration users about [system events](documentation/developers-and-admins/configuration/notifications), or to customers about [order-related events](documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses).


Other system emails that are not part of Xperience administration need to be created by developers and sent programmatically. For example, user password reset and registration emails are described in [Administration - Forms authentication](documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication).
## Design emails using email templates
[Email templates](documentation/developers-and-admins/digital-marketing-setup/email-templates) define the overall layout of [emails](documentation/business-users/digital-marketing/emails) and add fixed content, such as a header or footer. Editors can reuse a single template for multiple emails.
From a developer’s perspective, emails are _strongly typed_ and stored in [dedicated database tables](documentation/developers-and-admins/api/content-item-api/content-item-database-structure) according to their [content type](documentation/developers-and-admins/development/content-types). Developers can programmatically retrieve data from the _email content type fields_ and display it using any number of different [email templates](documentation/developers-and-admins/digital-marketing-setup/email-templates). They define the _template content_ using the [MJML markup](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#mjml-markup) or standard HTML, and CSS. Each email template is tied to specific allowed email content types to ensure Xperience correctly displays the email data.
A common workflow for creating templates is for a designer to prepare and optimize the template code using an external tool. Developers then transfer and map the template code onto Xperience artifacts, depending on the email’s purpose.
### Email Builder and structured emails
The _content model for emails_ depends on the approach you select when you define the email content type. This approach influences how editors compose emails in their email channels.
The [AIRA AI assistant](documentation/business-users/aira) is currently available only for **structured data fields** when editing emails in **Content** view mode. You can enable it for the email **Subject** and **Preview** text fields, as well as for other structured content fields within the email.
At this time, AIRA is **not supported in the Email Builder** , so it cannot assist with editing content directly within the builder’s drag-and-drop interface.
#### Email Builder
Similar to [page builder](guides/architecture/content-modeling/content-modeling-guide/design-website-content), editors create emails visually in a user-friendly interface from components that developers prepared. They arrange email widgets inside email sections on top of the email template.
Developers build widgets, sections, and the overall email template which they register against content types. From code perspective, sections and widgets are [Razor components](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#sections) that layout and display the email content with the MJLM or standard HTML markup, depending on the implementation.
Email Builder is currently supported for emails with the _Regular_ , _Automation_ , or _Order status change_ purposes.
[![Email Builder in Kbank demo site](docsassets/guides/design-email-content/email_builder_in_kbank.png)](https://docs.kentico.com/docsassets/guides/design-email-content/email_builder_in_kbank.png)
_Email widgets and sections_ differ technically from the _Page Builder widgets and sections_. Although developers can reuse similar visual designs to keep emails and web pages consistent with the brand, they need to redevelop email components from scratch to fit the limited set of features supported by [email clients](https://www.caniemail.com/).
#### Structured emails
Editors fill in predefined form fields in the _Content_ view mode, such as _Hero image_ , _Intro text_ , or _Featured articles_.
Each of the fields defines email content or a block of content within the email, and developers control how this content looks through the _email template_. The template retrieves data from the content type fields and displays it using HTML and CSS. Editors cannot rearrange the different template blocks, but they can reorder the content within the section if the email content type reuses content from a _website channel_ or _Content hub_. Developers can [define the email template](documentation/developers-and-admins/digital-marketing-setup/email-templates#create-email-templates) in the **Email templates** application within the administration UI using [email template macros](documentation/developers-and-admins/digital-marketing-setup/email-templates#email-template-macros), or create an _Email Builder_ template in the project codebase with no editable areas.
[![Linked content items in Kbank’s demo newsletter](docsassets/guides/design-email-content/featured_content_in_structured_email.png)](https://docs.kentico.com/docsassets/guides/design-email-content/featured_content_in_structured_email.png)
#### Hybrid emails
To benefit from _Email Builder_ and _structured emails_ , you can define email content types that combine both. _Hybrid emails_ allow editors to use the drag-n-drop interface from _Email Builder_ , while also displaying structured data from your content types.
This combinations allows developers and marketers to balance design flexibility with consistency. Editors can build their one-off email content in a user-friendly interface, while developers can still ensure consistency in the display of all required data. Meanwhile, both widgets and content types can reuse existing content through the _Combined content selector_.
## Define email content types
Before you start building email content types, verify your marketing team’s plans for writing and managing email content. You want to make editors efficient and provide flexible email editing experience that is consistent with the rest of the content model.
### Check the business requirements
When you define email content types, identify emails your team will send, including newsletters, emails targeted at specific contacts groups, product updates, order updates, or other transactional messages. Each type may need its own structure, visual layout, and approval workflow.
### Validate editor roles and workflows
Discuss with stakeholders who create and maintain the emails. Are marketers responsible for editing content directly, or do developers prepare most of the structure? The answer determines how much flexibility you should allow through _Email Builder_ , _structured data fields_ , or _a combination of both_.
### Review existing and planned digital channels
Align the email content model with the broader content modeling approach you use across websites and _Content hub_. Whether your project follows a page-based or atomic (content item–based) model, aim for a unified editing experience. Editors should feel comfortable while editing emails. They should be working with familiar content types, follow the same naming conventions, and rely on similar field behaviors, such as validation.
### Aim for scalability
Plan email content types for reuse and future expansion. For instance, if the same “Featured article” or “Hero image” blocks appear with the same content on both the website and in emails, define them as reusable content types, and provide a way to override stored values in each channel. This will reduce content duplication and keep digital marketing assets consistent across the ecosystem.
## Channel-specific content vs content reusability
Depending on the project requirements, you will need to create different types of emails. Before we dive into more complex types, consider creating a simple email composed only with structured content. For example, use simple text field for values like _title_ and rich text field for _email body_ , and display their values in a simple email template. Editors can use this Simple email to quickly craft email and focus on content. If you want to add complexity and allow for flexible layout and design, create this email as a hybrid one with _Email Builder_ containing at least one section and a _Text_ widget. Editors will be able to quickly craft message and provide additional content if necessary.
The following image shows the structure of a sample email. The template includes content loaded from email content type fields and linked content items used to display social platform links:
[![Email template containing individual email fields](docsassets/guides/design-email-content/Email_Structure.png)](https://docs.kentico.com/docsassets/guides/design-email-content/Email_Structure.png)
### Reusable content in emails
Editors can speed up how they compose emails by reusing existing content. They can build their emails from any structured content stored as pages in a [any website channel](guides/architecture/content-modeling/content-modeling-guide/design-page-based-content-model#identify-reusable-data), or as reusable items in the _Content hub_.
We recommend storing content in a [structured way](guides/architecture/content-modeling/content-modeling-guide/store-content) to support content reusability and improve [governance](guides/digital-marketing/content-governance-in-xbyk).
To sum up the principle: You should model any piece of content that could possibly be used in multiple emails (e.g., company icon in a signature, social links with icons, company logo) or in any other channel (e.g., event description, article teaser with a title, summary and teaser image) in a reusable manner. Use structured content type fields in _Content hub_ or a website channel to store it.
### Link content items strategically
To reference reusable content from your email content type, add a field with the **Pages and reusable content** data type in the **Content types** application. The following example shows an email field that enables marketers to add links to various social media platforms at the end of emails.
[![Linked content items in emails](docsassets/guides/design-email-content/email_linked_content_item.png)](https://docs.kentico.com/docsassets/guides/design-email-content/email_linked_content_item.png)
Linked content items need to be correctly displayed in the [email template](documentation/developers-and-admins/digital-marketing-setup/email-templates).
### Link pages
Pages from the content tree of any website channel can also be linked from emails. You can either define widgets in which editors select the referenced content or use email _Content_ view mode. Developers can retrieve the structured data and the page’s URL and display the data accordingly.
**Example**
Content types:
Email (Emails) |  Linked article (Reusable content) |  Article (Pages)  
---|---|---  
Field name |  Data type |  Field name |  Data type |  Field name  
EmailContent |  Rich text (HTML) |  ArticleTitle |  Text |  …  
LinkedArticles |  Pages and reusable content (Linked article) |  ArticlePage |  Pages |  …  
For more information about displaying [content items](documentation/developers-and-admins/digital-marketing-setup/email-templates) and [pages](documentation/developers-and-admins/digital-marketing-setup/email-templates#linked-pages-and-content-items) in email templates, see the [Email templates](documentation/developers-and-admins/digital-marketing-setup/email-templates) page.
## Recommendations for modeling emails
You can follow these recommended practices to define email content types that help marketers stay on brand and provide the flexibility they need to create compelling emails.
### Leverage structured content and Email builder
Many projects benefit from combining both structured and builder-based approaches to email design. This hybrid model allows teams to balance flexibility for marketers with consistency that brand managers require.
To design effective email content types:
  * Keep critical brand elements, such as the _logo_ , _email header_ , _footer_ , and company’s main _social links_ , as structured content. These assets rarely change and marketers need to adhere to approved design and compliance standards. If the project requires editors to vary between different branding requirements, e.g., different headers and logo sizes per email purpose, you have different options to support this requirement. You might create an _Email brand defaults_ content type. Editors will then store possible branding variants into dedicated content items and then select the one they need when they create the email. Or, you can create an email template for each variant and hardcode these brand elements into the template’s code.
  * For the main body of the email, allow flexibility through the _Email Builder_ , where editors can add and arrange widgets to suit each campaign’s message and layout.
  * Whenever possible, allow editors to reuse content items across both structured and builder-based emails. This ensures that key assets like article teasers, product descriptions, or event details remain consistent across all channels.
  * Developers should define _widgets_ and _sections_ with _properties_ that allow editors change the email design, but still stay aligned with the organization’s design system.
  * During content modeling, establish clear governance guidelines that describe when to use the different email content types you have provided. Clear rules help teams maintain consistency and avoid duplication of effort.


This combined strategy keeps emails on brand and gives editors the creative freedom they need to deliver engaging email content.
### Define override fields for reusable email content
Building emails with reusable content doesn’t mean editors are locked into always using the same values. They often need to fine-tune details to suit a specific message. For example, when reusing a product teaser or article summary from the website, editors might want to adapt the headline or description to better match the email’s tone and tailor it for a specific [contact group](guides/digital-marketing/work-with-contacts/contact-groups-overview).
To support this capability, define email content fields or widget properties that allow editors to override selected values in the email without altering the source content. We recommend using structured fields or _Email Builder_ widget properties editors will use to, for example, create an alternative title, select an image, or adjust a call-to-action text tailored to email experience.
This approach keeps shared content unified across the digital ecosystem, while also giving editors enough flexibility to tailor each email for its audience and purpose.
### Use dedicated content type to define email defaults
Emails typically contain header logo and social links. If you don’t want to force editors to recreate this content for every email, you can store this information directly in the template. This will, however, limit their flexibility, and your developer might need to, from time to time, adjust the email template.
To avoid additional developer time, consider creating a dedicated reusable content type to store these values, along with CSS definitions and other email design-related assets. Editors will select the appropriate email configuration to answer their needs.
We do not recommend creating dedicated fields to control email layout, such as _Show as a left column_ , _Align right_ , or _Additional padding_ , in the email content type. If you want editors to influence the layout of their emails, use the _Email Builder_ instead.
## What’s next?
You’ve learned absolute essentials about modeling content types for emails. In the [next section](guides/architecture/content-modeling/content-modeling-guide/design-headless-content), you’ll learn how headless content works in Xperience, and the recommendations to follow when modeling content types for headless.