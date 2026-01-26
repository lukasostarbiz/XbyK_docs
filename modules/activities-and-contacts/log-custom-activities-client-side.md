---
source: https://docs.kentico.com/modules/activities-and-contacts/log-custom-activities-client-side
scrape_date: 2026-01-26
---

Module: Activities and contacts
4 of 9 Pages
# Log custom activities from the client side
### Write logging javascript
The client-side activity logging example from the [documentation](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities#client-side-code) calls a function from the _onclick_ attribute of a specific button.
This example uses a similar function, but stores it in a separate file rather than inline, and dynamically registers it to any links with the  _download_ attribute. Make sure to include this attribute on any downloadable file links that you want to track.
  1. Add a new file called  _FileDownloadActivityLogger.js_ to the _~/wwwroot/assets/js_ folder of the  _TrainingGuides.Web_ project.
  2. Create a function called `handleClick` that logs a custom activity as outlined in the [documentation example](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities#client-side-code). 
    1. Assign the  _filedownload_ custom activity type.
    2. Set the value to the path of the current page.
    3. Assign a meaningful title that includes the `alt` attribute of the specific download link if it exists.
  3. In the `window.onload` event, iterate through all the links on the page, and assign the `handleClick` function to the  _click_ event of any links with the  _download_ attribute present.


JS
**FileDownloadActivityLogger.js**
Copy
```
window.onload = function () {
    const links = document.getElementsByTagName("a");

    for (let i = 0; i < links.length; i++) {
        if (links[i].hasAttribute("download")) {
            links[i].addEventListener("click", handleClick);
        }
    }
}

function handleClick() {
    kxt('customactivity', {
        type: 'filedownload',
        value: window.location.pathname,
        title: 'File downloaded - ' + this.getAttribute("alt"),
    persona: developer
        onerror: t => console.log(t)
    });
}
```

For the sake of readability, this example is not minified. In production scenarios, consider storing this file elsewhere and using an automated tool to render a minified version of this script to the _wwwroot_ folder. 
### Create a view component
Following the same process as [earlier in this series](/guides/development/activities-and-marketing/enable-activity-tracking), create a view component class to conditionally render a script reference to your new JavaScript file to the page.
  1. Add a folder called _CustomActivityScripts_ in _TrainingGuides.Web/Features/Activities/ViewComponents._
  2. Create a view component to conditionally render the custom activity script if the current contact has consented to tracking.
C#
**CustomActivityScriptsViewComponent.cs**
Copy
```
using Microsoft.AspNetCore.Mvc;
using TrainingGuides.Web.Features.Activities.ViewComponents.Shared;
using TrainingGuides.Web.Features.DataProtection.Services;

namespace TrainingGuides.Web.Features.Activities.ViewComponents.CustomActivityScripts;

public class CustomActivityScriptsViewComponent : ViewComponent
{
    private readonly ICookieConsentService cookieConsentService;

    public CustomActivityScriptsViewComponent(ICookieConsentService cookieConsentService)
    {
        this.cookieConsentService = cookieConsentService;
    }

    public IViewComponentResult Invoke()
    {
        var model = new ContactTrackingAllowedViewModel()
        {
            ContactTrackingAllowed = cookieConsentService.CurrentContactCanBeTracked()
        };

        return View("~/Features/Activities/ViewComponents/CustomActivityScripts/CustomActivityScripts.cshtml", model);
    }
}
```

  3. Add a view that renders a script tag for your _FileDownloadActivityLogger.js_ file.
cshtml
**CustomActivityScripts.cshtml**
Copy
```
@using TrainingGuides.Web.Features.Activities.ViewComponents.Shared

@model ContactTrackingAllowedViewModel

@if (Model.ContactTrackingAllowed)
{
    @*Scripts for logging custom activities*@
    <script src="~/assets/js/FileDownloadActivityLogger.js"></script>
}
```



### Add the view component to the Layout view
The script is ready to be added to the layout view. To make the script work, you need to add Xperience activity logging API to the page, along with a reference to the javascript file from the previous section.
  1. Navigate to _~Views/Shared/_Layout.cshtml_ in the _TrainingGuides.Web_ project, and add a using directive for your new view component.
cshtml
**_Layout.cshtml**
Copy
```
...
@using TrainingGuides.Web.Features.Activities.ViewComponents.CustomActivityScripts
...
```

  2. At the bottom of the body tag, use the tag helper to invoke the `custom-activity-scripts` view component.
cshtml
**_Layout.cshtml**
Copy
```
...
    <vc:custom-activity-scripts/>
</body>
...
```



If you only expect to have download links in certain parts of your site, you can improve performance by using [Razor sections](https://learn.microsoft.com/en-us/aspnet/core/mvc/views/layout#sections) to only include these scripts for views which you know will show downloads.
### Test the code
The coding is done, so the functionality is ready to be tested.
  1. Run the _TrainingGuides.Web_ project on your dev machine and visit the site in your browser.
  2. Check your browser cookies in your browser’s developer tools, and ensure that the _CMSCookieLevel_ cookie is set to 1000 or above.
If you’ve completed the consent-related training guides, go to the _/cookie-policy_ page and set your preferred cookie level to  _Marketing_.
  3. Visit the  _/policy-downloads_ page and click to download the privacy policy PDF.
  4. Visit the  _/admin_ path to sign in to the administration interface.
  5. Navigate to the **Activities** tab of the **Contact management** application to see that the download activity has been logged.

Your browser does not support the video tag. 
[ Previous page ](/modules/activities-and-contacts/define-custom-activities)
4 of 9
[ Mark complete and continue ](/modules/activities-and-contacts/log-custom-activities-server-side)
![]()
[]()[]()
