---
source: https://docs.kentico.com/documentation/developers-and-admins/api/use-the-xperience-by-kentico-api-externally
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [API](/documentation/developers-and-admins/api)
  * Use the Xperience by Kentico API externally 


# Use the Xperience by Kentico API externally
This page describes how to configure external .NET applications to work with the Xperience API. Using Xperience functionality externally is suitable for scenarios such as:
  * Separate applications integrated with your main Xperience website (e.g., for the purposes of data synchronization with third party applications)
  * Custom websites or applications that use Xperience as a content repository


**Limitations of** **SaaS deployments**
You cannot connect to Xperience applications [deployed to the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) from external .NET applications.
To enable Xperience by Kentico API in your external .NET application:
  1. [Connect to the Xperience database](#connect-to-the-xperience-database)
  2. [Add the Xperience API NuGet packages](#add-the-xperience-api-nuget-packages)
  3. [Initialize the Xperience application](#initialize-the-xperience-application)


## Connect to the Xperience database
To be able to access the Xperience database, you need to specify the appropriate connection string in your application’s configuration file.
If you are storing application configuration in files on the file system, make sure their **Copy to output directory** is set to **Always** in the _.csproj_.
XML
**Example**
Copy
```
<ItemGroup>
    <None Update="appsettings.json">
      <CopyToOutputDirectory>Always</CopyToOutputDirectory>
    </None>
</ItemGroup>
```

Add the connection string into the _ConnectionStrings_ object under the **CMSConnectionString** key:
JSON
Copy
```
"ConnectionStrings": {
    "CMSConnectionString": "Persist Security Info=False;database=Xperience;server=myserver;user id=username;password=mypassword;Current Language=English;Connection Timeout=120;"
 }
```

**Tip** : We recommend copying the exact connection string from the configuration file of your Xperience application.
## Add the Xperience API NuGet packages
Before you can use Xperience functionality in your application, you need to add the Xperience API NuGet packages:
  1. Right-click your application’s project in the Visual Studio **Solution Explorer** and select **Manage NuGet Packages**.
  2. Search for the **Kentico.Xperience.Core** package.
  3. Install the _Kentico.Xperience.Core_ version that matches the version of the connected Xperience database.


**Maintaining Kentico.Xperience.Core package version**
If you [update](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) the related Xperience project and its database to a newer version, you also need to update the NuGet packages to a matching version in your external application.
## Initialize the Xperience application
You need to initialize the system before making calls to its API from an external project.
### Web applications
Call the following code on application startup. The initialization sequence is performed only once and skipped during subsequent calls.
C#
**Program.cs - web app**
Copy
```
using CMS.Core;
using CMS.DataEngine;
using Microsoft.AspNetCore.Builder;

// ...

// Ensures a preconfigured environment (DI, configuration providers) for .NET web apps
var builder = WebApplication.CreateBuilder(args);

// Preinitializes Xperience by Kentico without building a custom DI container
CMSApplication.PreInit(false);
// Merges Xperience services with the application's service collection
Service.MergeDescriptors(builder.Services);

WebApplication app = builder.Build();

// Tells Xperience to use this app's service container for service resolution
Service.SetProvider(app.Services);

// Initializes Xperience APIs and database for use in the app
CMSApplication.Init();
```

### Non-web applications (Console applications, etc.)
  1. Edit your application’s `Main` method.
  2. Call the following code on application startup:
C#
**Program.cs - console app**
Copy
```
using CMS.Core;
using CMS.DataEngine;
using Microsoft.Extensions.Hosting;

// ...

// Ensures a preconfigured environment (DI, configuration providers) for .NET console apps
var builder = Host.CreateApplicationBuilder(args);

// Preinitializes Xperience by Kentico without building a custom DI container
CMSApplication.PreInit(false);
// Merges Xperience services with the application's service collection
Service.MergeDescriptors(builder.Services);

var app = builder.Build();

// Tells Xperience to use this app's service container for service resolution
Service.SetProvider(app.Services);

// Initializes Xperience APIs and database for use in the app
CMSApplication.Init();
```



You can now use the Xperience API in your external application. The API allows you to perform any action available in the standard administration interface.
**Custom code that requires discovery**
For external projects that compile into a different output type than a DLL assembly (console applications, etc.), do **NOT** directly add any custom classes that need to be detected during the API initialization. This includes classes registered using attributes, such as [custom module classes](/documentation/developers-and-admins/customization/run-code-on-application-startup), custom implementations of [services](/documentation/developers-and-admins/customization/decorate-system-services) or [providers](/documentation/developers-and-admins/customization/customize-system-providers), etc. The system is only able to discover such code within assemblies.
Instead, add the code into a _Class Library_ project with the `AssemblyDiscoverable` attribute and reference the assembly from your project. See [Integrate custom code](/documentation/developers-and-admins/customization/integrate-custom-code).
## Additional configuration and tips
### Share project files with the Xperience application
If you need to call API that works with a specific folder in the Xperience project, [map the physical path](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers) used by the Xperience API in your external application to a specific Xperience project.
  1. Open the external application in Visual Studio.
  2. Create a [custom module class](/documentation/developers-and-admins/customization/run-code-on-application-startup). The module’s initialization code runs when you [initialize the Xperience application](#initialize-the-xperience-application).
  3. Override the module’s `OnInit` method and perform the required `StorageProvider` modifications: 
    1. Create a new instance of the `StorageProvider` class (the `isSharedStorage` parameter of the `CreateFileSystemStorageProvider` method must be `true`).
    2. Set the target directory via the provider’s `CustomRootPath` property. The provider creates new files and folders relative to the given root.
    3. Map the folder used by your external application. For example, if an external application imports [content item assets](/documentation/business-users/content-hub/content-item-assets), map the **~/assets** folder.


C#
**Example**
Copy
```
public class CustomFileSystemMappingModule : Module
{
    // Module class constructor, the system registers the module under the name "CustomFSMapping"
    public CustomFileSystemMappingModule ()
        : base("CustomFSMapping")
    {
    }

    // Contains initialization code that is executed when the application starts
    protected override void OnInit()
    {
        base.OnInit();

        // Creates a new StorageProvider instance for regular file systems (NTFS, VFAT)
        var sharedAssetsProvider = StorageProvider.CreateFileSystemStorageProvider(isSharedStorage: true);

        // Specifies the root directory of the target Xperience project.
        // The provider creates the relative path of the mapped folders within the given directory.
        sharedMediaProvider.CustomRootPath = @"C:\inetpub\wwwroot\Xperience\";

        // Maps the ~/assets directory from your external application's folder to the provider
        StorageHelper.MapStoragePath("~/assets/", sharedMediaProvider);
    }
}
```

### Work with the user context
When working with the Xperience API in an external application, the default Xperience user context is not available.
To use API that relies on the context of a specific user, enclose the code into a `using` statement with a declared `CMSActionContext` instance and the appropriate `UserInfo` object as a parameter.
C#
**Example**
Copy
```
using Microsoft.Extensions.Logging;

using CMS.Base;
using CMS.Core;
using CMS.DataEngine;
using CMS.Membership;

// ...

// Instances of  services obtained using dependency injection
private readonly ILogger<ExternalAppClass> logger;
private readonly IUserInfoProvider userInfoProvider;

// ...

// Gets an object representing a specific Xperience user
UserInfo user = userInfoProvider.Get("Andy");

// Sets the context of the user
using (new CMSActionContext(user))
{
    // Logs a warning event into the Xperience event log, with "Andy" as the user who caused the event
    logger.LogWarning(new EventId(0, "Event_Code"), "Detailed event description.");
}
```

![]()
[]()[]()
