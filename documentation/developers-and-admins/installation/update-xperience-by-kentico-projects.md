---
source: https://docs.kentico.com/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Installation](/documentation/developers-and-admins/installation)
  * Update Xperience by Kentico projects 


# Update Xperience by Kentico projects
We recommend that you update your Xperience by Kentico projects regularly. Updates are released in the form of:
  * **Hotfixes** – weekly fixes of issues and reported security vulnerabilities. May include improvements that increase the system’s security against potential threats.
  * **Refreshes** – more extensive updates that add new features, introduce changes, and fix issues. Refreshes are planned for every month, but the release schedule may vary depending on the features included in each refresh.


Check the [Changelog](/documentation/changelog) for additional details, workarounds and instructions related to individual update versions.
In rare cases, **Refreshes** (only major version updates) may contain breaking changes to the codebase, typically for obsolete or infrequently-used code. Check the _Breaking changes_ sections of the _Changelog_ to see if your codebase or other important functionality is affected.
**Update automation**
While the instructions on this page can be followed and performed manually, it is also possible to automate all steps of the Xperience by Kentico update process using various types of automation tools, release management software, pipelines or scripts.
## Update process overview
**Before updating**
  * Create a backup of your Xperience database, project folder and the folder where you store assets (**~/assets** by default). In case of a failed update, use these backups to restore your environment to its original state to prevent inconsistent data.
  * When updating a development project with [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) enabled, familiarize yourself with the [Update instructions for CI](#update-development-projects-with-continuous-integration).


Start by performing the update on a local development instance of your Xperience project:
  1. [Update the project’s Xperience NuGet packages](#update-nuget-packages).
  2. [Update the project’s database and files](#update-data-and-files).


After you successfully test the update locally, you can apply the update to your Xperience application’s production deployment. The following steps outline the general approach, but the exact process will depend on your deployment flow and hosting environment.
  1. Temporarily stop your production application (provide a suitable offline page) or prepare a staging deployment that you can later swap.
  2. Deploy the [updated web project](#update-nuget-packages) to your production or staging environment.
  3. Perform the [database and file update](#update-data-and-files) for your production environment. 
     * There are various ways to run the update command, depending on your deployment process and environment. For example, from a deployment pipeline, a local project with a “production” configuration that connects to the production database and file system, or if possible directly on the target environment.
     * For deployed applications containing built assemblies, run the following [dotnet](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-run#description) command (replace the assembly with the name of your Xperience project’s DLL): 
CMD
Copy
```
dotnet myxperienceproject.dll --kxp-update
```

     * To skip the database backup prompt in an automated environment, add the `--skip-confirmation` parameter to the update command.
  4. Finalize the update (relaunch the stopped production application, swap your staging and production slots, etc.).


In addition to updating individual projects, we also recommend updating the templates used for installing new projects. See [Update project templates](#update-project-templates).
### Update NuGet packages
To update your project’s Xperience code libraries and administration:
  1. Open your application in **Visual Studio**.
  2. Right-click your website project in the **Solution Explorer** and select **Manage NuGet Packages**.
  3. On the **Updates** card, select all **Kentico.Xperience** NuGet packages and click **Update**.
     * **Kentico.Xperience.WebApp**
     * **Kentico.Xperience.Admin**
     * All installed [extension packages](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/xperience-by-kentico-nuget-packages)
  4. Rebuild the solution.


**Target update version**
We strongly recommend updating to the latest available version of Xperience. However, if you wish to update to a specific version, you can select it in Visual Studio’s _NuGet Package Manager_ interface or specify it when running the [dotnet add package](https://learn.microsoft.com/en-us/nuget/consume-packages/install-use-packages-dotnet-cli#install-or-update-a-package) command.
The application will not start until the NuGet package version matches your database version. Continue by [updating your project’s database and files](#update-data-and-files).
### Update data and files
After [updating your project’s NuGet packages](#update-nuget-packages), update the database and file system using the .NET command-line interface (CLI). This update always targets the new version of the project’s NuGet packages.
  1. Shut down your Xperience application if it is running and receives traffic.
  2. Open the command line prompt.
  3. Navigate to the root folder of the project you want to update.
  4. Run the following command:
CMD
Copy
```
dotnet run --no-build --kxp-update
```

**Command details**
If updating a deployed application containing built assemblies, use the `dotnet myxperienceproject.dll --kxp-update` command instead (replace the assembly with the name of your Xperience project’s DLL). For more information, see the [dotnet run documentation](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-run#description).
When updating a full project locally, add the `--no-build` parameter to the `dotnet run` command if you have already rebuilt your project after [updating the NuGet packages](#update-nuget-packages). This option saves time and avoids another unnecessary project build before the update. To prevent errors after building the project with a configuration other than _Debug_ mode, set the `--property:Configuration` parameter to the desired value.
Add the `--` syntax separator before any command parameters that you wish to pass directly to the Xperience application (`--kxp-update`). This prevents conflicts with general .NET CLI parameters, for example when using the `--help` option.
If you wish to skip the database backup prompt when running the update command (e.g., in an automated environment), add the `--skip-confirmation` parameter. For example:
```
dotnet run -- --kxp-update --skip-confirmation
```



The command updates the project’s database and in certain cases assets or folders on the file system (using SQL scripts and API operations). The database version now matches the version of the project’s Xperience NuGet packages.
The update process does not automatically start the application when finished. After the update, you can run the application again using the `dotnet run` command, or another suitable approach depending on your hosting environment.
## Update projects in the SaaS environment
Xperience by Kentico applications [deployed to the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) need to be updated manually. Use the following process:
  1. Update your project in a local development environment to the desired version. 
     * See [Update NuGet packages](#update-nuget-packages) and [Update data and files](#update-data-and-files).
  2. Create a new [deployment package](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-with-a-deployment-package). 
     * Update the **$CDRepository/repository.config** file according to your [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) object filtering requirements. If you use the `<IncludedObjectTypes>` element, check the _New object types for CI/CD_ section of the [changelog](/documentation/changelog) for any new object types introduced by the update.
  3. Redeploy the package to your Xperience Portal project.


The deployment process automatically ensures that the database in the SaaS environment is up-to-date.
## Update development projects with Continuous Integration
If you are updating a development project with [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) enabled:
  * Before running the update, disable CI.
  * Apply the update separately for each instance in your development environment. You cannot fully synchronize the database update using your source control system and CI.
  * If you use the `<IncludedObjectTypes>` element in your CI _repository.config_ file to [filter object types](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories), check the _New object types for CI/CD_ section of the [changelog](/documentation/changelog) for any new object types introduced by the update. Add any new object types that you wish to include in CI.
  * After updating, every developer needs to **run complete serialization for all objects** to recreate the content of the _CIRepository_ folder on their instance (use the `--kxp-ci-store` CLI command).
  * After one developer commits the update changes to the source control, other developers CANNOT commit or load changes until they apply the update to their own instance.


### Example – Update process with CI
The following steps show an example of the update process for a local development instance using Continuous Integration (suitable for an automated script, etc.).
  1. Commit all pending changes under the CI repository to your source control.
  2. Create a backup of your Xperience database and project folder.
  3. Shut down the Xperience application, and any other applications that connect to the same database.
  4. [Update the project’s NuGet packages](#update-nuget-packages).
  5. Disable CI.
SQL
**SQL**
Copy
```
UPDATE CMS_SettingsKey SET KeyValue = N'False' WHERE KeyName = N'CMSEnableCI'
```

  6. [Update project data](#update-data-and-files).
CMD
**.NET CLI**
Copy
```
dotnet run --no-build --kxp-update
```

  7. Re-enable CI.
SQL
**SQL**
Copy
```
UPDATE CMS_SettingsKey SET KeyValue = N'True' WHERE KeyName = N'CMSEnableCI'
```

  8. Serialize all objects to the _CIRepository_ folder.
CMD
**.NET CLI**
Copy
```
dotnet run --no-build --kxp-ci-store
```

  9. Commit the update changes to your source control.


## Update administration customization projects
If you have one or more [projects for custom administration development](/documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development), remember to perform the following when updating to a newer Xperience by Kentico version:
  * Update the Xperience by Kentico NuGet packages installed in the custom admin back-end project.
  * Update the dependencies of your custom admin client project – _@kentico/xperience-admin-base_ , _@kentico/xperience-admin-components_ , _@kentico/xperience-webpack-config_ , etc. Use the [npm-update](https://docs.npmjs.com/cli/v9/commands/npm-update) command:
CMD
Copy
```
# Switches to the directory containing client module code
cd ..\Acme.Web.Admin\Client
npm update
```



## Update project templates
If you have installed the [Xperience project templates package](/documentation/developers-and-admins/installation#install-project-templates-package) on your machine, we recommend keeping the templates up-to-date. This is required if you wish to create new projects that include released features and fixes.
To update the Xperience project templates, open the command line and run:
CMD
**Update project templates**
Copy
```
dotnet new update
```

![]()
[]()[]()
