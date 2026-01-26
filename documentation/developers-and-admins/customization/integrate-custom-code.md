---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/integrate-custom-code
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * Integrate custom code 


# Integrate custom code
When customizing or extending Xperience, developers often need to add code files (classes). Classes are required when integrating custom services (interface implementations) or other components.
Instead of adding classes directly into the main web project, create the files as part of a separate **Class Library** project (assembly). Custom assemblies provide a cleaner separation of code and better reusability between different projects.
**Group project assemblies within a solution**
If you have not yet done so, we recommend creating a [solution file](https://docs.microsoft.com/en-us/visualstudio/extensibility/internals/solutions-overview) for your project. Solutions allow you to track multiple assemblies within a single workspace, and to easily reference, navigate, and structure your codebase.
To create an assembly for custom classes in your Xperience solution:
  1. Open your Xperience solution in Visual Studio.
  2. Create a new _Class Library_ project in the solution.
  3. Add the Xperience API libraries to the project: 
    1. Right-click the solution in the **Solution Explorer** and select **Manage NuGet Packages for Solution**.
    2. Select the desired [package](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/xperience-by-kentico-nuget-packages) (e.g. **Kentico.Xperience.Core**).
    3. Install the package into the project (the version must match your main web project’s _Kentico.Xperience.WebApp_ package).
  4. Reference the project from your main web project.


You can now add your custom classes under the created class library project.
## Enable class discovery
In many cases, the system needs to detect and process custom classes on application start. For example, this is required for all custom classes registered using an attribute, such as `RegisterImplementation`, `RegisterModule`, etc. 
To allow class discovery, you need to add the `AssemblyDiscoverable` assembly attribute to your _Class Library_ project. We recommend using the following approach:
  1. Create a dummy class within your project, for example, _AssemblyAttributes.cs_.
  2. Add the `AssemblyDiscoverable` assembly attribute:
C#
Copy
```
using CMS;

[assembly:AssemblyDiscoverable]
```



The attribute ensures that Xperience processes your assembly on application start and discovers all contained custom classes that are properly registered.
**Adding the assembly attribute to the csproj file**
Adding assembly attributes to code files has advantages, such as proper compilation, warnings about potentially obsolete API, etc. However, if you do not wish to create a dummy class for this purpose, you can alternatively edit your project’s _csproj_ file and add the assembly attribute there:
XML
Copy
```
<ItemGroup>
    <AssemblyAttribute Include="CMS.AssemblyDiscoverableAttribute">
    </AssemblyAttribute>
</ItemGroup>
```

## Store application resources in resource files
We recommended storing all custom resources required by your customizations, such as text strings, in [resource files](https://learn.microsoft.com/en-us/dotnet/core/extensions/create-resource-files) (.resx). Resource files enable a clear separation between source code and text. By centralizing text resources, .resx files ensure consistency and make it easier to maintain, modify, and find text strings used by the application.
Create and register resource files in Xperience:
  1. Create a new .NET resource file. See [Create resource files for .NET apps](https://learn.microsoft.com/en-us/dotnet/core/extensions/create-resource-files).
Each resource class must be defined in its own separate .cs file. .NET localization resource conventions do not allow combining multiple resource classes into a single file. 
  2. Register the resource file in the system using the `RegisterLocalizationResource` attribute. The registration requires the type encapsulating the resource file, the language for which to use the resource, and optionally the resource target.
`SystemContext.SYSTEM_CULTURE_NAME` contains the default language code for English and should be used when creating English resource files. For localizing the admin UI to other languages, see [Admin UI localization](/documentation/developers-and-admins/customization/admin-ui-localization).
The available resource targets are:
     * **LocalizationTarget.Server** – Localization resources intended for the server. These resources are resolved dynamically and sent as part of API responses during request lifecycle processing. Default when no target provided.
     * **LocalizationTarget.Client** – Localization resources intended for the client. These resources are bundled and sent to the browser during admin UI initialization, making them immediately available for client components.
     * **LocalizationTarget.Builder** – Localization resources intended for [builder](/documentation/developers-and-admins/development/builders) components. These resources are resolved and sent later as part of builder-specific request lifecycle operations.
C#
**Example resource file registration - ~/Resources/AdminResourcesServer.cs**
Copy
```
using CMS.Base;
using CMS.Localization;

// Resource file registration for multiple languages
[assembly: RegisterLocalizationResource(typeof(AdminResourcesServer), LocalizationTarget.Server, SystemContext.SYSTEM_CULTURE_NAME)]
[assembly: RegisterLocalizationResource(typeof(AdminResourcesServer), LocalizationTarget.Server, "es-ES")]
[assembly: RegisterLocalizationResource(typeof(AdminResourcesServer), LocalizationTarget.Server, "de-DE")]

namespace MyProject.Resources;

// Class encapsulating the resource file
// Corresponding .resx files: AdminResourcesServer.resx, AdminResourcesServer.es-ES.resx, AdminResourcesServer.de-DE.resx
public class AdminResourcesServer { }
```



### Resource key naming
Use a consistent prefix in your resource keys to prevent conflicts with system resources. For example, include the project or company name in your keys:
```
// Good examples using company/project name prefixes
mycompany.admin.users.createButton
myproject.validation.emailRequired
myapp.dashboard.welcomeMessage

// Avoid generic keys that may conflict
createButton
validation
welcomeMessage
```

### Retrieval API
Retrieve resource strings from registered resource files using `ILocalizationService`.
C#
**Example - ILocalizationService usage**
Copy
```
// Instance resolved using dependency injection
private readonly ILocalizationService localizationService;

// Resolves the string under 'myproject.validations.nocontentwarning'
localizationService.GetString("myproject.validations.nocontentwarning")
```

## Integrate third-party libraries – troubleshooting
This section provides solutions to some issues you may encounter when integrating external libraries with the system.
### Database connection issues when scheduling parallel or asynchronous work from external libraries
When code from third-party libraries is responsible for scheduling asynchronous or parallel data retrieval from the Xperience database (e.g., using `Task.Run`), you may encounter unpredictable issues related to database connection, such as the following exceptions:
  * Connection does not support `MultipleActiveResultSets`.
  * There is already an open `DataReader` associated with this `Command`.
  * `ExecuteReader` requires an open and available `Connection`. The current state of the connection is closed.
  * `ExecuteNonQuery` requires an open and available `Connection`. The current state of the connection is open.


These issues can be caused by improperly sharing per-thread contextual information, such as the database connection context, among individual worker threads that process the parallel or asynchronous requests. An example is a thread being assigned a database connection that was, in the meanwhile, closed inside a different thread.
Consider the following code where `LoadData` is supplied by your custom code, while `ExternalLibrary` is called from within third-party code and is responsible for scheduling the data load operations from the Xperience database (spawning worker threads).
C#
**Asynchronous data retrieval via external library**
Copy
```
// Assume this implementation resides in third-party code (e.g., NuGet references)
public async Task ExternalLibrary()
{
    var tasks = new List<Task>();

    for (int i = 0; i < 100; ++i)
    {
        var t = Task.Run(async () =>
        {
            await LoadData();
        });
        tasks.Add(t);
    }

    await Task.WhenAll(tasks);
}

public async Task LoadData()
{
    // Creates a new connection scope for this operation
    using (new CMSConnectionScope(true))
    using (var resources =
                await ConnectionHelper.ExecuteReaderAsync("SELECT ResourceID FROM CMS_Resource",
                                                           null,
                                                           QueryTypeEnum.SQLQuery,
                                                           CommandBehavior.Default,
                                                           CancellationToken.None))
    {
        await resources.ReadAsync();

        // Process the data...
    }
}
```

As implemented in the example, the `LoadData` method is not robust enough to ensure each spawned worker thread gets assigned a fresh database connection scope when `LoadData` is called from `ExternalLibrary` (simulating delegated execution via third-party code).
To help maintain thread context separation, the system provides the `CMS.Base.ContextUtils.ResetCurrent` method, which ensures that the current thread (and all its child threads) are assigned a fresh connection scope instance. The method must be called before instantiating any context-sensitive objects, typically at the beginning of the delegated code.
Using `ContextUtils.ResetCurrent` to fix the sample scenario above yields the following updated `LoadData` code.
C#
**ResetCurrent usage**
Copy
```
public async Task LoadData()
{
    // Clears residual context data inherited from the parent
    // which scheduled the data load for execution (a thread from 'ExternalLibrary' in this case)
    ContextUtils.ResetCurrent();

    // Creates a new connection scope for this operation
    using (new CMSConnectionScope(true))
    using (var resources =
                await ConnectionHelper.ExecuteReaderAsync("SELECT ResourceID FROM CMS_Resource",
                                                           null,
                                                           QueryTypeEnum.SQLQuery,
                                                           CommandBehavior.Default,
                                                           CancellationToken.None))
    {
        await resources.ReadAsync();

        // Process the data...
    }
}
 


```

The `ContextUtils` class also provides the `PropagateCurrent` method, which is useful if you have direct control over the scheduling code (parallel or asynchronous operations). You can use a suitable overload of the `PropagateCurrent` method to pass the context of the current thread to all worker threads spawned by your logic without mutating the current (parent) context – and encountering similar problems with thread context sharing.
C#
**PropagateCurrent example**
Copy
```
var tasks = new List<Task>();

for (int i = 0; i < 100; ++i)
{
    // When a developer has control over scheduling the work, ContextUtils.PropagateCurrent() can be used
    // to create a snapshot of current context and propagate its copy to individual work items
    var t = Task.Run(ContextUtils.PropagateCurrent(async () =>
    {
        using (new CMSConnectionScope(true))
        using (var resources =
                    await ConnectionHelper.ExecuteReaderAsync("SELECT ResourceID FROM CMS_Resource",
                                                               null,
                                                               QueryTypeEnum.SQLQuery,
                                                               CommandBehavior.Default,
                                                               CancellationToken.None))
        {
            await resources.ReadAsync();

            // Process the data...
        }
    }));

    tasks.Add(t);
}

await Task.WhenAll(tasks);
 


```

![]()
[]()[]()
