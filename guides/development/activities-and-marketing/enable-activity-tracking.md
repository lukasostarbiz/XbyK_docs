---
source: https://docs.kentico.com/guides/development/activities-and-marketing/enable-activity-tracking
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Activities and digital marketing](/guides/development/activities-and-marketing)
  * Enable activity tracking 


# Enable activity tracking
Businesses like to know what visitors are doing on their sites. Data about how visitors browse their site tells marketers which features are most vital, and lets them personalize content and marketing for specific contacts.
Let’s examine how to enable standard activity tracking in Xperience by Kentico and show or hide tracking scripts depending on the visitor’s cookie level.
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](/documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


The examples in this guide require that you:
  * Have followed along with the samples from the _Consents_ section of the [Data protection series](/guides/development/data-protection).


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Enable tracking
Xperience’s activity tracking features help you monitor the actions of visitors on your site.
  * Visitors correspond to `ContactInfo` objects, which represent rows in the _OM_Contact_ table of the database.
  * The actions visitors take on the site correspond to `ActivityInfo` objects, which represent rows in the _OM_Activity_ table.


Xperience tracks form submit activities automatically. If no contact exists when a visitor submits a form, the system will create a new contact associated with their submission.
**Legitimate interest**
It is important to ensure that one of the following conditions is met:
  1. Forms only collect data that legally qualifies as legitimate interest.
  2. Visitors who have not consented to the storage of their personal information are unable to submit forms. 
This series includes [an example of this](/guides/development/activities-and-marketing/hide-builder-content-from-visitors-who-have-not-consented-to-tracking). 


Other activity types, however, require you to enable tracking during the application’s initialization.
  1. _Landing page_ - logs the first page a visitor opens when viewing the website, typically the destination of a link clicked by the visitor.
  2. _Page visit_ - logs a page the visitor has viewed while traversing the website.


To track these types of activities, follow the steps outlined on the  _[Set up activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities#enable-the-activity-tracking-feature)_ documentation page.
Once you enable activity tracking, Xperience will log the _Landing page_ and _Page visit_ activities for anyone who visits pages where the tracking script is present.
## Add a view component
To avoid legal issues related to tracking consent, you must modify the layout view to render Xperience’s tracking script only when the contact has consented to tracking.
To avoid embedding business logic in the view, you can create a method that determines whether the current contact has consented to tracking, and use it in a view component that conditionally renders the script.
**Tracking consent**
This section assumes that you have completed the training guides in the [_Data protection_ section](/guides/development/data-protection). If not, make sure consents have been configured to set the _CMSCookieLevel_ to _All_ (1000) or higher only when the visitor has consented to marketing tracking.
### Create a method to check the cookie level
The view component needs to check whether or not the current contact can be tracked. However, this functionality will be used in more places than just the view component, over the course of this series, so let’s create a reusable method for it.
  1. In the _TrainingGuides.Web/Features/DataProtection/Services/CookieConsentService.cs_ class created in the [_Data protection_ training guides](/guides/development/data-protection), add a new boolean method called `CurrentContactCanBeTracked`.
If you have not completed the data protection series, you can create a new service and register it with dependency injection.
  2. Use dependency injection to resolve an `ICurrentCookieLevelProvider` object, and use it to retrieve the current cookie level.
  3. If the cookie level is greater than or equal to 1000, return `true`.
  4. Otherwise, return `false`.
C#
**CookieConsentService.cs**
Copy
```
...
/// <summary>
/// Checks if the current contact's CMSCookieLevel is All (1000) or higher
/// </summary>
/// <returns>True if CMSCookieLevel is greater than or equal to 1000, false otherwise</returns>
public bool CurrentContactCanBeTracked() =>
    cookieLevelProvider.GetCurrentCookieLevel() >= 1000;
...
```

Alternatively, if you’ve completed the data protection series, you can check the value of the more granular `trainingguides.cookieconsentlevel` cookie. You can access its name in code through the `CookieNames.COOKIE_CONSENT_LEVEL` constant and compare its value to those in the `CookieConsentLevel` enumeration. 
  5. Add a corresponding signature to the ICookieConsentService interface.
C#
**ICookieConsentService.cs**
Copy
```
public interface ICookieConsentService
{
    ...
    bool CurrentContactCanBeTracked();
    ...
}
```



### Add a view model
The view component that conditionally renders the tracking script will be simple— it only needs to know if the script tags should be written to the page’s markup or not.
In theory, a simple boolean could be used as the model for its view. However, to make the code more readable and allow greater flexibility, create a view model with a boolean property.
Xperience’s `ActivityLoggingScriptV2` extension takes optional parameters that could correspond to view model properties, if your scenario requires more flexibility.
  1. Create the following folder structure in the _TrainingGuides.Web_ project: _~/Features/Activities/ViewComponents/Shared_
  2. Add a new file called _ContactTrackingAllowedViewModel.cs_ in the _Shared_ folder.
Over the course of this series, the view model will be shared across multiple view components.
  3. Define a boolean property to indicate whether or not tracking is allowed.


C#
**ContactTrackingAllowedViewModel.cs**
Copy
```
namespace TrainingGuides.Web.Features.Activities.ViewComponents.Shared;

public class ContactTrackingAllowedViewModel
{
    public bool ContactTrackingAllowed { get; set; } = false;
}
```

#### Define the view component
Now define the view component class that will use the `CurrentContactCanBeTracked` method from earlier to determine whether the tracking scripts should be rendered.
  1. Add a new folder called  _TrackingScripts_ to the _TrainingGuides.Web/Features/Activities/ViewComponents_ directory.
  2. Create a new file called _TrackingScriptsViewComponent.cs_.
  3. Use constructor injection to attain an `ICookieConsentservice` object.
  4. In the _Invoke_ method, define a new `ContactTrackingAllowedViewModel` that uses `CurrentContactCanBeTracked` method created in the [previous step](#create-a-method-to-check-the-cookie-level) to set its `ContactTrackingAllowed` property.
  5. Return a view, providing the path to a file you will create in the next section and the view model.


C#
**TrackingScriptsViewComponent.cs**
Copy
```
using Microsoft.AspNetCore.Mvc;
using TrainingGuides.Web.Features.Activities.ViewComponents.Shared;
using TrainingGuides.Web.Features.DataProtection.Services;

namespace TrainingGuides.Web.Features.Activities.ViewComponents.TrackingScripts;

public class TrackingScriptsViewComponent : ViewComponent
{
    private readonly ICookieConsentService cookieConsentService;

    public TrackingScriptsViewComponent(ICookieConsentService cookieConsentService)
    {
        this.cookieConsentService = cookieConsentService;
    }

    public IViewComponentResult Invoke()
    {
        var model = new ContactTrackingAllowedViewModel()
        {
            ContactTrackingAllowed = cookieConsentService.CurrentContactCanBeTracked()
        };

        return View("~/Features/Activities/ViewComponents/TrackingScripts/TrackingScripts.cshtml", model);
    }
}
```

### Implement a view
With the view component’s Invoke method handling the majority of the work, the view model must simply react to the provided model.
  1. Define a file in the _TrackingScripts_ folder called _TrackingScripts.cshtml_.
  2. Set the view’s model to `ContactTrackingAllowedViewModel`.
  3. Within an if statement that check’s the model’s `ContactTrackingAllowed` property, use Xperience’s `ActivityLoggingScriptV2` extension method to render tracking scripts to the page’s HTML.


cshtml
**TrackingScripts.cshtml**
Copy
```
@using TrainingGuides.Web.Features.Activities.ViewComponents.Shared
@using Kentico.Activities.Web.Mvc

@model ContactTrackingAllowedViewModel

@if (Model.ContactTrackingAllowed)
{
    @Html.Kentico().ActivityLoggingScriptV2();
}
```

## Add the tracking script to Layout view
This registered service allows you to evaluate whether the current visitor has consented to tracking in the layout view. You can use it to render the tracking script accordingly.
  1. Ensure that the  _TrainingGuides.Web_ namespace is included among the tag helpers in your __ViewImports.cshtml_ file.
cshtml
**_ViewImports.cshtml**
Copy
```
...
@addTagHelper *, TrainingGuides.Web
...
```

  2. Open _~/Views/Shared/_Layout.cshtml_ and add a `@using` directive for `TrainingGuides.Web.Features.Activities.ViewComponents.TrackingScripts`.
cshtml
**_Layout.cshtml**
Copy
```
...
@using TrainingGuides.Web.Features.Activities.ViewComponents.TrackingScripts
...
```

  3. Include `TrackingScriptsViewComponent` at the end of the header with the tag helper `<vc:tracking-scripts/>` .
cshtml
**_Layout.cshtml**
Copy
```
...
    <vc:tracking-scripts/>
</head>
...
```



Thanks to the logic in the view component’s `Invoke` method, _Landing page_ and _Page visit_ activities will not be logged for visitors who have not consented to tracking.
## What’s next?
Xperience now tracks standard activities, and [the next guide in this series](/guides/development/activities-and-marketing/log-custom-activities) will expand this to include custom activities. It will go over creating custom activity types, and logging them with both client-side and server-side approaches.
![]()
[]()[]()
