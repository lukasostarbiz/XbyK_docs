# Azure Blob storage
  * [ Copy page link ](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#) | [Get HelpService ID](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#)
Core MVC 5


[✖](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#)
Xperience by Kentico supports file system providers that allow you to map parts of the file system to [Microsoft Azure Blob Storage](https://azure.microsoft.com/en-us/services/storage/blobs/). You can use Blob storage when:
  * [Deploying Xperience projects to the SaaS environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment)
  * [Deploying to private cloud](documentation/developers-and-admins/deployment/deploy-to-private-cloud)
  * You need to store parts of the application file system in a shared storage. For example, shared storage is a requirement for all [scaled](documentation/developers-and-admins/configuration/auto-scaling-support) Xperience deployments.


Blob storage is particularly suitable for storing content item assets, and all other [unmanaged binary files](guides/architecture/content-modeling/content-modeling-guide/store-files) referenced by the Xperience application. Some application deployment environments, like [Azure Web Apps](https://azure.microsoft.com/en-us/products/app-service/web/) for example, do not guarantee a persistent file system for files created outside of the original deployment package. Therefore, if Azure needs to recycle the application due to rolling infrastructure updates or unexpected outages, all but the image with the original deployment is lost. Blob storage does not suffer from these limitations, as the infrastructure ensures redundancy in case of an outage.
Follow the instructions on this page to create Azure Blob storage providers for:
  * [projects deployed to the SaaS environment](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#azure-blob-storage-for-kenticos-saas)
  * [private cloud deployments](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#azure-blob-storage-for-private-cloud-deployments)


**File name case**
Unlike regular file systems (NTFS, VFAT), Azure Blob storage is case-sensitive. To ensure consistent behavior, Xperience automatically converts all file and folder names to lowercase when processing files on Azure storage.
## Media library files in Azure Blob storage
**Media libraries sunset**
Media libraries have been officially sunset. Support for media libraries will continue for one more year (until July 24, 2026), after which the feature and all associated APIs will be completely removed.
Before mapping media library files to the [Microsoft Azure Blob Storage](https://azure.microsoft.com/en-us/services/storage/blobs/), consider migrating media libraries and mapping content item assets instead. See [Media library migration](guides/architecture/media-libraries-migration-guidance) for instructions on how to migrate your media library files to [Content hub](documentation/business-users/content-hub).
Media library files stored in Azure Blob storage have the following limitations:
  * **Storing a large number (thousands) of media library files in a single media library can significantly affect the performance and user experience of the[Media libraries](documentation/business-users/media-libraries/create-media-libraries) application.**
    * We recommend structuring media library files into multiple media libraries and storing at most 100 files in a single media library folder.
  * Mapping subfolders of media libraries is not supported. You can map either the directory containing the media libraries (**~/assets/media**), or individual media libraries (**~/assets/media/ <MediaLibraryName>**).
  * The system’s automatic clearing of files from the server-side cache does not work for files stored in an external storage. If you modify a media file, the website may still display the old version until the cache expires (unless you manually clear the application’s cache). See also: [File caching](documentation/developers-and-admins/development/caching/file-caching).


## Azure Blob storage for Kentico’s SaaS
When developing an Xperience application that you want to [deploy to the SaaS environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), you need to create an Azure Blob storage [provider](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers) (`AzureStorageProvider`) for folders with external application files and media library files that you want to deploy with your application.
Azure Blob storage is part of your [Xperience by Kentico SaaS subscription](documentation/developers-and-admins/installation/licenses#licensing-xperience-by-kentico) and is used when deploying projects to the SaaS environment, as storing persistent data alongside Xperience application binaries deployed in [Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/overview) is not recommended.
**Managed and unmanaged data**
Only data that is not handled by the [CI/CD features](documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types) and is stored outside of the Xperience database needs to be deployed to the Azure Blob Storage.
  * Media library metadata is stored in the Xperience database, only the media library (binary) files are deployed.
  * Files stored in the content item asset fields do not need to be deployed as they are handled by CI/CD.


### Default Kentico-managed Azure Blob storage configuration
Xperience provides Azure Blob storage with the required accounts as part of every subscription. [Projects](documentation/developers-and-admins/installation#create-a-project) created with the `--cloud` parameter contain the **StorageInitializationModule.cs** file with a sample storage initialization module and the **Export-DeploymentPackage.ps1** deployment script.
The default configuration maps:
  * **~/assets** (content item assets, media libraries, files uploaded via the _Upload file_ [form component](documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components)) project folder to an Azure Blob storage provider for the **QA** , **UAT** , **STG** and **Production** environments. The default Azure Blob storage container name is **default**. 
    * Use [Azure Blob storage containers](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-portal) to categorize files.
  * **~/assets/media** (media libraries) project folder to a [local storage provider](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers) configured for the development environment. The media libraries are stored in the **~/$StorageAssets/default/assets/media** folder, and a container named **default** , but accessed under the **~/assets/media** path in your application. 
    * Using local storage providers for development is a good practice, as it is not recommended to use production containers during development.


The default configuration ensures that when your application accesses the **~/assets/media** folder, the mapping automatically forwards the request to the **~/$StorageAssets/default/assets/media** folder during development. When you deploy the application to the SaaS environment, your application stores the folders in the Azure Blob storage container named **default**. You can use multiple containers to store files.
The configuration can be further customized – see [Map folders to a Kentico-managed Azure Blob storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#map-folders-to-a-kentico-managed-azure-blob-storage).
### Map folders to a Kentico-managed Azure Blob storage
To use a different configuration (a different container name or multiple containers for multiple folders), create, configure, and map Azure Blob storage providers and [local file system providers](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers) for each folder:
  1. Get a basic understanding of [Azure Blob storage containers](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-portal). 
     * Use container names conforming to the naming requirements in [Container names.](https://docs.microsoft.com/en-us/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)
     * Use folder structure inside containers and file name requirements conforming to [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/en-us/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).
  2. Edit the custom module in the **StorageInitializationModule.cs** file that was automatically created during [installation](documentation/developers-and-admins/installation#create-a-project).
  3. Get familiar with the module code and modify it to suit your application: 
    1. Modify the `MapAzureStoragePath` method to map a directory to a specific Azure Blob storage provider:
       * Use the `path` method argument to set the folder you want to map to an Azure Blob storage provider, where `~` is your project root folder.
       * Set the `PublicExternalFolderObject` provider property to `true`, if you want the files from a container to be publicly accessible.
       * Use the `container_name` method argument to set the target Azure Blob storage container. Use a separate folder for each folder you want to deploy to a separate Azure Blob storage container.
Containers do not need to have an existing folder in the storage assets root folder, but need to have a valid mapping in your application – Xperience automatically ensures that the folder structure gets created in Azure Blob storage when your application tries to access those folders when deployed to Azure Blob storage.
Containers in deployment environments are isolated. You can map folders in different environments to containers with identical names.
C#
**Mapping a folder to an Azure Blob storage provider**
Copy
```
private void MapAzureStoragePath(string path, string containerName)
{
    // Creates a new StorageProvider instance for Azure Blob storage
    var provider = AzureStorageProvider.Create();
    // Specifies the target container
    provider.CustomRootPath = containerName;
    provider.PublicExternalFolderObject = false;

    StorageHelper.MapStoragePath(path, provider);
}
```

    2. Modify the `MapLocalStoragePath` method to create a local storage provider for storage assets. Use a separate folder for each folder you want to deploy to a separate Azure Blob storage container under **~/$StorageAssets/ <container_name>**:
       * Use the `path` method argument to set the folder you want to map to a local storage provider, where `~` is the root folder of your project,
       * Use the `container_name` method argument to set the target Azure Blob storage container. Use a separate folder for each folder you want to deploy to a separate Azure Blob storage container.
C#
**Mapping a folder to a local storage provider**
Copy
```
private void MapLocalStoragePath(string path, string containerName)
{
    // Creates a new StorageProvider instance for local storage
    var provider = StorageProvider.CreateFileSystemStorageProvider();
    // A local path where to map the folder
    provider.CustomRootPath = $"{LOCAL_STORAGE_ASSETS_DIRECTORY_NAME}/{containerName}";

    StorageHelper.MapStoragePath(path, provider);
}
```

You can use a different root folder than **$StorageAssets** to store the local binary files. Change the `$StorageAssetsFolderName` variable in **Export-DeploymentPackage.ps1** script and the `LOCAL_STORAGE_ASSETS_DIRECTORY_NAME` variable in **StorageInitializationModule.cs**.
    3. Modify the `OnInit()` method to map the folder path either to the Azure Blob storage provider or to a local storage provider, depending on the current environment.
C#
**Mapping project folders to managed Azure storage**
Copy
```
// Changed from "$StorageAssets". "Export-DeploymentPackage.ps1" needs to reflect this.
private const string LOCAL_STORAGE_ASSETS_DIRECTORY_NAME = "CustomStorageAssetsFolder";

private const string CONTAINER_NAME_LIBRARIES = "media-libraries-container";
private const string CONTAINER_NAME_CONTENT_ITEM_ASSETS = "content-item-assets-container";
private const string CONTAINER_NAME_FORM_FILES = "biz-form-files-container";
private const string CONTAINER_NAME_DOCUMENTS = "documents-container";

// Initialization code executed on application startup
protected override void OnInit()
{
    base.OnInit();
    //
    // NOTE: The "~" gets resolved into the project root
    // NOTE: The "documents" folder is an example folder with arbitrary document files
    //

    // Mapping for SaaS deployment environments:
    // -- media libraries to "media-libraries-container"
    // -- content item assets to "content-item-assets-container"
    // -- form files to "biz-form-files-container"
    // -- "documents" folder to "documents-container"
    //
    if (builder.Environment.IsQa() ||
        builder.Environment.IsUat() ||
        builder.Environment.IsEnvironment(CloudEnvironments.Custom) ||
        builder.Environment.IsEnvironment(CloudEnvironments.Staging) ||
        builder.Environment.IsProduction())
    {
        MapAzureStoragePath("~/assets/media", CONTAINER_NAME_LIBRARIES);
        MapAzureStoragePath("~/assets/contentitems", CONTAINER_NAME_CONTENT_ITEM_ASSETS);
        MapAzureStoragePath("~/assets/bizformfiles", CONTAINER_NAME_FORM_FILES);
        MapAzureStoragePath("~/assets/documents", CONTAINER_NAME_DOCUMENTS);
    }

    // Mapping for the local development environment, under the "~/CustomStorageAssetsFolder/" folder:
    // -- media libraries to the "media-libraries-container" folder
    // -- "documents" folder to the "documents-container" folder
    // NOTE: The "~/CustomStorageAssetsFolder/" folder is exported into the deployment package.
    else
    {
        MapLocalStoragePath("~/assets/media", CONTAINER_NAME_LIBRARIES);
        MapLocalStoragePath("~/assets/documents", CONTAINER_NAME_DOCUMENTS);
    }
}
```

  4. Rebuild the solution.
  5. [Create and deploy the deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-with-a-deployment-package).


The binary files are now deployed according to the defined configuration and environment – either to Azure Blob storage or to local storage.
## Azure Blob storage for private cloud deployments
To map parts of the file system to Azure Blob storage when deploying to [private cloud](documentation/developers-and-admins/deployment/deploy-to-private-cloud):
  1. Follow these recommendations:
    1. **Use separate[Azure Blob storage containers](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-portal) for each deployment environment for the same project (for example, production and testing environments)**. Such environments often contain identically named files that overwrite each other. To avoid collisions, use the `CustomRootPath` property to map folders for each environment to a different container.
    2. **Use HTTPS to connect to Azure Blob Storage accounts** (this behavior can be enabled via the [Security transfer required](https://docs.microsoft.com/en-us/azure/storage/common/storage-require-secure-transfer) setting available in the [Azure portal](https://portal.azure.com)). Xperience is by default configured to use HTTPS with Azure Blob Storage. 
       * If you need to use HTTP (for example because of storage accounts supporting only unencrypted (HTTP) connections), include the [CMSAzureBlobEndPoint](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#optional-application-settings-for-azure-storage) configuration key in your application’s configuration file. Set the value of the key to the full endpoint URL of the external storage account and explicitly specify the _HTTP_ protocol:
JSON
**appsettings.json**
Copy
```
{
...

    "CMSAzureBlobEndPoint": "http://_StorageAccountName_.blob.core.windows.net"
}
```

  2. Choose your authentication method. See [Authentication methods for Azure Blob storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#authentication-methods-for-azure-blob-storage) for detailed configuration options.
  3. [Add a new Class Library](documentation/developers-and-admins/customization/integrate-custom-code) to your project and add the **Kentico.Xperience.AzureStorage** NuGet package as a dependency.
  4. Create a [custom module class](documentation/developers-and-admins/customization/run-code-on-application-startup) in the created library.
  5. Override the module’s `OnInit` method and for each folder that you want to store in the blob storage:
    1. Create a new instance of the Azure storage provider.
    2. _(Optional)_ Specify the target container using the `CustomRootPath` property of the provider.
    3. _(Optional)_ You can specify whether you want the container to be publicly accessible using the `PublicExternalFolderObject` property of the provider. `True` means the container is publicly accessible.
    4. Map the folder to the provider:
As some deployment environments don’t provide a persistent file system, we recommend mapping the **~/assets/** project folder to prevent possible loss of files due to redeployment, swapping of slots, etc. 
C#
**Mapping assets to self-managed Azure storage**
Copy
```
/* The following code snippet demonstrates the recommended mappings
for the ~/assets/ folder mentioned in the note above.
You can use the same approach to also map other folders from your project's file system. */

using CMS;
using CMS.DataEngine;
using CMS.IO;

using Kentico.Xperience.AzureStorage;

// Registers the custom module into the system
[assembly: RegisterModule(typeof(CustomInitializationModule))]

public class CustomInitializationModule : Module
{
    // Module class constructor, the system registers the module under the name "CustomInit"
    public CustomInitializationModule()
        : base("CustomInit")
    {
    }

    // Contains initialization code that is executed when the application starts
    protected override void OnInit()
    {
        base.OnInit();

        // Creates a new StorageProvider instance for Azure Blob storage
        var assetsProvider = AzureStorageProvider.Create();

        // Specifies the target container, the provider ensures its existence in the storage account
        assetsProvider.CustomRootPath = "myassetscontainer";

        // Makes the 'myassetscontainer' container publicly accessible
        assetsProvider.PublicExternalFolderObject = true;

        // Maps the local directory to the storage provider
        StorageHelper.MapStoragePath("~/assets", assetsProvider);
    }
}
```

  6. _(Optional)_ Set [Optional application settings for Azure storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#optional-application-settings-for-azure-storage).


The application deployed in the Azure instance now stores files from the **~/assets** project folder in the **myassetscontainer** Azure Blob storage container.
### Optional application settings for Azure storage
The optional application settings are applicable only for [private cloud deployments](documentation/developers-and-admins/deployment/deploy-to-private-cloud).
Key |  Description  
---|---  
CMSAzureTempPath |  The system uses the specified folder to store temporary files on a local disk, for example when transferring large files to or from the storage account. If not set, the system creates and uses an **~/AzureTemp** directory in the project’s root. JSON **Sample value** Copy ```
"CMSAzureTempPath": "C:\\AzureTemp"
```
  
CMSAzureCachePath |  Specifies a folder on a local disk where files requested from the storage account are cached. This helps minimize the amount of blob storage operations, which saves time and resources. If not set, the system creates and uses an **~/AzureCache** directory in the project’s root. JSON **Sample value** Copy ```
"CMSAzureCachePath": "C:\\AzureCache"
```
  
CMSAzureBlobEndPoint |  Sets the endpoint used for the connection to the blob service of the specified storage account. If you wish to use the default endpoint, remove the setting completely from the appropriate files. JSON **Sample value** Copy ```
"CMSAzureBlobEndPoint": "http://127.0.0.1:10000/devaccount"
```
  
CMSAzurePublicContainer |  Indicates if the blob container used to store the application’s files is public. If true, it will be possible to access files directly through the URL of the appropriate blob service, for example: **https:// <StorageAccountName>.blob.core.windows.net/media/imagelibrary/logo.png** JSON **Sample value** Copy ```
"CMSAzurePublicContainer": true
```
  
CMSDownloadBlobTimeout |  Specifies the timeout interval in minutes for importing files from Azure Blob storage into Xperience. The default is **1.5 minutes**. Increase the interval if you encounter problems when importing large files (2GB+). JSON **Sample value** Copy ```
"CMSDownloadBlobTimeout": "5"
```
  
## Authentication methods for Azure Blob storage
Xperience supports two authentication methods for Azure Blob storage connections:
  * **Shared key authentication** – Uses the storage account name and access key.
  * **Managed identity authentication** – Uses Microsoft Entra authentication with [managed identities](https://learn.microsoft.com/en-us/entra/identity/managed-identities-azure-resources/overview) . This is the recommended approach for enhanced security as it eliminates the need to store sensitive access keys in your application configuration.


**Managed identity benefits**
Managed identity authentication provides several security advantages:
  * No need to store storage account keys in your application configuration
  * Automatic credential rotation handled by Azure
  * Fine-grained access control using Azure RBAC (Role-Based Access Control)
  * Reduced risk of credential exposure


### Shared key authentication
Specify the storage account name and primary access key in your application configuration file (_appsettings.json_ by default).
  1. Open the [Azure Management Portal](https://portal.azure.com).
  2. Open **Storage accounts**.
  3. Select your storage.
  4. Switch to the **Access keys** tab.
  5. Use the **Storage account name** and one of the provided access key values.
JSON
**appsettings.json**
Copy
```
{
    "CMSAzureAccountName": "StorageAccountName",
    "CMSAzureSharedKey": "PrimaryAccessKey"
}
```



### Managed identity authentication
Configure managed identity authentication using Microsoft Entra ID. This method provides enhanced security by eliminating the need to store access keys.
  1. Set up managed identity in Azure:
    1. Enable managed identity for your Azure resources. See the [Azure managed identities documentation](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/).
    2. Grant the managed identity appropriate roles and permissions to your storage account:
       * Role: **Storage Blob Data Owner**
       * This [built-in role](https://learn.microsoft.com/en-us/azure/role-based-access-control/built-in-roles/storage#storage-blob-data-owner) includes all necessary permissions for Xperience Azure Storage operations.
       * **_—OR—_**
       * Role: **Storage Blob Data Contributor**
       * With additional permissions: 
         * `Microsoft.Storage/storageAccounts/blobServices/containers/getAcl/action` ([Get Container ACL](https://learn.microsoft.com/en-us/rest/api/storageservices/get-container-acl))
         * `Microsoft.Storage/storageAccounts/blobServices/containers/setAcl/action` ([Set Container ACL](https://learn.microsoft.com/en-us/rest/api/storageservices/set-container-acl))
       * The [Storage Blob Data Contributor role](https://learn.microsoft.com/en-us/azure/role-based-access-control/built-in-roles/storage#storage-blob-data-contributor) provides blob read/write access but requires additional container ACL permissions for full functionality. Use this configuration to enforce the principle of least privilege.
  2. Configure storage account details in your application configuration:
JSON
**appsettings.json**
Copy
```
{
    "CMSAzureAccountName": "StorageAccountName"
}
```

  3. Configure managed identity authentication in your application startup:
C#
**Program.cs**
Copy
```
using Azure.Identity;
using Kentico.Xperience.AzureStorage;

var builder = WebApplication.CreateBuilder(args);

// Configure managed identity for Azure Storage
builder.Services.AddAzureStorageCredential(serviceProvider =>
{
    // For user-assigned managed identity, specify the client ID
    return new ManagedIdentityCredential("your-user-assigned-identity-client-id");

    // For system-assigned managed identity, use default constructor.
    // Note that for system-assigned identitites to work the connected
    // application must be deployed in Azure as well.
    return new ManagedIdentityCredential();

    // For DEVELOPMENT environments, you can also return
    // DefaultAzureCredential for automatic credential discovery.
    // See the Azure documentation for details.
    return new DefaultAzureCredential();
});

// ...
```