---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/reference-configuration-keys
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * Reference - Configuration keys 


# Reference - Configuration keys
[configuration providers](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/).
By default, Xperience projects contain an **appsettings.json** file for this purpose. Unless otherwise specified, Xperience application setting keys are added to the first level of the configuration hierarchy (i.e., directly under the root JSON object in the _appsettings.json_ file).
The _appsettings.json_ file also contains the database connection string.
You can use multiple application settings files to configure different values for [each environment](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects#application-settings-for-different-saas-deployment-environments).
The following configuration categories are available:
  * [General settings](#general-settings)
  * [Continuous Integration and Deployment settings](#continuous-integration-and-deployment-settings)
  * [Contact management settings](#contact-management-settings)
  * [Headless API settings](#headless-api-settings)
  * [Connection string](#connection-string)
  * [Other settings](#other-settings)


## General settings
Key |  Description |  Default  
---|---|---  
CMSForbiddenURLValues |  Specifies characters that are forbidden in page URLs (see [content tree-based routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing)). Forbidden characters are automatically replaced by the character specified in the **Forbidden characters replacement** setting in **Settings → URLs and SEO**. By default, the following characters are forbidden: \/:*?“<>|&%.’#[]+=„“ and the space and tab characters. If you add this key, its value overrides the default forbidden character set. You can either allow some of the forbidden characters or add new ones. We recommend keeping the default characters forbidden, since they may prevent URLs from working correctly. This key cannot be used to [configure](/documentation/developers-and-admins/development/routing/configure-forbidden-url-characters#allow-the-slash-character-in-vanity-urls) the support for the slash character (‘/’) in [vanity URLs](/documentation/business-users/website-content/manage-page-urls). JSON Copy ```
"CMSForbiddenURLValues": "$\\/:?&quot;&lt;&gt;|&amp;%.&apos;#[] ="
```
|   
CMSHashStringSalt |  Sets the [salt](https://en.wikipedia.org/wiki/Salt_%28cryptography%29) value the system uses in hash functions, for example when creating [macro signatures](/documentation/developers-and-admins/configuration/macro-expressions/macro-signatures). If a specific hash salt value is not assigned during the installation of a new Xperience project, the key is set to a random [GUID](https://en.wikipedia.org/wiki/GUID). If you plan to deploy your project to the[ SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), use the hash salt value provided in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal). **Note** : Changing the hash salt on an instance with defined content may prevent macros from resolving correctly. If you encounter such problems, you must [re-sign macros](/documentation/developers-and-admins/configuration/macro-expressions/macro-signatures) (the system then creates the hashes using the new salt). JSON Copy ```
"CMSHashStringSalt": "eb2d6fac-8b9e-427c-b98b-3c562dffbb35",
```
|  Random GUID  
CMSImageExtensions |  Specifies the file extensions that the system recognizes as image files. For example, files of these types are offered in the _Insert image_ dialog of the [rich text editor](/documentation/business-users/rich-text-editor). Note: If this key is not configured, the system attempts to automatically detect and support any media image file type. Adding the key disables the automatic detection, and only the explicitly listed file types are supported as images. JSON Copy ```
"CMSImageExtensions": "bmp;gif;ico;png;wmf;jpg;jpeg;tiff;tif;webp;svg;avif"
```
|  bmp;gif;ico;png;  
wmf;jpg;jpeg;tiff;  
tif;webp;svg;avif;  
jfif;jfi;jif;jpe;dib  
CMSLogKeepPercent |  The size of the system’s [event log](/documentation/developers-and-admins/configuration/event-log) is limited by the **Settings → System → Event log size** setting. This key determines the extra percentage of events that is retained in the log over the specified limit. This percentage of the oldest events is deleted by batch when the percentage is exceeded. For example, if the event log size is 1000 and the _CMSLogKeepPercent_ is 20, the event log can contain up to 1200 events, and the 200 oldest events are deleted when the log size reaches 1201. If the key is set to 0, the event log retains the exact number of records specified by the limit. JSON Copy ```
"CMSLogKeepPercent": 20,
```
|  10  
CMSBuilderScriptsIncludeJQuery |  Determines whether the system links the jQuery 3.5.1 library to live site pages containing [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) content or [forms](/documentation/business-users/digital-marketing/forms). _jQuery_ is considered deprecated and will be phased out completely in one of the future major releases. The library will not be updated, even in the case of new vulnerability disclosures. Projects with custom components that rely on the library should consider referencing an external implementation to prevent possible issues in the future. Set to _true_ if you want to include jQuery on pages with Page Builder and Form Builder content. See [Bundle static assets of builder components](/documentation/developers-and-admins/development/builders/bundle-static-assets-of-builder-components#link-jquery-as-part-of-builder-scripts). JSON Copy ```
{
    "CMSBuilderScriptsIncludeJQuery": true
}
```
|  false  
CMSEnableAira |  Enables [AIRA](/documentation/business-users/aira) globally. By default, AIRA is enabled. Set to _false_ to [disable AIRA](/documentation/developers-and-admins/configuration/aira-configuration#disable-aira-globally) globally in your project. JSON Copy ```
{
    "CMSEnableAira": false
}
```
|  true  
[> Back to list of configuration key categories](#ReferenceConfigurationkeys-KeysTop)
## Continuous Integration and Deployment settings
You can use the following keys to configure the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) features:
Key |  Description |  Default  
---|---|---  
CMSCIEncoding |  Sets the character encoding used when the CI/CD features generate non-binary files in the repository folder. The key supports values matching the encoding names listed in the [Encoding Class](https://docs.microsoft.com/en-us/dotnet/api/system.text.encoding) article. Changing the value of the _CMSCIEncoding_ key does not update the encoding type of existing files in the repository folder. To fully update the encoding of the repository content, you need to:
  * Run complete serialization for all objects.
  * Manually update the encoding type of your repository configuration file.

JSON Copy ```
"CMSCIEncoding": "utf-16",
```
|  utf-8  
CMSCIRepositoryPath |  Sets the location of the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) file repository root folder. The key’s value must be an absolute or relative path of a folder on a local drive, or a network location for which the application has sufficient permissions. The specified location is used when serializing object data to the file system and when restoring data back to the database. The key also allows you to set a shared Continuous Integration repository when developing multiple projects with the same database. JSON Copy ```
"CMSCIRepositoryPath": "C:\\ExternalSourceControl\\CIRepository",
```
|  App_Data\CIRepository  
[> Back to list of configuration key categories](#ReferenceConfigurationkeys-KeysTop)
## Contact management settings
By adding the following keys, you can configure the behavior of [contact management](/documentation/business-users/digital-marketing/contact-management).
Key |  Description |  Default  
---|---|---  
CMSProcessContactActionsInterval |  Sets the interval (in seconds) in which contact data and activities are batch processed by the system. Consider raising the interval if your system processes a large number of activities. The interval affects the following:
  * How often the application saves logged activities and contact detail updates from the memory into the database.
  * How often the system recalculates data that could be affected by activities and contact updates, such as contact groups.

JSON Copy ```
"CMSProcessContactActionsInterval": 30,
```
|  10  
CMSCreateContactActionsLogWorker |  If enabled, all applications in [multi-instance environments](/documentation/developers-and-admins/configuration/auto-scaling-support) recalculate contact groups. If you wish to improve performance, you can set this key to _false_ for one or more of your instances, and the recalculation will be disabled for them. **Note** : If you set the key value to _false_ for all of your application instances, the recalculation will be disabled completely. JSON Copy ```
"CMSCreateContactActionsLogWorker": false,
```
|  true  
[> Back to list of configuration key categories](#ReferenceConfigurationkeys-KeysTop)
## Headless API settings
The following keys configure the behavior of the GraphQL API and Tracking API for all [headless channels](/documentation/developers-and-admins/configuration/headless-channel-management).
The configuration keys for the headless API must be placed under the `CMSHeadless` section in the configuration hierarchy.
JSON
**Headless API configuration example**
Copy
```
{
  ...

  "CMSHeadless": {
    "AllowIntrospection": true,
    "GraphQlEndpointPath": "/api",
    "Caching": {
      "AbsoluteExpiration": "360"
    }
  }
}
```

Key |  Description |  Default  
---|---|---  
Enable |  Specifies whether headless channel endpoints are enabled (both [GraphQL API](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content) and [Tracking API](/documentation/developers-and-admins/digital-marketing-setup/headless-tracking) endpoints). |  true  
EnableTracking |  Specifies whether [Tracking API](/documentation/developers-and-admins/digital-marketing-setup/headless-tracking) endpoints are enabled. Allows you to disable the tracking API while keeping the GraphQL API enabled. The default value is _true_. |  true  
EnableComplexity |  Specifies whether [complexity analysis](https://typegraphql.com/docs/complexity.html) is enabled. |  true  
MaxFieldCycleDepth |  Specifies the maximum allowed depth of field cycles in a GraphQL query. |  5  
AllowIntrospection |  Specifies whether [GraphQL introspection](https://graphql.org/learn/introspection/) is enabled (`__schema` queries and GUI tools for exploring the schema). |  false  
GraphQlEndpointPath |  Specifies the slug used in headless channel GraphQL endpoint URLs. Must include the leading slash (‘ _/_ ’). |  /graphql  
CorsAllowedOrigins |  Specifies domains that are allowed origins for [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) (Cross-Origin Resource Sharing). See [Retrieve headless content](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content#cross-origin-resource-sharing) to learn more. Note: We recommend configuring allowed origins for CORS in the **Settings** application within the Xperience administration, via the **Content → Headless → Allowed origins** setting. The setting overrides any values set in your application’s configuration file or startup options. |  “”  
CorsAllowedHeaders |  Indicates which HTTP headers are allowed for content retrieval requests. If not set, all headers are allowed by default. See [Retrieve headless content](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content#cross-origin-resource-sharing) to learn more. |  All headers  
Caching |  Contains options which allow you to customize caching behavior for headless content. The default values provide optimal caching performance for most projects.
  * `Enable` – a boolean value that specifies whether caching is enabled. The default value is _true_.
  * `UseRequestCacheControlHeaders` – a boolean property that specifies whether caching respects the _Cache-Control_ header of individual requests. The default value is _true_.
  * `AddResponseCacheControlHeaders` – a boolean property that specifies whether caching sets the _Cache-Control_ response header. The default value is _true_.
  * `UseCacheDependencies` – a boolean property that specifies whether caching uses cache dependencies to flush the cache when data in a cached response changes. The default value is _true_.
  * `AbsoluteExpiration` – specifies the maximum expiration time for a cache entry in minutes. The default value is 12 hours.
  * `SlidingExpiration` – specifies a sliding expiration date for cache entries in minutes. Cannot exceed the value of `AbsoluteExpiration`. The default value is 1 hour.
  * `SizeLimit` – maximum size for in-memory cache in bytes. The default value is 100 megabytes.

|  See Description  
[> Back to list of configuration key categories](#ReferenceConfigurationkeys-KeysTop)
## Connection string
The database used by Xperience is specified by the **CMSConnectionString** connection string.
JSON
Copy
```
{
  ...
  "ConnectionStrings": {
    "CMSConnectionString": "Data Source=myserver;Initial Catalog=Xperience;Integrated Security=True;Persist Security Info=False;Connect Timeout=120;Encrypt=False;Current Language=English;" },
  ...
}
```

## Other settings
Key |  Description |  Default  
---|---|---  
AdminRedirectOptions |  Used when configuring a dedicated [administration domain](/documentation/developers-and-admins/configuration/administration-domain-configuration#set-up-an-administration-domain-redirect). |   
CMSAdminClientModuleSettings |  Configures the client module in the Xperience web application. See section [Add the boilerplate to an Xperience project](/documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development#add-the-boilerplate-to-an-xperience-project). |   
CMSDeleteTemporaryUploadFilesOlderThan |  Sets the interval (in hours) at which the system deletes the contents of the temporary folder that stores files uploaded through the _Upload file_ [component](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components#configure-the-upload-file-component) in Form Builder. See section [Protect against Denial of Service](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components#protect-against-denial-of-service) for more information. |  2  
CMSPhysicalFilesCacheMinutes |  Sets client cache expiration time (in minutes) for physical files served by Xperience through the _GetResource_ handler. See [File caching](/documentation/developers-and-admins/development/caching/file-caching) for more information. |  10 080  
(1 week)  
CMSStorageProviderAssembly |  Configures the assembly name of a [custom file system provider](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/custom-file-system-providers). |   
CMSAzureAccountName, CMSAzureSharedKey |  Specifies the account name and the primary access key for Azure Blob storage. Used in the process of [mapping](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#azure-blob-storage-for-private-cloud-deployments) parts of the file system to Azure Blob storage when deploying to [private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud). |   
CMSAzureTempPath, CMSAzureCachePath, CMSAzureBlobEndPoint, CMSAzurePublicContainer, CMSDownloadBlobTimeout |  Keys used for [optional application settings for Azure storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#optional-application-settings-for-azure-storage). Applicable only for [private cloud deployments](/documentation/developers-and-admins/deployment/deploy-to-private-cloud). |   
WebsiteChannelDomains |  Configures [domain overrides](/documentation/developers-and-admins/configuration/website-channel-management#domain-aliases-and-environment-specific-domains) for website channels when using [private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud). |   
EmailChannelDomains |  Configures domain overrides for [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management#email-domains-in-private-cloud-environments) when using [private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud). |   
HeadlessChannelUrls |  Configures [URL overrides](/documentation/developers-and-admins/configuration/headless-channel-management#configure-preview-url-overrides) for headless channels. |   
SystemSmtpOptions |  Used to configure SMTP server for system emails and notifications that do not belong under a specific email channel. Contains the `Server` option with nested options `Host`, `Port`, `Username`, `Password` and optional encoding options. See [SMTP servers](/documentation/developers-and-admins/configuration/email-configuration#smtp-servers) for details and for information about configuration of specific email channels. |   
SmtpOptions |  Used for setting the fallback SMTP configuration for system emails, notifications and and all email channels without their own dedicated configuration. Contains the `Server` option with nested options `Host`, `Port`, `Username`, `Password` and optional encoding options. See [SMTP servers](/documentation/developers-and-admins/configuration/email-configuration#smtp-servers) for details and for information about configuration of specific email channels. |   
SystemSendGridClientOptions |  Used for configuration of system emails and notification that don’t belong to a specific channel when using [SendGrid integration for private cloud environments](/documentation/developers-and-admins/configuration/email-configuration#self-managed-sendgrid-integration). You can configure SendGrid for other email channels in a similar way. |   
SystemEmailOptions |  Configures the [global sending domain for system emails](/documentation/developers-and-admins/configuration/email-configuration#global-sending-domain-for-system-emails), i.e. sending domain for all notications and other system emails that are not created under a specific email channel. The specified domain is used when sending system emails and notifications if only the local part of the sender address is set (e.g., via the **Notifications** application). |   
BouncedEmailsGlobalOptions |  Contains the `SoftBounceLimit` option that limits the number of soft bounces allowed before an email address is excluded when sending emails to any recipient list. Used when setting up [email tracking](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking). Applicable only if you send emails using an SMTP server. See section [SMTP server](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking#smtp-server) for information about configuration of `BouncedEmailsOptions` for every email channel. |   
[> Back to list of configuration key categories](#ReferenceConfigurationkeys-KeysTop)
![]()
[]()[]()
