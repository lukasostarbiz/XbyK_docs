---
source: https://docs.kentico.com/modules/data-protection/build-tracking-consent-banner-component
scrape_date: 2026-01-22
---

Module: Data protection
6 of 13 Pages
# Build a tracking consent banner component
Many websites offer a simple banner that allows visitors to agree to all tracking. With this kind of banner, visitors don’t need to go to a dedicated cookie policy page to read the full details of each consent and configure their preferences accordingly.
Here, we will look over the process of creating a banner that allows visitors to consent to all tracking cookies.
[![Consent tracking banner example](/docsassets/guides/build-a-tracking-consent-banner/2023-12-06+14_51_16-Kentico+training+guides.png)](/docsassets/guides/build-a-tracking-consent-banner/2023-12-06+14_51_16-Kentico+training+guides.png)
The banner in this example also ensures that contacts who have revoked consent in a different browser will have tracking cookies removed from their current browser.
## Define the model
To start out, consider what kind of data the banner will need to display and create a view model with properties that reflect those needs.
The content of the banner needs to include the title, the message, a link to configure the cookie level, and text for the accept button. You should also add properties to indicate if the visitor has agreed to any consents or if they have accepted any cookie level in the past.
Add a new class `TrackingConsentViewModel` to the _~/Features/DataProtection/ViewComponents/TrackingConsent_ folder in the  _TrainingGuides.Web_ project.
C#
**TrackingConsentViewModel.cs**
Copy
```
using Microsoft.AspNetCore.Html;
namespace TrainingGuides.Web.Features.DataProtection.ViewComponents.TrackingConsent;

public class TrackingConsentViewModel
{
    public bool CookieAccepted { get; set; }
    public bool IsAgreed { get; set; }
    public HtmlString CookieHeaderHtml { get; set; } = HtmlString.Empty;
    public HtmlString CookieMessageHtml { get; set; } = HtmlString.Empty;
    public string AcceptMessage { get; set; } = string.Empty;
    public string RedirectUrl { get; set; } = string.Empty;
    public string ConfigureMessage { get; set; } = string.Empty;
    public string ConsentMapping { get; set; } = string.Empty;
    public string BaseUrl { get; set; } = string.Empty;
}
```

## Add a view component
This example does not need configurable properties and should be rendered on every page by way of the Layout view. It is well-suited for a view component.
Looking at the [tracking consent view component example](/documentation/developers-and-admins/data-protection/consent-development) from the documentation, the component behaves in the following ways:
  * It retrieves the consent the user will be presented with.
  * It checks whether the current contact has agreed to that consent, and if not, it ensures that the contact no longer has tracking cookies.
    * This covers the scenario of a visitor being identified on two different browsers and revoking consent on one.
  * It puts together a view model with the data that will be displayed on the banner.


This example will follow the same principle but with multiple consents to account for the more granular cookie-level configuration present in the Training guides site.
  1. Under the _~/Features/DataProtection/ViewComponents/TrackingConsent_ folder in _TrainingGuides.Web_ , create a `TrackingConsentViewComponent`_._
  2. Retrieve the relevant consent by pulling the current cookie-level consent mapping and passing the codenames to the consent provider.
  3. Check which consent the current contact has agreed to and ensure that their cookies reflect the corresponding level.
  4. Assemble a view model with the relevant consent short texts.
**Consent data encryption**
As with the [cookie preferences widget](/modules/data-protection/create-cookie-consent-widget), the view model needs to hold encrypted data about the consents it renders. This ensures that no consent agreements are created between the contact and the wrong consent, in the event that an editor changes the cookie level mapping after the banner is rendered but before it is submitted.
This time, however, since the banner always accepts the highest cookie consent level, we only need to include a list of consents, with no need for information about which cookie level they are mapped to.


