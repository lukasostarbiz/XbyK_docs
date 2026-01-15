# Email Builder templates
  * [ Copy page link ](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates#) | [Get HelpService ID](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates#)
Core MVC 5


[✖](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates# "Close page link panel") [Copy to clipboard](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates#)
**Email license requirements**
The license tier required for email features depends on the size of the [email channels](documentation/developers-and-admins/digital-marketing-setup/email-channel-management) that you use:
  * Email microchannels – available for all license tiers
  * Standard size email channels – require the **Advanced** license tier


To learn more, see the description of the [Channel size](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels) property for email channels.
Email templates for [Email Builder](documentation/developers-and-admins/development/builders/email-builder) set the overall design and layout for emails, and contain fixed content, such as a header and footer. Templates are prepared by your project’s developers and assigned by editors when creating new emails. Editors can also switch to a different template for existing emails.
**Prerequisites and limitations**
Your project’s developers need to set up the [Email Builder](documentation/developers-and-admins/development/builders/email-builder#enable-the-email-builder-feature) feature and prepare [Email Builder components](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components). Email Builder becomes available if you select an Email Builder template when creating your email.
Currently, Email Builder is only available for emails with the _Automation_ , _Regular_ and _Order status change_ [purposes](documentation/business-users/digital-marketing/emails#email-purposes).
## Email Builder template types
Two types of Email Builder templates are available:
  * **Default email templates** – determine an email’s layout and design. The [Email Builder content](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder) of an email is empty when a default template is initially assigned. Default templates first need to be created by your project’s developers.
  * **Preset email templates** – can be saved from emails that were created using another Email Builder template. A _preset email template_ uses the original template’s layout and design, but additionally stores a snapshot of the email’s current [Email Builder content](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder). Preset templates can then be used as a starting point for new emails. The Email Builder content in emails created using preset templates can be freely adjusted without impacting other emails – all modifications only affect the current email (there is no logical connection between the content of emails created with the same template).


## Create emails based on email templates
See [Create emails in Email Builder](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder) to learn how to create emails with Email Builder templates.
After creating an email from an Email Builder template editors can save their email as a _preset email template_ along with the Email Builder content created in their email. See [Save emails as preset email templates](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates#save-emails-as-preset-email-templates).
## Configure email templates
Some Email Builder templates may have properties that you can configure to adjust the email’s appearance or content. The available configuration options depend on the implementation prepared by the project’s developers.
  1. Open an email based on a template and select the **Email Builder** view mode.
  2. Click the orange configure button (  
[![Template configuration dialog location](docsassets/documentation/email-builder-templates/templateconfiguration.png)](https://docs.kentico.com/docsassets/documentation/email-builder-templates/templateconfiguration.png)
  3. Configure any available properties in the dialog.  
[![Template configuration dialog example](docsassets/documentation/email-builder-templates/templateconfigurationdialog.png)](https://docs.kentico.com/docsassets/documentation/email-builder-templates/templateconfigurationdialog.png)
  4. **Apply** the changes.


The configured properties now apply to the template for the given email. Any other emails that use the same template have their own separate property configuration.
## Change templates of existing emails
You can change the email template of an existing email, provided there is more than one Email Builder template available for the given email.
  1. Open an email in an email channel application and select the **Email Builder** view mode.
  2. Click the orange change template button (  
[![Change template button location](docsassets/documentation/email-builder-templates/templateselection.png)](https://docs.kentico.com/docsassets/documentation/email-builder-templates/templateselection.png)
  3. Select one of the available email templates.  
[![Template selection dialog](docsassets/documentation/email-builder-templates/templateselectiondialog.png)](https://docs.kentico.com/docsassets/documentation/email-builder-templates/templateselectiondialog.png)
**Preserving Email Builder content when changing _default_ email templates**
When changing from a _default_ email template, you have the option to _Keep current widgets and sections if possible_. This option allows you to preserve widgets and sections from the current template if the new template contains editable areas with matching identifiers. Otherwise, Email Builder content from the current email is discarded. If you’re unsure about the expected behavior on your site, consult your site’s administrators. 
  4. **Apply** the changes.


The email now uses the selected template. 
## Save emails as preset email templates
If you want to reuse Email Builder content, such as the layout of sections or content of widgets, you can save template-based emails as preset templates.
  1. Create or edit an email based on a template.
  2. Edit the email and add desired content in the **Email Builder** view mode.
  3. **Save** your changes and select **Save as preset template**. A new dialog window opens.  
[![Save as preset template](docsassets/documentation/email-builder-templates/saveastemplate.png)](https://docs.kentico.com/docsassets/documentation/email-builder-templates/saveastemplate.png)
  4. Fill in the template’s fields: 
     * **Name** – display name of the template.
     * **Description** – you can add a detailed description, which is displayed as a tooltip in the template selector.
     * **Icon** – icon under which the template is displayed in the administration.  
[![Save as preset template - dialog](docsassets/documentation/email-builder-templates/saveastemplatedialog.png)](https://docs.kentico.com/docsassets/documentation/email-builder-templates/saveastemplatedialog.png)
  5. **Save** the preset template.


You now have a preset template that you can select when creating new emails that support the original default template. Such emails automatically start with the Email Builder content stored within the preset template and can be further adjusted. Changes made in the content of one email **do not** affect the content of other emails that use the same preset email template.
**Preset email templates do not store structured content**
Content stored in the fields of an email is not included when saving an email as a preset template.
## Manage preset email templates
You can see a list of your saved templates in the **Preset email templates** tab of the **Preset templates** application.
[![List of preset email templates](docsassets/documentation/email-builder-templates/presetemailtemplates.png)](https://docs.kentico.com/docsassets/documentation/email-builder-templates/presetemailtemplates.png)
The tab displays a listing with the names and descriptions of individual preset email templates. It also displays the base default templates from which the preset templates are derived.
### Edit preset email templates
Direct editing of preset email templates is not currently supported. In order to change a template, you need to [delete](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates#delete-preset-email-templates) it and [save](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates#save-emails-as-preset-email-templates) it again.
### Delete preset email templates
To delete a saved preset template:
  1. Open the **Preset templates** application.
  2. Open the **Preset email templates** tab.
  3. **Delete** (


You have now successfully deleted the email template. Note, that this has no impact on the emails created from the template.