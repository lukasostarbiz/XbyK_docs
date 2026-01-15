# Email Builder
  * [ Copy page link ](documentation/developers-and-admins/development/builders/email-builder#) | [Get HelpService ID](documentation/developers-and-admins/development/builders/email-builder#)
Core MVC 5


[✖](documentation/developers-and-admins/development/builders/email-builder# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/builders/email-builder#)
Email Builder provides a user-friendly interface where non-technical users can create and edit email content using configurable components prepared by developers.
[![Creating an email in Email Builder](docsassets/documentation/email-builder/EB_UI.png)](https://docs.kentico.com/docsassets/documentation/email-builder/EB_UI.png)
Emails created using the Email Builder consist of components, which are implemented using [ASP.NET Blazor](https://learn.microsoft.com/en-us/aspnet/core/blazor/) as [Razor components](https://learn.microsoft.com/en-us/aspnet/core/blazor/components/). The markup of Email Builder components can either be standard HTML or use the [MJML markup language](https://documentation.mjml.io/#mjml-guides) – a framework for easily creating responsive emails. For detailed information, see [Develop Email Builder components](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components).
To start using Email Builder:
  1. [Enable the Email Builder feature](documentation/developers-and-admins/development/builders/email-builder#enable-the-email-builder-feature)
  2. Develop and register [Email Builder components](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components):
     * Templates (the overall email design and fixed content)
     * Sections (layouts for placing widgets)
     * Widgets (individual pieces of email content, such as text, images, CTA links, etc.)
  3. Create [emails](documentation/business-users/digital-marketing/emails) using one of the registered Email Builder templates.


Currently, Email Builder is only supported for emails with the _Regular_ , _Automation_ or _Order status change_ [purposes](documentation/business-users/digital-marketing/emails#email-purposes).
**Email Builder Starter Kit**
The Email Builder Starter Kit provides a basic set of components, which greatly reduce the development time required to begin using Email Builder. The Starter Kit components allow users to easily create the most common types of email content. Developers can adjust or extend the components according to specific project requirements, or use them as examples when implementing custom components.
The Starter Kit is available in the [Xperience by Kentico Email Builder Starter Kit](https://github.com/Kentico/xperience-by-kentico-email-builder-starter-kit) GitHub repository. See the repository’s _README_ file for detailed information and instructions.
## Enable the Email Builder feature
To use Email Builder, you need to enable it as a feature in your Xperience project.
  1. Open your Xperience project in Visual Studio.
  2. Adjust your application’s startup class (_Program.cs_).
  3. Within `AddKentico`, call `UseEmailBuilder`.
  4. [Configure](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options) `EmailBuilderOptions` and set the `AllowedEmailContentTypeNames` property.
     * Specify [content type](documentation/developers-and-admins/development/content-types) code names for the email content types where you wish to use Email Builder.
  5. Decide whether you wish to use [MJML markup](https://documentation.mjml.io/#mjml-guides) for your Email Builder components.
     * See our [MJML markup](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#mjml-markup) documentation for detailed information.
  6. Make sure your application’s service collection and middleware pipeline reflects the sample below.
C#
**Program.cs**
Copy
```
using Kentico.Web.Mvc;
using Kentico.EmailBuilder.Web.Mvc;
using Kentico.Xperience.Mjml;

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

// ...

builder.Services.AddKentico(features =>
{
    // Enables Email Builder
    features.UseEmailBuilder();

    // ...
});

builder.Services.Configure<EmailBuilderOptions>(options =>
{
    // Allows Email Builder for the 'Acme.Email' content type
    options.AllowedEmailContentTypeNames = ["Acme.Email"];
    // Replaces the default Email Builder section (only required when using MJML markup for components)
    options.DefaultSectionIdentifier = "Acme.DefaultMjmlSection";
    options.RegisterDefaultSection = false;
});

// (Optional) Enables MJML markup processing for Email Builder components
builder.Services.AddMjmlForEmails();

// ...

// Middleware pipeline configuration
WebApplication app = builder.Build();

app.InitKentico();
app.UseStaticFiles();

// ...

app.UseKentico();

// ...

app.Kentico().MapRoutes();
 
 



 
 
 
 
 
 
 
 



 
 


```



The Email Builder feature is now enabled. You can now [develop Email Builder components](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components), assign builder-based email templates to new [emails](documentation/business-users/digital-marketing/emails) of the allowed content types, and start creating emails in the [Email Builder visual interface](documentation/business-users/digital-marketing/emails/create-emails-in-email-builder).