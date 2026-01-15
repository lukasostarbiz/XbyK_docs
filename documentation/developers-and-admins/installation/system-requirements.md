# System requirements
  * [ Copy page link ](documentation/developers-and-admins/installation/system-requirements#) | [Get HelpService ID](documentation/developers-and-admins/installation/system-requirements#)
Core MVC 5


[✖](documentation/developers-and-admins/installation/system-requirements# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/installation/system-requirements#)
This page summarizes the requirements for installing, developing, and hosting Xperience by Kentico projects.
## Software requirements
The machine where you want to run Xperience must have the following components installed:
  * [.NET 8](https://dotnet.microsoft.com/en-us/download/dotnet/8.0) through [.NET 10](https://dotnet.microsoft.com/en-us/download/dotnet/10.0).
  * Microsoft SQL Server 2019 or newer (including the free [SQL Server Express Edition](https://www.microsoft.com/en-us/sql-server/sql-server-editions-express) and corresponding LocalDB editions). The **[collation](https://docs.microsoft.com/en-us/sql/relational-databases/collations/collation-and-unicode-support)** set for the SQL server **_must be case-insensitive_**.
  * Tools that support ASP.NET Core development, such as [Visual Studio 2022](https://visualstudio.microsoft.com/vs/), or [Visual Studio Code](https://code.visualstudio.com/) for use during development.


You can run Xperience on operating systems that support .NET 8 through .NET 10, including Windows, macOS, and various Linux distributions. For specific supported OS versions, refer to:
  * [Supported operating systems for .NET 8](https://github.com/dotnet/core/blob/main/release-notes/8.0/supported-os.md)
  * [Supported operating systems for .NET 9](https://github.com/dotnet/core/blob/main/release-notes/9.0/supported-os.md)
  * [Supported operating systems for .NET 10](https://github.com/dotnet/core/blob/main/release-notes/10.0/supported-os.md)


### Task-specific requirements
To run Xperience, only the [required components](documentation/developers-and-admins/installation/system-requirements#software-requirements) are needed. However, some tasks may depend on additional components:
  * [Node.js 22](https://nodejs.org/en/download) or newer. Required for running the [Management API MCP server](documentation/developers-and-admins/development/content-types/management-api#management-api-mcp-server) and building [custom admin UI components](documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development).
  * [React v18.3.1](https://react.dev/). Required for building [custom admin UI components](documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development).


## Hosting requirements
### SaaS hosting
We recommend hosting your projects in [Kentico’s SaaS](documentation/developers-and-admins/saas/saas-overview), allowing you to offload the set up and management of application hosting to Kentico.
### Private cloud hosting
If you want to host your Xperience by Kentico applications in a [private cloud](documentation/developers-and-admins/deployment/deploy-to-private-cloud) environment, for an average project, the server has minimum requirements of:
  * 4 GB memory (RAM)
  * 200 MB database size


The recommended hosting parameters depend on various aspects, such as the scale of the project, its architecture, implementation details, expected traffic, use of cache, utilization of background processes, etc. We recommend that you carry out your own performance testing and choose adequate hosting resources based on the results.
## Browser requirements
Recommended resolution and screen size |  The Xperience administration is optimized for the 1920x1080 resolution (also known as 1080p or Full HD) on full-size desktop monitors. Xperience makes use of the resolution to display graph areas and other elements correctly. Using Xperience on lower resolutions or smaller screens may result in some of the UI elements displaying incorrectly.  
---|---  
Supported browsers |  Xperience by Kentico supports the following browsers: 
  * Chrome
  * Firefox
  * Microsoft Edge
  * Safari on Mac OS

We officially support and strongly recommend using the **_latest versions_** of the browsers. Older browser versions (up to several versions back) should work correctly but may display simplified renderings of certain components. The list of supported browsers above also applies to **_visitors of websites_** running on Xperience. However, the exact browser requirements always depend on the implementation of specific websites. With appropriate styling, live websites can work well in a wide variety of browsers, including responsiveness and general support for mobile browsers.  
JavaScript |  JavaScript must be enabled and allowed in the browser for the administration to work correctly.