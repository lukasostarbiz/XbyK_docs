# Auto-scaling support
  * [ Copy page link ](documentation/developers-and-admins/configuration/auto-scaling-support#) | [Get HelpService ID](documentation/developers-and-admins/configuration/auto-scaling-support#)
Core MVC 5


[✖](documentation/developers-and-admins/configuration/auto-scaling-support# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/configuration/auto-scaling-support#)
Xperience projects can distribute computing among multiple applications. Each application increases the number of requests the project can handle, allowing you to scale performance as necessary. You can also use this feature to increase availability – if one of the servers hosting the project stops working, the other servers ensure the project continues running normally.
Auto-scaling ensures that [cached](documentation/developers-and-admins/development/caching) content and object data is automatically synchronized between all applications.
**Testing auto-scaling in local environments**
Auto-scaling is designed for deployed environments and relies on internal mechanisms specific to that context. **It is currently not supported for local development or testing, even if a multi-instance setup is replicated locally.** You can only reliably test auto-scaling functionality in a deployed environment.
The following image illustrates the structure of a scaled environment:
[![Auto-scaling schema](docsassets/documentation/auto-scaling-support/webfarm_schema.png)](https://docs.kentico.com/docsassets/documentation/auto-scaling-support/webfarm_schema.png)
For instance, if a page update occurs on one server (e.g., `192.168.1.2`), other servers typically remain unaware. The auto-scaling feature resolves this by notifying all servers of the change, ensuring they take appropriate action, such as clearing their cache. This synchronization works because all auto-scaled instances are [connected to the same project database](documentation/developers-and-admins/configuration/auto-scaling-support#scale-xperience-applications). Xperience uses this central database to store and retrieve the necessary synchronization metadata.
## Handling physical files in scaled environments
When a file is [uploaded](documentation/business-users/content-hub/content-item-assets) on one instance in a scaled environment, auto-scaling ensures other instances are notified about the change (e.g., to clear cache). To ensure these files are consistently accessible across all instances, you **must** configure shared storage.
  * **Configure Shared Storage:** Set up a shared storage location accessible by all instances, such as [Azure Blob Storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage), [Amazon S3](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3), or a [shared network drive](documentation/developers-and-admins/api/files-api-and-cms-io). When using shared storage, files uploaded via any instance are placed directly into this central location. Auto-scaling then informs other instances about new or updated files, ensuring data consistency. This configuration is **required** for managing files reliably in scaled environments.


## Scale Xperience applications
**Important:** Xperience by Kentico does not support scaling via the web garden configuration.
### SaaS environment
For projects [deployed to the SaaS environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), no configuration is necessary. The number of available instances depends on your [SaaS service plan](documentation/developers-and-admins/saas/saas-service-plans). When the load increases to peak levels in the production environment, an additional instance is temporarily added to ensure load balancing. One auto-scaling instance is available across all [plans](documentation/developers-and-admins/saas/saas-service-plans). If more instances are required, they can be purchased through a custom service plan.
### Private cloud hosting
Xperience auto-scaling is also compatible with other third-party cloud hosting providers, such as Microsoft Azure, that provide their own auto-scaling solutions. Deploy the project to [Azure Web Apps](https://azure.microsoft.com/en-us/services/app-service/web/) or [Azure Virtual Machines](https://docs.microsoft.com/en-us/azure/virtual-machines/) as you would any other application and use the [scaling functionality](https://docs.microsoft.com/en-us/azure/azure-monitor/autoscale/autoscale-overview) inside [Azure Portal](https://portal.azure.com).
**Important:** Regardless of the cloud provider’s scaling mechanism, you must configure shared storage for all web app instances. This is required to reliably handle [physical files](documentation/developers-and-admins/configuration/auto-scaling-support#handling-physical-files-in-scaled-environments) managed by the system across the scaled environment.
### Private on-premise hosting
For projects hosted on-premise, auto-scaling is enabled by connecting all running applications to the same database.
  1. Create copies of your Xperience application.
  2. Open an application’s configuration file (_appsettings.json_ by default).
  3. Check that `CMSConnectionString` points to the same database for all applications.
  4. Configure [shared storage](documentation/developers-and-admins/configuration/auto-scaling-support#handling-physical-files-in-scaled-environments) for all application instances.
  5. Deploy and run all applications.


Xperience automatically recognizes all running applications connected to the project’s database and synchronizes the necessary information between them.