---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/content-sync-configuration
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * Content sync configuration 


# Content sync configuration
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Content synchronization allows users to easily transfer content from one instance of Xperience by Kentico to another.
The purpose of content sync is to help manage content when using multiple environments. For example, a project’s developers may have a local **Development** instance, from which initial content is sent to a hosted **Staging** instance. The staging instance is accessible only by editors and other staff, who use it to prepare content updates. The final content is then synchronized during regular releases to a public-facing **Production** instance.
This page describes how to set up and configure your applications to use content sync:
  1. [Learn about the supported environment topologies](#supported-topologies)
  2. Set up content sync connections between environments: 
     * [Local environments and private cloud deployments](#set-up-content-sync)
     * [SaaS deployment environments](#content-sync-for-saas-environments)
  3. [Configure Xperience features and data for content sync](#configure-xperience-for-content-sync)


To learn how to synchronize content, see [Content sync](/documentation/business-users/content-sync).
## Supported topologies
Before you start [setting up content sync](#set-up-content-sync), it is important to understand which environment topologies are supported.
The most basic usage of content sync consists of a connection between one **source** instance and one **target** instance. Such connections can be chained to additional instances – a target can be configured as the source for another instance. However, only strictly linear topologies are supported. For example:
[![Example of a supported content sync topology](/docsassets/documentation/content-sync-configuration/content_sync_config_topology.png)](/docsassets/documentation/content-sync-configuration/content_sync_config_topology.png)
#### Unsupported topologies
More complex topologies, for example with multiple branching target connections, loops, or bi-directional connections are not supported. The system does not strictly block content sync with unsupported topologies, but it may result in inconsistencies, errors or unintentionally overwritten content.
[![Examples of unsupported content sync topologies](/docsassets/documentation/content-sync-configuration/content_sync_topologies_unsupported.png)](/docsassets/documentation/content-sync-configuration/content_sync_topologies_unsupported.png)
## Set up content sync
### Prerequisites
  * Instances connected via content sync must use **exactly the same version** of Xperience (including the hotfix version).
  * Target instances must run on **HTTPS** with a valid trusted certificate.
  * If using [auto-scaling](/documentation/developers-and-admins/configuration/auto-scaling-support) for a [private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud) project with content sync, the **~/assets** folder must be mapped to a shared storage location, such as [Azure Blob Storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) or [Amazon S3](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3).


### Source and target configuration
To set up an application as the source or target in a content sync connection, configure `ContentSynchronizationOptions` using the [.NET options pattern](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options#the-options-pattern) (available in the `CMS.ContentSynchronization` namespace).
The options are separated into `Source` and `Target` configuration. The following properties must be configured:
  * `Enabled` – a bool value that enables the application as a source or target for content sync.
  * `Secret` – a secret key containing at least 32 characters. The same secret must be configured for the source and target applications.
  * `TargetUrl` – only available for the `Source` configuration. Specify the URL where the target application is running. Must be absolute, with the HTTPS scheme, without query or fragment components.


Use [environment-specific configuration](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/environments) or [IHostEnvironment extension methods](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.ihostenvironment) to set the correct values for each application in your environment. To learn how to identify environments for [SaaS projects](/documentation/developers-and-admins/saas/saas-overview), see [Environment identification extension methods](/documentation/developers-and-admins/configuration/saas-configuration#environment-identification-extension-methods) or [Application settings for different SaaS deployment environments](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects#application-settings-for-different-saas-deployment-environments).
C#
**Example - Content sync configuration**
Copy
```
using Microsoft.Extensions.Hosting;

...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

...

// Configures the development environment as the content sync source
if (builder.Environment.IsDevelopment())
{
    builder.Services.Configure<ContentSynchronizationOptions>(options =>
    {
        options.Source.Enabled = true;
        options.Source.Secret = "147283795efb915cf7bcbe207678764e";
        options.Source.TargetUrl = "https://productiondomain.com";
    });
}
// Configures the production environment as the content sync target
if (builder.Environment.IsProduction())
{
    builder.Services.Configure<ContentSynchronizationOptions>(options =>
    {
        options.Target.Enabled = true;
        options.Target.Secret = "147283795efb915cf7bcbe207678764e";
    });
}
```

## Content sync for SaaS environments
For [SaaS projects](/documentation/developers-and-admins/saas/saas-overview), you do not need to configure content sync manually (except for local development environments).
[SaaS deployment environments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-the-package-to-another-deployment-environment) have their content sync configuration done automatically based on [Content synchronization connections](#manage-synchronization-connections) defined in Xperience Portal. However, the automatic configuration may be overridden by environment-specific configuration of `ContentSynchronizationOptions` as described in [Source and target configuration](#source-and-target-configuration).
**Xperience 30.5.0 or newer required**
Content synchronization and _Staging_ environments are only available if the deployed Xperience project uses version **30.5.0** or newer.
### Content staging best practices
You can use the content sync feature to transfer content from all types of environments, including non-production ones (local development, QA, UAT). This is a viable approach during development and for small projects. However, non-production environments do not provide sufficient performance, availability, and backup policies for projects with larger content editing teams.
To set up a stable and robust environment for long-term editing and staging of content, add the optional **Staging** (STG) deployment environment to your [service plan](/documentation/developers-and-admins/saas/saas-service-plans).
The _Staging_ environment provides a platform where editors and other staff can prepare content, and then transfer it to the production environment using content sync. Staging environments are designed to be nearly identical to production environments in most aspects, such as performance, availability, backup policies, and the option to set [custom domains](/documentation/developers-and-admins/configuration/website-channel-management#manage-channel-domains-in-xperience-portal).
#### Set up a Staging environment
Use the following process to initialize a _Staging_ deployment environment for an existing project:
  1. Download an [export](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) of your production environment.
  2. Create a new [deployment package](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#create-a-deployment-package). 
     * Make sure that the package contains all required data by editing the **$CDRepository/repository.config** file (see [Configure CI/CD repositories](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories)).
     * If you are updating to version 30.5.0 or newer as part of this deployment, you must include the `cms.user` object type in the package and use the `CreateUpdate` [restore mode](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#configure-cd-restore-mode) (`Create` is not sufficient).
  3. Deploy the package through the available environments up to _Staging_ (STG).
  4. Configure any required [custom domains](/documentation/developers-and-admins/configuration/website-channel-management#manage-channel-domains-in-xperience-portal) for the staging environment.


### Manage synchronization connections
To use content sync for SaaS projects, you need to create synchronization connections between environments in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal):
The limitations described in [Supported topologies](#supported-topologies) apply for content sync between SaaS environments. Only linear connection chains are supported, without multiple branching target connections, loops, or bi-directional connections.
The connected environments must also be running exactly the same version of Xperience by Kentico.
  1. Open the **Content synchronization** application in Xperience Portal.
  2. Select **Create new connection**.
  3. Choose the **Source** and **Target environment**. 
     * When setting up a **LOCAL** development environment as the source, see [Add connections from local development environments](#add-connections-from-local-development-environments).
  4. Select **Create**.


[![Adding a content synchronization connection](/docsassets/documentation/content-sync-configuration/SaaS_content_sync_connections.png)](/docsassets/documentation/content-sync-configuration/SaaS_content_sync_connections.png)
After you create connections, select the **Apply changes** button in the information ribbon at the top of the interface. Applying these changes triggers a restart of the affected applications. You may need to manually refresh the current page for the _Apply changes_ button to appear in the information ribbon.
Once the connection becomes **Active** , content sync is configured for the given environments and users can start [transferring content](/documentation/business-users/content-sync).
You can delete connections and apply the changes if you wish to disable content synchronization between the given environments.
### Add connections from local development environments
When you create a [content sync connection](#manage-synchronization-connections) with a **LOCAL** environment as the source, Xperience Portal displays **Target URL** and **Connection secret** values. Copy the values and use them in the [content sync configuration](#source-and-target-configuration) of the application in your local development environment:
  * Target URL → `ContentSynchronizationOptions.Source.TargetURL`
  * Connection secret → `ContentSynchronizationOptions.Source.Secret`


The **Connection secret** value is not stored and cannot be displayed again in the future. If you lose the secret, you need to delete the connection and create a new one.
You need to ensure that the source configuration only applies to the local development environment. To learn how to identify environments for SaaS deployment environments, see [Environment identification extension methods](/documentation/developers-and-admins/configuration/saas-configuration#environment-identification-extension-methods) or [Application settings for different SaaS deployment environments](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects#application-settings-for-different-saas-deployment-environments).
## Configure Xperience for content sync
To prepare your Xperience instances for content sync, configure the following features:
  * Permissions 
    * To synchronize content items, users need to have a role with the **Synchronize** [permission](/documentation/developers-and-admins/configuration/users/role-management) assigned for the **Content hub** application and the appropriate [workspaces](/documentation/developers-and-admins/configuration/users/role-management/workspaces).
    * To synchronize website channel pages, users need to have a role with the **Synchronize** [permission for individual pages](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management) or the **Manage permissions** permission for the website channel.
    * To synchronize forms, users need to have a role with the **Synchronize** [permission](/documentation/developers-and-admins/configuration/users/role-management) assigned for the **Forms** application.
  * [Workflows](/documentation/developers-and-admins/configuration/workflows) – if you use workflows to control the lifecycle of your content, only set up and apply the workflow on the source instance where content is edited. Any workflow applied on target content sync instances is ignored, as content items and pages are always transferred with the _Published_ or _Unpublished_ status and a completed workflow cycle.
  * [Allowed asset extensions](/documentation/developers-and-admins/configuration/content-hub-configuration#customize-supported-file-types) – instances connected using content synchronization must use the same set of allowed file extensions for content item assets.
  * [Allowed slash character in page vanity URLs](/documentation/developers-and-admins/development/routing/configure-forbidden-url-characters#allow-the-slash-character-in-vanity-urls) – to ensure consistent page URL behavior, instances connected using content synchronization must have the same enabled/disabled status for the slash (‘/’) character in page vanity URLs.
  * [Content folders](/documentation/business-users/content-hub/content-hub-folders#content-folders) – instances connected using content synchronization must have the same enabled/disabled status for content folders. See [Enable content hub folders](/documentation/developers-and-admins/configuration/content-hub-configuration#enable-content-hub-folders).


### Ensure compatibility between instances
The following objects are required by content items and/or pages, but are **not** included in content synchronization:
  * Channels that use the content ([website](/documentation/developers-and-admins/configuration/website-channel-management), [email](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management), [headless](/documentation/developers-and-admins/configuration/headless-channel-management))
  * [Content types](/documentation/developers-and-admins/development/content-types)
    * [Page restrictions](/documentation/developers-and-admins/development/content-types/limit-the-pages-users-can-create) for content types do not apply when restoring synchronized content on target instances.
    * [Image variant definitions](/documentation/developers-and-admins/development/content-types#configure-image-variants) for asset content types must also match between instances connected using content sync.
  * [Languages](/documentation/developers-and-admins/configuration/languages)
  * [Workspaces](/documentation/developers-and-admins/configuration/users/role-management/workspaces)


You need to ensure that all instances connected through content sync have exactly the same set of these objects. For example, an error will occur if you try to synchronize a content item for which an exactly matching [content type](/documentation/developers-and-admins/development/content-types) is not available on the target.
The compatibility of objects is calculated based on their GUID identifiers and all available data. It is not sufficient to manually recreate objects with the same code name (with the exception of [languages](/documentation/developers-and-admins/configuration/languages), which are only validated by their code name). When you update or add any of the required objects on the source server, you need to transfer them to the next environments using other deployment methods, i.e., [SaaS deployment via a deployment package](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-with-a-deployment-package) or [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment).
**Project code compatibility requirements**
To enable synchronization of [website channel pages](/documentation/business-users/website-content) and [forms](/documentation/business-users/digital-marketing/forms), you also need to deploy via [SaaS deployment package](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-with-a-deployment-package) or [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) to transfer all code components related to pages and forms.
This includes page views, controllers, [routing configuration](/documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing), [Page Builder components](/documentation/developers-and-admins/development/builders/page-builder), Form Builder [components](/documentation/developers-and-admins/development/builders/form-builder/form-components) and [sections](/documentation/developers-and-admins/development/builders/form-builder/form-sections), etc.
### Synchronization of linked content
Synchronization of linked content items, pages, and forms relies on the system’s usage tracking features:
  * [Track usage of content items](/documentation/business-users/content-hub/content-items#track-usage-of-content-items)
  * [Track page usages](/documentation/business-users/website-content/track-page-usages)
  * [Track usage of forms](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#track-usage-of-forms)


The limitations described for usage tracking also apply to content sync. Content items, pages or forms that are “linked” in an untracked manner are not automatically synchronized together with the linking item or page.
If your Xperience by Kentico instance was updated from an older version, you need to manually run the following [scheduled tasks](/documentation/developers-and-admins/customization/scheduled-tasks) to track the missing content item or form usages:
  * **Track missing content item usages** scheduled task 
    * Generates content item and page usage references when updating from a version older than **29.3.0**
  * **Track missing object usages** scheduled task 
    * Generates form usage references when updating from a version older than **30.11.0**


## Content sync internals
When a target application receives content through content sync, the updates are not immediately applied. Every synchronization is represented and stored in the following way:
  * Data and asset files stored in the project’s `~/assets/synchronizations` folder
  * Synchronization task (stored in the `CMS_Synchronization` database table)


The synchronizations are applied automatically by the _Content sync restoration_ [scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks), which runs every 30 seconds. During the restoration, content is created or updated using the Xperience API:
  * All content operations are performed in the context of the **kentico-system-service** (“System service user”) internal [user](/documentation/developers-and-admins/configuration/users) account. Permissions are not evaluated.
  * If an error occurs during the restoration, any updates that affect published content are rolled back. In some cases, added or modified _Draft_ versions of content may remain after a failed synchronization restoration.
  * [Global events](/documentation/developers-and-admins/customization/handle-global-events) are triggered for the changes. The API is called in multiple waves when restoring content: 
    1. Content items and pages are first created or updated in the _Draft_ status.
    2. Items and pages are published or unpublished (depending on the status of the synchronized content).
  * [Workflow events](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events#workflow-events) are not triggered (having [workflows](/documentation/developers-and-admins/configuration/workflows) applied on target content sync instance is not recommended)


You can monitor synchronizations that recently occurred or are scheduled to take place in the **Content synchronization** application.
[![View planned or performed synchronizations](/docsassets/documentation/content-sync-configuration/content_sync_tasks.png)](/docsassets/documentation/content-sync-configuration/content_sync_tasks.png)
**Synchronization cleanup**
The _Content sync clean data_ scheduled task automatically cleans up synchronizations once per day. During the cleanup, synchronizations with the _Failed_ or _Completed_ status are removed. In rare cases, a synchronization may get stuck in one of the other transitional statuses that occur during restoration. Such tasks are cleaned up if they are older than 24 hours.
## Troubleshoot content sync
If you encounter any issues or errors when using content sync, you can use the system’s [logging](/documentation/developers-and-admins/development/logging) functionality to analyze the content sync process. Content sync provides structured logging data during all steps of the process, including content preparation and transfer on the source instance and content restoration on the target instance.
To view the full content sync logs, your preferred logging provider must be [configured](/documentation/developers-and-admins/development/logging#configure-logging) in the following way:
  * **Log level** : `Debug` or higher
  * **Log categories** : 
    * `CMS.ContentSynchronization`
    * `CMS.Websites`
    * `CMS.ContentEngine`  
(for simplicity, you can adjust the configuration for all log categories starting with `CMS`)


The logs should help you identify the source of your issue, and can be helpful when describing the problem to [Kentico support](https://xperience.io/services/support).
If your logging provider supports [distributed tracing](https://learn.microsoft.com/en-us/dotnet/core/diagnostics/distributed-tracing), you can enable collection of content sync activities by adding the `Kentico.Xperience.ContentSynchronization` source to your tracing configuration. For example:
C#
**Program.cs**
Copy
```
using Microsoft.Extensions.Logging;
using OpenTelemetry.Logs;
using OpenTelemetry.Trace;

// ...

var builder = WebApplication.CreateBuilder(args);

// ...

builder.Services.AddLogging(logging =>
{
    logging.AddOpenTelemetry(options =>
    {
        // ...
    });
});

// Configures OpenTelemetry tracing
builder.Services.AddOpenTelemetry()
    .WithTracing(t => t
        // Adds the source for content sync activities
        .AddSource("Kentico.Xperience.ContentSynchronization")
        .AddAspNetCoreInstrumentation()
        .AddOtlpExporter());
```

![]()
[]()[]()
