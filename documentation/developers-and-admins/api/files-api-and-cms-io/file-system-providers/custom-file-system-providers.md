---
source: https://docs.kentico.com/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/custom-file-system-providers
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [API](/documentation/developers-and-admins/api)
  * [Files API and CMS.IO](/documentation/developers-and-admins/api/files-api-and-cms-io)
  * [File system providers](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers)
  * Custom file system providers 


# Custom file system providers
The `CMS.IO` library allows you to customize Xperience to support a file system of your choice. As described in [Files API and CMS.IO](/documentation/developers-and-admins/api/files-api-and-cms-io), you can achieve this by developing a custom provider based on the classes contained within `CMS.IO`.
## Preparation
To implement a custom file system provider, you need to add a new assembly to the Xperience solution:
  1. Open your solution in Visual Studio.
  2. [Add a new Class Library project](/documentation/developers-and-admins/customization/integrate-custom-code).


## Implementation
To create a file system provider, implement the following classes:
  1. Create two separate classes that inherit from the following abstract classes:
     * CMS.IO.AbstractDirectory
     * CMS.IO.AbstractFile
  2. Implement all methods defined in the abstract classes.
  3. Create three other classes that inherit from the following classes:
     * CMS.IO.DirectoryInfo
     * CMS.IO.FileInfo
     * CMS.IO.FileStream
  4. Override all methods and properties from those classes.
  5. Create constructors for the classes listed in step 3 according to the following table:
Inherits from  |  Constructors   
---|---  
CMS.IO.DirectoryInfo  |  public DirectoryInfo(string path)   
CMS.IO.FileInfo  |  public FileInfo(string filename)   
CMS.IO.FileStream  |  public FileStream(string path, CMS.IO.FileMode mode)  public FileStream(string path, CMS.IO.FileMode mode, CMS.IO.FileAccess access)  public FileStream(string path, CMS.IO.FileMode mode, CMS.IO.FileAccess access, CMS.IO.FileShare share)  public FileStream(string path, CMS.IO.FileMode mode, CMS.IO.FileAccess access, CMS.IO.FileShare share, int bSize)   


**Note** : The custom file system provider classes must be placed into a namespace that **exactly** matches the name of the given assembly.
## Configuration
Perform the following configuration steps to start using your custom file system provider:
  1. Add the **CMSStorageProviderAssembly** application setting to your project. Set the key’s value to the assembly name of your custom provider.
  2. Configure the project to use the provider. 
     * Use the API to create an instance of the `CMS.IO.StorageProvider` class and [map specific project folders](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers) to your custom provider.


### Example
The following code registers a module that maps the content items folder to a custom storage provider:
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

        // Creates a new StorageProvider instance using the custom 'CustomFileSystemProvider' assembly
        var customContentItemAssetProvider = new StorageProvider("custom", "CustomFileSystemProvider");

        // Maps a directory to the provider
        StorageHelper.MapStoragePath("~/assets/contentitems/", customContentItemAssetProvider);
    }
}
```

To create an instance of the `StorageProvider` class for your custom file system provider, call the constructor with the following parameters:
  1. The provider’s external storage name. Can be an empty string for providers that use the local file system. If you are using [Azure Blob storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) or [Amazon S3](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3) providers in your project, the _Azure_ or _Amazon_ names are reserved by the corresponding providers.
  2. The name of the class library containing the provider’s implementation.
  3. (Optional) A _bool_ parameter that specifies whether the storage is shared.


For more information about storage provider mapping options, see [File system providers](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers).
![]()
[]()[]()
