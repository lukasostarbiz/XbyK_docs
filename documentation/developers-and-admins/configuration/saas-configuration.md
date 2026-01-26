---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/saas-configuration
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * SaaS configuration 


# SaaS configuration
Kentico’s [SaaS](/documentation/developers-and-admins/saas) offering provides infrastructure and supporting services that allow you to deploy, host and manage Xperience by Kentico projects in the cloud. With the features and benefits of hosting your projects in the cloud also comes some necessary configuration you need to do for your projects to work optimally. To configure your application:
  1. [Identify your working environment](#environment-identification-extension-methods).
  2. [Enable Microsoft Application Insights](#DeploytotheSaaSenvironment-EnableAppInsights).
  3. [Enable managed SendGrid integration](/documentation/developers-and-admins/configuration/email-configuration#Emailconfiguration-EnableManagedSendGrid).
  4. (_Optional_) Configure [Cloudflare CDN](#cloudflare-cdn).
  5. (_Optional_) Configure [Azure Blob storage folder mapping](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) for [physical files](/guides/architecture/content-modeling/content-modeling-guide/store-files) and any [unmanaged binary files](/guides/architecture/content-modeling/content-modeling-guide/store-files#unmanaged-files-and-when-to-use-them) referenced by your application. Consider this configuration, especially if your application works with binary files stored outside of the dedicated _~/assets_ directory that is mapped to the blob by default. Persisting binary data outside of the blob storage can be unreliable and lead to data loss in case the hosting service recycles the application (e.g., due to infrastructure changes).
  6. (_Optional_) [Configure the content synchronization feature](/documentation/developers-and-admins/configuration/content-sync-configuration).
  7. (_Optional_) [Define a custom static page to display when your application is unavailable](#define-a-custom-app-offline-file).


To see more information about the configuration of custom domains visit [website domains](/documentation/developers-and-admins/configuration/website-channel-management#configure-a-domain-name-in-the-xperience-administration) or [email sending domains](/documentation/developers-and-admins/configuration/email-configuration#configurable-sender-email-addresses).
### Environment identification extension methods
Use the following extension methods to identify your application’s currently active environment:
  * `IsQa()` and `IsUat()` (available in the `Kentico.Xperience.Cloud` namespace)
  * native .NET `IHostEnvironment` extensions: 
    * [IsDevelopment()](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.hostenvironmentenvextensions.isdevelopment)
    * [IsProduction()](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.hostenvironmentenvextensions.isproduction)
    * [IsEnvironment()](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.hostenvironmentenvextensions.isenvironment) with the environment names from `CloudEnvironments` (available in the `Kentico.Xperience.Cloud` namespace)


For example, use the environment identification extension methods to [integrate Twilio SendGrid](/documentation/developers-and-admins/configuration/email-configuration#Emailconfiguration-EnableManagedSendGrid) in SaaS deployment environments but not in your local development environment.
C#
**Environment identification in Program.cs**
Copy
```
using Microsoft.Extensions.Hosting;

using Kentico.Xperience.Cloud;

...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

...

if (builder.Environment.IsQa() ||
    builder.Environment.IsUat() ||
    builder.Environment.IsEnvironment(CloudEnvironments.Custom) ||
    builder.Environment.IsEnvironment(CloudEnvironments.Staging) ||
    builder.Environment.IsProduction())
{
    // Code executed in SaaS deployment environments (QA, UAT, STG, Custom, PROD)
    builder.Services.AddKenticoCloud(builder.Configuration);
    builder.Services.AddXperienceCloudSendGrid(builder.Configuration);
    builder.Services.AddXperienceCloudDataProtection(builder.Configuration);
    // ...
}
```

### Microsoft Application Insights integration
Xperience integrates [Application Insights for ASP.NET Core applications](https://docs.microsoft.com/en-us/azure/azure-monitor/app/asp-net-core) to enable monitoring and aggregation of metrics for your website:
  * Event log – errors and warnings from the [Xperience event log](/documentation/developers-and-admins/configuration/event-log).
  * .NET exceptions and errors – in addition to errors that are included in the Xperience event log, also covers exceptions that can occur before the Xperience application is fully running.
  * (_Optional_) [Client-side telemetry](https://docs.microsoft.com/en-us/azure/azure-monitor/app/usage-overview)


You can view the Application Insights logs for your deployments in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) using the corresponding applications under the **Monitoring** section:
  * **Event log** – displays entries from the Xperience event log and any custom messages sent via the Application Insights SDK.
  * **Exceptions** – displays all .NET exceptions (errors) from Application Insights.


Enabling the Application Insights integration is also helpful if you ever need to debug or monitor your application with the Kentico support team.
#### Enable Microsoft Application Insights integration
  1. Open your Xperience project in Visual Studio and edit the **Program.cs** file.
  2. Call `WebApplicationBuilder.Services.AddXperienceCloudApplicationInsights` when building the web application. 
     * See [Configure new projects](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects) for the recommended middleware order.
C#
**Enabling AppInsights integration**
Copy
```
using Kentico.Xperience.Cloud;

...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

...

builder.Services.AddXperienceCloudApplicationInsights(builder.Configuration);
```

Refer to Microsoft’s documentation on how to use [Application Insights in Visual Studio in a development environment](https://docs.microsoft.com/en-us/azure/azure-monitor/app/visual-studio).
  3. (_Optional_) [Enable client-side telemetry](https://docs.microsoft.com/en-us/azure/azure-monitor/app/asp-net-core#enable-client-side-telemetry-for-web-applications).
  4. Rebuild the solution.


### Cloudflare CDN
The SaaS environment integrates [Cloudflare CDN](https://www.cloudflare.com/learning/cdn/what-is-a-cdn/) to ensure better application performance and availability by distributing the network load across the CDN data centers and by caching website resources.
Cloudflare CDN also provides security protection against [DDoS attacks](https://en.wikipedia.org/wiki/Denial-of-service_attack) and other threats. You can monitor security events in [Xperience Portal](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#security-events).
Cloudflare CDN performs caching as described in [Default Cache Behavior](https://developers.cloudflare.com/cache/concepts/default-cache-behavior/).
  * Files with the [Default cached extensions](https://developers.cloudflare.com/cache/concepts/default-cache-behavior/#default-cached-file-extensions) (images, videos, CSS, JS, PDF, etc.) are cached. By default, the cache time-to-live (TTL) is based on the response’s HTTP status code – see [Edge TTL](https://developers.cloudflare.com/cache/how-to/configure-cache-status-code/#edge-ttl).
  * The HTML output of pages is not cached.


You can control the CDN caching for supported file extensions by setting the [Cache-Control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control) HTTP header directives for each response. For example:
  * _no-cache_ , _private_ , _no-store_ , _max-age=0_ – disables caching
  * _max-age= <seconds>_ – enables caching with the specified time-to-live (TTL). For more information, see [Retention versus Freshness (TTL)](https://developers.cloudflare.com/cache/concepts/retention-vs-freshness/).


There are several ways to set HTTP headers, for example, by using the `ResponseCache` attribute to set _Cache-Control_ directives. See [Response caching in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/performance/caching/response) to learn how to set HTTP headers in ASP.NET Core.
For example, the following code shows how to set the _Cache-Control_ header for static files in your application’s _Program.cs_ file:
C#
**Program.cs**
Copy
```
...

builder.Services.Configure<StaticFileOptions>(o =>
{
    o.OnPrepareResponse = context =>
    {
        // Caches static files for 7 days
        context.Context.Response.Headers.Append("Cache-Control", "public,max-age=604800");
    };
});

...

app.UseStaticFiles();

...
```

### Define a custom App Offline file
When the application is offline, for example, when [scheduled Xperience Portal project maintenance](/documentation/developers-and-admins/saas/xperience-portal#schedule-maintenance-of-xperience-portal-projects) is in progress, a generic .NET static page is displayed when accessing the application.
Create an **app_offline.htm_** file in the project’s root to replace the default generic page. The **app_offline.htm** page is displayed only if the database is updated during the run of the deployment pipeline. The file will be packaged with the deployment package. See [Deploy](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-with-a-deployment-package).
Use the file name suffixed with an underscore (_), as **app_offline.htm** is not a valid custom App Offline name in Xperience.
See Microsoft documentation for more information about the [App Offline file (app_offline.htm)](https://learn.microsoft.com/en-us/aspnet/core/host-and-deploy/app-offline?view=aspnetcore-6.0).
![]()
[]()[]()
