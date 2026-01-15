# Set up local hosting
  * [ Copy page link ](documentation/developers-and-admins/development/website-development-basics/set-up-local-hosting#) | [Get HelpService ID](documentation/developers-and-admins/development/website-development-basics/set-up-local-hosting#)
Core MVC 5


[✖](documentation/developers-and-admins/development/website-development-basics/set-up-local-hosting# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/website-development-basics/set-up-local-hosting#)
When developing websites, you often need to quickly build and test code changes made to the project. For this reason, it’s useful to have the site hosted locally on your development machine. ASP.NET Core is very flexible in this aspect as it offers multiple hosting options for its supported platforms.
See the following sections for a breakdown of hosting options available on each platform supported by Xperience:
  * [Windows platforms](documentation/developers-and-admins/development/website-development-basics/set-up-local-hosting#windows-platforms)
  * [Linux distributions](documentation/developers-and-admins/development/website-development-basics/set-up-local-hosting#linux-distributions)


## Windows platforms
### Kestrel
The [Kestrel](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel) web server is the **recommended** hosting option. It is cross-platform, usually offers better performance, and will have the strongest .NET support in the future.
You can launch all projects locally via the `dotnet run` command: 
CMD
Copy
```
cd <project_directory>
dotnet run
```

This launches the application on _localhost_ using an empty port randomly assigned during the [installation process](documentation/developers-and-admins/installation). You can change the assigned defaults via the project’s [launchSettings.json file](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/environments#development-and-launchsettingsjson).
### IIS Express
[IIS Express](https://docs.microsoft.com/en-us/iis/extensions/introduction-to-iis-express/iis-express-overview) is a lightweight, self-contained version of IIS optimized for developers. IIS Express makes it easy to use the most current version of IIS to develop and test websites. It has all the core capabilities of IIS 7 and above as well as additional features designed to ease website development including:
  * It doesn’t run as a service or require administrator user rights to perform most tasks.
  * IIS Express works well with ASP.NET Core and PHP applications.
  * Multiple users of IIS Express can work independently on the same computer.


To host the installed project using IIS Express:
  1. Open the installed project in Visual Studio.
  2. Right-click the project file and select **Properties**.
  3. Under Debug: 
     * Set **Launch** to **IIS Express**.  
[![Configure IIS hosting for the application](docsassets/documentation/set-up-local-hosting/CoreProjectHostingConfiguration.png)](https://docs.kentico.com/docsassets/documentation/set-up-local-hosting/CoreProjectHostingConfiguration.png)
  4. Save the changes and run the project using the profile you configured (**IIS Express** in this example).


Alternatively, you can use the project’s [launchSettings.json](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/environments#development-and-launchsettingsjson) file to configure launch profiles.
You can now launch the project using IIS Express.
### IIS
ASP.NET Core also supports hosting using [Internet Information Services](https://www.iis.net/) (IIS). You can host applications either _in-process_ (through the **wpw3.exe** worker) or _out-of-process_ (using reverse proxy with Kestrel).
See the official Microsoft documentation [Host ASP.NET Core on Windows with IIS](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/iis/) for the required configuration and set up. Make sure that you have [ASP.NET Core 6.0 Runtime - Windows Hosting Bundle](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer) installed.
## Linux distributions
### Kestrel
The [Kestrel](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel) web server is supported by default by all ASP.NET Core web applications.
You can launch all projects locally via the `dotnet run` command: 
Bash
Copy
```
$ cd <project_directory>
$ dotnet run
```

Which launches the application on _localhost_ using an empty port randomly assigned during the [installation process](documentation/developers-and-admins/installation).
### Ngnix and Apache
ASP.NET Core also supports hosting using [Apache](https://httpd.apache.org/) and [Nginx](https://www.nginx.com/).
See the official Microsoft documentation for
  * [Apache](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/linux-apache)
  * [Nginx](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/linux-nginx)


to learn how to configure hosting using these services.
## Hosting configuration notes
### Enable static web assets in non-Development local environments
The Xperience application must have static web assets enabled to correctly load script files and other resources. When running Xperience locally (e.g., using the built-in Visual Studio IIS Express server), static web assets are enabled by default in the [Development](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.environments.development) environment. However, if you are using a different environment, e.g., by setting ASPNETCORE_ENVIRONMENT to a different value in the _Properties/launchSettings.json_ file, you need to enable static web assets – call [UseStaticWebAssets](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestaticwebassets) on [WebApplicationBuilder](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.webapplicationbuilder) in _Program.cs_.
C#
**Program.cs**
Copy
```
using Kentico.Web.Mvc;

var builder = WebApplication.CreateBuilder(args);

...

builder.Services.AddKentico(features =>
{
    ...
});

...

// Enables static web assets
builder.WebHost.UseStaticWebAssets();
```