---
source: https://docs.kentico.com/documentation/developers-and-admins/development/website-development-basics/configure-new-projects
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Website development basics](/documentation/developers-and-admins/development/website-development-basics)
  * Configure new projects 


# Configure new projects
This page covers the configuration of newly installed Xperience projects. The process can be divided into the following main steps:
  1. [Configure the project’s startup](#configure-application-startup).
  2. (Optional) [Enable email-sending functionality](/documentation/developers-and-admins/configuration/email-configuration).
  3. (Optional) [Enable additional Xperience features](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/enable-application-features) according to your requirements.
  4. (Optional) [Miscellaneous application configuration](#application-configuration).


## Configure application startup
The Xperience ASP.NET Core integration requires specific service and middleware components to function. You need to configure your application’s startup with the following requirements in mind.
Add the following services to the application’s service collection:
  * (_Optional_) Xperience SaaS services – added to the service collection for projects deployed to the Xperience [SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment).
    * `AddKenticoCloud` – configures basic SaaS infrastructure.
    * `AddXperienceCloudApplicationInsights` – configures the [Microsoft Application Insights integration](/documentation/developers-and-admins/configuration/saas-configuration#microsoft-application-insights-integration).
    * `AddXperienceCloudSendGrid` – configures the [managed SendGrid integration](/documentation/developers-and-admins/configuration/email-configuration#managed-sendgrid-integration).
    * `AddXperienceCloudDataProtection` – configures the ASP.NET [DataProtection](https://learn.microsoft.com/en-us/aspnet/core/security/data-protection/introduction?view=aspnetcore-9.0) API required for Xperience administration interface authentication.
    * `AddXperienceCloudChannelRequirements` – configures channel code name validation to enforce the same character restrictions when creating a new channel (e.g., website, email) in the administration interface as in the [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal).
  * Xperience services – added via `IServiceCollection.AddKentico`.
  * Framework support for the MVC architecture – added via `IServiceCollection.AddControllersWithViews`.
**Newtonsoft.Json response data formatters not supported**
[Response data formatters](https://learn.microsoft.com/aspnet/core/web-api/advanced/formatting) based on _Newtonsoft.Json_ are not globally compatible with Xperience applications. Installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and calling `AddNewtonsoftJson` in the application’s service configuration may cause issues with certain parts of the system’s functionality. 


The project’s [middleware pipeline](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/) must contain the following middleware **in the listed order** (see the code snippet below):
  * System initialization (`InitKentico`) – bootstraps the Xperience by Kentico integration. Must be called **first** in the middleware pipeline. 
    * `InitKentico` also adds a logging provider with the `XperienceEventLog` alias. The logging provider is built using conventional [ASP.NET logging API](https://docs.microsoft.com/aspnet/core/fundamentals/logging/) and writes log entries to the [Xperience event log](/documentation/developers-and-admins/configuration/event-log). By default, this provider includes all application warnings and errors, as well as information-level events originating from the Xperience libraries (`CMS` and `Kentico` event categories). To learn more, see [Logging](/documentation/developers-and-admins/development/logging).
  * [Static files](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/static-files/) (`UseStaticFiles`) – used by Xperience to serve files required by the [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) and [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) features, [content item assets](/documentation/business-users/content-hub/content-item-assets), etc.
  * Cookie policy (`UseCookiePolicy`) – required for the system’s [cookie support](/documentation/developers-and-admins/data-protection/cookies).
  * Authentication middleware (`UseAuthentication`) – required by certain features, such as Page Builder (when accessing the live site via the Xperience administration).
  * SaaS environment initialization (`UseKenticoCloud`) – only for projects that will be deployed to the [SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment).
  * Xperience middleware (`UseKentico`) – registers middleware and configuration required by the system. This call also adds the framework’s routing ([UseRouting](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.userouting)) middleware.


The **Boilerplate** [project template](/documentation/developers-and-admins/installation) comes with a startup class preconfigured according to the specified requirements.
The `--cloud` template also contains additional code required for [deployment to the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment).
Kentico strongly recommends creating projects from the provided templates.
The following code demonstrates required service registration and recommended middleware order. Ellipsis indicate breaks where other middleware may be added:
C#
**Program.cs - Recommended middleware order**
Copy
```
using Kentico.Web.Mvc;
using Kentico.Xperience.Cloud;

using Microsoft.Extensions.Hosting;
using Microsoft.AspNetCore.Builder;
using Microsoft.Extensions.DependencyInjection;

// Application service registrations
var builder = WebApplication.CreateBuilder(args);

// Required for projects that will be deployed to the SaaS environment
builder.Services.AddXperienceCloudApplicationInsights(builder.Configuration);

// Required for projects that will be deployed to the SaaS environment
if (builder.Environment.IsQa() ||
    builder.Environment.IsUat() ||
    builder.Environment.IsEnvironment(CloudEnvironments.Custom) ||
    builder.Environment.IsEnvironment(CloudEnvironments.Staging) ||
    builder.Environment.IsProduction())
{
    builder.Services.AddKenticoCloud(builder.Configuration);
    builder.Services.AddXperienceCloudSendGrid(builder.Configuration);
    builder.Services.AddXperienceCloudDataProtection(builder.Configuration);
}

builder.Services.AddKentico();
builder.Services.AddAuthentication();
builder.Services.AddControllersWithViews();

// Application middleware pipeline configuration
var app = builder.Build();

// These middleware components need to be called in this specific order
app.InitKentico();
app.UseStaticFiles();

...
app.UseCookiePolicy();
...

app.UseAuthentication();

// Required for projects that will be deployed to the SaaS environment
app.UseKenticoCloud();

app.UseKentico();
...

// Adds system routes such as HTTP handlers and feature-specific routes
app.Kentico().MapRoutes();

// Starts the application
app.Run();
```

**IIS application initialization**
When hosting the application on IIS with the [Application initialization](https://learn.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) feature enabled and configured to send a preload request (often enabled by default, for example on [Microsoft Azure](https://azure.microsoft.com/)), you may encounter preload errors in your [event log](/documentation/developers-and-admins/configuration/event-log). To prevent these issues from occurring, configure `IISApplicationInitializationOptions` for your application and enable the `UseDefaultSystemResponseForPreload` property in the **Program.cs** file.
C#
**Program.cs**
Copy
```
using Kentico.Web.Mvc;
...

builder.Services.Configure<IISApplicationInitializationOptions>(options =>
{
    options.UseDefaultSystemResponseForPreload = true;
});
```

### Middleware required by Xperience
The Xperience integration configures its required middleware components using options classes stored in the application’s service container (following the [options pattern](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options)).
When adding custom configuration for middleware [used by the Xperience integration](#configure-application-startup), **do not** configure the middleware components directly within the corresponding _Use <Middleware>_ methods (by directly passing their options objects). If a configuration object is passed directly to middleware registration, the framework disregards all configuration stored in the service container. As Xperience relies on certain configuration options stored within the service container, overriding middleware configuration in this fashion may break Xperience functionality dependent on the corresponding middleware.
Instead, add custom middleware configuration into the application’s service container using [IServiceCollection.Configure](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configure) or [IServiceCollection.ConfigureOptions](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configureoptions) methods. This way configuration set by the Xperience integration does not get overridden.
C#
**Example**
Copy
```
// Configures options for the Static files middleware
builder.Services.Configure<StaticFileOptions>(o =>
{
    //...
});

...

app.UseStaticFiles();
```

## Application configuration
This section covers miscellaneous configuration options you may wish to implement for your application.
### Application setting keys
Xperience applications support the ASP.NET Core [configuration provider](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration) approach for [application settings (configuration keys)](/documentation/developers-and-admins/configuration/reference-configuration-keys). The system looks for application settings in the sources specified by the application’s [IConfigurationBuilder](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration#default-configuration).
Similar to any other .NET application, you can use the `Microsoft.Extensions.Configuration.IConfiguration` service to retrieve the values of individual keys. For example:
C#
Copy
```
using Microsoft.Extensions.Configuration;

// Service instance provided by dependency injection
private readonly IConfiguration configuration;

// Retrieves the value of the 'CMSCIRepositoryPath' key
var path = configuration["CMSCIRepositoryPath"];

// Retrieves the value of the 'CMSConnectionString' key nested within the ConnectionStrings object
var connectionString = configuration["ConnectionStrings:CMSConnectionString"];
```

Additionally, Xperience provides various settings that allow users in the administration to enable and configure individual features. Such settings store their values in the database. To learn how to work with Xperience settings, see [Settings](/documentation/developers-and-admins/configuration/settings).
### Application settings for different SaaS deployment environments
You can use dedicated _appsettings.{Environment}.json_ files to configure different application configuration values for each environment. Apart from default [ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/environments?view=aspnetcore-9.0) environments, QA, UAT and STG environments are available for [SaaS](/documentation/developers-and-admins/saas) deployments according to your [service plan](/documentation/developers-and-admins/saas/saas-service-plans). Corresponding names for application settings files are _appsettings.Qa.json_ , _appsettings.Uat.json_ , _appsettings.Stg.json_.
Once you create these files locally, they are included in the [deployment package](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#create-a-deployment-package) when it’s generated and later uploaded to the [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal).
### Startup options in configuration files
You can use ASP.NET Core [configuration providers](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/) to separate the options from the application’s startup code to a configuration source, such as the default _appsettings.json_ file. This allows you to make runtime changes to options, without needing to adjust the startup code or restart the application (depending on the specifics of your selected configuration source).
C#
**Example**
Copy
```
// Separates the SmtpOptions to a configuration file section
builder.Services.AddXperienceSmtp();
builder.Services.Configure<SmtpOptions>(builder.Configuration.GetSection("SmtpOptions"));
```

JSON
**Example - appsettings.json**
Copy
```
{
  "SmtpOptions": {
    "Server" : {
      "Host":"smtp.server.com",
      "Port": 587
    }
  }
  ...
}
```

![]()
[]()[]()
