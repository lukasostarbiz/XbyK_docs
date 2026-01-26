---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/run-code-on-application-startup
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * Run code on application startup 


# Run code on application startup
Xperience customization scenarios sometimes require you to call specific system API during application startup to achieve the desired behavior. Some examples include: 
  * assigning handlers to [global system events](/documentation/developers-and-admins/customization/handle-global-events)
  * adding custom implementations of objects to global collections (e.g., [personal data erasers](/documentation/developers-and-admins/data-protection/personal-data-erasure) and [collectors](/documentation/developers-and-admins/data-protection/personal-data-collection))
  * registering custom JavaScript modules containing [admin UI customizations](/documentation/developers-and-admins/customization/extend-the-administration-interface)


The system provides a convenient entry point for such code in the form of **code-only modules**. Modules are [custom classes](/documentation/developers-and-admins/customization/integrate-custom-code) that provide the following methods:
  * `OnInit` – the system executes the code during the initialization (start) of the application. A typical example of `OnInit` code is assigning handler methods to system events.
  * `OnPreInit` – the system executes the code before `OnInit`. Does _not_ support any operations that require access to the database. For example, can be used to register [custom data types](/documentation/developers-and-admins/customization/field-editor/data-type-management).


Xperience collects all module classes during application startup and executes their `OnInit` and `OnPreInit` methods. The initialization order of modules is not guaranteed and cannot be controlled (modules can be initialized in a different order every time the application starts). For this reason, we don’t recommend relying on the initialization order in your custom code.
### Run custom code on application startup
To run custom code on application startup:
  1. Open your Xperience project in Visual Studio.
  2. Create a custom class that inherits from `CMS.DataEngine.Module`.
     * See [Integrate custom code](/documentation/developers-and-admins/customization/integrate-custom-code) for best practices about adding custom classes to Xperience projects.
  3. Define the constructor of the module class:
     * Inherit from the base constructor.
     * Enter the code name of the module as the base constructor parameter. The code name must be unique within the application.
  4. Register the module class using the `RegisterModule` assembly attribute.
  5. Override the `OnInit` or `OnPreInit` method (follow the guidelines of your particular customization scenario) and call the required startup code inside.
     * Override `OnInit(ModuleInitParameters)` to access the application’s service container via [IServiceProvider](https://learn.microsoft.com/en-us/dotnet/api/system.iserviceprovider), for example if you need to resolve dependencies required for the module’s code.
C#
Copy
```
protected override void OnInit(ModuleInitParameters parameters)
{
    // Resolve services via IServiceProvider. e.g., ILogger
    parameters.Services.GetRequiredService<ILogger<CustomModule>>();
    ...
}
```

     * Override `OnPreInit(ModuleInitParameters)` if you wish to access the application’s [IServiceCollection](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection) during module pre-initialization. For example, you can dynamically configure the application’s startup options.
C#
Copy
```
protected override void OnPreInit(ModulePreInitParameters parameters)
{
    // Access IServiceCollection, e.g., to configure startup options like EmailQueueOptions
    parameters.Services.Configure<EmailQueueOptions>(...);
    ...
}
```



The system executes the methods when collecting registered modules during application startup.
### Example
The following example resolves dependencies on other services from `IServiceProvider` accessed via `ModuleInitParameters`.
C#
**Service resolution using ModuleInitParameters**
Copy
```
using CMS;
using CMS.ContentEngine;
using CMS.Core;
using CMS.DataEngine;

using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

[assembly: RegisterModule(typeof(CustomModule))]

public class CustomModule : Module
{
    private ILogger<CustomModule> logger;

    // Module class constructor, inherits from the base constructor with the code name of the module as the parameter
    public CustomModule() : base(nameof(CustomModule))
    {
    }

    protected override void OnInit(ModuleInitParameters parameters)
    {
        // Access 'IServiceProvider' via ModuleInitParameters and resolve 'ILogger'
        logger = parameters.Services.GetRequiredService<ILogger<CustomModule>>();

        // Registers an event handler
        ContentItemEvents.Publish.Execute += ContentItem_Published;
    }

    private void ContentItem_Published(object sender, PublishContentItemEventArgs e)
    {
        // Logs an information event whenever a content item is published
        string message = $"Content item '{e.DisplayName}' was published.";
        logger.LogInformation(new EventId(0, "CONTENT_ITEM_PUBLISHED"), message);
    }
}
```

![]()
[]()[]()
