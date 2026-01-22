---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-marketing-setup/custom-form-autoresponder-emails
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital marketing setup](/documentation/developers-and-admins/digital-marketing-setup)
  * Custom form autoresponder emails 


# Custom form autoresponder emails
[Forms](/documentation/business-users/digital-marketing/forms) provide the option to automatically send an email message to every user who submits the form.
For most scenarios, we recommend that you prepare the autoresponder email content directly in the Xperience administration using an email channel application (see [Emails](/documentation/business-users/digital-marketing/emails)), and then select an appropriate email for each form.
However, you may need to implement custom emails if you require advanced or dynamic autoresponder content. For example, you can develop a promotional code generator, and then prepare custom form autoresponder emails containing the value generated for every recipient.
**Prerequisite**
To allow the system to send out emails, you first need to set up and configure an email client (for example an SMTP server or SendGrid integration). For more information, see [Email configuration](/documentation/developers-and-admins/configuration/email-configuration).
**Email statistic tracking not available**
The system does not [track statistics](/documentation/business-users/digital-marketing/emails/track-email-statistics) for custom form autoresponder emails. Tracking functionality is only available for emails created using an email channel application in Xperience.
## Custom form autoresponder emails
To enable custom form autoresponder emails for a form:
  1. Open the **Forms** application in Xperience.
  2. Select the form in the list.
  3. Select **Autoresponder** in the options panel on the right.
  4. Choose the **Custom** option.
  5. Select **Save**.


Until developers prepare and register customized emails, the **Custom** autoresponder sends a basic thank you email with the following parameters:
**From** |  no-reply@localhost.local The sending domain in the default From address reflects the [global sending domain for system emails](/documentation/developers-and-admins/configuration/email-configuration), for example: _no-reply@company.com_  
---|---  
**To** |  <email address of the [contact](/documentation/business-users/digital-marketing/contact-management) who submitted the form>  
**Subject** |  Thank you for filling out the form  
**Body** |  We appreciate the information you provided us in the submitted form. Thank you and have a nice day.  
This thank you message will not be suitable for most forms on real-world websites. Developers need to override the custom autoresponder emails with appropriate content based on the website’s branding and the context of each specific form. The emails can also include values that the user entered into the form.
  1. Open your project’s solution in Visual Studio.
  2. [Add a custom assembly](/documentation/developers-and-admins/customization/integrate-custom-code) (_Class Library_ project) to your solution or re-use an existing one.
  3. Create a new class that implements the `IFormSubmitAutomationEmailProvider` interface.
  4. Define the `GetEmail` method: 
     * The method provides the following parameters: 
       * `BizFormInfo` – the form that was submitted. For example, you can evaluate the form’s `FormName` property to branch your code and create different autoresponder emails for specific forms.
       * `BizFormItem` – the data that the user submitted into the form. Use the `GetValue(<FieldName>)` method to access the values of form fields (or the alternative _Get <type>Value_ methods).
       * `ContactInfo` – the [contact](/documentation/business-users/digital-marketing/contact-management) who submitted the form.
     * Return an `AutomationEmail` object with the content and other parameters of the form autoresponder email. The object exposes standard email properties: `Subject`, `Body`, `Sender`, `Recipients`.
     * Return `null` in cases where you cannot or do not wish to send an autoresponder email.
  5. Register your custom implementation by adding the `RegisterImplementation` assembly attribute.


The system now sends emails according to your custom implementation whenever a user submits a form with the **Custom** autoresponder.
You can reuse or fall back to the default `IFormSubmitAutomationEmailProvider` implementation. See [Decorate system services](/documentation/developers-and-admins/customization/decorate-system-services) for more information.
C#
**Example**
Copy
```
using System;
using System.Collections.Generic;
using System.Threading.Tasks;

using CMS;
using CMS.ContactManagement;
using CMS.OnlineForms;

using CMS.Automation;

// Registers the custom IFormSubmitAutomationEmailProvider implementation
[assembly: RegisterImplementation(typeof(IFormSubmitAutomationEmailProvider), typeof(Custom.CustomFormSubmitAutomationEmailProvider))]

namespace Custom
{
    public class CustomFormSubmitAutomationEmailProvider : IFormSubmitAutomationEmailProvider
    {
        // Stores an instance of the default IFormSubmitAutomationEmailProvider implementation
        private readonly IFormSubmitAutomationEmailProvider defaultAutomationEmailProvider;

        public CustomFormSubmitAutomationEmailProvider(IFormSubmitAutomationEmailProvider defaultAutomationEmailProvider)
        {
            this.defaultAutomationEmailProvider = defaultAutomationEmailProvider;
        }

        // Creates an autoresponder email based on the submitted form
        public Task<AutomationEmail> GetEmail(BizFormInfo form, BizFormItem formData, ContactInfo contact)
        {
            // Gets the email address from the contact's Email attribute
            string recipient = contact.ContactEmail;

            if (string.IsNullOrEmpty(recipient))
            {
                // The user hasn't provided an email address, so no autoresponder email is sent
                return null;
            }

            // Prepares a custom autoresponder email for a form with the 'ContactUs' code name
            if (form.FormName.Equals("ContactUs", StringComparison.InvariantCultureIgnoreCase))
            {
                return Task.FromResult(new AutomationEmail
                {
                    Subject = "Thanks for contacting us",
                    // Builds the email body, including the name of the contact who submitted the form,
                    // and the value of the 'UserMessage' form field
                    Body = $"Hello {contact.ContactFirstName}, <br />" +
                           "thank you for contacting us. You sent us the following message:<br />" +
                           formData.GetStringValue("UserMessage", ""),
                    Sender = "no-reply@company.com",
                    Recipients = new List<string> { recipient }
                });
            }

            // Returns the default form autoresponder email as a fallback
            return defaultAutomationEmailProvider.GetEmail(form, formData, contact);
        }
    }
}
```

**Context data not available**
Autoresponder emails are not sent within the context of a web request, and are internally processed using an automation process. As a result, your `IFormSubmitAutomationEmailProvider` code cannot access context data, such as the current request, website channel or page.
You can get certain context-related data from the `BizFormInfo` and `ContactInfo` parameters of the `GetEmail` method.
![]()
[]()[]()
