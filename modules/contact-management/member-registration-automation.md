# Member registration automation scenarios
  * [ Copy page link ](modules/contact-management/member-registration-automation#) | [Get HelpService ID](modules/contact-management/member-registration-automation#)
Core MVC 5


[✖](modules/contact-management/member-registration-automation# "Close page link panel") [Copy to clipboard](modules/contact-management/member-registration-automation#)
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Processes with a _Registration_ trigger run when the _Member registration_ [activity](documentation/developers-and-admins/digital-marketing-setup/set-up-activities#activity-type-overview) is logged for a contact. This activity is logged only when the member becomes active (enabled), for example after confirming the registration when using double opt-in via [email confirmation](documentation/developers-and-admins/development/registration-and-authentication/forms-authentication#email-confirmation).
### Member to contact mapping
Automation processes always run for a [contact](modules/contact-management/contact-management), not directly for the [member](modules/contact-management/members) created during registration. The system maps the member to a contact based on the email address submitted during registration.
**Scenario** : A visitor registers after giving consent to be tracked as a contact
  * If the registration email matches the current contact’s email, the process is triggered for the current contact.
  * If the current contact does not have a known email address, the contact’s email is automatically updated to match the member registration email. The process is triggered for the current contact.
  * If the current contact has a known email address that doesn’t match the registration email, the process is triggered for: 
    * Another contact, if one exists with a matching email address.
    * A new untracked contact, which is automatically created with the member email if a matching contact doesn’t exist (based on [legitimate interest](documentation/developers-and-admins/digital-marketing-setup/set-up-activities#activity-logging-based-on-legitimate-interest)).


In the last case, the process runs for a different contact than the current contact. This means that any subsequent activities by the given visitor, such as page visits or clicks, are not logged for the contact in the automation process. Keep this in mind if the process uses [conditions](modules/contact-management/analyze-and-customize-automation) that evaluate such activities.
**Scenario** : A visitor registers without giving consent to be tracked, so the current contact is unknown
  * If the registration email matches the email address of an existing contact, the process is triggered for this contact.
  * If a matching contact doesn’t exist, a new untracked contact with the registration email is automatically created (based on [legitimate interest](documentation/developers-and-admins/digital-marketing-setup/set-up-activities#activity-logging-based-on-legitimate-interest)). The process is triggered for this new contact.


In this scenario, the contact in the automation process is untracked, and not directly linked to the visitor. Subsequent activities by the visitor, such as page visits or clicks, are not logged at all. Keep this in mind if the process uses [conditions](modules/contact-management/analyze-and-customize-automation) that evaluate such activities.
### Contact is member conditions
The _Contact is member_ [condition](modules/contact-management/analyze-and-customize-automation) is only fulfilled for new members who have the [member registration activity](documentation/developers-and-admins/digital-marketing-setup/set-up-activities#activity-type-overview) logged. This activity cannot be logged retroactively. Pre-existing members who have no registration activity logged will not satisfy the condition.
If you have existing members who may have registered before the _Member registration_ activity became available, the condition will only work reliably in processes with the _Registration_ trigger.
Additionally, the condition requires members to exist and be active (enabled) at the time of the evaluation. If a member’s account was deleted or disabled after the member registration activity was logged, the condition is evaluated as false.
[ Previous page ](modules/contact-management/email-scenarios-forms)
42 of 44
[ Mark complete and continue ](modules/contact-management/custom-activity-forms)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/contact-management/member-registration-automation)
[](https://docs.kentico.com/modules/contact-management/member-registration-automation)[](https://docs.kentico.com/modules/contact-management/member-registration-automation)