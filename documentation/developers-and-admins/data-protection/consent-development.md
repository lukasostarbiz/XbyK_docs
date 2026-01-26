---
source: https://docs.kentico.com/documentation/developers-and-admins/data-protection/consent-development
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Data protection](/documentation/developers-and-admins/data-protection)
  * Consent development 


# Consent development
In Xperience, consents are records used to inform visitors about the means of collecting and manipulating their personal data by the system, administrators, marketers and anyone else who has access to said data, including third parties to whom this data is forwarded.
You can use consents to comply with the requirements of the [GDPR](/documentation/developers-and-admins/data-protection/gdpr-compliance) and other personal data regulations.
Whenever any personal data of a visitor is obtained, it is necessary to have a consent agreement from the visitor to legally process this data. This includes [tracking of contacts](/documentation/business-users/digital-marketing/contact-management) and their [activities](/documentation/business-users/digital-marketing/contact-activities). Every consent agreement of a visitor is directly connected to a corresponding contact. Deleting contacts from the system therefore also causes deletion of the contact’s consent agreements.
**Prerequisite**
Since consent agreements are directly connected to contacts, you need to enable [contact tracking](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration) in your Xperience application.
This page describes how to set up consent functionality on Xperience websites. To learn how to create consents in the Xperience administration, see [Consent management](/documentation/developers-and-admins/data-protection/consent-management).
## Set up tracking consent
When creating a consent for the [tracking of contacts](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration) and [activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities), you need to provide a way for visitors to adjust their allowed [cookie level](/documentation/developers-and-admins/data-protection/cookies). The system only performs the tracking for visitors whose cookie level is  _Visitor_ or higher.
To ensure that visitors are not tracked until they give the tracking consent, set the **default cookie level** in the Xperience administration:
  1. Open **Channel management** application.
  2. Select your website channel.
  3. Open the **Channel settings** tab.
  4. In the **Cookies** section, choose a **Default cookie level**. 
     * For an overview of individual cookie levels, see [Cookie levels](/documentation/developers-and-admins/data-protection/cookies#cookie-levels).
  5. Select **Save**.


To allow visitors to give agreements with the tracking consent on your site, you need to develop corresponding components. For example, you can implement tracking consent functionality as a [view component](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/view-components).
### Create the consent
Start by [defining the tracking consent](/documentation/developers-and-admins/data-protection/consent-management) in the Xperience administration interface (in the **Data protection** application on the **Consents** tab).
### Create the model
We recommend creating a view model class in your project to represent data related to the tracking consent. Include the following properties:
  * A `string` property for storing the consent’s short text
  * A `bool` property to indicate whether an agreement with the consent was given
  * A `string` property for passing the current page URL as the return URL


C#
**View model**
Copy
```
public class TrackingConsentViewModel
{
    public string ShortText { get; set; }

    public bool IsAgreed { get; set; }

    public string ReturnUrl { get; set; }
}
```

### Create the view component
Create a new [view component](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/view-components) class in your project:
  * Initialize instances of services, which are necessary to manage the tracking consent:
    * `IInfoProvider<ConsentInfo>` – to load consent data.
    * `IConsentAgreementService`– to manage consent agreements.
    * `ICurrentCookieLevelProvider`– to manage the current visitor’s cookie level.
    * `IWebPageDataContextRetriever` and `IWebPageUrlRetriever` – to obtain the URL of the current page.
    * `IPreferredLanguageRetriever` – to get the currently selected content language.
We recommend using [dependency injection](/documentation/developers-and-admins/development/website-development-basics/dependency-injection) to initialize service instances.
C#
**Services**
Copy
```
public class TrackingConsentViewComponent : ViewComponent
{

    private readonly IConsentAgreementService consentAgreementService;
    private readonly ICurrentCookieLevelProvider currentCookieLevelProvider;
    private readonly IInfoProvider<ConsentInfo> consentInfoProvider;
    private readonly IWebPageDataContextRetriever webPageDataContextRetriever;
    private readonly IWebPageUrlRetriever urlRetriever;
    private readonly IPreferredLanguageRetriever preferredLanguageRetriever;

  public TrackingConsentViewComponent(IConsentAgreementService consentAgreementService,
                                    ICurrentCookieLevelProvider currentCookieLevelProvider,
                                    IInfoProvider<ConsentInfo> consentInfoProvider,
                                    IWebPageDataContextRetriever webPageDataContextRetriever,
                                    IWebPageUrlRetriever urlRetriever,
                                    IPreferredLanguageRetriever preferredLanguageRetriever)
    {
        this.consentAgreementService = consentAgreementService;
        this.currentCookieLevelProvider = currentCookieLevelProvider;
        this.consentInfoProvider = consentInfoProvider;
        this.webPageDataContextRetriever = webPageDataContextRetriever;
        this.urlRetriever = urlRetriever;
        this.preferredLanguageRetriever = preferredLanguageRetriever;
    }
}
```

  * Implement the component’s `InvokeAsync` method to display information about the tracking consent:
C#
**Invoke the component**
Copy
```
public async Task<IViewComponentResult> InvokeAsync()
{
    // Gets the related tracking consent
    // Fill in the code name of the appropriate consent object in Xperience
    ConsentInfo consent = consentInfoProvider.Get("SampleTrackingConsent");

    if (consent != null)
    {
        // Gets the current contact
        ContactInfo currentContact = ContactManagementContext.GetCurrentContact();

        // Gets the code name of the currently selected language
        var preferredLanguage = preferredLanguageRetriever.Get();

        // Sets the default cookie level for contacts who have revoked the tracking consent
        // Required for scenarios where one contact uses multiple browsers
        if ((currentContact != null) && !consentAgreementService.IsAgreed(currentContact, consent))
        {
            // Gets the default cookie level for the current channel and sets it for the contact
            var defaultCookieLevel = currentCookieLevelProvider.GetDefaultCookieLevel();
            currentCookieLevelProvider.SetCurrentCookieLevel(defaultCookieLevel);
        }

        var consentModel = new TrackingConsentViewModel
        {
            // Adds the consent's short text to the model
            ShortText = (await consent.GetConsentTextAsync(preferredLanguage)).ShortText,

            // Checks whether the current contact has given an agreement for the tracking consent
            IsAgreed = (currentContact != null) && consentAgreementService.IsAgreed(currentContact, consent),

            // Adds the current page URL to the model
            ReturnUrl = webPageDataContextRetriever.TryRetrieve(out var currentWebPageContext)
                ? (await urlRetriever.Retrieve(currentWebPageContext.WebPage.WebPageItemID, preferredLanguage)).RelativePath
                // Gets the URL from the request context for pages using custom routing
                : (HttpContext.Request.PathBase + HttpContext.Request.Path).Value

        };

        // Displays a view with tracking consent information and actions
        return View("~/Components/ViewComponents/TrackingConsent/_TrackingConsent.cshtml", consentModel);
    }

    return Content(string.Empty);
}
```



### Create the view
Add the view referenced in your view component’s invoke method. Display the text of the tracking consent and inputs for giving or revoking agreements. Depending on your preferences and legal requirements, you can hide the view’s output for visitors who have given the tracking consent or display alternative content (for example the option to [revoke the consent](#revoke-consents)).
cshtml
**Tracking consent view**
Copy
```
@using Kentico.Content.Web.Mvc;

@model TrackingConsentViewModel

@* Displays content only if the current contact has not yet given a tracking consent agreement *@
@if (!Model.IsAgreed)
{
    var routeData = new Dictionary<string, string> { { "returnUrl", Model.ReturnUrl } };

    <div>
        @* Displays the consent text*@
        @Html.Raw(@Model.ShortText)
    </div>
    @* Displays an input for giving a consent agreement *@
    @* Replace "TrackingConsent" with the name of your controller that handles consent agreement requests *@
    <form asp-action="Agree"
          asp-controller="TrackingConsent"
          asp-antiforgery="true"
          asp-all-route-data="routeData"
          method="post">
        <input type="submit" value="I agree">
    </form>
}
```

### Create the controller
Add a controller that handles requests for adding consent agreements:
  * Initialize instances of services required to manage the tracking consent (some of the same services described for the [View component code](#create-the-view-component)).
  * Define a POST action for creating consent agreements.
  * Retrieve the visitor’s contact using the `ContactManagementContext` API (see [Contact configuration](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration)).
  * Modify the visitor’s cookie level according to the performed action – call the `SetCurrentCookieLevel` method of the `ICurrentCookieLevelProvider` service.
    * Set the _All_ cookie level when creating tracking consent agreements.
  * Create consent agreements using the `Agree` method of the `IConsentAgreementService` instance.
C#
**Agree POST action**
Copy
```
[HttpPost]
[ValidateAntiForgeryToken]
public ActionResult Agree(string returnUrl)
{
    // Gets the related tracking consent
    ConsentInfo consent = consentInfoProvider.Get("SampleTrackingConsent");

    if (consent != null)
    {
        // Sets the visitor's cookie level to 'All' (enables contact tracking)
        currentCookieLevelProvider.SetCurrentCookieLevel(Kentico.Web.Mvc.CookieLevel.All.Level);

        // Gets the current contact and creates a consent agreement
        ContactInfo currentContact = ContactManagementContext.GetCurrentContact();

        if (currentContact != null)
        {
            consentAgreementService.Agree(currentContact, consent);
        }

        return Redirect(returnUrl);
    }

    return new StatusCodeResult(StatusCodes.Status400BadRequest);
}
```



### Invoke the component
Invoke the view component from a suitable location in your website’s content. For example, use the main layout to ensure that the input to give the tracking consent is displayed on all pages.
cshtml
**_Layout example**
Copy
```
...

<html>
<head >
    ...
</head>
<body>
    <div>
        @* Invokes 'TrackingConsentViewComponent' *@
        <vc:tracking-consent />

        ...
```

Visitors on your website now have an option to give agreements with your tracking consent. New visitors are not tracked as contacts until they positively confirm that they agree with the tracking.
## Create conditions based on consents
If you have any functionality that stores or processes personal data, you can create conditions to ensure that it only runs for contacts who have given consent.
  1. Initialize an instance of the `IConsentAgreementService` interface and make it available in your code.
  2. Obtain the related contact and consent objects.
  3. Evaluate whether the contact has given an agreement with the given consent by calling the `IsAgreed` method of the `IConsentAgreementService` instance.


## Revoke consents
To ensure compliance of your website with the GDPR or other data protection regulations, you need to give visitors the option to view and revoke their previously given consent agreements. We recommend creating a privacy page, where visitors can view the consents for which they have given agreements and potentially revoke them.
“It shall be as easy to withdraw as to give consent.”
(Source: [GDPR Article 7](http://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679#d1e2001-1-1), Paragraph 3)
The following sections describe how to create a privacy page using MVC architecture. The privacy page lists accepted consents for visitors, and allows them to view the full consent details or revoke their agreements.
  * Create a model representing the list of consents for which visitors have given agreements.
  * Create a model representing consent details.
  * Create a controller that provides actions for displaying and revoking consents.
  * Implement a handler for the `DataProtectionEvents.RevokeConsentAgreement`[global event](/documentation/developers-and-admins/customization/handle-global-events) to clear related cookies and personal data.
  * Create views for the consent list and details pages.


### Create the models
  * Add a model class that represents the list of consents for which visitors have given agreements.
C#
**List of consents**
Copy
```
using System.Collections.Generic;
using System.Linq;

using CMS.DataProtection;

public class ConsentListingModel
{
    public IEnumerable<Consent> Consents { get; set; } = Enumerable.Empty<Consent>();
}
```

**`Consent`and`ConsentInfo` objects**
The Xperience API uses `ConsentInfo` objects to work with consents in general. However, when you load consents for which a contact has given an agreement, the `ConsentAgreementService.GetAgreedConsents` method returns a collection of `Consent` objects. Instances of the `Consent` class provide the `GetConsentText` method, which automatically retrieves either the current texts of the given consent or the texts of the archived consent version for which the agreement was given.
When displaying accepted consents to visitors, always work with `Consent` objects to ensure that the correct consent text version is used (for scenarios where the consent text was changed after agreements had been given).
For more information, see the _View archived consents_ section of the [Consent management](/documentation/developers-and-admins/data-protection/consent-management) page.
**Tip** : You can directly use an `IEnumerable<Consent>` collection as a model if you do not need to work with any other data in your controller or views. However, having a dedicated model class allows you to extend the model based on future requirements.
  * Add a model class that represents the full details of a consent, including properties for the short text, full text, and any other information about the consent you may need. 
C#
**Consent properties model**
Copy
```
public class ConsentDetailsModel
{
    public string ConsentShortText { get; set; }

    public string ConsentFullText { get; set; }

    public string ConsentDisplayName { get; set; }
}
```



### Create the controller
Create a new controller class or edit an existing one:
  * Initialize instances of the `IConsentAgreementService` and `IInfoProvider<ConsentInfo>` services (and any other required services), and make them available in the controller’s code:
C#
**Services**
Copy
```
private readonly IConsentAgreementService consentAgreementService;
private readonly IInfoProvider<ConsentInfo> consentInfoProvider;
private readonly IPreferredLanguageRetriever preferredLanguageRetriever;

public PrivacyPageController(IConsentAgreementService consentAgreementService,
                             IInfoProvider<ConsentInfo> consentInfoProvider,
                             IPreferredLanguageRetriever preferredLanguageRetriever)
{
    this.consentAgreementService = consentAgreementService;
    this.consentInfoProvider = consentInfoProvider;
    this.preferredLanguageRetriever = preferredLanguageRetriever;
}
```

We recommend using [dependency injection](/documentation/developers-and-admins/development/website-development-basics/dependency-injection) to initialize service instances.
  * Implement a GET action which retrieves a list of all consents accepted by the visitor:
    1. Retrieve the current contact using the `ContactManagementContext` API (see [Contact configuration](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration)).
    2. Get the consents for which the contact has given an agreement by calling the `GetAgreedConsents` method of the `IConsentAgreementService` instance.
    3. Create an instance of the consent list model class and pass it to the view.
C#
**GET list of consents**
Copy
```
/// <summary>
/// Loads and displays consents for which visitors have given agreements.
//  / </summary>
public ActionResult Index()
{
    // Gets the current visitor's contact
    ContactInfo currentContact = ContactManagementContext.GetCurrentContact();

    var consentListingModel = new ConsentListingModel();

    // Does not attempt to load consent data if the current contact is not available
    // This occurs if contact tracking is disabled, or for visitors who have not given an agreement with the tracking consent
    if (currentContact != null)
    {
        // Gets all consents for which the current contact has given an agreement
        consentListingModel.Consents = consentAgreementService.GetAgreedConsents(currentContact);
    }

    return View(consentListingModel);
}
```

  * Implement a GET action which retrieves the full details of a specified consent:
    1. Retrieve the current contact.
    2. Get the consents for which the contact has given an agreement using the consent agreement service, and select the required consent based on the action’s parameter. This approach ensures that the correct consent text version is used in cases where the text was changed after the agreement had been given.
    3. Create an instance of the consent details model class, set its properties, and pass it to the view.
C#
**GET consent details**
Copy
```
/// <summary>
/// Display details of the specified consent.
/// </summary>
public ActionResult ConsentDetails(int consentId)
{
    // Gets a list of consents for which the current contact has given an agreement
    ContactInfo currentContact = ContactManagementContext.GetCurrentContact();
    IEnumerable<Consent> consents = consentAgreementService.GetAgreedConsents(currentContact);

    // Gets the consent matching the identifier for which the details were requested
    // Using this approach to get objects of the 'Consent' class is necessary to ensure that the correct consent text
    // is displayed, either from the current consent text or the archived consent version for which the agreement was given
    Consent consent = consents.FirstOrDefault(c => c.Id == consentId);

    // Displays the privacy page (consent list) if the specified consent identifier is not valid
    if (consent == null)
    {
        return View("Index");
    }

   // Gets the consent text in the currently selected language
    ConsentText consentText = consent.GetConsentText(preferredLanguageRetriever.Get());

    // Sets the consent's details into the view model
    var model = new ConsentDetailsModel
    {
        ConsentDisplayName = consent.DisplayName,
        ConsentShortText = consentText.ShortText,
        ConsentFullText = consentText.FullText
    };

    return View(model);
}
```

  * Implement a POST action that revokes agreements for a specified consent:
    1. Retrieve the current contact.
    2. Retrieve the required consent (by calling the `IInfoProvider<ConsentInfo>().Get` method).
    3. Create a revocation for the consent agreement using the `Revoke` method of the `IConsentAgreementService` instance.
C#
**POST action**
Copy
```
/// <summary>
/// Revokes the current contact's agreement with the specified consent.
/// </summary>
[HttpPost]
[ValidateAntiForgeryToken]
public ActionResult Revoke(int consentId)
{
    // Gets the related consent object
    ConsentInfo consent = consentInfoProvider.Get(consentId);

    // Gets the current visitor's contact
    ContactInfo currentContact = ContactManagementContext.GetCurrentContact();

    if (consent != null && currentContact != null)
    {
        // Revokes the consent agreement
        consentAgreementService.Revoke(currentContact, consent);
    }

    return RedirectToAction("Index");
}
```



### Add a RevokeConsentAgreement handler
Websites and their related functionality often contain multiple locations where visitors can revoke consents. To ensure that revocations are handled correctly in all cases, we recommend assigning a custom handler to the system’s `DataProtectionEvents.RevokeConsentAgreement` [global event](/documentation/developers-and-admins/customization/handle-global-events). The handler provides a central place where you can perform additional actions whenever a consent agreement is revoked.
The following are typical scenarios to handle in the `RevokeConsentAgreement`event:
  * **Setting the cookie level** – when revoking the [tracking consent](#set-up-tracking-consent), you also need to lower the visitor’s [cookie level](/documentation/developers-and-admins/data-protection/cookies) to ensure that contact tracking is disabled. Call the `SetCurrentCookieLevel` method of the `ICurrentCookieLevelProvider` service and set the default cookie level from the website channel’s settings.
  * **Clearing personal data** – when a visitor revokes a consent agreement, you may also need to delete or anonymize certain types of personal data stored by the system. See also: [Personal data erasure](/documentation/developers-and-admins/data-protection/personal-data-erasure)


C#
**Example**
Copy
```
using System;

using CMS;
using CMS.Core;
using CMS.DataEngine;
using CMS.DataProtection;
using CMS.Helpers;

// Registers the custom module into the system
[assembly: RegisterModule(typeof(CustomDataProtectionModule))]

internal class CustomDataProtectionModule : Module
{
    private ICurrentCookieLevelProvider currentCookieLevelProvider;

    // Module class constructor, the system registers the module under the name "CustomDataProtection"
    public CustomDataProtectionModule()
        : base("CustomDataProtection")
    {
    }

    // Contains initialization code that is executed when the application starts
    protected override void OnInit(ModuleInitParameters parameters)
    {
        // Resolves service dependencies from the IoC container
        currentCookieLevelProvider = parameters.Services.GetRequiredService<ICurrentCookieLevelProvider>();

        // Assigns a handler to the RevokeConsentAgreement event
        DataProtectionEvents.RevokeConsentAgreement.Execute += RevokeConsentHandler;
    }

    private void RevokeConsentHandler(object? sender, RevokeConsentAgreementEventArgs e)
    {
        // For the tracking consent, lowers the cookie level to the website channel's default in order to disable tracking
        if (e.Consent.ConsentName.Equals("SampleTrackingConsent", StringComparison.OrdinalIgnoreCase))
        {
            currentCookieLevelProvider.SetCurrentCookieLevel(currentCookieLevelProvider.GetDefaultCookieLevel());
        }
    }
}
```

### Create the views
Add appropriate views to your project to display the consent list (privacy page), the consent details and revocation actions.
Consent text values may contain HTML tags added via the editor in the Xperience administration (e.g., for formatting). To ensure that the text is displayed correctly, disable the HTML encoding for the values using the `Html.Raw` method.
cshtml
**Example**
Copy
```
<h3>Short text</h3>
<p>@Html.Raw(Model.ConsentShortText)</p>
<h3>Full text</h3>
<p>@Html.Raw(Model.ConsentFullText)</p>
```

Visitors on your site are now able to view a list of all consents for which they have given an agreement. They can also access the full details of the given consents and revoke their agreements if required.
![]()
[]()[]()
