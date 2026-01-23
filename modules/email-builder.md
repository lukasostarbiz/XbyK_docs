---
source: https://docs.kentico.com/modules/email-builder
scrape_date: 2026-01-22
---

Module: Email Builder
1 of 13 Pages
# Email Builder overview
Email Builder is a powerful, user-friendly platform where marketers can craft visually compelling emails without technical expertise. It also simplifies development, allowing teams to easily manage templates, sections, and widgets through source control, and utilizing MJML markup to avoid complicated, client-specific HTML.
Through configurable templates, sections, and widgets, developers can empower marketers to quickly assemble emails that align with brand standards and business goals. By deciding what aspects of each component should be configurable as properties, carefully setting content-type restrictions, and choosing the allowed widgets or sections at each level, developers decide exactly how much flexibility their marketers have in building emails.
## Make a checklist
Let’s take a high-level look at the elements you’ll need to work with Email Builder:
  * **[An email channel](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management)** for drafting and managing emails
  * **[An email content type](/documentation/developers-and-admins/development/content-types#create-content-types)** that is assigned to the channel
  * **[A generated class](/documentation/developers-and-admins/api/generate-code-files-for-system-objects)** representing the email content type in the code of your solution
  * **[An Email Builder template](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#templates)** configured to work with the content type you’ve created
  * **[Configuration](/documentation/developers-and-admins/development/builders/email-builder#enable-the-email-builder-feature)** that runs when the application starts, enabling Email Builder in the project and for your content type
  * **[Email Builder sections and widgets (optional)](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#sections)** to populate the template 
    * Templates can contain their own content, along with properties and editable configurations to allow plenty of flexibility without the need for additional components.


Note that there are additional requirements to actually _send_ emails to recipients, once you’ve created an email using Email Builder:
  * **[A sender address](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management#add-sender-addresses)** assigned to the channel and used for the email
  * **[A recipient list](/guides/digital-marketing/work-with-email/create-a-recipient-list)** with at least one recipient to send the email to, assigned to the email
  * **[A functioning email service](/documentation/developers-and-admins/configuration/email-configuration)** to ensure the email reaches its destination


With all of these elements, you can start working with Email Builder and develop additional functionality.
Previous page
1 of 13
[ Mark complete and continue ](/modules/email-builder/email-builder-prerequisites)
![]()
[]()[]()
