---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking/log-email-activities
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital marketing setup](/documentation/developers-and-admins/digital-marketing-setup)
  * [Set up email tracking](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking)
  * Log email activities 


# Log email activities
**Email license requirements**
The license tier required for email features depends on the size of the [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) that you use:
  * Email microchannels – available for all license tiers
  * Standard size email channels – require the **Advanced** license tier


To learn more, see the description of the [Channel size](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels) property for email channels.
Unlike general email statistics, [email activities](/documentation/business-users/digital-marketing/emails/track-email-statistics#contact-activities-for-emails) are not anonymous, and provide information about the actions of individual email recipients. To ensure compliance with data protection regulations, email activities are **not logged by default**.
Developers need to [implement](#implement-an-email-tracking-activity-evaluator) logic that decides when it is possible to track email activities for specific contacts.
**Email tracking prerequisite**
To log email activities, you must also have [Email tracking](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking) properly configured for your application.
## Implement an email tracking activity evaluator
To log email activities, you must create and register an `IEmailActivityTrackingEvaluator` service that decides when the logging is allowed for specific contacts and emails. The implementation depends on your project’s requirements, on the target audience of your emails, and the [data protection](/documentation/developers-and-admins/data-protection) regulations you need to comply with.
A common approach is to prepare one or more tracking [consents](/documentation/developers-and-admins/data-protection/consent-management), and only log activities for contacts who agree to be tracked.
The following example demonstrates how to enable email activity logging based on a consent:
  1. Open your Xperience solution in Visual Studio.
  2. Create a new class implementing the `IEmailActivityTrackingEvaluator` interface. 
     * See [Integrate custom code](/documentation/developers-and-admins/customization/integrate-custom-code) for best practices about adding custom code to your project.
  3. Add the `IsTrackingAllowed` method. 
     * Return a true or false value based on the provided `ContactInfo` and `EmailConfigurationInfo` arguments.
  4. Register the implementation using the `RegisterImplementation` assembly attribute.


C#
**IEmailActivityTrackingEvaluator example**
Copy
```
// Registers the custom implementation of IEmailActivityTrackingEvaluator
[assembly: RegisterImplementation(typeof(IEmailActivityTrackingEvaluator), typeof(CustomEmailActivityTrackingEvaluator))]

public class CustomEmailActivityTrackingEvaluator : IEmailActivityTrackingEvaluator
{
    // Services required to evaluate consent agreements
    private readonly IConsentAgreementService consentAgreementService;
    private readonly IInfoProvider<ConsentInfo> consentInfoProvider;

    public CustomEmailActivityTrackingEvaluator(IConsentAgreementService consentAgreementService, IInfoProvider<ConsentInfo> consentInfoProvider)
    {
        this.consentAgreementService = consentAgreementService;
        this.consentInfoProvider = consentInfoProvider;
    }

    public async Task<bool> IsTrackingAllowed(ContactInfo contact, EmailConfigurationInfo emailConfiguration)
    {
        // Gets the 'TrackingConsent' consent
        ConsentInfo consent = await consentInfoProvider.GetAsync("TrackingConsent");

        // Returns a true value if the contact has given an agreement for the consent
        return consent is not null && consentAgreementService.IsAgreed(contact, consent);
    }
}
```

Email activities are now only logged if the contact representing the recipient has given an agreement for the specified consent.
![]()
[]()[]()
