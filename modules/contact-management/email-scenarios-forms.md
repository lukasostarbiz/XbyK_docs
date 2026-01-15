# Email scenarios for forms
  * [ Copy page link ](modules/contact-management/email-scenarios-forms#) | [Get HelpService ID](modules/contact-management/email-scenarios-forms#)
Core MVC 5


[✖](modules/contact-management/email-scenarios-forms# "Close page link panel") [Copy to clipboard](modules/contact-management/email-scenarios-forms#)
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
A common scenario involves setting up a more complex email follow-up sequence after a [form submission](modules/contact-management/properties-and-after-submission-behavior#actions-after-form-submission), beyond a simple email response. For simple email scenarios, the standard form autoresponder can be used without designing a process in the Automation Builder.
With automation, you can send multiple emails as a follow-up to form submission, but the entire sequence must be predefined from the beginning, with each email sent by a separate step. You can also include wait steps or conditions between the email steps.
To comply with personal data protection regulations, you may need to implement conditions that check for some form of opt-in from the contact, such as a [consent](documentation/developers-and-admins/data-protection/consent-management) or whether the contact is subscribed to a [recipient list](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-recipient-lists).
  * **Consent** – Can be obtained directly through the submitted form by including a _Consent agreement_ field, or more broadly (e.g., through a tracking/cookie consent banner implemented across a website).
  * **Recipient list** – The first email set by the process must use the _Form autoresponder_ [purpose](documentation/business-users/digital-marketing/emails#email-purposes) and include a double opt-in link for the recipient list. A wait step should follow to give recipients time to complete the double opt-in process, after which conditions related to the recipient list can be used to control how the process sends further emails.


**Regular newsletter scenarios**
Automation is **not intended** for long-term newsletter distribution or regular email sending to a recipient list. In these cases, it’s difficult to predict how many email issues will be sent in the future. Currently, if an automation process is triggered by a subscription form submission for a recipient list, it can only contain a fixed number of email steps. Once a subscribed contact reaches the end of the process, there is no way to extend the process or add additional email issues.
You can [send regular emails to subscribers](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) manually.
### Multilingual emails
If you wish to send emails in multiple [languages](documentation/developers-and-admins/configuration/languages) from an automation process, you need to prepare a separate form for each of the language variants. Each form requires its own automation process, which then sends emails from the corresponding language-specific [email channel](documentation/developers-and-admins/digital-marketing-setup/email-channel-management).
[ Previous page ](modules/contact-management/disable-or-modify-automation)
41 of 44
[ Mark complete and continue ](modules/contact-management/member-registration-automation)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/contact-management/email-scenarios-forms)
[](https://docs.kentico.com/modules/contact-management/email-scenarios-forms)[](https://docs.kentico.com/modules/contact-management/email-scenarios-forms)