---
source: https://docs.kentico.com/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/xperience-by-kentico-nuget-packages
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Website development basics](/documentation/developers-and-admins/development/website-development-basics)
  * [Configure new projects](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects)
  * Xperience by Kentico NuGet packages 


# Xperience by Kentico NuGet packages
Xperience by Kentico is distributed as a set of NuGet packages. See [Website development basics](/documentation/developers-and-admins/development/website-development-basics) to learn more about the system’s architecture.
The following diagram illustrates the most important NuGet packages and their roles:
[![Xperience by Kentico NuGet packages](/docsassets/documentation/xperience-by-kentico-nuget-packages/nugetpackages.png)](/docsassets/documentation/xperience-by-kentico-nuget-packages/nugetpackages.png)
Package |  Description  
---|---  
[Kentico.Xperience.Templates](https://www.nuget.org/packages/Kentico.Xperience.Templates/) |  Provides a set of .NET project templates used to bootstrap new Xperience projects.  
[Kentico.Xperience.DbManager](https://www.nuget.org/packages/Kentico.Xperience.DbManager/) |  Provides tooling to install and maintain project databases.  
[Kentico.Xperience.Admin](https://www.nuget.org/packages/Kentico.Xperience.Admin) |  Contains the administration interface used to manage Xperience applications.   
[Kentico.Xperience.WebApp](https://www.nuget.org/packages/Kentico.Xperience.WebApp) |  Required to run Xperience in ASP.NET Core applications. The package also contains Xperience features and API intended for use within web applications:
  * Preview mode support
  * Page Builder feature
  * Form Builder feature
  * Support for content retrieval (pages, reusable content, content item assets)
  * Integration with Xperience users

  
[Kentico.Xperience.Core](https://www.nuget.org/packages/Kentico.Xperience.Core) |  Contains general Xperience API usable in any application or external system.  
[Kentico.Xperience.Core.Tests](https://www.nuget.org/packages/Kentico.Xperience.Core.Tests) |  Provides a framework for creating automated tests for the Xperience API. Contains base classes for tests and additional supporting API.  
[Kentico.Xperience.ImageProcessing](https://www.nuget.org/packages/Kentico.Xperience.ImageProcessing) |  An implementation of Xperience’s [image processing functionality](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-from-media-libraries) using the [SkiaSharp graphics library](https://github.com/mono/SkiaSharp). This package is required to upload, process, and correctly display images in Xperience as [media library files](/documentation/business-users/media-libraries/manage-media-files). Do not remove the package if you use media libraries unless you provide a custom implementation of `IImageProcessingService`. The package doesn’t guarantee compatibility with niche distributions and platforms. If you run into issues with image processing on the hosting platform of your choice, you will need your own `IImageProcessingService` implementation.  
## Extension packages
In addition to the main Xperience NuGet packages, you can install optional extension packages that introduce specific functionality to Xperience and its API:
Package |  Description  
---|---  
[Kentico.Xperience.Cloud](https://www.nuget.org/packages/Kentico.Xperience.Cloud) |  Contains:
  * Logic that enables Xperience deployment to the [SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment).
  * Extension methods and environment names for deployment environment identification. See [Environment identification extension methods](/documentation/developers-and-admins/configuration/saas-configuration#environment-identification-extension-methods).
  * [Kentico.Xperience.SendGrid](https://www.nuget.org/packages/Kentico.Xperience.SendGrid/) integration for mail delivery with a managed SendGrid account. See [Enable managed SendGrid integration](/documentation/developers-and-admins/configuration/email-configuration#Emailconfiguration-EnableManagedSendGrid).
  * [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) integration. See [Microsoft Application Insights integration](/documentation/developers-and-admins/configuration/saas-configuration#DeploytotheSaaSenvironment-EnableAppInsights).

  
[Kentico.Xperience.SendGrid](https://www.nuget.org/packages/Kentico.Xperience.SendGrid/) |  Integration for sending mail from Xperience using the [Twilio SendGrid](https://sendgrid.com/) email platform. For more information, see [SendGrid integration](/documentation/developers-and-admins/configuration/email-configuration#sendgrid-integration).  
[Kentico.Xperience.AzureStorage](https://www.nuget.org/packages/Kentico.Xperience.AzureStorage) |  Contains [file system providers](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers) used to map parts of the file system to Azure Blob storage.  
[Kentico.Xperience.AmazonStorage](https://www.nuget.org/packages/Kentico.Xperience.AmazonStorage) |  Contains file system providers used to map parts of the file system to Amazon S3.  
[Kentico.Xperience.Mjml](https://www.nuget.org/packages/Kentico.Xperience.Mjml) |  Provides support for developing and using [Email Builder](/documentation/developers-and-admins/development/builders/email-builder) components with markup based on the [MJML markup language](https://documentation.mjml.io/#mjml-guides).  
[Kentico.Xperience.ManagementApi](https://www.nuget.org/packages/Kentico.Xperience.ManagementApi) |  Currently, the management API functionality is in preview mode and _Kentico.Xperience.ManagementApi_ is a **prerelease** package. Provides endpoints that allow retrieval and management of objects within Xperience. At this time, the API allows management of [content types](/documentation/developers-and-admins/development/content-types) and [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas). The management API is intended to be used via AI tools together with a corresponding [Model Context Protocol (MCP)](https://modelcontextprotocol.io/docs/getting-started/intro) server. See [Content type management API](/documentation/developers-and-admins/development/content-types/management-api) for more information and instructions.  
![]()
[]()[]()
