---
source: https://docs.kentico.com/documentation/developers-and-admins/ci-cd/continuous-integration
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [CI/CD](/documentation/developers-and-admins/ci-cd)
  * Continuous Integration 


# Continuous Integration
Xperience provides a [Continuous Integration](https://en.wikipedia.org/wiki/Continuous_integration) feature (CI) that allows you to serialize the data of objects from the database into XML files on the file system. You can then add the files to a [source control system](https://en.wikipedia.org/wiki/Revision_control) (for example [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/server/) or [Git](https://git-scm.com/)) and use them to synchronize database data between team members. The system ensures that the XML data of matching objects is always identical and consistent (including element and attribute order), even when serialized on different instances of Xperience.
The system stores the XML files containing the serialized data of objects in the project’s **App_Data\CIRepository** folder.
Continuous Integration actions are performed via the [.NET command-line interface](https://docs.microsoft.com/en-us/dotnet/core/tools/) (included with the .NET SDK).
## Continuous Integration overview
CI is usable in environments with **multiple development instances** (each with its own project files and database) and a **source control system** that provides change management and version control for files.
To start developing projects using CI:
  1. Set up your development environment and enable CI.
     * [Prepare the development environment](#prepare-the-development-environment)
     * [Exclude objects from CI/CD](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories)
  2. Establish a process for transferring objects back into the database from the repository XML files.
     * [Restore repository files to the database](#restore-repository-files-to-the-database)
     * Synchronize database changes not managed by CI using [migration scripts](/documentation/developers-and-admins/ci-cd/ci-cd-database-migration-scripts).
  3. Begin development.


### Recommended usage and limitations
Continuous Integration is designed to allow synchronization and version control of database data **_during development_**.
CI has a **_negative impact on the application’s performance_**. The impact is not significant for development instances with a low number of users, but the feature should **_not be enabled on production deployments_**.
CI only tracks object changes made through the administration interface or API. Changes made directly in the database or by external tools that modify the database are not tracked. After making such changes, you need to manually serialize all objects to synchronize the database with the CI repository.
You **_cannot_** reliably use CI in environments where the repository folder is located on a **_shared location_** (multiple Xperience instances with separate databases connected to the same file repository). Common examples of shared file systems are external storage providers, such as Microsoft Azure Blob storage or Amazon S3.
CI does not support all types of data available in Xperience. Certain object types represent live production data or are otherwise strictly related to specific Xperience instances. Such data is not included in CI, because synchronization in a development environment is not required or intended. For example, contacts representing visitors or data records submitted via forms are not supported by CI. See [Reference - CI/CD object types](/documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types) for detailed information.
## Prepare the development environment
Before you can use Continuous Integration, you need to set up Xperience instances for your development team.
Start by preparing an initial instance:
  1. If using Git for Windows as your source control solution, consider enabling long paths support: see [Long paths support in Git for Windows](#long-paths-support-in-git-for-windows).
  2. Either [install](/documentation/developers-and-admins/installation) a new instance of Xperience or select an existing instance as a starting point.
  3. Enable CI on the given instance (see [Enable Continuous Integration](#enable-continuous-integration) for details).
  4. Create a backup of the instance’s database.
  5. Add the **_entire_** Xperience solution to your source control (adding only the CI repositoryfolder may lead to problems and is not recommended).


**Source control ignore rules**
Certain source control systems may have ignore rules, such as _gitignore_ , which can unintentionally exclude files or folders used in the CI/CD repository (for example, the _*.class_ and _*.user_ file and folder extensions).
Such rules may prevent your environment from working correctly. We recommend that you evaluate ignore rules for the repository and disable them as required.
To add development instances into your environment, perform the following steps for each instance:
  1. Connect to the source control from the development machine and load the latest version of the solution files onto the local file system. 
     * Decide how to handle source control for the project’s configuration file (_appsettings.json_ by default). Each development machine needs to have a connection string to a different database, but you may want to synchronize other parts of the configuration.
  2. Create (restore) a copy of the initial database on your SQL server.
  3. Connect the instance to the new copy of the database by setting the appropriate connection string in the project’s configuration file.


After you complete the process, all development machines will contain their own Xperience project files connected to a separate copy of the database. You can start using your source control system to synchronize project files between the instances. CI serializes database data onto the file system and you can include it in your source control, just like any other files.
The process described above ensures that objects have identical GUID values (globally unique identifiers) across your entire development environment. If you perform a separate database installation for each development instance, you will need to manually resolve a large number of GUID conflicts when synchronizing data through CI.
If you need to add further instances later after development with CI starts, use the same process. However, you need to make sure the database is synchronized with the rest of the development environment. Use one of the following approaches:
  * Maintain a central database connected to the mainline source control and always create an up-to-date backup for new developers.  
– OR –
  * Use the original database backup and then get the latest data:
    1. Restore the current object status from the CI repository into the database. See [Restore repository files to the database](#restore-repository-files-to-the-database).
    2. Manually transfer changes made to the data of any object types not supported by CI (maintain documentation of such changes).


### Enable Continuous Integration
To start using Continuous Integration on an Xperience instance:
  1. Open the **Settings** application in the administration interface.
  2. Navigate to the **Synchronization →** **Continuous Integration**.
  3. Select the **Enable Continuous Integration** checkbox.
  4. **Save** the changes.
  5. [Store all object data to the Continuous Integration repository](#store-object-data-to-the-repository).


The project’s **App_Data\CIRepository** folder now contains XML files storing the serialized data of all supported objects from the database. The system also tracks create, update and delete operations for the given objects and automatically transfers the changes to the serialized data on the file system.
### Configure instances to synchronize macros
To ensure that [macro expressions](/documentation/developers-and-admins/configuration/macro-expressions) work correctly when synchronizing objects using Continuous Integration, all development instances must use the same hash salt value.
If your development instances do not all start with the same configuration file (_appsettings.json_ by default), you need to set a matching value for the `CMSHashStringSalt` key on all instances. The best option is the hash salt from the starting instance that you used to create your initial database backup. You can use any string as the value, but the salt should be random and at least 16 characters long. For example, a randomly generated [GUID](https://en.wikipedia.org/wiki/GUID) is a strong salt:
JSON
Copy
```
"CMSHashStringSalt": "e68b9ad6-a461-4707-8e3e-ece73f03dd02",
```

The salt value is used as part of the input for the hash function that creates the security [signatures of macros](/documentation/developers-and-admins/configuration/macro-expressions/macro-signatures). Having the same hash salt value on all instances is necessary to ensure that macro signatures are valid when transferring data between instances.
The best option is to set the hash salt value before you start development. Changing the salt causes all current hash values to become invalid.
### Long paths support in Git for Windows
Due to certain limitations present in Windows APIs, Git for Windows cannot by default work with paths longer than 260 characters.
The CI/CD solution names filesystem objects using database entity names as defined in Xperience. Depending on your naming conventions, you may encounter objects whose filesystem path to the serialized representation exceeds the 260 character limit.
Git for Windows will have issues working with such files and it will be unable to recreate the affected files for other users cloning the repository. To correct this behavior, all developers must deliberately opt-in for long paths support via the following command, which enables long paths support in all local git repositories:
CMD
**Enable core.longpaths support**
Copy
```
git config --global core.longpaths true
```

Note that this configuration is distinct from the [LongPathsEnabled](https://learn.microsoft.com/en-us/windows/win32/fileio/maximum-file-path-limitation?tabs=registry#registry-setting-to-enable-long-paths) Windows registry key, which enables long paths support across the entire system.
## Store object data to the repository
Use the following steps to store all supported objects from the database to the Continuous Integration repository:
  1. Open the command line prompt.
  2. Navigate to your Xperience project’s root directory.
  3. Use [dotnet run](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-run) to execute the `--kxp-ci-store` command:
     * If your project is up-to-date and rebuilt, run the command with the `--no-build` parameter. This option saves time and avoids an unnecessary project build before the store operation.
CMD
Copy
```
dotnet run --no-build --kxp-ci-store
```



When the process completes, the CI repository contains XML files with the serialized data of all supported objects from the database. Objects are stored from the database specified by the `CMSConnectionString` in the application’s configuration file (_appsettings.json_ by default).
## Restore repository files to the database
The restore action **_deletes_** any supported objects in the database that do not exist as files in the Continuous Integration repository. Before you run the restore process, make sure the repository contains the required state of your object data.
To avoid loss of local data, you need to maintain the file system repository as a full image of your database – serialize all objects to the file system at the start of development and leave CI enabled. You then need to merge the file content of the repository when loading data from another team member or the central source control.
To ensure that the restore process works correctly, you need to **_stop your Xperience application before running the restore process_**. If you always rebuild your solution before restoring, the application stops and starts automatically.
If the application runs during the restore action, you may encounter the following problems:
  * Deadlocks or data inconsistencies if changes occur while data is being restored from the files
  * Outdated content in the application’s cache if you restore without restarting (can cause inconsistencies in displayed content or the administration)


Perform the following steps to transfer objects from the CI repository to the database:
  1. Open the command line prompt.
  2. Navigate to your Xperience project’s root directory.
  3. Use [dotnet run](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-run) to execute the `--kxp-ci-restore` command:
CMD
Copy
```
dotnet run --kxp-ci-restore
```



The action deserializes the objects stored in the CI repository and creates, overwrites or removes corresponding data in the database. Objects are restored to the database specified by the `CMSConnectionString` in the application’s configuration file (_appsettings.json_ by default).
## CI development best practices
This section contains best practices and tips for developing projects with a source control and the Xperience Continuous Integration feature.
### Track create and delete operations
When you add a new object or delete an existing one in Xperience, the system creates or removes the corresponding XML files. You may need to perform additional actions to correctly track such changes, depending on the type of your source control repository.
For example with **Git** , files representing new objects appear as _Untracked_ changes. You need to _Add_ the files before you can _Commit_ and _Push_ them to a shared repository.
### Object identifiers
Identifiers are values that CI uses to determine the names and locations of files representing objects in the repository folder:
  * In most cases, objects use their **code name** as the identifier.
  * Object types that do not have a suitable code name use other values (a notable example are pages, which use their _alias path_ as the primary identifier).
  * Identifiers may also include values of related objects, such as the code name of the parent object, the name of the related channel, etc.


Avoid making changes to object identifiers if possible, particularly for objects that have a large number of child objects or affect the identity of other objects (such as channels). This also includes operations that change the identifier indirectly, for example moving a child object under a different parent.
When an object’s identifier changes, CI **_deletes_** the file representing the object in the file system and creates a new one based on the new identifier. This causes a **_loss of the file’s version history_** in the source control and makes it difficult to merge changes made to the same object by other developers.
### Commit changes
When committing changes to your source control, we strongly recommend including **all** detected file changes in the CI repository folder. It may not be obvious which files correspond to specific changes made in the Xperience administration. Certain objects are represented by multiple files and changes may also affect related objects or relationships between objects.
To learn more about how objects are represented, see the following pages:
  * [CI/CD repository structure](/documentation/developers-and-admins/ci-cd/ci-cd-repository-structure)
  * [Reference - CI/CD object types](/documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types)


### Undo changes
We do not recommend undoing changes that create new CI repository files. Undoing changes may lead to inconsistencies between the state of the file system repository and the database. You can instead delete the given object in the Xperience administration, which also makes the corresponding changes in the file system.
If you undo a delete or modify operation and revert an XML file to its previous state, you then need to [restore the objects into the database](#restore-repository-files-to-the-database) to reverse the object changes in Xperience.
### Get new versions of the repository content
Whenever you load (pull) a different version of any files in the CI repository folder from your source control, you need to:
  1. Merge any files that conflict with your instance’s local versions. See also: [Resolving object conflicts](#resolve-conflicts)
  2. [Restore the objects into your database](#restore-repository-files-to-the-database).


The restore operation ensures that your instance’s database is consistent with the file system repository. If you continue working on the instance without performing the restore operation, changes to local data may overwrite the new versions of the files that you loaded from the source control.
### Resolve conflicts
You may encounter object conflicts when pulling or committing CI repository files. Conflicts happen if your local folder and the opposing folder contain a file with the same name, but different content. File names are based on unique identifiers of objects (in most cases the object’s code name).
**Planned conflicts** can occur if you need to create **_the same object_** across your entire development environment. For example, multiple developers may create a website channel with the same name when starting development. Even if the objects have completely identical values for all configurable properties, a conflict will occur during synchronization, because the object’s GUID (globally unique identifier) value is different for each developer.
The best solution is to have one developer create the object and commit the resulting XML files to the central source control. Other developers then load the XML files from the source control and [restore the object](#restore-repository-files-to-the-database) to their local database. This approach completely avoids object conflicts and file merging.
If you cannot avoid an object conflict and need to merge XML files, always use the GUID value that is already committed to the source control and replace your local GUID. Do NOT push your local GUID values to the source control – this could cause conflicts for all other developers in your environment.
**Note** :
  * The XML elements and attributes used in the CI repository are **_case sensitive_**. Make sure you preserve the letter case if you edit the XML content when merging files.
  * If you change an object’s GUID during conflict resolution and then [restore the object](#restore-repository-files-to-the-database) to your database, you may need to manually fix broken references on your local development instance. For example, object and page fields may reference files via the GUID of the selected file.


**Unplanned conflicts** occur if developers unintentionally create **_different objects_** of the same type with matching identifiers (typically code names) and then synchronize through the source control. In such cases, you need to contact the developer who committed the conflicting XML file and one of you must delete and recreate the object with a different identifier (or rename the identifier values). We strongly recommend changing the local object that is not committed to the source control yet, which prevents potential conflicts for other developers.
### Delete and recreate objects
If you delete an object and then create a new object of the same type with an identical [identifier](#object-identifiers), you may encounter errors when restoring the CI data. For example, such problems can occur if you delete a _/Home_ page, and then create a new /_Home_ page with a different content type.
The CI restore operation deletes objects only if an XML file with a matching identifier is not found in the CI repository. This means that the system cannot differentiate between “delete and recreate” actions and standard update actions. Errors can occur when restoring “recreated” objects, because the original object is not properly deleted together with its dependencies (child objects or bindings, other referenced objects).
To work around this problem, run the CI restore in multiple steps:
  1. Delete the object.
  2. Commit the changes to your source control and inform other developers to pull and restore the CI data.
  3. Recreate the object once the delete action is properly shared across your entire development environment.
  4. Commit your changes again.


## Update projects that use Continuous Integration
If you need to apply an [update](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) to an instance that has CI enabled, use the following procedure:
  1. [Restore objects from the Continuous Integration repository to your database](#restore-repository-files-to-the-database).
You can skip the restore if you are sure that your database is synchronized with the current status of your repository folder.
  2. Disable CI (e.g., using the [settings](#enable-continuous-integration) in the Xperience administration).
  3. Apply the update.
  4. Re-enable CI.
  5. If you use the `<IncludedObjectTypes>` element in your CI _repository.config_ file to [filter object types](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories), check [Reference - CI/CD object types](/documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types) for any new object types introduced by the update. Add any new object types that you wish to include in CI.
  6. [Store object data to the repository](#store-object-data-to-the-repository) to recreate the serialized content.


This approach ensures that your CI repository folder contains all object changes made by the update and that the serialization process itself runs according to the new version.
You need to apply the update separately for each development instance. After one developer commits the new changes to the source control, other developers CANNOT commit or load changes until they apply the update to their own instance.
For major updates, it may be more efficient to only update one instance and then set up your environment again according to the process described in [Prepare the development environment](#prepare-the-development-environment).
## Custom repository location
After you set up and enable CI, the system serializes database data into XML and stores the results on the file system. By default, the files are created in the project’s **App_Data\CIRepository** folder.
If your development environment requires a different location for the CI repository, set the `CMSCIRepositoryPath` key in your project’s configuration file (_appsettings.json_ by default). The key’s value must be an absolute or relative path of a folder on a local drive, or a network location for which the application has sufficient permissions, for example:
JSON
Copy
```
"CMSCIRepositoryPath": "C:\\ExternalSourceControl\\CIRepository",
```

When serializing object data to the file system or [restoring data](#restore-repository-files-to-the-database) back to the database, the system now uses the specified folder as the root of the CI repository. You can add the folder into your source control system.
You can use the key to set a shared CI repository.
**Recommended repository location**
We recommend keeping your CI repository in your Xperience project’s _App_Data_ folder and having the entire solution included within your source control. This setup allows you to easily synchronize all types of related project files together with the database data tracked by CI.
If you decide to change the CI repository location, do NOT use the new folder as the root of your overall source control repository. Always add it as a subfolder under a different root folder. CI **deletes all folders** within the repository when storing all objects. If the folder is the root of your source control repository, you may encounter errors or other problems when using source control systems that utilize custom files or folders in the root.
## Character encoding for repository files
By default, Continuous Integration generates non-binary files in the repository folder using [UTF-8](https://en.wikipedia.org/wiki/UTF-8) character encoding.
If you need to use a different encoding type for the repository files (for example due to requirements of a file comparison tool), add the `CMSCIEncoding` key to your project’s configuration file (_appsettings.json_ by default). For example:
JSON
Copy
```
"CMSCIEncoding": "utf-16",
```

The `CMSCIEncoding` key supports values matching the encoding names listed in the [Encoding Class](https://docs.microsoft.com/en-us/dotnet/api/system.text.encoding) article.
Changing the value of the `CMSCIEncoding` key does not update the encoding type of existing files in the CI repository folder. To fully update the encoding of the repository content, you need to:
  * Run complete serialization for all objects.
  * Manually update the encoding type of your _repository.config_ file.


![]()
[]()[]()
