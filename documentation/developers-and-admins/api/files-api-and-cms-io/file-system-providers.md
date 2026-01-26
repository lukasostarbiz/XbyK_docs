---
source: https://docs.kentico.com/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [API](/documentation/developers-and-admins/api)
  * [Files API and CMS.IO](/documentation/developers-and-admins/api/files-api-and-cms-io)
  * File system providers 


# File system providers
File system providers introduce an abstraction layer over the underlying file system of the hosting machine. You can use different providers to access specific parts of the application’s file system and change the default locations of different types of files.
Use the `CMS.IO` API and the following general process:
  1. Open the Xperience solution in Visual Studio.
  2. Create a [custom module class](/documentation/developers-and-admins/customization/run-code-on-application-startup).
  3. Override the module’s `OnInit` method and perform the required `StorageProvider` modifications. See the following use cases:
    1. [Change content file paths to a shared storage](#change-content-file-paths-to-a-shared-storage)
    2. [Change content file paths to a different disk drive](#change-content-file-paths-to-a-different-disk-drive)


## Change content file paths to a shared storage
The system allows you to change default file paths to a shared storage directory on a specific server. This is important in environments with [multiple application instances](/documentation/developers-and-admins/configuration/auto-scaling-support). For example, shared storage is a requirement for all [scaled](/documentation/developers-and-admins/configuration/auto-scaling-support) Xperience deployments.
  1. Edit the custom project holding your storage provider logic in Visual Studio (see the general instructions at the start of this page).
  2. Do the following in your module’s `OnInit` method: 
    1. Create a new instance of the `StorageProvider` class (the `isSharedStorage` parameter of the `CreateFileSystemStorageProvider` method must be `true`).
    2. Set the target directory via the provider’s `CustomRootPath` property. The provider creates new files and folders relative to the given root.
    3. Map a directory from your project to the provider.


C#
Copy
```
using CMS;
using CMS.Base;
using CMS.DataEngine;
using CMS.IO;

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

        // Creates a new StorageProvider instance for regular file systems (NTFS, VFAT)
        var sharedMediaProvider = StorageProvider.CreateFileSystemStorageProvider(isSharedStorage : true);

        // Specifies the target root directory. The provider creates the relative path of the mapped folders within the given directory.
        sharedMediaProvider.CustomRootPath = @"\\Server\CustomMediaRoot";

        // Maps a directory from your project to the provider
        StorageHelper.MapStoragePath("~/assets/media/", sharedMediaProvider);
    }
}
```

Every time the application requests or creates a file in the **~/assets/media** directory, the location specified in the `CustomRootPath` property is used instead. In this example, the target folder is **\\\Server\CustomMediaRoot\assets\media**.
Using this configuration, all application instances can access the shared storage (the default location for media libraries is **~/assets/media/ <MediaLibraryName>**, for content item assets **~/assets/contentitems**).
**Azure Blob and Amazon S3 cloud storage**
Xperience also provides built-in support for mapping the contents of specific folders to [Azure Blob](https://azure.microsoft.com/en-us/services/storage/blobs/) or [Amazon S3](https://aws.amazon.com/s3/) cloud-based storage.
For more information, see [Azure Blob storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) and [Amazon S3](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3).
## Change content file paths to a different disk drive
The `CMS.IO` API allows you to change default file paths. For example, you can store media files and assets on a different disk drive.
  1. Edit the custom project holding your storage provider logic in Visual Studio (see the general instructions at the start of this page).
  2. Do the following in your module’s OnInit method:
    1. Create a new instance of the `StorageProvider` class.
    2. Specify the target directory via the `CustomRootPath` property of the provider. This sets the **root** of the target path – the provider creates the relative path of the mapped folders within the given root directory.
    3. Map a directory to the provider.


C#
Copy
```
using CMS;
using CMS.Base;
using CMS.DataEngine;
using CMS.IO;

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

        // Creates a new StorageProvider instance for regular file systems (NTFS, VFAT)
        var mediaProvider = StorageProvider.CreateFileSystemStorageProvider();

        // Specifies the target root directory. The provider creates the relative path of the mapped folders within the given directory.
        mediaProvider.CustomRootPath = @"D:\CustomMediaRoot";

        // Maps a directory to the provider
        StorageHelper.MapStoragePath("~/assets/media/", mediaProvider);
    }
}
```

The code ensures that every time the application requests or creates a file with the **~/assets/media** path, the location specified in the `CustomRootPath` property is used instead. In this example, the target folder is **D:\CustomMediaRoot\assets\media**.
**Azure Blob and Amazon S3 cloud storage**
Xperience also provides built-in support for mapping the contents of specific folders to [Azure Blob](https://azure.microsoft.com/en-us/services/storage/blobs/) or [Amazon S3](https://aws.amazon.com/s3/) cloud-based storage.
For more information, see [Azure Blob storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) and [Amazon S3](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3).
![]()
[]()[]()
