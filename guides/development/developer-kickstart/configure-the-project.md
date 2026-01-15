# Configure the project to display content
  * [ Copy page link ](guides/development/developer-kickstart/configure-the-project#) | [Get HelpService ID](guides/development/developer-kickstart/configure-the-project#) | This page is part of a module: [ Xperience by Kentico Developer kickstart ](modules/developer-kickstart)
Core MVC 5


[✖](guides/development/developer-kickstart/configure-the-project# "Close page link panel") [Copy to clipboard](guides/development/developer-kickstart/configure-the-project#)
You have created a _page content type – Landing page_ – with a field that allows for referencing another _reusable content type – Slogan_. The next step in line with our recommended practices is to create a **Page template**.
But first, let’s get some necessary configuration tasks out of the way.
## Enable Content tree-based routing and Page Builder
Page templates require the following features to be enabled:
  * [Page Builder](documentation/developers-and-admins/development/builders/page-builder) allows editors to design web pages using drag-and-drop widgets, templates, and other configurations.
  * [Content tree-based routing](documentation/developers-and-admins/development/routing/content-tree-based-routing) creates URLs for web pages based on their position in the content tree and automatically handles requests to those URLs.


When you run your _Kickstart.Web_ project, you see a default message rendered on the screen:
The Kickstart.Web site has not been configured yet. 
Right now, this message displays for any requests to the application to prevent errors. You need to remove the line of code that renders this message; otherwise, no pages will be accessible.
Open the _Program.cs_ file in your _Kickstart.Web_ project and remove or comment out this line of code:
C#
**Program.cs**
Copy
```
app.MapGet("/", () => "The Kickstart.Web site has not been configured yet.");
```

Now you can enable the Page Builder and Content tree-based routing in your [middleware pipeline](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/middleware/?view=aspnetcore-8.0).
Un-comment the lines calling `UsePageBuilder` and `UseWebPageRouting` from the `features` collection passed to `builder.Services.AddKentico`.
C#
**Program.cs**
Copy
```
...
// Enable desired Kentico Xperience features
builder.Services.AddKentico(features =>
{
    features.UsePageBuilder();
    // features.UseActivityTracking();
    features.UseWebPageRouting();
    // features.UseEmailStatisticsLogging();
    // features.UseEmailMarketing();
});
...
```

Add `using` directives for `Kentico.PageBuilder.Web.Mvc` and `Kentico.Content.Web.Mvc.Routing` to resolve errors.
C#
**Program.cs**
Copy
```
using Kentico.Content.Web.Mvc.Routing;
using Kentico.PageBuilder.Web.Mvc;
...
```

Provide `PageBuilderOptions` with the _Landing page_ content type name to the `UsePageBuilder` method – add `using Kickstart` to resolve errors.
Use the `CONTENT_TYPE_NAME` constant from the [generated class](documentation/developers-and-admins/api/generate-code-files-for-system-objects) to access the content type identifier.
C#
**Program.cs**
Copy
```
...
using Kickstart;
...
builder.Services.AddKentico(features =>
{
    features.UsePageBuilder(new PageBuilderOptions {
        ContentTypeNames = new[] {
            LandingPage.CONTENT_TYPE_NAME,
        }
    });
    ...
});
...
```

If you run your project now, your website will display an [HTTP 404](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/404) error. That’s because you removed the temporary message, and we haven’t defined a home page yet. We will fix that soon. You should still be able to sign in to the administration UI when you navigate to _~/admin_.
Your browser does not support the video tag. 
## Create a ViewImports file
The second small step in preparation for displaying content is setting up a [ViewImports file](https://learn.microsoft.com/en-us/aspnet/core/mvc/views/layout?view=aspnetcore-8.0#importing-shared-directives).
ViewImports files allow sharing directives across all views in the application or a particular subdirectory. (Typically `@using` directives and tag helpers.)
Many templates and components will need to use the same resources. Let’s make things easier and use a global __ViewImports_ file to centrally manage imports for all future templates.
Create a new empty Razor view named __ViewImports.cshtml_ in the root of your _Kickstart.Web_ project.
Add the following directives and tag helpers:
cshtml
**_ViewImports.cshtml**
Copy
```
@using CMS.Helpers
@using Kentico.Web.Mvc
@using Kentico.PageBuilder.Web.Mvc
@using Kentico.Content.Web.Mvc.PageBuilder

@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper *, Kentico.Content.Web.Mvc
@addTagHelper *, Kentico.Web.Mvc
@addTagHelper *, Kickstart.Web
```

This will allow any future page template views you add to use common features without explicit `@using` directives.
See an example __ViewImports.cshtml_ in our [Kickstart repository](https://github.com/Kentico/xperience-by-kentico-kickstart/blob/main/src/Kickstart.Web/_ViewImports.cshtml).
Learn more about the [available tag helpers](documentation/developers-and-admins/development/reference-tag-helpers) in the Xperience by Kentico.
Now that you have added these configurations to your project let’s move on to the next step: building a page template.
## Continue learning
When you’re ready, move on to the next page: [Build a page template](guides/development/developer-kickstart/build-a-page-template)