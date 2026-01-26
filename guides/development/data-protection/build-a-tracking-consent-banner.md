---
source: https://docs.kentico.com/guides/development/data-protection/build-a-tracking-consent-banner
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Data protection](/guides/development/data-protection)
  * Build a tracking consent banner 


# Build a tracking consent banner
Many websites offer a simple banner that allows visitors to agree to all tracking. With this kind of banner, visitors don’t need to go to a dedicated cookie policy page to read the full details of each consent and configure their preferences accordingly.
Here, we will look over the process of creating a banner that allows visitors to consent to all tracking cookies.
[![Consent tracking banner example](/docsassets/guides/build-a-tracking-consent-banner/2023-12-06+14_51_16-Kentico+training+guides.png)](/docsassets/guides/build-a-tracking-consent-banner/2023-12-06+14_51_16-Kentico+training+guides.png)
The banner in this example also ensures that contacts who have revoked consent in a different browser will have tracking cookies removed from their current browser.
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](/documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


The examples in this guide require that you:
  * Have followed along with the samples from the [earlier guides in the series](/guides/development/data-protection).


The example presented in this [Data protection guide series](/guides/development/data-protection) is a **valid implementation of data protection for[Contacts](/documentation/business-users/digital-marketing/contact-management)** in Xperience by Kentico. (Note that it does not cover the collection and erasure of [Members](/documentation/business-users/members) and their associated data.)
You can copy-paste the code samples into your own solution.
However, if you choose to do so, **make sure to consult your legal team** to determine whether the implementation, texts, and consent levels **meet the requirements of your region and market**.
**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
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
As with the [cookie preferences widget](/guides/development/data-protection/create-a-cookie-preferences-widget), the view model needs to hold encrypted data about the consents it renders. This ensures that no consent agreements are created between the contact and the wrong consent, in the event that an editor changes the cookie level mapping after the banner is rendered but before it is submitted.
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
## Make the view
Much like the view for the [cookie preferences widget](/guides/development/data-protection/create-a-cookie-preferences-widget), the view for this component will use an AJAX form with a hidden value containing encrypted consent data. It should post the contents of the AJAX form to a controller action, which will be added in a subsequent step.
cshtml
**TrackingConsent.cshtml**
Copy
```
@using TrainingGuides.Web.Features.DataProtection.ViewComponents.TrackingConsent;

@model TrackingConsentViewModel

@{
    var messageId = "BannerMessage";
}

@* Displays content only if the current contact has not yet given a tracking consent agreement, or chosen only essential cookies. *@
@if (!Model.IsAgreed && !Model.CookieAccepted)
{
    @using (Html.AjaxBeginForm("CookieBanner", "cookiebannersubmit", new AjaxOptions
    {
        HttpMethod = "POST",
        InsertionMode = InsertionMode.Replace,
        UpdateTargetId = messageId
    }, new { action = $"{Model.BaseUrl}/cookies/cookiebannersubmit" }))
    {

        <div class="xpcookiebanner js-xpcookiebanner is-active">
            <input id="ConsentMapping" name="ConsentMapping" type="hidden"  value="@Model.ConsentMapping" />
            <div class="xpcookiebanner__inner container">
                <h5 class="xpcookiebanner__header"> @Model.CookieHeaderHtml</h5>
                @Model.CookieMessageHtml
                <div class="xpcookiebanner__ctas">
                    @if (!string.IsNullOrWhiteSpace(Model.AcceptMessage))
                    {
                        <button type="submit" value="4" name="CookieLevelSelected" class="btn tg-btn-primary text-uppercase xpcookiebanner__cta js-close-cookie" data-tracking-code="CookieBar_Accept">
                            @Model.AcceptMessage
                        </button>
                    }
                    @if (!string.IsNullOrWhiteSpace(Model.RedirectUrl) && !string.IsNullOrWhiteSpace(Model.ConfigureMessage))
                    {
                        <a href="@string.Format("{0}{1}", Model.BaseUrl, Model.RedirectUrl)" class="xpcookiebanner__cta" data-tracking-code="CookieBar_More">
                            @Model.ConfigureMessage
                        </a>
                    }
                </div>
            </div>
            <div id="@messageId" class="xpcookiebanner__message"></div>
        </div>
    }
}
```

## Expand the Cookie consent service
Before you make the controller action to handle the view component’s POST request, expand the `CookieConsentService` class with some methods that will help handle the POST.
Since the banner posts a list of consents to accept rather than a full mapping, it requires a new overload of the `SetCurrentCookieConsentLevel` method, which accepts all consents in the provided list.
C#
**CookieConsentService.cs**
Copy
```
...
/// <summary>
/// Sets current cookie consent level, internally sets system CookieLevel and agrees to provided consents.
/// </summary>
/// <param name="level">Cookie consent level to set</param>
/// <param name="acceptAllList">List of all cookie consents included when the visitor accepts all from the cookie banner</param>
/// <exception cref="Exception">Throws if no cookie level consent mappings are provided</exception>
/// <returns>true if the cookie level was updated successfully and all consents in list were successfully agreed</returns>
public async Task<bool> SetCurrentCookieConsentLevel(CookieConsentLevel level, IEnumerable<string> acceptAllList)
{
    if (acceptAllList == null || acceptAllList.Count() == 0)

        throw new Exception();

    bool cookiesUpToDate = UpdateCookieLevels(level);

    // Get current contact after changes to the cookie level
    var currentContact = ContactManagementContext.GetCurrentContact();

    bool consentsAllAgreed = await AcceptAllConsents(currentContact, acceptAllList);
    bool successful = consentsAllAgreed && cookiesUpToDate;

    if (successful)
        SetCookieAcceptanceCookie();

    return successful;
}
...
```

C#
**CookieConsentService.cs**
Copy
```
...
/// <summary>
/// Accepts all consents in the provided list for the provided contact
/// </summary>
/// <param name="contact">The contact who has accepted the consents</param>
/// <param name="acceptAllList">The consents that have been acceepted</param>
private async Task<bool> AcceptAllConsents(ContactInfo contact, IEnumerable<string> acceptAllList)
{
    bool allConsentsExist = true;

    foreach (string codename in acceptAllList)
    {
        var consent = await consentInfoProvider.GetAsync(codename);

        if (consent == null)
            allConsentsExist = false;

        else if (!consentAgreementService.IsAgreed(contact, consent))
            consentAgreementService.Agree(contact, consent);
    }

    return allConsentsExist;
}
...
```

With this new method, everything is in place to create the controller action.
## Expand the controller
Open _~/Features/DataProtection/Controllers/CookiesController.cs_ in the _TrainingGuides.Web_ project.
  1. Add an asynchronous action to the controller called `CookieBanner`.
  2. Register it under the route specified in the view component’s AJAX form.
  3. In the `CookieBanner` controller action, pull the list of consents from the request provided through the hidden input (rather than a dictionary like the `CookiePreferences` controller action.)
  4. Return a new success message view if it is successful.


With both the actions for the cookie banner and the cookie preferences widget, the controller should look like the following.
C#
**CookiesController.cs**
Copy
```
using Microsoft.AspNetCore.Mvc;
using Newtonsoft.Json;
using TrainingGuides.Web.Features.DataProtection.Services;
using TrainingGuides.Web.Features.DataProtection.Shared;
using TrainingGuides.Web.Features.DataProtection.Widgets.CookiePreferences;

namespace TrainingGuides.Web.Features.DataProtection.Controllers;

public class CookiesController : Controller
{
    private const string COOKIE_UPDATE_MESSAGE = "~/Features/DataProtection/Shared/CookieUpdateMessage.cshtml";
    private const string COOKIE_UPDATE_MESSAGE_SUCCESS = "Cookie consents have been successfully updated.";
    private const string COOKIE_UPDATE_MESSAGE_FAILURE = "Unable to update cookie consents. Please try again.";

    private readonly IStringEncryptionService stringEncryptionService;
    private readonly ICookieConsentService cookieConsentService;

    public CookiesController(
        IStringEncryptionService stringEncryptionService,
        ICookieConsentService cookieConsentService)
    {
        this.stringEncryptionService = stringEncryptionService;
        this.cookieConsentService = cookieConsentService;
    }

    [HttpPost("/cookies/submit")]
    public async Task<IActionResult> CookiePreferences(CookiePreferencesViewModel requestModel)
    {
        IDictionary<int, string> mapping;
        try
        {
            mapping = GetDictionaryMapping(requestModel.ConsentMapping);
        }
        catch
        {
            return ErrorView();
        }

        CookieConsentLevel selectedConsentValue;
        if (requestModel.CookieLevelSelected is > 0 and < 5)
        {
            selectedConsentValue = (CookieConsentLevel)requestModel.CookieLevelSelected;
        }
        else
        {
            return ErrorView();
        }

        try
        {
            if (!await cookieConsentService.SetCurrentCookieConsentLevel(selectedConsentValue, mapping))
            {
                throw new Exception();
            }
        }
        catch
        {
            return ErrorView();
        }

        return SuccessView(COOKIE_UPDATE_MESSAGE_SUCCESS);
    }


    [HttpPost("/cookies/cookiebannersubmit")]
    public async Task<IActionResult> CookieBanner(CookiePreferencesViewModel requestModel)
    {
        IEnumerable<string> consents;
        try
        {
            consents = GetConsentsList(requestModel.ConsentMapping);
        }
        catch
        {
            return ErrorView();
        }

        try
        {
            if (!await cookieConsentService.SetCurrentCookieConsentLevel(CookieConsentLevel.Marketing, consents))
            {
                throw new Exception();
            }
        }
        catch
        {
            return ErrorView();
        }

        return SuccessView();
    }

    /// <summary>
    /// Gets a dictionary of consent codenames and the cookie levels to which they are mapped from an encrypted string
    /// </summary>
    /// <param name="mappingEncrypted">The encrypted string representation of the mapping</param>
    /// <returns>A dictionary of integer cookie levels and consent codename values</returns>
    /// <exception cref="Exception">Throws if there is no encrypted string, or if the dictionary can't be decrypted and deserialized, or if the mapping does not contain the required cookie level keys</exception>
    private IDictionary<int, string> GetDictionaryMapping(string mappingEncrypted)
    {
        if (string.IsNullOrEmpty(mappingEncrypted))
        {
            throw new Exception("No encrypted string.");
        }

        Dictionary<int, string> consentMapping;

        try
        {
            string mappingDecrypted = stringEncryptionService.DecryptString(mappingEncrypted);
            consentMapping = JsonConvert.DeserializeObject<Dictionary<int, string>>(mappingDecrypted) ?? [];
        }
        catch
        {
            throw new Exception("Dictionary can't be decrypted or deserialized.");
        }

        if (!(consentMapping.ContainsKey((int)CookieConsentLevel.Preference)
            && consentMapping.ContainsKey((int)CookieConsentLevel.Analytical)
            && consentMapping.ContainsKey((int)CookieConsentLevel.Marketing)))
        {
            throw new Exception("Mapping does not contain the required cookie level keys.");
        }

        return consentMapping;
    }

    /// <summary>
    /// Gets a list of consent codenames from an encrypted string
    /// </summary>
    /// <param name="mappingEncrypted">The encrypted string representation of the consents list</param>
    /// <returns>A list of consent codenames</returns>
    /// <exception cref="Exception">Throws if there is no encrypted string, or if no consents are found from decryption</exception>
    private IEnumerable<string> GetConsentsList(string mappingEncrypted)
    {
        if (string.IsNullOrEmpty(mappingEncrypted))
        {
            throw new Exception();
        }

        string mapping = stringEncryptionService.DecryptString(mappingEncrypted);

        IEnumerable<string> consents = mapping.Split(Environment.NewLine).ToList();

        if (consents.Count() == 0)
        {
            throw new Exception();
        }

        return consents;
    }

    private IActionResult SuccessView(string message = "") =>
        PartialView(COOKIE_UPDATE_MESSAGE, new CookieUpdateMessageViewModel(message));

    private IActionResult ErrorView() =>
        PartialView(COOKIE_UPDATE_MESSAGE, new CookieUpdateMessageViewModel(COOKIE_UPDATE_MESSAGE_FAILURE));

}
```

## Invoke the component
For the component to appear on the site, it must be invoked.
  1. Go to _~/Views/Shared/_Layout.cshtml_ in the _TrainingGuides.Web_ project, and add a `@using` directive for the view component’s namespace.
  2. Invoke it with a  _vc_ tag. (Tag helpers are registered in __ViewImports.cshtml file,_ in the root of the _TrainingGuides.Web_ project _._)


cshtml
**_Layout.cshtml**
Copy
```
...
@using TrainingGuides.Web.Features.Header
@using TrainingGuides.Web.Features.DataProtection.ViewComponents.TrackingConsent
...
```

cshtml
**_Layout.cshtml**
Copy
```
...
<body class="t-default">
    <div>
        <vc:header />
        <vc:tracking-consent />
...
```

Now, the cookie banner should appear at the top of the page for any users who have not set a cookie level or agreed to any consents. You can test this by running your Xperience instance a new private or incognito window (assuming one is not already open.)
[![Tracking consent banner](/docsassets/guides/build-a-tracking-consent-banner/cookie-banner-2.png)](/docsassets/guides/build-a-tracking-consent-banner/cookie-banner-2.png)
## What’s next?
The [next parts of this series](/guides/development/data-protection/identity-collectors-gather-contacts-associated-with-a-visitor) will cover the process of implementing data collection and erasure.
![]()
[]()[]()
