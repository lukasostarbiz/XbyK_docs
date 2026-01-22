---
source: https://docs.kentico.com/documentation/developers-and-admins/deployment/read-only-deployments
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Deployment](/documentation/developers-and-admins/deployment)
  * Read-only deployments 


# Read-only deployments
Xperience by Kentico applications can be deployed in **read-only mode** , which enables zero-downtime updates to your production database and file system. Switching traffic to a read-only deployment allows you to perform [Xperience updates](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) on your production application, while continuing to serve content from a frozen snapshot of the data using a cloned database.
The read-only mode prevents all write operations to the database and Azure Blob storage, ensuring data consistency when applying updates. Once updates are complete, you can switch traffic back to the updated database without any service interruption.
## Prerequisites
Before implementing support for read-only deployments, ensure your infrastructure meets the following requirements.
### SaaS
For [SaaS deployments](/documentation/developers-and-admins/saas/saas-overview), Kentico’s deployment infrastructure automatically manages infrastructure requirements (deployment slots, database replication, blob versioning). However, you must still:
  1. **Configure your application code to support read-only mode** - See [Required project changes](#required-project-changes) for mandatory code changes
  2. **Mark deployment packages as zero-downtime ready** - Use the `-ZeroDowntimeSupportEnabled` parameter when creating deployment packages


See [Deploy to the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#zero-downtime-deployments) for complete SaaS deployment instructions.
### Private cloud
[Private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud) deployments require the following infrastructure components:
Component |  Requirements  
---|---  
**Database** | 
  * **Two databases** – a production database and a clone database for performing updates
  * Database replication or cloning capability to create synchronized copies

  
**Application hosting** | 
  * **Two deployment slots** – for production and staging deployments.
  * The read-only application instance must be an exact copy of the production application, differing only in the read-only mode configuration.

  
**Blob storage** | 
  * [Azure Blob storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) configured for your Xperience project.
  * [Blob versioning](https://learn.microsoft.com/en-us/azure/storage/blobs/versioning-overview) must be configured on your Azure Storage account

**Azure Blob Storage requirement** Read-only deployments currently support **Azure Blob Storage only**. Other cloud storage providers such as AWS S3 or Google Cloud Storage are **not supported** for read-only mode. Ensure your project uses the [Kentico.Xperience.AzureStorage](https://www.nuget.org/packages/Kentico.Xperience.AzureStorage) package and has blob versioning enabled before implementing read-only deployments.  
## Read-only mode behavior
When read-only mode is enabled, Xperience automatically restricts write operations across the entire application to prevent data modifications.
This behavior applies to both [SaaS](/documentation/developers-and-admins/saas/saas-overview) [zero-downtime deployments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#zero-downtime-deployments) and [private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud) read-only deployments.
### Disabled functionality
The following Xperience features are disabled or restricted when read-only mode is enabled:
Category |  Disabled operations  
---|---  
**Database operations** |  All [data provider](/documentation/developers-and-admins/api/database-table-api) write operations (insert, update, delete, bulk operations).  
**Background workers** |  [Scheduled tasks](/documentation/developers-and-admins/customization/scheduled-tasks) and all other background processes (Xperience-specific `BackgroundService` implementations, for example) that interact with the database are completely disabled in read-only mode.  
**Azure Blob Storage** |  File and directory write operations (create, delete, move, modify).  
**Digital marketing and related endpoints** | 
  * [Activity tracking](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities) (page visits, custom activities, headless activities) 
    * Activity logging endpoints return HTTP 503
  * Contact creation and modification 
    * Contact creation endpoints return HTTP 503
    * Calling `GetCurrentContact(true)` returns `null` if the contact does not exist, instead of creating a new contact
  * [Cross-site tracking](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking) operations
  * [Consent agreement](/documentation/developers-and-admins/data-protection/consent-development) operations
  * Email [statistic tracking](/documentation/business-users/digital-marketing/emails/track-email-statistics) (opens, clicks)

  
**Forms** |  In read-only mode, [forms](/documentation/business-users/digital-marketing/forms) automatically become non-interactive on the live site – the submit button and all input fields have the [disabled attribute](https://www.w3schools.com/tags/att_disabled.asp) applied.  
**Administration interface** |  The administration interface is fully disabled. Users attempting to access the UI are shown a splash screen indicating read-only state.  
### Required project changes
While Xperience automatically prevents its own write operations, you must manually guard **all custom code** that performs database write operations. Failure to do so will result in `ReadOnlyModeException` errors or HTTP 503 statuses when your application runs in read-only mode.
These code changes are mandatory for both SaaS and private cloud deployments. Even though SaaS [zero-downtime deployments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#zero-downtime-deployments) automate the infrastructure, your application code must properly handle read-only mode to avoid errors during deployments.
This requirement extends to the implementations of the following **live site features** :
  * [Forms](/documentation/business-users/digital-marketing/forms) – forms automatically become non-interactive in read-only mode (elements have the [disabled attribute](https://www.w3schools.com/tags/att_disabled.asp) applied). However, you may need to adjust your form styling to provide an appropriate user experience. You can also modify your pages to serve alternative content to forms when read-only mode is enabled.
  * [Data protection](/documentation/developers-and-admins/data-protection) – consent management, cookie level changes.
  * [Membership](/documentation/business-users/members) – user registration, sign-in, profile modifications, password resets.
  * [Digital marketing](/documentation/business-users/digital-marketing) – activity tracking, contact management, cross-site tracking. 
    * Email [subscription and unsubscription](/documentation/developers-and-admins/digital-marketing-setup/enable-email-subscriptions) operations return HTTP 503 or redirect to a configured fallback page. See [Email marketing fallback pages](#email-marketing-fallback-pages).
  * [Digital commerce](/documentation/developers-and-admins/digital-commerce-setup) – shopping cart operations, order creation, payment processing, inventory updates, customer account modifications.


Test your application thoroughly in a staging environment with read-only mode enabled to identify and guard all custom write operations.
Inject and use the `IReadOnlyModeProvider` interface to check read-only status before performing write operations.
C#
**Check read-only status**
Copy
```
// Provider instance obtained via dependency injection
private readonly IReadOnlyModeProvider readOnlyProvider;

if (readOnlyProvider.IsReadOnly)
{
    // Handle read-only scenarios
}
 


```

C#
**Enforce read-only restrictions**
Copy
```
// Provider instance obtained via dependency injection
private readonly IReadOnlyModeProvider readOnlyProvider;

// Throws ReadOnlyModeException if read-only mode is enabled
readOnlyProvider.ThrowIfReadOnly();

// Perform write operations after the read-only guard clause
customInfo.Insert();
 


```

## Configure read-only mode for applications
Configure read-only mode through the `ReadOnlyModeOptions` class in your application settings. The class follows the [options pattern](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options).
For example, you can configure the application via your **appsettings.json** file:
JSON
**appsettings.json**
Copy
```
{
  "ReadOnlyModeOptions": {
    "ReadOnly": true,
    "VersioningTimestamp": "2025-01-15T14:30:00.0000000Z"
  }
}
```

Option |  Description  
---|---  
`ReadOnly` |  Set to `true` to enable read-only mode, `false` to allow normal write operations.  
`VersioningTimestamp` |  The timestamp for blob version retrieval. When read-only mode is enabled, Azure Blob storage operations retrieve the newest blob version created before this timestamp. Set to the time the read-only database snapshot was created.  
Register the read-only mode options in your **Program.cs** file:
C#
**Program.cs**
Copy
```
var builder = WebApplication.CreateBuilder(args);

// Register read-only mode configuration
builder.Services.Configure<ReadOnlyModeOptions>(
    builder.Configuration.GetSection("ReadOnlyModeOptions")
);

// ...
```

## Deployment process overview
The following diagram illustrates the high-level flow of a read-only deployment:
[![Read-only deployment timeline](/docsassets/documentation/read-only-deployments/deployment-timeline.drawio.svg)](/docsassets/documentation/read-only-deployments/deployment-timeline.drawio.svg)
The deployment process follows these general steps:
  1. **Clone the database** – Use database replication, snapshotting, or similar features to create a copy of your production database while the source database remains in read-write mode. After cloning is complete, set the clone database as _read-only_ using your database management platform (for example, Azure SQL Database portal) or SQL commands to prevent accidental modifications.
  2. **Enable read-only mode on the clone** – Deploy the same application build (binaries) to a separate deployment slot and configure it to run in read-only mode. Set the application’s connection string to point to the cloned database and [configure](#configure-read-only-mode-for-applications) the `VersioningTimestamp` to match the timestamp when the database was cloned. This ensures blob operations retrieve versions that align with the frozen database state.
     * We recommend using [managed identities](https://learn.microsoft.com/en-us/entra/identity/managed-identities-azure-resources/overview) for Azure Blob Storage authentication, granting the read-only application identity only read permissions to the blob storage.
  3. **Switch traffic to read-only instance** – Update your traffic management or deployment setup to direct all incoming requests to the read-only application instance. Your live site continues serving content without interruption, but write operations are blocked.
  4. **Perform database updates** – With traffic switched away, safely update the original production database.
**Avoid file changes during updates**
During the update:
     * **Do not delete** any blob storage files used by the read-only instance.
     * **Do not replace** or update any files managed by the system (content item assets, etc.) that are in use by the read-only instance.
Blob versioning cannot correctly deal with such changes. Deleting or replacing files during the update of the production instance may affect or break the content being served by the read-only instance. If possible, we recommend using [content synchronization](/documentation/business-users/content-sync) to deploy file changes rather than performing full deployments. 
  5. **Switch traffic back** – After updates are complete and verified, switch traffic back to the updated production database. The application resumes normal read-write operations.
  6. **Discard the clone** – Remove the temporary clone database and associated resources. The read-only deployment is complete.


## Email marketing fallback pages
When read-only mode is enabled, email [subscription and unsubscription](/documentation/developers-and-admins/digital-marketing-setup/enable-email-subscriptions) operations cannot modify the database. To provide a better user experience, configure a fallback page that visitors see when attempting to subscribe or unsubscribe during read-only mode.
### Configure fallback pages
  1. Open the **Channel management** application in the Xperience administration.
  2. Select your [website channel](/documentation/developers-and-admins/configuration/website-channel-management) and navigate to the **Channel settings** tab.
  3. In the **Email marketing** section, locate the **Fallback page for read-only mode** setting.
  4. Select a page to display when visitors attempt subscription operations during read-only mode.
  5. **Save** your changes.


When a fallback page is configured, subscription and unsubscription requests redirect to this page instead of returning an error. The fallback page applies to [recipient lists](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-recipient-lists) with a _Thank you_ or _Goodbye_ page from the given channel.
If no fallback page is configured, the application returns HTTP 503 to indicate the operation cannot be completed.
**Fallback page content**
Create a dedicated fallback page that explains the temporary unavailability and provides alternative contact information or instructions for subscribing after the maintenance window.
![]()
[]()[]()
