---
source: https://docs.kentico.com/guides/development/customizations-and-integrations/access-custom-channel-specific-configurations
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Customizations and integrations](/guides/development/customizations-and-integrations)
  * Access custom channel-specific configurations 


# Access custom channel-specific configurations
When you build [custom module UIs](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) with Xperience by Kentico, chances are you’ll need to access and react to the [data stored in the modules’ classes](/documentation/developers-and-admins/customization/object-types).
Let’s go over the process of accessing and utilizing the values of module classes that are associated with specific web channels with practical examples. We will explore how to access custom channel-specific settings with Microsoft’s [options pattern](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-8.0), then later without.
[Earlier in this series](/guides/development/customizations-and-integrations/add-channels-to-module), we went over the process of creating custom channel-specific settings:
Your browser does not support the video tag. 
Now, we’ll dive into the process of accessing these settings in code.
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](/documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


The examples in this guide require that you:
  * Have followed along with the samples from [the previous guide](/guides/development/customizations-and-integrations/add-channels-to-module) about creating the UI for channel-specific configurations.


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Work with the Options pattern (Serve robots.txt)
As with the global configurations described in the [earlier guide](/guides/development/customizations-and-integrations/access-custom-global-configurations#expose-your-custom-configuration), you can use channel-specific custom module configurations with the [options pattern](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-8.0).
To put this into practice, let’s create functionality to serve the **robots.txt** file specified in the _SEO settings_ of the [we created earlier](/guides/development/customizations-and-integrations/add-channels-to-module).
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
## Access data directly (Render snippets to the site)
We recommend using the options pattern to follow .NET best practices, but if it does not fit your team’s code style, it is not necessary.
Earlier in this series, we created a module that allows users to define code snippets associated with a given channel. For this guide’s sample, let’s create a view component that retrieves the code snippets for the current channel and renders them to the layout page.
### Enter the data
Once again we need to add some data, so that our code has something to work with.
In the admin UI, navigate to **Project settings → Channel settings → Training guides pages → Channel snippets** in the Xperience admin UI.
Add one of each type of snippet. If you don’t have specific tags you want to include, you can use:
  1. First snippet 
     * **Label** : Test meta
     * **Identifiers → Code name** : TestMeta
     * **Type** : Metadata
     * **Code** : 
HTML
Copy
```
<meta name="this is a test" content="12345">
```

  2. Second snippet 
     * **Label** : Test CSS
     * **Identifiers → Code name** : TestCSS
     * **Type** : CSS
     * **Code** : 
HTML
Copy
```
<style>
    .newclass{
        display:inline;
    }
</style>
```

  3. Third snippet 
     * **Label** : Test javascript
     * **Identifiers → Code name** : TestJavascript
     * **Type** : Javascript
     * **Code** :  

HTML
Copy
```
<script>
    var toInsert = document.createElement("div");
    toInsert.innerHTML = "This text was added through a javascript snippet in the <b>Project settings</b> application.";
    document.body.appendChild(toInsert);
</script>
```



The CSS and JavaScript snippets do not need to be inline code, as in this example. They can link to other resources.
### Add supporting files
Our example view component needs some additional foundational code:
First, create a _CodeSnippets_ folder beneath the _Features_ folder of the _TrainingGuides.Web_ project.
Then add a `CodeSnippetType` enumeration that corresponds to the **WebChannelSnippetType** property that we defined in the class.
C#
**CodeSnippetType.cs**
Copy
```
public enum CodeSnippetType
{
    Metadata,
    CSS,
    Javascript,
}
```

Finally, define a view model with properties for the code, type, and label of a code snippet.
C#
**CodeSnippetViewModel.cs**
Copy
```
using Microsoft.AspNetCore.Html;

namespace TrainingGuides.Web.Features.CodeSnippets;

public class CodeSnippetViewModel
{
    public HtmlString CodeSnippetHtml { get; set; } = new HtmlString(string.Empty);
    public string CodeSnippetType { get; set; } = string.Empty;
    public string CodeSnippetLabel { get; set; } = string.Empty;
}
```

### Retrieve the values
In general, retrieving channel-specific module class values without the options pattern is the same approach you used in the [options setup file](#OptionsSetupFile). You can use an `IWebsiteChannelContext` object to get the current channel, and an `IInfoProvider<TInfo>` object or [dedicated provider](/documentation/developers-and-admins/api/generate-code-files-for-system-objects#generate-code-files) to access objects of your module class.
For our example, add an asynchronous view component to the _CodeSnippets_ folder. Add parameters to `InvokeAsync` that specify which type of snippet should be retrieved, and whether the snippet should be labelled with an HTML comment.
C#
**CodeSnippetsViewComponent.cs**
Copy
```
using CMS.DataEngine;
using CMS.Websites.Routing;
using Microsoft.AspNetCore.Html;
using Microsoft.AspNetCore.Mvc;
using TrainingGuides.ProjectSettings;

namespace TrainingGuides.Web.Features.CodeSnippets;

public class CodeSnippetsViewComponent : ViewComponent
{
    private readonly IInfoProvider<WebChannelSnippetInfo> webChannelSnippetInfoProvider;
    private readonly IInfoProvider<WebChannelSettingsInfo> webChannelSettingsInfoProvider;
    private readonly IWebsiteChannelContext websiteChannelContext;

    public CodeSnippetsViewComponent(IInfoProvider<WebChannelSnippetInfo> webChannelSnippetInfoProvider, IInfoProvider<WebChannelSettingsInfo> webChannelSettingsInfoProvider, IWebsiteChannelContext websiteChannelContext)
    {
        this.webChannelSnippetInfoProvider = webChannelSnippetInfoProvider;
        this.webChannelSettingsInfoProvider = webChannelSettingsInfoProvider;
        this.websiteChannelContext = websiteChannelContext;
    }

    public async Task<IViewComponentResult> InvokeAsync(CodeSnippetType codeSnippetType, bool addLabelComments = false)
    {
        // Retrieve the current channel
        int currentChannelID = websiteChannelContext.WebsiteChannelID;

        // Use the channel to get the associated web channel settings
        var settings = webChannelSettingsInfoProvider
            .Get()
            .WhereEquals(nameof(WebChannelSettingsInfo.WebChannelSettingsChannelID), currentChannelID)
            .AsSingleColumn(nameof(WebChannelSettingsInfo.WebChannelSettingsID));

        // Aquire snippets associated with the web channel settings
        var snippets = await webChannelSnippetInfoProvider
            .Get()
            .WhereIn(nameof(WebChannelSnippetInfo.WebChannelSnippetWebChannelSettingsID), settings)
            .WhereEquals(nameof(WebChannelSnippetInfo.WebChannelSnippetType), codeSnippetType.ToString())
            .GetEnumerableTypedResultAsync();

        // Return an IEnumerable collection of CodeSnippetViewModel objects
        var model = snippets.Select(snippet => new CodeSnippetViewModel
        {
            CodeSnippetHtml = new HtmlString(snippet.WebChannelSnippetCode),
            CodeSnippetType = snippet.WebChannelSnippetType,
            // If the view component is configured to add comment labels, use the display name
            CodeSnippetLabel = addLabelComments
            ? snippet.WebChannelSnippetDisplayName
            : string.Empty
        });
        return View("~/Features/CodeSnippets/CodeSnippetsViewComponent.cshtml", model);
    }
}
```

### Utilize the data
Now that you have your custom module data, you can put it into practice. For example, use it in your business logic, or to control how your application renders parts of the display.
In the case of our example, it means creating the view for our view component, and using it to render code snippets to the page.
Add a new view, matching the path and name specified in the view component. For each code snippet in the provided model, render it to the page. 
Make sure to include a comment label if the model provides one. 
cshtml
**CodeSnippetsViewComponent.cshtml**
Copy
```
@using TrainingGuides.Web.Features.CodeSnippets
@model IEnumerable<CodeSnippetViewModel>

@foreach (var codeSnippet in Model)
{
    if (!string.IsNullOrWhiteSpace(codeSnippet.CodeSnippetLabel))
    {
        <!-- code snippet for @codeSnippet.CodeSnippetLabel -->
    }
    @codeSnippet.CodeSnippetHtml
}
```

Finally, utilize this view component in the __Layout.cshtml_ view, found in the _~/Views/Shared_ folder of the _TrainingGuides.Web_ project.
Place three instances of the view component into the view:
  * one after the `meta` tags, with `code-snippet-type` set to `@CodeSnippetType.Metadata`
  * one after the stylesheet links in the header, with `code-snippet-type` set to `@CodeSnippetType.CSS`
  * one after the `script` tags at the bottom of the body, with `code-snippet-type` set to `@CodeSnippetType.Javascript`


Try setting `add-label-comments` to `true` on one of them, so you can compare the results.
cshtml
**_Layout.cshtml**
Copy
```
...
<html>
    <head>
        ...
        <vc:code-snippets code-snippet-type="@CodeSnippetType.Metadata" add-label-comments="true"/>
        ...
        <vc:code-snippets code-snippet-type="@CodeSnippetType.CSS" />
    </head>
    <body>
        ...
        <vc:code-snippets code-snippet-type="@CodeSnippetType.Javascript" />
    </body>
</html>
```

For reference, you can find the completed layout file [in the _finished_ branch of the _Training guides_ repo](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Views/Shared/_Layout.cshtml).
### Check the result
If you’ve followed the example, you should be able to find the tags in the source view of the page, and see the message rendered by the javascript at the very bottom.
[![View component output for metadata and CSS](/docsassets/guides/access-custom-channel-specific-configurations/ViewComponentOutput1.png)](/docsassets/guides/access-custom-channel-specific-configurations/ViewComponentOutput1.png)
[![View component output for Javascript](/docsassets/guides/access-custom-channel-specific-configurations/ViewComponentOutput2.png)](/docsassets/guides/access-custom-channel-specific-configurations/ViewComponentOutput2.png)
## What’s next?
Throughout the [Custom module series](/guides/development/customizations-and-integrations#custom-modules), you’ve seen how to create custom module, including data associated with specific channels, and how to put the module data into practice.
Use this experience to expand upon the samples, or to build your your own complex module.
Check out [this guide](/guides/development/customizations-and-integrations/add-custom-contact-field) to see how to add custom pages to existing system applications.
![]()
[]()[]()
