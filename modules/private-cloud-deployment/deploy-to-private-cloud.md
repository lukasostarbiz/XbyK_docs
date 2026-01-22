---
source: https://docs.kentico.com/modules/private-cloud-deployment/deploy-to-private-cloud
scrape_date: 2026-01-22
---

Module: Private cloud deployment
6 of 7 Pages
# Explore the private cloud reference
Xperience supports a wide variety of private cloud hosting options. You can deploy and manage Xperience projects using
  * Managed or unmanaged cloud hosting providers like Microsoft Azure, Amazon AWS, GCP, Digital Ocean (on Windows or Linux)
  * Hybrid cloud combining public cloud and private hosting
  * Fully private data center hosting on traditional VMs


and similar hosting solutions.
## Deploy the project
Xperience is a conventional .NET project, so its code and other files support any compatible deployment process or pipeline. For general information, see the [Host and deploy](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/) chapter of the .NET documentation.
### Map parts of the file system to a shared storage
Some deployment environments, like [Microsoft Azure](#microsoft-azure-deployment) for example, don’t provide a persistent file system. This can potentially lead to a loss of certain binary files when running new deployments, swapping slots, switching servers, etc. To prevent the possible loss, we recommend mapping the **~/assets/** folders from your project’s file system to a shared storage. The folder contains content item assets, media libraries and files uploaded through forms using the _Upload file_ [form component](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components).
Xperience already provides built-in support for mapping of this folder to [Azure Blob](https://azure.microsoft.com/en-us/services/storage/blobs/) or [Amazon S3](https://aws.amazon.com/s3/) cloud-based storage.
For more information, see [Azure Blob storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#azure-blob-storage-for-private-cloud-deployments) and [Amazon S3](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3).
### IIS application initialization
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

## Deploy the database
Prepare the Xperience database within your development environment, and then use a standard backup&restore process to move the database to the target environment.
If you wish to deploy an empty database with default data necessary to run Xperience by Kentico, create a project using the `kentico-xperience-mvc` boilerplate template and run the `kentico-xperience-dbmanager` CLI utility. See [Installation](/documentation/developers-and-admins/installation).
To update database content as part of later deployments, we recommend using the Xperience [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) feature. Note that CD data does not include the [license key](/documentation/developers-and-admins/installation/licenses#license-keys), which needs to be added manually after the deployment.
**Database server time zones**
To ensure consistent handling of time values, the server running your Xperience by Kentico application must be configured to use the same time zone as the database server.
If possible, use the same time zone for both your local environment and the production server hosting your database. Otherwise you may encounter time shift problems with the deployed data.
## Update the Xperience version of deployments
To learn how to update a deployment to a newer version of Xperience by Kentico, see [Update Xperience by Kentico projects](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects).
## Update content with content sync
To use [content sync](/documentation/business-users/content-sync) for transferring supported content between environments, see [Content sync configuration](/documentation/developers-and-admins/configuration/content-sync-configuration).
## Microsoft Azure deployment
For most Microsoft Azure deployments, we recommend [Azure Web Apps](https://azure.microsoft.com/en-us/services/app-service/web/). Azure Web Apps are quick to create and easy to set up and manage.
Xperience applications that require heavy customization may benefit from [Azure Virtual Machine](https://docs.microsoft.com/en-us/azure/virtual-machines/) deployments, where developers have the ability to control every aspect of the environment.
### Azure SQL Database tier recommendations
Microsoft Azure provides the following [purchasing models](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-service-tiers) for Azure SQL databases:
  * [vCore-based purchasing model](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-service-tiers-vcore) that enables you to choose the exact amount of storage capacity and compute as necessary.
  * [DTU-based purchasing model](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-service-tiers-dtu) where you can choose bundled compute&storage packages balanced for common workloads.


Each model is further divided into tiers and performance levels. See the corresponding Microsoft documentation for details.
Recommendations for running Xperience databases in Azure SQL:
Project demands |  DTU-based tier |  vCore-based tier |  Notes  
---|---|---|---  
Projects in development |  Standard |  General purpose |  The lowest Standard level is suitable only for projects that are not in production and have only small performance demands. Even though Xperience runs in the DTU-based _Basic_ tier, we do not recommend using this tier as it severely restricts database performance.  
Read-only projects |  Standard |  General purpose |  For small Xperience projects only.  
Larger read/write intensive projects |  Premium |  Business critical |  Websites expecting a large amount of visitors and database requests.  
We also recommend that you perform a load test when switching to a new Azure SQL tier to make sure the database provides satisfactory performance. Choosing performance levels that do not cover your project demands can cause slow database response times and generally low performance.
You can switch between deployment models and adjust their configuration on the [Azure Management Portal](https://portal.azure.com/) → Select your database → **Configure** tab.
**Azure SQL database elastic pools**
In environments where multiple independent projects each use a single database, you can store individual databases in an  _elastic pool_. The databases in an elastic pool are on a single Azure SQL Database server and share a set number of dynamically allocated resources (DTUs or vCores), which helps minimize overall expenses. See the [Elastic pools](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-elastic-pool) Microsoft documentation for more information.
[ Previous page ](/modules/private-cloud-deployment/deploy-without-the-administration)
6 of 7
[ Mark complete and continue ](/modules/private-cloud-deployment/private-cloud-deployment-learn-more)
![]()
[]()[]()
