---
source: https://docs.kentico.com/modules/data-protection/handle-tracking-consent-banner-data
scrape_date: 2026-01-22
---

Module: Data protection
7 of 13 Pages
# Handle the tracking consent banner data
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
[ Previous page ](/modules/data-protection/build-tracking-consent-banner-component)
7 of 13
[ Mark complete and continue ](/modules/data-protection/hide-builder-content-from-visitors-who-have-not-consented-to-tracking)
![]()
[]()[]()
