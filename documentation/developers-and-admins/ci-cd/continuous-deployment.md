---
source: https://docs.kentico.com/documentation/developers-and-admins/ci-cd/continuous-deployment
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [CI/CD](/documentation/developers-and-admins/ci-cd)
  * Continuous Deployment 


# Continuous Deployment
The Continuous Deployment (CD) feature enables you to use Xperience projects in automated development and deployment flows.
Xperience is a standard .NET project, so its code and other files support any compatible deployment process or pipeline. However, deploying and updating content stored in the Xperience database can present more of a challenge.
The Continuous Deployment feature helps address this scenario by converting content and other objects from the database into XML files in a repository on the file system. The CD repository can then be deployed together with the other project files, and restored to the database in the target environment (staging, UAT, production, etc.).
Deployment with CD uses the following general process:
  1. Prepare new projects or changes to existing ones in a development environment.
  2. Configure the development instance to correctly [synchronize macro signatures](#synchronize-macro-signatures) with the target deployment instances.
  3. [Generate a CD configuration file](#generate-cd-configuration-files) and adjust it according to your requirements.
  4. [Store database objects to a CD repository](#store-objects-to-a-cd-repository) using the appropriate configuration file for your deployment.
  5. Run your deployment process or pipeline for the project. [Restore the CD repository](#restore-the-cd-repository-to-the-database) to the target environment as part of the overall process.


Continuous Deployment actions are performed via the [.NET command-line interface](https://docs.microsoft.com/en-us/dotnet/core/tools/) (included with the .NET SDK).
**Continuous Deployment is not a substitute for staging**
Continuous Deployment is intended to be used for initial deployments and basic data manipulation. It should not be used to deploy large volumes of data.
**DO** use CD for:
  * Website/email/headless channels, content types representing the content model, page tree structure, basic emails, essential content items
  * Assets that are essential for the design and branding, such as background images, graphics, and company logos


**DON’T** use CD for:
  * Deploying production data to the live site, such as marketing assets, images, videos, large documents, large amounts of content items


To transfer new and updated content from an editing environment to production, we recommend using the [Content sync](/documentation/business-users/content-sync) feature instead of CD. See [Content sync configuration](/documentation/developers-and-admins/configuration/content-sync-configuration) to learn more about setting up content sync.
When [deploying](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) a large amount of data to the [SaaS environment](/documentation/developers-and-admins/saas), use [custom restore](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-a-large-amount-of-data-during-initial-deployments).
## Generate CD configuration files
Before creating a Continuous Deployment repository for an Xperience project, you need to prepare a configuration file that determines which types of objects are included and which operations are performed when restoring the repository. For detailed information about CD configuration files, see [Exclude objects from CI/CD](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories).
You don’t need to write a new configuration file from nothing. Xperience provides a CLI command that generates a basic CD configuration file:
  1. Open the command line prompt.
  2. Navigate to your Xperience project’s root directory.
  3. Use [dotnet run](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-run) to execute the `--kxp-cd-config` command:
     * We recommend running the command with the `--no-build` parameter. This option saves time and avoids an unnecessary project build.
CMD
Copy
```
dotnet run --no-build -- --kxp-cd-config --path "C:\CD_configs\Production\repository.config"
```

**Command parameter**
The `--kxp-cd-config` command’s `--path` parameter sets the location where the CD configuration file is generated. Must include both the path and file name.
Add the `--` syntax separator before any command parameters that you wish to pass directly to the Xperience application and avoid conflicts with .NET CLI parameters.


The command generates the CD configuration file in the specified location. Adjust the configuration according to your deployment requirements (see [Exclude objects from CI/CD](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories)).
Continue by using the configuration file when you [store objects to a CD repository](#store-objects-to-a-cd-repository).
**CD restore mode**
The repository configuration file **must** contain the `<RestoreMode>` element. The restore mode specifies which types of object operations are performed in the target database when restoring the repository. The restore mode provides restrictive options that allow you to safely deploy to production environments, where you do not wish to delete or overwrite live data.
The following `<RestoreMode>` values are possible:
  * `Create` – only creates new objects. Never deletes or modifies existing objects.
  * `CreateUpdate` – creates new objects and updates existing objects. Never deletes objects. This is the default option for newly generated CD repository configuration files.
  * `Full` – creates new objects, updates existing objects, deletes objects that do not exist in the repository. 
    * **Note** – Using this option with deployment packages containing large amounts of objects or with large amounts of data already present in the system can result in a failed restore.


## Store objects to a CD repository
Use the following steps to store all supported objects from the database to a CD repository:
  1. Open the command line prompt.
  2. Navigate to your Xperience project’s root directory.
  3. Use [dotnet run](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-run) to execute the `--kxp-cd-store` command:
     * If your project is up-to-date and rebuilt, run the command with the `--no-build` parameter. This option saves time and avoids an unnecessary project build before the store operation.
CMD
Copy
```
dotnet run --no-build -- --kxp-cd-store --repository-path "C:\Xperience_Deployments\Production\CDRepository" --config-path "C:\CD_configs\Production\repository.config"
```

**Command parameters**
The `--kxp-cd-store` command has the following parameters: 
       * `--repository-path` – file system path of the CD repository folder, where the object files are stored. You can either create a new repository folder for your deployment or overwrite an existing CD repository.
       * `--config-path` – file system path of the [CD configuration file](#generate-cd-configuration-files), including the file name. Required if storing objects to a new repository folder. When storing to an existing repository, the parameter is optional – if not specified, the system searches for a **repository.config** file in the root of the repository folder. Do not explicitly specify the `--config-path` parameter if you want to use the configuration file in the root of the repository folder.
Add the `--` syntax separator before any command parameters that you wish to pass directly to the Xperience application and avoid conflicts with .NET CLI parameters.


When the process completes, the CD repository contains XML files with the serialized data of objects from the database, based on the specified configuration file. Objects are stored from the database specified by the `CMSConnectionString` in the application’s configuration file (_appsettings.json_ by default).
After the store operation, the root of the CD repository always contains a configuration file named **repository.config** (the content is copied/overwritten from the file provided in the `--config-path` parameter).
The CD repository can then be used to [restore the data](#restore-the-cd-repository-to-the-database) to a target environment’s database.
**Source control ignore rules**
Certain source control systems may have ignore rules, such as _gitignore_ , which can unintentionally exclude files or folders used in the CI/CD repository (for example, the _*.class_ and _*.user_ file and folder extensions).
Such rules may prevent your environment from working correctly. We recommend that you evaluate ignore rules for the repository and disable them as required.
## Synchronize macro signatures
To ensure that [macro expressions](/documentation/developers-and-admins/configuration/macro-expressions) (for example used in [contact group](/documentation/business-users/digital-marketing/contact-groups) conditions) have valid [signatures](/documentation/developers-and-admins/configuration/macro-expressions/macro-signatures) and work correctly after deployment, the source instance from which you create the CD repository must use the same hash salt as the target instance.
You can check and configure the hash salt in the value of the `CMSHashStringSalt` key within the project configuration file (_appsettings.json_ by default). New Xperience projects have a randomly generated [GUID](https://en.wikipedia.org/wiki/GUID) as their hash salt.
JSON
Copy
```
"CMSHashStringSalt": "e68b9ad6-a461-4707-8e3e-ece73f03dd02",
```

If you plan to [deploy to the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), use the hash salt value from your [Xperience Portal project](/documentation/developers-and-admins/saas/xperience-portal#hash-string-salt-for-the-xperience-portal-project).
For [deployment to private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud), the simplest solution is to have matching `CMSHashStringSalt` values for your development project and all target deployment instances. If this is not possible, we recommend the following approach:
  1. Set up [multiple environments](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/environments) for your development project.
  2. Prepare a separate configuration file for each deployment target, for example: _appsettings.Staging.json_ , _appsettings.Production.json_
  3. Get the `CMSHashStringSalt` value from your target deployment instances, and set matching values for the corresponding environments on your development instance.
  4. Extend your CD process to [re-sign](/documentation/developers-and-admins/configuration/macro-expressions/macro-signatures) macros **before** you [store objects](#store-objects-to-a-cd-repository) to a repository.
     * Select the correct environment for your deployment via the `--launch-profile` option of the [dotnet run](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-run) command used to re-sign macros.
     * **Note** : The user account that you use to re-sign macros must also exist on the target deployment instance.
CMD
**Example**
Copy
```
dotnet run --launch-profile "Production" -- --kxp-resign-macros --old-salt "Old_Salt" --username administrator
dotnet run --no-build -- --kxp-cd-store --repository-path "C:\Xperience_Deployments\Production\CDRepository" --config-path "C:\CD_configs\Production\repository.config"
```



With this process, any macros stored in the repository files have signatures that are valid for the target deployment instance.
## Restore the CD repository to the database
To ensure that the restore process works correctly, you need to **_stop the target Xperience application before restoring the CD repository_**.
If the application runs during the restore process, you may encounter the following problems:
  * Deadlocks or data inconsistencies if changes occur while data is being restored from the files
  * Outdated content in the application’s cache if you restore without restarting (can cause inconsistencies in displayed content or the administration)


Perform the following steps to transfer objects from the CD repository to the database of your deployment target:
  1. Open the command line prompt.
  2. Navigate to your Xperience project’s root directory.
  3. Use [dotnet run](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-run) to execute the `--kxp-cd-restore` command:
CMD
**Local development projects**
Copy
```
dotnet run -- --kxp-cd-restore --repository-path "C:\Xperience_Deployments\Production\CDRepository"
```

CMD
**Deployed projects with built assemblies**
Copy
```
dotnet myxperienceproject.dll --kxp-cd-restore --repository-path "C:\Xperience_Deployments\Production\CDRepository"
```

**Command details**
If running the restore from a deployed application containing built assemblies, use the `dotnet myxperienceproject.dll --kxp-cd-restore` command (replace the assembly with the name of your Xperience project’s DLL). For more information, see the [dotnet run documentation](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-run#description).
The restore command must be called with the `--repository-path` parameter, which sets the file system path of the CD repository folder from which data is restored. 


The restore action deserializes objects from the CD repository and updates the target database according to the [restore mode set in the repository’s configuration file](#generate-cd-configuration-files).
### Set the target database
The target database of the CD restore is specified by the `CMSConnectionString` key in the configuration file of the application for which the restore command is executed (_appsettings.json_ by default).
JSON
**appsettings.json**
Copy
```
...

"ConnectionStrings": {
    "CMSConnectionString": "Data Source=myserver;Initial Catalog=Xperience;Integrated Security=True;Persist Security Info=False;Connect Timeout=120;Encrypt=False;Current Language=English;"
},

...
```

To set the CD deployment target, select an approach that best fits your development environment and deployment process or pipeline. For example:
  * Set up an automated deployment pipeline that sets the `CMSConnectionString` key before running the restore command. You can use [multiple environments](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/environments) with separate configuration files.
  * Pass the connection string as a command line argument using the [Command-line configuration provider](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration#clcp) when executing `--kxp-cd-restore`:
CMD
Copy
```
dotnet myxperienceproject.dll --kxp-cd-restore -- /ConnectionStrings:CMSConnectionString "..." --repository-path "C:\Xperience_Deployments\Production\CDRepository"
```



### Where to run the CD restore?
Depending on the hosting environment to which you are deploying, you can run the CD restore from the following locations:
  * **From your development environment** – when you have your project ready for deployment, run the restore command from the given project and [specify the target production database](#set-the-target-database).
  * **From your deployment pipeline** – if you use a deployment pipeline, you can set the target database and run the CD restore command within the steps of your pipeline.
  * **On the target environment** – if your target environment allows you to run the .NET CLI, you can include the CD repository in the deployed project files and run the restore command from the project after deployment.


#### Deploy content item asset files
When running the CD restore outside of the target environment (from a local development environment or deployment pipeline), you need to take additional steps to deploy files managed as [Content item assets](/documentation/business-users/content-hub/content-item-assets).
  1. [Map](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers) the _~/assets_ folder to the target environment’s file system (e.g., [Azure Blob storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage)) for the project where you run the CD restore.
  2. Delete all files that you wish to deploy from the local file system’s _~/assets_ folder before running the CD restore.
     * For deployments to [Azure Storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) or [Amazon S3](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3), you can instead disable the mapping’s fallback to the local folder – set the `EnableFallbackToLocalFileSystem` property of `AzureStorageOptions` or `AmazonStorageOptions` to `false` in your application’s _Program.cs_ file.


C#
**Program.cs**
Copy
```
using Kentico.Xperience.AzureStorage;
using Kentico.Xperience.AmazonStorage;

...

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddKentico( ... );

// Disables the fallback to the local file system for Azure Storage
builder.Services.Configure<AzureStorageOptions>(options => options.EnableFallbackToLocalFileSystem = false);

// Disables the fallback to the local file system for Amazon S3
builder.Services.Configure<AmazonStorageOptions>(options => options.EnableFallbackToLocalFileSystem = false);
```

![]()
[]()[]()
