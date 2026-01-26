---
source: https://docs.kentico.com/modules/custom-settings/access-channel-specific-settings-options-pattern
scrape_date: 2026-01-26
---

Module: Custom modules: custom settings
7 of 9 Pages
# Use the Options pattern to access channel-specific settings
When you build [custom module UIs](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) with Xperience by Kentico, chances are you’ll need to access and react to the [data stored in the modules’ classes](/documentation/developers-and-admins/customization/object-types).
Let’s go over the process of accessing and utilizing the values of module classes that are associated with specific web channels with practical examples. We will explore how to access custom channel-specific settings with Microsoft’s [options pattern](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-8.0), then later without.
[Earlier in this series](/modules/custom-settings/model-channel-specific-settings), we went over the process of creating custom channel-specific settings:
Your browser does not support the video tag. 
Now, we’ll dive into the process of accessing these settings in code.
## Work with the Options pattern (Serve robots.txt)
As with the global configurations described in the [earlier guide](/modules/custom-settings/access-custom-global-configurations#expose-your-custom-configuration), you can use channel-specific custom module configurations with the [options pattern](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-8.0).
To put this into practice, let’s create functionality to serve the **robots.txt** file specified in the _SEO settings_ of the [we created earlier](/modules/custom-settings/model-channel-specific-settings).
### Populate the data
Start by adding data for the code to retrieve.
For this guide’s example, navigate to **Project settings → Channel settings → Training guides pages → SEO settings** in the admin UI.
Save a value to the **Robots.txt** text box, for example:
Text
Copy
```
ignore: *
```

### Set up options
Moving to the code files, create an _options class_ to hold all the data your front-end needs, and a corresponding _options setup class_ to populate that data.
To retrieve the value of your setting, use an `IWebsiteChannelContext` instance to determine which channel’s module data to retrieve, and an `IInfoProvider<TInfo>` for each module class you need to access.
Then, register the setup class with the dependency injection container during startup, for example in an `IServiceCollection` extension method.
If you’re following along with the example, add a folder called _SEO_ within the _Features_ directory of the _TrainingGuides.Web_ project.
Create _options_ and _options setup_ classes for _robots.txt_. 
Make sure to evaluate the current channel to retrieve the correct settings. 
C#
**RobotsOptions.cs**
Copy
```
namespace TrainingGuides.Web.Features.SEO;

public class RobotsOptions
{
    public string RobotsText { get; set; } = string.Empty;
}
```

C#
**RobotsOptionsSetup.cs**
Copy
```
using CMS.DataEngine;
using CMS.Websites.Routing;
using Microsoft.Extensions.Options;
using TrainingGuides.ProjectSettings;

namespace TrainingGuides.Web.Features.SEO;
public class RobotsOptionsSetup : IConfigureOptions<RobotsOptions>
{
    private readonly IInfoProvider<SeoSettingsInfo> seoSettingsInfoProvider;
    private readonly IWebsiteChannelContext websiteChannelContext;
    private readonly IInfoProvider<WebChannelSettingsInfo> webChannelSettingsInfoProvider;

    public RobotsOptionsSetup(IInfoProvider<SeoSettingsInfo> seoSettingsInfoProvider, IWebsiteChannelContext websiteChannelContext, IInfoProvider<WebChannelSettingsInfo> webChannelSettingsInfoProvider)
    {
        this.seoSettingsInfoProvider = seoSettingsInfoProvider;
        this.websiteChannelContext = websiteChannelContext;
        this.webChannelSettingsInfoProvider = webChannelSettingsInfoProvider;
    }

    public void Configure(RobotsOptions options)
    {
        int currentChannelID = websiteChannelContext.WebsiteChannelID;

        var channelSettings = webChannelSettingsInfoProvider
            .Get()
            .WhereEquals(nameof(WebChannelSettingsInfo.WebChannelSettingsChannelID), currentChannelID)
            .FirstOrDefault();

        var seoSettings = seoSettingsInfoProvider.Get()
            .WhereEquals(nameof(SeoSettingsInfo.SeoSettingsWebChannelSettingID), channelSettings?.WebChannelSettingsID ?? 0)
            .FirstOrDefault();

        options.RobotsText = seoSettings?.SeoSettingsRobots ?? string.Empty;
    }
}
```

Then, in the `ServiceCollectionExtensions.AddTrainingGuidesOptions` method from the earlier guide, use the `IServiceCollection.ConfigureOptions` method to register your options.
C#
**ServiceCollectionExtensions.cs**
Copy
```
...
public static void AddTrainingGuidesOptions(this IServiceCollection services)
{
    ...
    services.ConfigureOptions<RobotsOptionsSetup>();
}
...
```

You can find the complete file [in the finished branch of the _Training guides repository_](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/ServiceCollectionExtensions.cs) for reference.
### Utilize the options in your project
Once you have set up and registered, you can utilize them anywhere in your site that allows dependency injection.
**Choose which[**Options interface**](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-8.0#options-interfaces) to use**
In cases where the values of the options do not change during the life of the application, you can use [`IOptions<TOptions>`](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.options.ioptions-1?view=net-8.0) to retrieve the value. It is a **singleton** service that evaluates the options when the application stops.
In this case, you’ll most likely want to use [`IOptionsSnapshot<TOptions>`](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.options.ioptionssnapshot-1?view=net-8.0) instead, as it is a **scoped service** that re-evaluates the options _whenever it is constructed_. This ensures that if users make changes to your module objects after the application starts, the new values will be included in the options.
For our example, let’s create a controller that returns a text response with the value of the _robots.txt_ setting from the injected options.
C#
**RobotsController.cs**
Copy
```
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Options;

namespace TrainingGuides.Web.Features.SEO;
public class RobotsController : Controller
{
    private readonly IOptionsSnapshot<RobotsOptions> robotsOptions;

    public RobotsController(IOptionsSnapshot<RobotsOptions> robotsOptions)
    {
        this.robotsOptions = robotsOptions;
    }

    [HttpGet("/robots.txt")]
    public IActionResult Index() => Content(robotsOptions.Value.RobotsText, "text/plain");
}
```

### See the result
If you’ve followed along with the example, try visiting the **/robots.txt** path of the live site.
You should see a response containing the value you saved in the SEO settings.
[![Screenshot of robots.txt page](/docsassets/guides/access-custom-channel-specific-configurations/Robots.png)](/docsassets/guides/access-custom-channel-specific-configurations/Robots.png)
Feel free to change the value of the setting, and see that it is reflected in this response.
[ Previous page ](/modules/custom-settings/build-channel-specific-settings-ui)
7 of 9
[ Mark complete and continue ](/modules/custom-settings/access-channel-specific-settings-directly)
![]()
[]()[]()
