---
source: https://docs.kentico.com/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [API](/documentation/developers-and-admins/api)
  * [Files API and CMS.IO](/documentation/developers-and-admins/api/files-api-and-cms-io)
  * [File system providers](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers)
  * Amazon S3 


# Amazon S3
Xperience by Kentico supports file system providers that allow you to map parts of the file system to [Amazon S3](https://aws.amazon.com/s3/) storage. You can use Amazon S3 when hosting your project in [private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud) or when you wish to store parts of the application file system in external shared storage. For example, shared storage is a requirement for all [scaled](/documentation/developers-and-admins/configuration/auto-scaling-support) Xperience environments.
**Filename case**
Unlike regular file systems (NTFS, VFAT), Amazon S3 storage is case-sensitive. To ensure consistent behavior, Xperience automatically converts all file and folder names to lower case when processing files on Amazon S3.
## Media library files in Amazon S3
**Media libraries sunset**
Media libraries have been officially sunset. Support for media libraries will continue for one more year (until July 24, 2026), after which the feature and all associated APIs will be completely removed.
Before mapping media library files to the [Amazon S3](https://aws.amazon.com/s3/) storage, consider migrating media libraries and mapping content item assets instead. See [Media library migration](/guides/architecture/media-libraries-migration-guidance) for instructions on how to migrate your media library files to [Content hub](/documentation/business-users/content-hub).
Media library files stored in Amazon S3 have the following limitations:
  * **Storing a large number (thousands) of media library files in a single media library can significantly affect the performance and user experience of the[Media libraries](/documentation/business-users/media-libraries/create-media-libraries) application.**
    * We recommend structuring media library files into multiple media libraries and storing at most 100 files in a single media library folder.
  * Mapping subfolders of media libraries is not supported. You can map either the directory containing the media libraries (**~/assets/media**), or individual media libraries (**~/assets/media/ <MediaLibraryName>**).
  * The system’s automatic clearing of files from the server-side cache does not work for files stored in an external storage. If you modify a media file, the website may still display the old version until the cache expires (unless you manually clear the application’s cache). See also: [File caching](/documentation/developers-and-admins/development/caching/file-caching).


## Map files to Amazon storage
Mapping folders to Amazon storage allows you to store files on a shared storage and leverage Amazon’s Cloudfront CDN.
Before you can map parts of the file system to Amazon storage, add the following application settings to your project:
  1. The ID of your Amazon access key:
JSON
**appsettings.json**
Copy
```
"CMSAmazonAccessKeyID": "YourKey"
```

  2. The Amazon access key:
JSON
**appsettings.json**
Copy
```
"CMSAmazonAccessKey": "YourSecret"
```

  3. Add the following key to specify the bucket that you want to use to store files.
JSON
**appsettings.json**
Copy
```
"CMSAmazonBucketName": "YourBucketName"
```



With the Amazon account connected and configured, use the following process to map parts of the file system to the specified bucket:
  1. Open the Xperience project in Visual Studio.
  2. [Add a custom Class Library project](/documentation/developers-and-admins/customization/integrate-custom-code) and install the **Kentico.Xperience.AmazonStorage** NuGet package as a dependency.
  3. Create a [custom module class](/documentation/developers-and-admins/customization/run-code-on-application-startup) in the created library.
  4. Override the module’s `OnInit` method and for each folder that you want to store in the shared storage:
    1. Create a new instance of the Amazon S3 storage provider.
    2. Specify the target bucket using the `CustomRootPath`property of the provider.
    3. _(Optional)_ You can specify whether you want the bucket to be publicly accessible using the `PublicExternalFolderObject` property of the provider. `True` means the bucket is publicly accessible.
    4. Map the directory to the provider:
As some deployment environments don’t provide a persistent file system, we recommend mapping the **~/assets/** project folder to prevent possible loss of files due to redeployment, swapping of slots, etc. 
C#
**Mapping assets to Amazon storage**
Copy
```
/* The following code snippet demonstrates the recommended mappings
for the **~/assets/** folder mentioned in the note above.
You can use the same approach to also map other folders from your project's file system.*/

using CMS;
using CMS.DataEngine;
using CMS.IO;

using Kentico.Xperience.AmazonStorage;

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

        // Creates a new StorageProvider instance for Amazon S3
        var assetsProvider = AmazonStorageProvider.Create();

        // Specifies the target bucket, the provider ensures its existence in the storage account
        assetsProvider.CustomRootPath = "myassetsbucket";

        // Makes the 'myassetscontainer' container publicly accessible
        assetsProvider.PublicExternalFolderObject = true;

        // Maps the local directory to the storage provider
        StorageHelper.MapStoragePath("~/assets", assetsProvider);
    }
}
```



The deployed application now stores files from the **~/assets** project folder in the **myassetsbucket** Amazon S3 bucket.
### Optional application settings for Amazon S3
Key |  Description  
---|---  
CMSAmazonTempPath |  Path to a local directory that the system uses to store temporary files. **Default value** : <project root>\App_Data\AmazonTemp JSON **Sample value** Copy ```
"CMSAmazonTempPath": "C:\\Windows\\Temp"
```
  
CMSAmazonCachePath |  Path to a local directory where the provider stores cached files. **Default value** : <project root>\App_Data\AmazonCache JSON **Sample value** Copy ```
"CMSAmazonCachePath": "C:\\Cache"
```
  
CMSAmazonEndPoint |  Allows you to change the default URL of the Amazon S3 [Website Endpoint](http://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteEndpoints.html). For example, you can change the endpoint if you want to use CloudFront CDN. **Default value** : http://<yourbucketname>.s3.amazonaws.com JSON **Sample value** Copy ```
"CMSAmazonEndPoint": "http://someendpoint.s3.amazonaws.com"
```
  
CMSAmazonRestApiEndPoint |  Allows you to change the default URL of the Amazon S3 [REST API Endpoint](http://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteEndpoints.html#WebsiteRestEndpointDiff). The system uses the REST API to determine the region of buckets. **Default value** : https://s3.amazonaws.com JSON **Sample value** Copy ```
"CMSAmazonRestApiEndPoint": "http://s3.amazonaws.com"
```
  
CMSAmazonPublicAccess |  Specifies whether files uploaded to Amazon S3 through Xperience are accessible for public users. **Default value** : _true_ if you specify an endpoint _false_ If no endpoint is specified JSON **Sample value** Copy ```
"CMSAmazonPublicAccess": true
```
  
## Configuring Xperience to use Amazon CloudFront CDN
A Content Delivery Network (CDN) speeds up distribution of content to the end users through a network of data centers. See [Amazon CloudFront Product Details](https://aws.amazon.com/cloudfront/features/) to learn more.
To start using the Amazon CloudFront service with Xperience:
  1. Create a **CloudFront Distribution**. You can use the [Amazon Management Console](https://console.aws.amazon.com). Select your Amazon S3 storage bucket as the **Origin Domain Name**.
  2. Edit the application settings file of your Xperience project and add the **CMSAmazonPublicAccess** and **CMSAmazonEndPoint** keys:
JSON
**appsettings.json**
Copy
```
"CMSAmazonPublicAccess": true,
"CMSAmazonEndPoint": "EndpointURL"
```

  3. Set the value of the **CMSAmazonEndPoint** key to the **Domain Name** URL of your created CDN.
If your website runs under **HTTPS** , we recommend always specifying the endpoint URL with the **HTTPS** protocol as well.
For example: _https://domain.cloudfront.net_
Without this configuration you may receive [Mixed Content](https://developers.google.com/web/fundamentals/security/prevent-mixed-content/what-is-mixed-content?hl=en) warnings in your browser’s console when retrieving files from the CDN.


Your project now uses the created CDN service.
![]()
[]()[]()
