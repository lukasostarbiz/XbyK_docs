---
source: https://docs.kentico.com/guides/development/activities-and-marketing/implement-cross-site-activities
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Activities and digital marketing](/guides/development/activities-and-marketing)
  * Implement cross-site activities 


# Implement cross-site activities
Today, digitally mature businesses often have multiple web sites contributing to their online presence. For example, they may have separate websites for different branches of the company, microsites that tie in with viral marketing campaigns, or specially designated brochure sites for upcoming events. 
Developers often craft these sites on diverse platforms, not all of which have the same kind of activity tracking functionality as Xperience by Kentico.
In these cases, Xperience’s _Cross-site tracking_ feature enables the activity of visitors on these external sites to be tracked. It appears in the **Activities** tab of the **Contact management** application, alongside contact activities logged directly on the Xperience by Kentico site.
Let’s explore how Xperience by Kentico can track contact activities on a separate, external site.
**Host your sites securely**
As a prerequisite to this example, you must find a way to securely host the external site files provided in the _\src\TrainingGuides - External Site_ folder of the [Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides).
Note that for cross-site tracking to work, both your Xperience site and the external site that you want to use cross-site tracking on must be hosted under HTTPS with trusted SSL certificates.
_Using self-signed certificates for local testing**will not work**._
The site must also have unique domain names or subdomains, with no application path or port numbers. You can find more information about the prerequisites in [the documentation](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking#prerequisites).
This example uses a static HTML page for demonstration purposes. You will likely need to do things slightly differently in the framework you are using in order to get your project to output HTML like this example.
**We discovered conflicts between cross-site tracking and the multi-tiered consent system from the[Data protection series](/guides/development/data-protection). You can find more details in [this GitHub issue](https://github.com/Kentico/xperience-by-kentico-training-guides/issues/85).**
If your project uses a similar data protection implementation and you encounter any issues after setting up cross-site tracking, you may need to modify the code from this example, as described in [the GitHub issue](https://github.com/Kentico/xperience-by-kentico-training-guides/issues/85).
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](/documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


**Activity tracking series**
This guide is part of a series on Activity tracking.
If you want to follow along, you can [start here](/guides/development/activities-and-marketing/enable-activity-tracking).
Activity tracking functionality ties in with _consents_ , which are covered in detail in the [Data protection series](/guides/development/data-protection).
The examples in this guide require that you:
  * Have followed along with the samples from the _Consents_ section of the [Data protection series](/guides/development/data-protection) and the [Previous guides in the series](/guides/development/activities-and-marketing).


**Code samples**
You can find commented-out versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository. Feel free to re-enable the commented code in _~/Program.cs_ and _~/Features/Helpers/Startup/StartupHelper.cs_ in the _TrainingGuides.Web_ project.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Enable cross-site tracking with dynamic consent
The [Data protection series](/guides/development/data-protection) details how to create a dynamic mapping between granular cookie levels and consents. If you have not gone through the data protection series, you can follow the [simpler example in the documentation](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking#enable-the-cross-site-tracking-feature) to enable cross-site tracking in the _Program.cs_ file.
If you have completed the Data protection series, follow this example to dynamically use whichever consent is mapped to the Marketing cookie level.
This example uses the _Marketing_ cookie consent for the sake of example, but you should review which consent is most applicable to cross-site tracking with your legal team and ensure that its wording accurately explains all of its uses. You may even consider making an additional mapping specifically for cross-site tracking.
  1. Create a folder in the _TrainingGuides.Web_ project with the following path: ~_/Features/Shared/Helpers/Startup_ , and define a file within called  _StartupHelper.cs_.
  2. Add a method to this class that retrieves the current `CookieLevelConsentMapping` configured for the application
Do not use dependency injection or dependency resolution, as this code will run at startup, before the `ServiceProvider` and its collection exist.
C#
**StartupHelper.cs**
Copy
```
using CMS.DataEngine;
using TrainingGuides.DataProtectionCustomizations;

namespace TrainingGuides.Web.Features.Shared.Helpers.Startup;

public class StartupHelper
{
    /// <summary>
    /// Retrieves the codename of the currently mapped Marketing consent, returning an empty string if it is not found
    /// </summary>
    /// <remarks>
    /// Used in Program.cs to prevent <see cref="DataClassNotFoundException"/> when running CI restore for the first time.
    /// </remarks>
    /// <returns></returns>
    public static async Task<string> GetMarketingConsentCodeName()
    {
        string consentCodeName;
        try
        {
            // Do not resolve IInfoProvider<CookieLevelConsentMappingInfo> or ICookieConsentService --
            // This code runs on startup before the app is built, so no ServiceProvider exists.
            var consentQuery = await Provider<CookieLevelConsentMappingInfo>.Instance.Get()
                .GetEnumerableTypedResultAsync();

            var consent = consentQuery.FirstOrDefault();

            consentCodeName = consent?.MarketingConsentCodeName?.FirstOrDefault() ?? string.Empty;
        }
        catch (DataClassNotFoundException)
        {
            consentCodeName = string.Empty;
        }

        return consentCodeName ?? string.Empty;
    }
}
```

  3. Open the Program.cs file, and go to the features delegate passed to the `AddKentico` method, adding the async keyword.
  4. Call the `UseCrossSiteTracking` extension, and use the new `StartupHelper` class to determine the `ConsentName` property of the `CrossSiteTrackingConsentOptions` object.
  5. Set the `WebSiteChannelName` to the codename of the only web channel present in the repository, and the `AgreeCookieLevel` to the `All` _CMSCookieLevel_.


C#
**Program.cs**
Copy
```
...
// Enable desired Kentico Xperience features
builder.Services.AddKentico(async features =>
{
    ...
    features.UseCrossSiteTracking(
        new CrossSiteTrackingOptions
        {
            ConsentSettings = new[] {
                new CrossSiteTrackingConsentOptions
                {
                    WebsiteChannelName = "TrainingGuidesPages",
                    ConsentName = await StartupHelper.GetMarketingConsentCodeName(),
                    AgreeCookieLevel = CookieLevel.All.Level
                }
            },
        });
    ...
});
...
```

## Register the site for cross-site tracking
Now the external site needs to be registered in the Xperience administration application.
Sign in to the Xperience by Kentico administration interface and follow the [steps outlined in the documentation](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking#manage-tracked-sites) to enable tracking for the external site.
Do not copy the tracking snippet into the site’s HTML yet. You need to modify the snippet slightly first, and we will cover the process in a later step.
## Add a controller to indicate the right consent
Looking at the [Cross-site tracking documentation](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking), you might have noticed that the [consent example](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking#implement-tracking-consent) has a hard-coded consent codename.
The consent functionality from the data protection series dynamically maps consents to different cookie tracking levels, so you must account for this, and retrieve the correct consent from the external site.
This section applies if you have followed along with the Data protection series, or have otherwise created a custom consent mapping.
Create a controller to provide the codename of the relevant consent to the external site.
  1. Add a new empty controller named  _ConsentCodenameController.cs_ to the _~/Features/DataProtection/Controllers_ folder of the _TrainingGuides.Web_ project.
  2. Create an asynchronous controller action called `MarketingConsent`.
  3. Decorate the action with an attribute to specify the _~/consent/marketing_ route for the controller, and designate it as an HTTP GET endpoint.
  4. Inject an ICookieConsentService instance, and use it to get the current cookie level mapping, returning the codename of the marketing consent as JSON.


C#
**ConsentCodenameController.cs**
Copy
```
using Microsoft.AspNetCore.Mvc;
using TrainingGuides.Web.Features.DataProtection.Services;

namespace TrainingGuides.Web.Features.DataProtection.Controllers;

public class ConsentCodenameController : Controller
{
    private readonly ICookieConsentService cookieConsentService;

    public ConsentCodenameController(ICookieConsentService cookieConsentService)
    {
        this.cookieConsentService = cookieConsentService;
    }

    //Provides the name of the consent mapped to the Marketing cookie level for cross-site tracking.
    [HttpGet("consent/marketing")]
    public async Task<IActionResult> MarketingConsent()
    {
        var cookieLevelConsentMapping = await cookieConsentService.GetCurrentMapping();
        string marketingConsent = cookieLevelConsentMapping?.MarketingConsentCodeName.FirstOrDefault() ?? string.Empty;
        return Json(marketingConsent);
    }
}
```

This controller action will allow the external site to fetch the codename of the marketing consent.
## Set a CORS policy
When the tracking snippet is added to the external site, it will add a link to a script from the Xperience site, meaning you will need to set up a [CORS (cross-origin resource sharing) policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) that allows the external site to use resources from your Xperience project.
This is enabled during application startup in .NET, so it can be enabled in the  _Program.cs_ file.
  1. Open _Program.cs_ in the  _TrainingGuides.Web_ project and add a variable to store the name of your policy.
C#
**Program.cs**
Copy
```
...
string trainingGuidesAllowSpecificOrigins = "_trainingGuidesAllowSpecificOrigins";
...
```

  2. After the builder is created, use the `AddCors` extension to add CORS to the service collection.
  3. Add a policy, assigning the name defined earlier, and use the `WithOrigins` extension to allow sharing with the domain where you hosted your external site.
  4. Use the `WithHeaders` and `AllowCredentials` extensions to allow requests with credentials and the `content-type` header.
C#
**Program.cs**
Copy
```
...
var builder = WebApplication.CreateBuilder(args);
...
builder.Services.AddCors(options =>
{
    options.AddPolicy(name: trainingGuidesAllowSpecificOrigins,
        policy =>
        {
            policy
            .WithOrigins("https://The-URL-of-your-external-site.com")
            .WithHeaders("content-type")
            .AllowAnyMethod()
            .AllowCredentials();
        });
});
...
```

  5. After the application is built, but before it is run, call the `UseCors` extension, passing the name of the policy you set up.
C#
**Program.cs**
Copy
```
...
app.UseCors(trainingGuidesAllowSpecificOrigins);
...
```



Now resource requests from the external domain to the Xperience application will not be blocked.
## Add scripts to the page
With a CORS policy in place that allows your external site to use scripts from Xperience, there are no more blocking factors, and you can add the necessary scripts to the external site.
Looking over the  _index.html_ page found in the /_src/TrainingGuides - External Site_ folder of the Training guides repository, you may notice the buttons and label match the [basic example for consent management found in the documentation](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking#consent-management-on-external-sites). Much of the JavaScript in this section will also mirror functionality from this example, with some key changes to fit our current scenario.
Add the Xperience tracking snippet
  1. Sign in to the administration interface of Xperience and open the record for your external site in the **Cross-site tracking** application.
  2. Copy the script snippet and paste it into the header of the external site.
  3. Remove the `kxt('pagevisit');` line.
This line, which logs a _Page visit_ activity, will be added in later, after consent has been confirmed. 


HTML
**Index.html**
Copy
```
...
<script type="text/javascript">
    (function (w, d, s, e, n) {
        w.XperienceTrackerName = n;
        w[n] = w[n] || function () {
            (w[n].q = w[n].q || []).push(arguments);
        };
        var scriptElement = d.createElement(s);
        var scriptSection = d.getElementsByTagName(s)[0];
        scriptElement.async = 1;
        scriptElement.src = e.replace(/\/+$/, '') + '/Kentico.Resource/CrossSiteTracking/Logger.js';
        scriptSection.parentNode.insertBefore(scriptElement, scriptSection);
        w[n]('init', { mainSiteUrl: e, document: d, window: w });
    })(window, document, 'script', 'https://The-URL-of-your-Xperience-site.com', 'kxt');
</script>
...
```

This snippet adds a script reference to a JavaScript file in your Xperience instance which contains functions related to activities and consent.
### Retrieve the consent codename from the controller
Xperience tracking scripts that rely on consent need to know the codename of the consent associated with tracking. You need to create a function that retrieves the codename from the controller action defined [earlier](#add-a-controller-to-indicate-the-right-consent). Create a javascript file with a function to retrieve this value to the existing script block.
  1. Add a new JavaScript file to the external site called _activity.js_.
  2. Create a function `getConsentCodeName`, which fetches a response from the URL of your `MarketingConsent` controller action, located at the  _/consent/marketing_ path of your Xperience site.
  3. Convert the JSON data from the response to a string in your consuming code.
  4. Remove any extraneous quotation marks, and return the value.


JS
**activity.js**
Copy
```
...
//Fetches the codename of the marketing consent
function getConsentCodeName(){
    return fetch("https://The-URL-of-your-Xperience-site.com/")
        .then(response => response.json())
        .then(consentJson => JSON.stringify(consentJson).replace(/"+/g, ''));
}
...
```

### Add handler functions for activities
The file also needs functions to log the activities that will be tracked on the page.
  1. Add a function that agrees to a given consent for the current contact.  
Use a similar approach to the [example in the documentation](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking#consent-management-on-external-sites), but accept a parameter to specify the consent codename.
JS
**activity.js**
Copy
```
...
// Click handler that creates a consent agreement for the current contact
function trackingConsentAgree(consentName) {
    kxt('consentagree', {
        codeName: consentName,
        callback: () => {
            // Enables tracking for any subsequent logging scripts
            kxt('updateconsent', {
                allow_tracking: true,
                allow_datainput: true
            });
        },
        onerror: t => console.log(t)
    });
}
...
```

  2. With the same approach, add a function that revokes a given consent for the current contact.
JS
**activity.js**
Copy
```
...
// Click handler that revokes the tracking consent agreement for the current contact
function trackingConsentRevoke(consentName) {
    kxt('consentrevoke', {
        codeName: consentName,
        callback: () => {
            // Disables tracking for any subsequent logging scripts
            kxt('updateconsent', {
                allow_tracking: false,
                allow_datainput: false
            });
        },
        onerror: t => console.log(t)
    });
}
...
```

  3. Create a function that logs a link click activity, using the alt text of the link to populate the label.  
The label should be unique in order to indicate which link on the external site was clicked in the Xperience admin interface. Keep this in mind when adding links to your external site.
JS
**activity.js**
Copy
```
...
//Click handler that logs a link click.
function logLinkClick() {
    kxt('click', {
        label: this.getAttribute("alt"),
        onerror: t => console.log(t)
    });
}
...
```

  4. Define a function that logs the file download custom activity, similar to the [custom activities example](/guides/development/activities-and-marketing/log-custom-activities#log-file-downloads) from earlier in this series.
JS
**activity.js**
Copy
```
...
//Click handler that logs a file download activity
function logDownload() {
    kxt('customactivity', {
        type: 'filedownload',
        value: this.getAttribute('alt') + ', ' + window.location.pathname,
        title: 'File download',
persona: developer
        onerror: t => console.log(t)
    });
}
...
```



### Set up the page on load
These functions allow you to set up click event handlers, and set up the page using the marketing consent.
  1. Add a new function that handles the `DOMContentLoaded` event.
  2. Disable tracking by default, then call the `getConsentCodeName` function.
  3. When the promise is fulfilled, write the text of the marketing consent to the `lblConsentText` label.
  4. If the current contact has already agreed to the marketing consent, enable tracking.
The activity will only be logged if the consent is given, thanks to the previous step. Call the `kxt` function to log a page visit.
  5. Register click handlers for the `btnConsentAgree` and `btnConsentRevoke` buttons, assigning the corresponding functions and passing the marketing consent codename.
  6. Outside of the consuming code for the consent name promise, assign the `logDownload` function to the click event of any links with the `download` attribute, and the `logLinkClick` function to any other links.


JS
**activity.js**
Copy
```
//When the document loads
document.addEventListener('DOMContentLoaded', () => {
    // Disables all tracking by default
    kxt('consentdefault', {
        allow_tracking: false,
        allow_datainput: false,
        onerror: t => console.log(t)
    });

    getConsentCodeName()
        .then(consentName => {
            // Retrieves and displays the consent text
            kxt('consentdata', {
                codeName: consentName,
                languageName: 'en',
                callback: consentData => {
                    document.getElementById('lblConsentText').innerHTML = consentData.shortText;
                },
                onerror: t => console.log(t)
            });

            // Enables tracking if the current contact has agreed with the consent
            kxt('consentcontactstatus', {
                codeName: consentName,
                callback: consentStatus => {
                    if (consentStatus.isAgreed) {
                        kxt('updateconsent', {
                            allow_tracking: true,
                            allow_datainput: true
                        });
                    }
                },
                onerror: t => console.log(t)
            });

            // Logs a page visit activity (if tracking is enabled for the current contact)
            kxt('pagevisit', {
                onerror: t => console.log(t)
            });

            //Registers click event handlers for consent functions
            const consentAgreeButton = document.getElementById("btnConsentAgree");
            consentAgreeButton.addEventListener("click", () =>
                trackingConsentAgree(consentName)
            );

            const consentRevokeButton = document.getElementById("btnConsentRevoke");
            consentRevokeButton.addEventListener("click", () =>
                trackingConsentRevoke(consentName)
            );
        });

    const links = document.getElementsByTagName("a");
    //Registers click event handlers for download and standard links
    for (let i = 0; i < links.length; i++) {
        if (links[i].hasAttribute("download")) {
            links[i].addEventListener("click", logDownload);
        }
        else{
            links[i].addEventListener("click", logLinkClick);
        }
    }
});
```

### Add the scripts to the page
The JavaScript code is fully implemented, and can be referenced from the HTML page.
  1. Return to the _index.html_ file of the external site
  2. Beneath the Xperience snippet, add a script reference to the newly created file.
HTML
**index.html**
Copy
```
<script src="./activity.js" ></script>
```



With this code in place, the external site will log page visit, file download, and link click activities on the index page, depending on the current contact’s consent. These activities can be viewed on the **Activities** tab of the **Contact management** application in the Xperience admin interface.
[![Screenshot of the activities tab of the Contact management application](/docsassets/guides/implement-cross-site-activities/image-2023-12-8_9-49-57.png)](/docsassets/guides/implement-cross-site-activities/image-2023-12-8_9-49-57.png)
The following video demonstrates the functionality we’ve added with this example.
After the visitor gives consent on the external site, Xperience tracks their page visit, download, and link click activity. Then, after consent is revoked, these activities are no longer tracked.
Your browser does not support the video tag. 
## Looking forward
Now that the external site logs activities with your Xperience instance, you can experiment with improvements to this basic site. For example, you can log different activities, such as the _Page like_ activity from the [Custom activities example](/guides/development/activities-and-marketing/log-custom-activities#log-page-likes), and hide the corresponding buttons depending on whether the current contact has or hasn’t consented to tracking.
You can also [handle data input to map data to contacts](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking#data-input), and [handle error states](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking#handle-error-states) as shown in the documentation example.
![]()
[]()[]()
