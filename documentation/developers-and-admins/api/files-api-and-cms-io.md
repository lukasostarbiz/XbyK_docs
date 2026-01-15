# Files API and CMS.IO
  * [ Copy page link ](documentation/developers-and-admins/api/files-api-and-cms-io#) | [Get HelpService ID](documentation/developers-and-admins/api/files-api-and-cms-io#)
Core MVC 5


[✖](documentation/developers-and-admins/api/files-api-and-cms-io# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/api/files-api-and-cms-io#)
**CMS.IO** is a namespace that serves as an interlayer between the business layer of Xperience and storage used for physical files. `CMS.IO` is used throughout the system instead of the default `System.IO` library provided by .NET in order to be compatible with various types of storage systems.
`CMS.IO` contains abstractions that access file storage by means of a provider object. Depending on the storage type that you use for your files, the system utilizes one of the following providers:
  * **File system storage** – used by default for files stored in the Windows file system. The provider is a wrapper for the standard `System.IO` library.
  * **Azure storage** – used when storing files in [Azure Blob storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage).
  * **Amazon storage** – used when storing files in the [Amazon S3](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3) storage service.


The following diagram demonstrates how `CMS.IO` accesses different types of file storage via provider objects:
[![CMS.IO abstraction layer](docsassets/documentation/files-api-and-cms-io/CMS_IO_Diagram.png)](https://docs.kentico.com/docsassets/documentation/files-api-and-cms-io/CMS_IO_Diagram.png)
This documentation assumes that you are familiar with the `System.IO` library and know how to use it to manipulate files and directories. [Learn how to use System.IO](https://docs.microsoft.com/en-us/dotnet/api/system.io).
## CMS.IO
Working with `CMS.IO` is in most aspects the same as working with `System.IO`. We recommend using `CMS.IO` instead of `System.IO` in your code so that your custom code doesn’t depend on a single file system type.
## Similarities between CMS.IO and System.IO
### Classes
  * Directory
  * DirectoryInfo
  * File
  * FileInfo
  * FileStream
  * Path
  * StreamReader
  * StreamWriter
  * StringReader
  * StringWriter


### Enumerations
  * FileAccess
  * FileAttributes
  * FileMode
  * FileShare
  * SearchOption


**Other classes and enumerations**
For classes and enumerations not implemented by `CMS.IO`, we recommend using the standard implementations from the `System.IO` namespace (for example [System.IO.Stream](https://msdn.microsoft.com/en-us/library/system.io.stream\(v=vs.110\).aspx)). Xperience expects these types across its API.
## Differences between CMS.IO and System.IO
The most significant difference is in the creation of new instances of objects. Instead of a constructor, each class contains a `New` method, which accepts the same parameters as the class’ constructor.
The following example shows how to write text into a file. You can see that the instance of the FileInfo class is created using the `New` method. Note that the `StreamWriter` class used in the example is also a member of `CMS.IO`, not `System.IO`.
C#
Copy
```
using CMS.IO;

...

FileInfo fi = FileInfo.New("MyFile.txt");

using (StreamWriter sw = fi.CreateText())
{
    sw.WriteLine("Hello world!");
}
```

There are a number of types that can be found in `System.IO`, but are not implemented in `CMS.IO`. These include seldom used classes, class members, and methods. Additionally, `CMS.IO` doesn’t define exceptions. You need to use exceptions from `System.IO`, or implement custom ones.
### Helper methods
`CMS.IO` contains additional methods and properties which simplify operations with files and directories. The following list describes the most useful methods:
#### DirectoryHelper class
  * **void DeleteDirectoryStructure(string path)** – deletes the directory specified by the path parameter and all subdirectories.
  * **void EnsureDiskPath(string path, string startingPath)** – checks whether all directories between `startingPath` and `path` exist and creates them if necessary.
  * **void EnsurePathBackslash(string path)** – adds a backslash to the end of the path, if missing.