C#
**TrackingConsentViewComponent.cs**
Copy
```
using Microsoft.AspNetCore.Html;
using Microsoft.AspNetCore.Mvc;
using CMS.ContactManagement;
using CMS.DataProtection;
using CMS.Helpers;
using CMS.DataEngine;
using Kentico.Content.Web.Mvc.Routing;
using Kentico.Web.Mvc;
using TrainingGuides.DataProtectionCustomizations;
using TrainingGuides.Web.Features.DataProtection.Services;
using TrainingGuides.Web.Features.DataProtection.Shared;
using TrainingGuides.Web.Features.Shared.Services;

namespace TrainingGuides.Web.Features.DataProtection.ViewComponents.TrackingConsent;

public class TrackingConsentViewComponent : ViewComponent
{
    private readonly IConsentAgreementService consentAgreementService;
    private readonly IInfoProvider<ConsentInfo> consentInfoProvider;
    private readonly IStringEncryptionService stringEncryptionService;
    private readonly ICookieAccessor cookieAccessor;
    private readonly IPreferredLanguageRetriever preferredLanguageRetriever;
    private readonly ICookieConsentService cookieConsentService;
    private readonly IHttpRequestService httpRequestService;

    public TrackingConsentViewComponent(
        IConsentAgreementService consentAgreementService,
        IInfoProvider<ConsentInfo> consentInfoProvider,
        IStringEncryptionService stringEncryptionService,
        ICookieAccessor cookieAccessor,
        IPreferredLanguageRetriever preferredLanguageRetriever,
        ICookieConsentService cookieConsentService,
        IHttpRequestService httpRequestService)
    {
        this.consentAgreementService = consentAgreementService;
        this.consentInfoProvider = consentInfoProvider;
        this.stringEncryptionService = stringEncryptionService;
        this.cookieAccessor = cookieAccessor;
        this.preferredLanguageRetriever = preferredLanguageRetriever;
        this.cookieConsentService = cookieConsentService;
        this.httpRequestService = httpRequestService;
    }

    /// <summary>
    /// Invokes the view component, ensures the correct cookie level based on accepted consents
    /// </summary>
    /// <returns>cookie banner view if visitor has not chosen a cookie level, empty if it has already been chosen </returns>
    public async Task<IViewComponentResult> InvokeAsync()
    {
        var currentMapping = await cookieConsentService.GetCurrentMapping();

        if (currentMapping == null
            || currentMapping.PreferenceConsentCodeName.Count() == 0
            || currentMapping.AnalyticalConsentCodeName.Count() == 0
            || currentMapping.MarketingConsentCodeName.Count() == 0)
        {
            return Content(string.Empty);
        }

        var consents = await consentInfoProvider
            .Get()
            .WhereIn("ConsentName", new string[] {
                currentMapping.PreferenceConsentCodeName.First(),
                currentMapping.AnalyticalConsentCodeName.First(),
                currentMapping.MarketingConsentCodeName.First()
            })
            .GetEnumerableTypedResultAsync();

        if (consents.Count() > 0)
        {
            var currentContactInfo = ContactManagementContext.GetCurrentContact(false);

            string text = "<ul>";
            List<string> codenames = [];
            bool isAgreed = false;
            foreach (var consent in consents)
            {
                codenames.Add(consent.ConsentName);

                text += $"<li>{(await consent.GetConsentTextAsync(preferredLanguageRetriever.Get())).ShortText}</li>";

                //agreed will end up being true if the contact has agreed to at least one consent
                isAgreed = isAgreed || ((currentContactInfo != null) && consentAgreementService.IsAgreed(currentContactInfo, consent));
            }
            text += "</ul>";

            string mapping = codenames.Join(Environment.NewLine);

            // Sets the cookie level according to which consents have been accepted
            // Required for scenarios where one contact uses multiple browsers, in case of revoked consent
            EnsureCorrectCookieLevel(currentContactInfo, consents, currentMapping);

            var consentModel = new TrackingConsentViewModel
            {
                CookieAccepted = ValidationHelper.GetBoolean(cookieAccessor.Get(CookieNames.COOKIE_ACCEPTANCE), false),

                CookieMessageHtml = new HtmlString(text),

                CookieHeaderHtml = new HtmlString("This site uses cookies in the following ways"),

                AcceptMessage = "Accept all cookies",

                // Checks whether the current contact has given an agreement for a cookie level
                IsAgreed = isAgreed,

                RedirectUrl = "/cookie-policy",

                ConfigureMessage = "Configure cookies",

                ConsentMapping = stringEncryptionService.EncryptString(mapping),

                BaseUrl = httpRequestService.GetBaseUrl()
            };

            // Displays a view with tracking consent information and actions
            return View("~/Features/DataProtection/ViewComponents/TrackingConsent/TrackingConsent.cshtml", consentModel);
        }

        return Content(string.Empty);
    }

    /// <summary>
    /// Makes sure that the cookie level of the current visitor matches the consents they are agreed to. This accounts for contacts who revoke consent in one browser then revisit the site in another.
    /// </summary>
    /// <param name="contact">The contact associated with the visitor</param>
    /// <param name="consents">ConsentInfo objects that are mapped to cookie levels</param>
    /// <param name="mapping">Cookie level consent mapping</param>
    /// <returns>true if cookie levels were updated or alredy up-to-date, false if there is an exception</returns>
    private bool EnsureCorrectCookieLevel(
        ContactInfo? contact,
        IEnumerable<ConsentInfo> consents,
        CookieLevelConsentMappingInfo mapping)
    {

        var level = ValidationHelper.GetBoolean(cookieAccessor.Get(CookieNames.COOKIE_ACCEPTANCE), false)
                ? CookieConsentLevel.Essential
                : CookieConsentLevel.NotSet;
        var preferenceConsent = consents
            .Where(consent => consent.ConsentName == mapping.PreferenceConsentCodeName.FirstOrDefault())
            .FirstOrDefault();

        if (contact != null && preferenceConsent != null && consentAgreementService.IsAgreed(contact, preferenceConsent))
        {
            level = CookieConsentLevel.Preference;
            var analyticalConsent = consents
                .Where(consent => consent.ConsentName == mapping.AnalyticalConsentCodeName.FirstOrDefault())
                .FirstOrDefault();

            if (contact != null && analyticalConsent != null && consentAgreementService.IsAgreed(contact, analyticalConsent))
            {
                level = CookieConsentLevel.Analytical;
                var marketingConsent = consents
                    .Where(consent => consent.ConsentName == mapping.MarketingConsentCodeName.FirstOrDefault())
                    .FirstOrDefault();

                if (contact != null && marketingConsent != null && consentAgreementService.IsAgreed(contact, marketingConsent))
                {
                    level = CookieConsentLevel.Marketing;
                }
            }
        }
        return cookieConsentService.UpdateCookieLevels(level);
    }
}
```

**Cookie level consistency**
Notice the `EnsureCorrectCookieLevel` method, which checks the contact’s consents, and changes their cookie level accordingly.
This is necessary because the same contact can be identified across multiple browsers. If they were to consent to the highest cookie level, then revoke their consent in one browser before visiting the site in a second browser or device, the second browser would still contain tracking cookies based on their previous consent. As a result, your app would directly violate their personal data rights.
For this reason, this example will indiscriminately invoke the consent banner from the layout view so that this code always executes, regardless of whether or not it displays anything.
Also, note the `RedirectUrl` property, which sets the URL of the cookie configuration page. It is hard-coded in this example, but you can change it to pull a URL from a specific content item or custom configuration class.
[ Previous page ](/modules/data-protection/handle-cookie-consent-requests)
6 of 13
[ Mark complete and continue ](/modules/data-protection/handle-tracking-consent-banner-data)
![]()
[]()[]()
