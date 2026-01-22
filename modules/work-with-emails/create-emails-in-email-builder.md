---
source: https://docs.kentico.com/modules/work-with-emails/create-emails-in-email-builder
scrape_date: 2026-01-22
---

Module: Work with emails
11 of 14 Pages
# Create emails in Email Builder
**Email license requirements**
The license tier required for email features depends on the size of the [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) that you use:
  * Email microchannels – available for all license tiers
  * Standard size email channels – require the **Advanced** license tier


To learn more, see the description of the [Channel size](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels) property for email channels.
Email Builder is a user-friendly interface that allows you to compose emails using configurable components prepared by developers.
[![Adding content in Email Builder](/docsassets/documentation/create-emails-in-email-builder/EB_overview.png)](/docsassets/documentation/create-emails-in-email-builder/EB_overview.png)
See [Emails](/modules/work-with-emails/introduction-to-emails) to learn how to work with emails in general. Information, such as the description of the [Email lifecycle](/documentation/business-users/digital-marketing/emails#email-lifecycle) and instructions for [Sending draft emails](/modules/work-with-emails/personalize-emails#send-draft-emails) or [Cloning emails](/modules/work-with-emails/personalize-emails#clone-emails), applies even when using Email Builder.
**Prerequisites and limitations**
Your project’s developers need to set up the [Email Builder](/documentation/developers-and-admins/development/builders/email-builder#enable-the-email-builder-feature) feature and prepare [Email Builder components](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components). Email Builder becomes available if you select an Email Builder template when creating your email.
Currently, Email Builder is only available for emails with the _Automation_ , _Regular_ and _Order status change_ [purposes](/modules/work-with-emails/introduction-to-emails#email-purposes).
**Email appearance in Email Builder mode**
In some cases, there may be differences in the appearance and styling of Email Builder components when they are viewed in the _Email Builder_ mode. Switch to **Preview** mode to get an accurate reference for how recipients will see the email.
## Email Builder components
Emails created in Email Builder consist of components that fit together in the following way:
[![Email Builder component structure](/docsassets/documentation/create-emails-in-email-builder/EB_component_structure.png)](/docsassets/documentation/create-emails-in-email-builder/EB_component_structure.png)
  * **Email template** – the top-level component that sets the email’s overall design, as well as any fixed content, such as a header and footer. You select the template when initially creating the email. Some templates may have properties that you can [configure](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates#configure-email-templates) to adjust the email’s appearance or content. See [Email Builder templates](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates) for more information.
  * **Sections** – sections set a layout for email content, typically with one or more columns. You can add or remove sections, but each editable area in the template must always contain at least one section. Sections contain one or more widget zones. See [Adjust the email layout using sections](#adjust-the-email-layout-using-sections) to learn how to work with sections. 
    * **Widget zones** – places where you can add widgets. Each zone can contain any number of widgets (depending on the design of the template and section).
  * **Widgets** – reusable components that represent individual pieces of email content, such as text, images, CTA links, etc. See [Add content using widgets](#add-content-using-widgets) to learn how to work with widgets.


## Adjust the email layout using sections
You can modify an email’s layout by adding, configuring or removing sections. Manage sections in the following way:
  * **Add sections** – select a gray  _plus_ button located on the left side of an editable area to insert a new section. The list of available sections depends on the implementation of your project.
  * **Move sections** – hover over a section and drag it by the 
  * **Change section type** (only displayed if multiple section types are available) – allows you to replace an existing section with a different section. Any widgets within the section remain, but may be automatically moved if the sections have different widget zones. Hover over the section you want to modify, select the 
  * **Configure sections** (only displayed for configurable sections) – select the _Apply_. Check with your site’s developers for more information about the section types and their properties.
  * **Copy sections** – hover over the section you want to copy and select the 
    * You can paste the copied sections onto any email that uses Email Builder using the _Add section_ button.
    * A section can only be pasted into an editable area if all widgets in the copied section and the section itself are allowed in the area.
    * The copy feature allows you to store up to 10 sections for 12 hours.
  * **Delete sections** – hover over the section you want to remove and select the 


[![Section management UI elements](/docsassets/documentation/create-emails-in-email-builder/EB_sections.png)](/docsassets/documentation/create-emails-in-email-builder/EB_sections.png)
Each section defines one or more zones, where you can add widgets.
After making any changes in the email, do not forget to select the **Save** button. Changes made to the sections can be previewed in the **Preview** mode.
## Add content using widgets
Widgets allow you to add individual pieces of email content, such as text, images, CTA links, etc. Manage widgets by interacting with the purple UI elements in Email Builder:
  * **Add widgets** – select a purple  _plus_ button to insert a widget to the desired location. The list of available widgets depends on the implementation of your project.
  * **Move widgets** – select a widget and drag it to a different position by its 
  * **Personalize widgets** – select the [Personalize widgets](#personalize-widgets) to learn more.
  * **Configure widgets** (only displayed for configurable widgets) – select the _Apply_. 
    * **Note** : Widget configuration options and property editors are implemented individually with each widget. For more information, visit your project’s documentation or contact your developers.
  * **Copy widgets** – hover over a widget you want to copy and select the _Add widget_ functionality. This feature allows you to store up to 10 widgets for 12 hours.
  * **Delete widgets** – select a widget you want to remove and select the 


[![Widget management UI elements](/docsassets/documentation/create-emails-in-email-builder/EB_widgets.png)](/docsassets/documentation/create-emails-in-email-builder/EB_widgets.png)
After making any changes in the email, do not forget to select the **Save** button. Changes made to the widgets can be previewed in the **Preview** mode.
### Personalize widgets
**Advanced license required**
Widget personalization requires the Xperience by Kentico Advanced license tier.
Personalization allows you to send different email content based on the [contact groups](/documentation/business-users/digital-marketing/contact-groups) to which individual recipients belong. This can help you provide more accurately targeted information and increase the effectiveness of your emails. For example, you can use personalization to adjust the email text or display additional relevant offers.
Personalization works by creating multiple variants of [widgets](#add-content-using-widgets). Each variant can have its own configuration of properties or different content added through inline editors. When the system sends emails to individual recipients, the appropriate widget variants are used according to the recipient’s contact groups.
**Prerequisite**
Your project’s developers need to prepare [Email Builder widgets](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#widgets) that change their content based on their property configuration.
To personalize a widget:
  1. Open an email channel application in Xperience.
  2. Select an email with an Email Builder template.
  3. Switch to the **Email Builder** view mode.
  4. Select the **Personalize** (**Is in contact group**.  
[![Personalizing an Email Builder widget](/docsassets/documentation/create-emails-in-email-builder/EB_widget_personalize.png)](/docsassets/documentation/create-emails-in-email-builder/EB_widget_personalize.png)
  5. Enter a **Variant name** and choose the **Contact groups** to which recipients need to belong to get this variant of the widget.
  6. Select **Apply**.
  7. Modify the properties or content of the new widget variant. Consider the target audience when preparing the modified content.
  8. **Save** the email to confirm the personalization.


The new personalization variant of your widget is now created. In the _Email Builder_ view mode, you can switch between personalization variants of a widget by selecting the **Personalize** (
[![Switching between widget variants](/docsassets/documentation/create-emails-in-email-builder/EB_widget_variants.png)](/docsassets/documentation/create-emails-in-email-builder/EB_widget_variants.png)
You can add further variants to a personalized widget:
  1. Select **Personalize** (
  2. Select **Add variant** under the list.
  3. Enter a **Variant name** and choose the **Contact groups** to which recipients need to belong to get this variant of the widget.
  4. Select **Apply**.
  5. Modify the properties or content of the new widget variant. Consider the target audience when preparing the modified content.
  6. **Save** the email to confirm the changes.


**Priority of variants**
Widget variants are evaluated from top to bottom according to their position in the variant list. The email uses the first variant for which the contact group condition is satisfied for the given recipient.
**Recommended number of variants**
You can create an unlimited number of variants for a single widget. However, for optimal displaying of the variant list, we do not recommend creating more than 10 variants per widget.
You can manage widget variants in the following ways by **hovering** over them in the variant list:
  * Change the variant name or contact group condition via the **Edit variant** (
  * Adjust the priority of variants by dragging them using the **Change priority** (
  * Remove variants via the **Delete variant** (


Remember that you need to **Save** the email to confirm any changes to widget variants.
[ Previous page ](/modules/work-with-emails/create-newsletter-confirmation-and-unsubscription-pages)
11 of 14
[ Mark complete and continue ](/modules/work-with-emails/email-statistics-in-xperience)
![]()
[]()[]()
