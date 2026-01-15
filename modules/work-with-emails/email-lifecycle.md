# Email lifecycle
  * [ Copy page link ](modules/work-with-emails/email-lifecycle#) | [Get HelpService ID](modules/work-with-emails/email-lifecycle#)
Core MVC 5


[✖](modules/work-with-emails/email-lifecycle# "Close page link panel") [Copy to clipboard](modules/work-with-emails/email-lifecycle#)
When viewing an email in an email channel application, you can find the current status at the end of the administration breadcrumbs navigation, and the available action in the upper right corner of the view.
[![Email workflow status and actions](docsassets/documentation/emails/Email_Workflow.png)](https://docs.kentico.com/docsassets/documentation/emails/Email_Workflow.png)
Emails with the **Form autoresponder** , **Confirmation** , **Automation** or **Order status change** [purpose](modules/work-with-emails/introduction-to-emails) can have one of the following statuses:
  * **Draft (Initial)** – new emails that have never been published. 
    * Cannot be sent until you use the **Publish** action to move the email to the _Published_ status.
  * **Published** – emails that are ready to be sent. 
    * Displayed as read-only and cannot be edited. If you wish to modify the email, use the **Edit email** action to change the status to _Draft_.
  * **Draft (New version)** – emails that have already been published, but a new version is being edited. 
    * If the system needs to send an email while it is in the _Draft_ status (e.g., a form autoresponder), the previously published content and properties are used.
    * You can edit the email’s content or properties and **Save** the changes.
    * When the editing is complete, use the **Publish** action to overwrite the previously published content and move back to the _Published_ status.
    * You can revert back to the previously published content at any time. Expand the available actions drop-down and select **Revert to published**.
  * **Custom workflow step** – emails can go through custom workflow steps before they are ready to be sent (“between” the _Draft_ and _Published_ status). To learn more about these steps, see [workflows](documentation/developers-and-admins/configuration/workflows). The email works as if in the _Draft_ status until the workflow step is changed to _Published_.


Emails with the **Regular** purpose are sent only once to a specific list of recipients, and **have a different workflow cycle**. See [Send regular emails to subscribers](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) to learn more.
**Linked content items and workflow**
In many cases, your emails will have fields or Email Builder component properties that allow you to add content by selecting **reusable content items** (blocks of text, images, etc.). Every linked content item has its own workflow status. If you send an email that contains unpublished content items, the corresponding content will be missing in the email.
When publishing an email with fields linking to reusable content items or pages, the cascade publishing dialog opens. You can view all linked items that are currently not in the _Published_ status. Select any of the listed items to publish them together with the email. See [Content hub](documentation/business-users/content-hub/content-items#cascade-publishing) to learn more.
### Edit emails
When editing an email in an email channel application, you can switch between view modes in the options panel on the right:
  * **Preview** – allows you to see 3 different views (_desktop_ , _mobile_ , _inbox_) of the email content as it will appear to recipients, including the fixed content from the email template. Any [personalized dynamic text](modules/work-with-emails/personalize-emails) in the email appears as it would for a fake recipient named _John Doe_.
  * **Content** – allows you to edit the email content fields, as well as the email’s subject and preview text. While in the _Content_ view mode, you can expand the following panels: 
    * **Properties** – to adjust the email properties, i.e., the email’s template and sender information.
    * **Plain text** – to enter the plain text version of the email’s content. The plain text must combine content from all of the email’s fields, as well as static text contained in the template. Adding plain text content improves the deliverability of emails. Some recipients may prefer plain text emails, and certain email clients only accept plain text.
  * **Email Builder** – allows you to compose the email’s content using configurable components in a visual interface. Only available for emails with the _Automation_ , _Regular_ and _Order status change_ [purposes](modules/work-with-emails/introduction-to-emails). For detailed information, see [Create emails in Email Builder](modules/work-with-emails/create-emails-in-email-builder). While in the _Email Builder_ view mode, you can access the _Properties_ panel to adjust the email’s sender information.
  * **Statistics** – displays statistics tracked for the email, including the number of sent emails, email opens and clicked links. These statistics are anonymous, collected without connecting actions to individual recipients. See [Track email statistics](modules/work-with-emails/email-statistics-in-xperience).
  * **Contact activities** – displays actions performed by individual email recipients, specifically link clicks. Depending on your project’s [implementation](documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking/log-email-activities), email activities are only logged for recipients who agree to be tracked, for example based on [consents](documentation/developers-and-admins/data-protection/consent-management)). This can result in discrepancies between the number of logged activities and the email’s overall statistics, which are anonymously logged for all recipients.


[ Previous page ](modules/work-with-emails/introduction-to-emails)
3 of 14
[ Mark complete and continue ](modules/work-with-emails/personalize-emails)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/work-with-emails/email-lifecycle)
[](https://docs.kentico.com/modules/work-with-emails/email-lifecycle)[](https://docs.kentico.com/modules/work-with-emails/email-lifecycle)