---
source: https://docs.kentico.com/documentation/developers-and-admins/data-protection/cookies
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Data protection](/documentation/developers-and-admins/data-protection)
  * Cookies 


# Cookies
A cookie is a small piece of data that a website asks your browser to store on your computer or mobile device. The cookie allows the website to “remember” your past actions or preferences.
Xperience provides an extensible framework, built around [cookie levels](#cookie-levels), for manipulating cookies – from assigning specific levels of allowed cookies to users and contacts, to [registering custom cookies](#register-custom-cookies) and their corresponding cookie levels.
## Set the default cookie level
A website channel’s default cookie level determines which cookies the system stores in the browsers of visitors by default. To set the default cookie level:
  1. Open **Channel management** application in Xperience.
  2. Select a website channel.
  3. Open the **Channel settings** tab.
  4. In the **Cookies** section, choose a **Default cookie level**. 
     * For an overview of individual cookie levels, see [Cookie levels](#cookie-levels).
  5. Select **Save**.


The system now uses the selected cookie level when evaluating which cookies should be stored.
Developers can add functionality to your website to allow visitors to give consent with tracking and increase their cookie level above the default. See [Enable users to select a preferred cookie level](#enable-users-to-select-a-preferred-cookie-level).
**_All cookies_ level required for Digital marketing features to work correctly in Xperience**
Xperience [Digital marketing features](/documentation/business-users/digital-marketing) only work fully for visitors who have their cookie level set to **All** cookies.
For example, the system does not track [contacts](/documentation/business-users/digital-marketing/contact-management) for visitors whose allowed cookie level is only _System_ or _Essential_. Contacts may still be created in certain cases for visitors without the required cookie level – for example, if they submit data through a form – but such contacts only store the provided data and do not track the visitor.
### Cookie levels
Xperience classifies cookies into several levels according to their purpose. These levels determine which cookies are set for administration users and website visitors. The following table lists the default levels available in the API (`Kentico.Web.Mvc.CookieLevel` class):
Cookie level |  Description  
---|---  
**None (-1000)** |  Absolutely no cookies are allowed, including the cookie that stores the cookie level selected for users. This makes sense only if you want to disable absolutely every cookie by default.  
**System (-100)** |  This level allows the _CookieLevel_ cookie, which stores the cookie level selected by the user. In terms of the end user and the Cookie law, this means **_“Cookies are not allowed”_**.  
**Essential (0)** |  Cookies that are required for all website functionality needed by visitors. This includes authentication, language selection, etc. From the visitor’s perspective, this means **_“Allow only cookies that I may need, but do not track me”_**.  
**Editor (100)** |  Cookies required for correct administration interface functionality. For example used to remember selected preferences, tabs, etc.  
**Visitor (200)** |  All cookies that are not assigned to an explicit level are considered to be cookies identifying the visitor, i.e. tracking cookies, which are not really needed, but are useful for the website owner if using Xperience digital marketing features. This level is used for all custom cookies by default. You can [register custom cookies](#register-custom-cookies) to adjust their assigned cookie level.  
**All (1000)** |  Allows all cookies, no matter what their level is. From the visitor’s perspective, this means **_“Allow all cookies now and in the future”_**.  
The numbers associated with each cookie level are integer constants, which allows you to further customize the granularity of available cookie levels. The levels ‘ _None_ ’ and ‘ _All_ ’ are set to an absolute value of 1000 to provide enough space for future Xperience updates or developer-specified custom levels.
Automatic [tracking of contacts](/documentation/business-users/digital-marketing/contact-management) and their [activities](/documentation/business-users/digital-marketing/contact-activities) only works for new visitors if the **Default cookie level** setting to ‘ _Visitor_ ’ or ‘ _All_ ’. For more information, see [Consent development](/documentation/developers-and-admins/data-protection/consent-development).
**Automatic cookie consent for administrators and editors**
If a user signs in to the Xperience administration, cookies are automatically enabled to provide full editing functionality. The system assumes that working in the administration interface identifies the user as staff, so there is no need for cookie consent.
## Register custom cookies
If your website uses any custom or third-party cookies, we recommend that you register them with an appropriate [cookie level](#cookie-levels). Unregistered cookies are processed with the _Visitor_ level by default.
When a visitor adjusts their allowed cookie level (for example by [accepting or revoking a consent](/documentation/developers-and-admins/data-protection/consent-development)), the system automatically clears all cookies of a level higher than consented from the visitor’s browser. You may also encounter problems with custom or third-party cookies not being stored if your website channel’s **Default cookie level** setting has a lower value than _Visitor_ , for example when managing tracking consent.
Custom cookies are registered via the `CookieLevelOptions` object on application startup. The options object exposes a `CookieConfigurations` dictionary where you can add your cookies. Use the following parameters:
  * `key` – the name of the cookie that you are registering.
  * `value` – an integer value representing the [cookie level](#cookie-levels) required to use the cookie. You can access the default values in the `Kentico.Web.Mvc.CookieLevel` enumeration.


### Example
The following example demonstrates how to register a custom cookie named **CustomCookie** with the **Essential** cookie level:
C#
**Program.cs - Register custom cookies**
Copy
```
using Kentico.Web.Mvc;

var builder = WebApplication.CreateBuilder(args);

// ...

// Registers 'CustomCookie' with the 'Essential' cookie level in Xperience
builder.Services.Configure<CookieLevelOptions>(options =>
{
    options.CookieConfigurations.Add("CustomCookie", CookieLevel.Essential);
});
```

The _CustomCookie_ is now registered and can be used by visitors with the  _Essential_ and above cookie level (based on your website channel’s [default cookie level](#set-the-default-cookie-level), the users’ [preferred cookie level](#enable-users-to-select-a-preferred-cookie-level), or other custom actions such as [consent agreements](/documentation/developers-and-admins/data-protection/consent-development)). 
**Handling cookies in ASP.NET Core**
When setting cookies in ASP.NET Core projects, there is an option to use the [CookieOptions.IsEssential](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.http.cookieoptions.isessential) property.
C#
**Designating cookies as essential**
Copy
```
var cookieOptions = new CookieOptions
{
    IsEssential = true
};

// Adds the cookie to the response cookie collection
HttpContext.Response.Cookies.Append("cookieName", "cookieValue", cookieOptions);
```

Cookies that are not registered via `CookieLevelOptions` are seen as _Visitor_ level cookies by Xperience, but the `CookieOptions.IsEssential` option overrides the system cookie settings as it is handled directly by the framework. As a result, such cookies are always set for visitors.
## Set and work with cookies
You can use the Xperience API to create and set custom cookies into a user’s browser. Use `ICookieAccessor.Set` within the scope of an HTTP request:
C#
**Set cookies using ICookieAccessor**
Copy
```
using Microsoft.AspNetCore.Http;

using Kentico.Web.Mvc;
...

// Contains an instance of 'ICookieAccessor' e.g., obtained using dependency injection
private readonly ICookieAccessor cookieAccessor;

cookieAccessor.Set("MyCookie", "SomeValue");
```

Cookies set without specifying any [Microsoft.AspNetCore.Http.CookieOptions](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.http.cookieoptions) use the **Lax** [SameSite mode](https://web.dev/samesite-cookies-explained/) by default. To set a different _SameSite_ mode, configure the desired behavior via the options object. The `Microsoft.ApeNetCore.Http.SameSiteMode` enumeration stores all available values:
C#
**Set cookie SameSite**
Copy
```
using Microsoft.AspNetCore.Http;

...

cookieAccessor.Set("CustomCookie", "CustomValue", new CookieOptions() { SameSite = SameSiteMode.Strict });

// Adds the Secure attribute to the cookie with 'SameSite=None'.
// The user agent will include the cookie in an HTTP request only if the request is transmitted over a secure channel (typically HTTPS).
cookieAccessor.Set("CustomCookie", "CustomValue", new CookieOptions() {
                                                        SameSite = SameSiteMode.None,
                                                        Secure = true });
```

To get and remove cookies, use `ICookieAccessor.Get` and `ICookieAccessor.Remove` respectively.
C#
**Get and remove cookies**
Copy
```
// Gets the cookie called 'MyCookie' from the current request
cookieAccessor.Get("MyCookie");

// Removes the cookie called 'MyCookie' from the current request
cookieAccessor.Remove("MyCookie");
```

## Enable users to select a preferred cookie level
In compliance with the cookie law, you can allow users to select their preferred cookie level. Use the `SetCurrentCookieLevel` method provided by the `ICurrentCookieLevelProvider` service.
The following code snippets demonstrate the usage of the `ICurrentCookieLevelProvider` API on a basic example. The code allows users to set their preferred cookie level via a simple form.
C#
**CookieLevelController.cs**
Copy
```
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Rendering;
using System.Collections.Generic;

using CMS.Helpers;

public class CookieLevelController : Controller
{
    private readonly ICurrentCookieLevelProvider cookieLevelService;

    // Initializes instances of required services using dependency injection
    public CookieLevelController(ICurrentCookieLevelProvider cookieLevelService)
    {
        this.cookieLevelService = cookieLevelService;
    }

    public ActionResult Index()
    {
        // Creates a list with the three default levels of cookie compliance relevant for visitors.
        // The CookieLevel class is used to directly extract integer values corresponding to each cookie level
        // so that no additional mapping is necessary.
        var cookieLevels = new List<object>() {
                new { label = "Essential", value = Kentico.Web.Mvc.CookieLevel.Essential.Level },
                new { label = "Visitor", value = Kentico.Web.Mvc.CookieLevel.Visitor.Level },
                new { label = "All", value = Kentico.Web.Mvc.CookieLevel.All.Level }
            };

        return View(new SelectList(cookieLevels, "value", "label"));
    }

    // Sets the cookie level for the current user
    // The value passed into the action from the corresponding view directly corresponds to one of the system's
    // cookie levels thanks to the CookieLevel class.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult SetCookieLevel(int selectedValue)
    {
        // Sets the cookie level for the current user to the level corresponding to the provided value
        cookieLevelService.SetCurrentCookieLevel(selectedValue);

        return RedirectToAction(nameof(Index));
    }
}
```

The corresponding view contains a drop-down selector that allows users to select the desired level of cookies.
cshtml
**Index.cshtml**
Copy
```
@model SelectList

@using (Html.BeginForm("SetCookieLevel", "CookieLevel", FormMethod.Post))
{
    @Html.AntiForgeryToken()
    @Html.DropDownListFor(x => x.SelectedValue, Model)
    <input type="submit" value="Select" />
}
```

![]()
[]()[]()
