# Deploy to the SaaS environment
  * [ Copy page link ](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#) | [Get HelpService ID](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#)
Core MVC 5


[✖](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#)
**Minimum Xperience by Kentico version**
The minimum Xperience by Kentico version for new projects that can be deployed to the SaaS environment is **30.1.0**. If you are using an earlier version, you need to [update](documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) your project to the latest version before deploying it to the SaaS environment. Existing projects are not affected, but we recommend updating them to the latest version.
Xperience by Kentico enables you to deploy, host, and maintain Xperience websites in a Software-as-a-Service (SaaS) environment. You can deploy to multiple environments to isolate the production and non-production application instances.
Kentico manages the deployment infrastructure and selected third-party services for you – such as [Microsoft Application Insights](documentation/developers-and-admins/configuration/saas-configuration#microsoft-application-insights-integration), [CloudFlare CDN](documentation/developers-and-admins/configuration/saas-configuration#cloudflare-cdn), and [Twilio SendGrid](documentation/developers-and-admins/configuration/email-configuration#Emailconfiguration-EnableManagedSendGrid). This allows you to focus on website and content development.
Develop a website for deployment to the SaaS environment:
  1. [Get access to your project in Xperience Portal](documentation/developers-and-admins/saas/xperience-portal#XperiencePortal-AccessPortal).
  2. [Prepare a .NET project for deployment to the SaaS environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#prepare-a-project-for-deployment-to-the-saas-environment).
  3. [Develop](documentation/developers-and-admins/development) and [configure](documentation/developers-and-admins/configuration) features in your project.
  4. [Deploy](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#DeploytotheSaaSenvironment-deploy) your application to the SaaS environment.
  5. [Manage](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments) your deployed application in Xperience Portal.


[![Overview of the SaaS deployment](docsassets/documentation/deploy-to-the-saas-environment/saas_deployment_diagram.png)](https://docs.kentico.com/docsassets/documentation/deploy-to-the-saas-environment/saas_deployment_diagram.png)
## Prepare a project for deployment to the SaaS environment
You can now prepare a .NET project for deployment to the SaaS environment.
  1. [Install the Xperience project templates](documentation/developers-and-admins/installation#install-project-templates-package).
  2. [Create an Xperience project](documentation/developers-and-admins/installation#create-a-project).
     * Use the `--cloud` parameter when [creating the Xperience project](documentation/developers-and-admins/installation#create-a-project). This parameter ensures the necessary configuration is included: 
       * The [Kentico.Xperience.Cloud](https://www.nuget.org/packages/Kentico.Xperience.Cloud) NuGet package
       * [SendGrid](documentation/developers-and-admins/configuration/email-configuration#managed-sendgrid-integration) and [Microsoft Application Insights](documentation/developers-and-admins/configuration/saas-configuration#DeploytotheSaaSenvironment-EnableAppInsights) startup configurations
       * A PowerShell script for [generating the deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#create-a-deployment-package) and a [CD configuration file](documentation/developers-and-admins/ci-cd/continuous-deployment#generate-cd-configuration-files)
       * **StorageInitializationModule.cs** file with a sample storage initialization module (for [mapping of files to Azure Blob storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#default-kentico-managed-azure-blob-storage-configuration))
  3. [Create the project database](documentation/developers-and-admins/installation#create-the-project-database).
     * Generate [a developer license key](documentation/developers-and-admins/installation/licenses#license-keys-for-saas-local-development).
     * Set the value of the `--hash-string-salt` parameter to the one found on the [Xperience Portal Dashboard](documentation/developers-and-admins/saas/xperience-portal#hash-string-salt-for-the-xperience-portal-project).
  4. Include the `UseKenticoCloud` component in the [project’s middleware pipeline](documentation/developers-and-admins/development/website-development-basics/configure-new-projects#configure-application-startup).
     * Use the `.IsQa()` and `.IsUat()` [extension methods](documentation/developers-and-admins/configuration/saas-configuration#environment-identification-extension-methods) to include environment-specific configuration or code
     * [Call](documentation/developers-and-admins/configuration/saas-configuration#microsoft-application-insights-integration) `AddXperienceCloudApplicationInsights` when building the web application
     * Configure cache headers for [Cloudflare CDN](documentation/developers-and-admins/configuration/saas-configuration#cloudflare-cdn)
  5. Build the project. Run the following command from the project’s root directory or build the project using Visual Studio.
CMD
Copy
```
dotnet build
```



The project is now ready for further [development](documentation/developers-and-admins/development) and [customization](documentation/developers-and-admins/customization). You can add content to the project or [extend](documentation/developers-and-admins/customization/extend-the-administration-interface) the connected Xperience administration. The following code example demonstrates the required SaaS configuration. Ellipsis indicates breaks where other middleware may be added:
C#
**Program.cs - SaaS configuration**
Copy
```
using Kentico.Web.Mvc;
using Kentico.Xperience.Cloud;

using Microsoft.Extensions.Hosting;
using Microsoft.AspNetCore.Builder;
using Microsoft.Extensions.DependencyInjection;

var builder = WebApplication.CreateBuilder(args);

// Required for projects that will be deployed to the SaaS environment
builder.Services.AddXperienceCloudApplicationInsights(builder.Configuration);
builder.Services.AddXperienceCloudChannelRequirements();

// Required for projects that will be deployed to the SaaS environment
if (builder.Environment.IsQa() || builder.Environment.IsUat() || builder.Environment.IsProduction() || builder.Environment.IsEnvironment(CloudEnvironments.Custom))
{
    builder.Services.AddKenticoCloud(builder.Configuration);
    builder.Services.AddXperienceCloudSendGrid(builder.Configuration);
    builder.Services.AddXperienceCloudDataProtection(builder.Configuration);
}

builder.Services.AddKentico();
builder.Services.AddAuthentication();
builder.Services.AddControllersWithViews();

...

app.UseAuthentication();

// SaaS-specific middleware (required for SaaS deployment)
app.UseKenticoCloud();

app.UseKentico();

...

// Starts the application
app.Run();
```

## Deploy to the SaaS environment
When your Xperience project is [prepared](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#prepare-a-project-for-deployment-to-the-saas-environment) and [configured](documentation/developers-and-admins/configuration/saas-configuration) in a local development environment, you can deploy the application to the SaaS environment. Choose the appropriate deployment method for your scenario:
  * **Initial deployments** : Deploy to the SaaS environment with a [deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-with-a-deployment-package). If your project includes a large amount of assets or other data, use [custom restore](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-a-large-amount-of-data-during-initial-deployments) to deploy this data.
  * **Regular content updates** : Use [content sync](documentation/business-users/content-sync) to deploy updates for [supported](documentation/business-users/content-sync#supported-content) types of content (_Advanced_ license required). See [Content sync configuration](documentation/developers-and-admins/configuration/content-sync-configuration) for configuration details. If the updated content is not supported by content sync or if the feature is not available, deploy the changes via a new [deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-with-a-deployment-package).
  * **Application updates** : Use the [deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-with-a-deployment-package) when [updating](documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) to a new version of Xperience by Kentico or when deploying new [custom features](documentation/developers-and-admins/customization). Changing application files is only possible through this type of deployment.


## Deploy with a deployment package
Deploying to the SaaS environment with a deployment package allows you to deploy all parts of your SaaS project (application binaries, selected database data and storage assets). To deploy using this method, [create a deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#create-a-deployment-package) and [upload it to your Xperience Portal project](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#upload-a-deployment-package-to-your-xperience-portal-project).
Deployment packages do not create an exact copy of your local project in the target environment because they contain only the database objects included in your [CD repository](documentation/developers-and-admins/ci-cd/continuous-deployment). See [Deployment overview](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deployment-overview) and [Create a deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#create-a-deployment-package) for details.
### Deployment overview
The following steps provide an overview of the entire lifecycle of a deployment:
  1. You [install](documentation/developers-and-admins/installation), [configure](documentation/developers-and-admins/configuration/saas-configuration) and [develop](documentation/developers-and-admins/development) an Xperience project from a [template](documentation/developers-and-admins/installation#available-project-templates). A project installed with the `--cloud` option contains the following: 
     * An Xperience .NET application.
     * Adjustments for SaaS deployment in the application’s service collection and middleware pipeline within the **Program.cs** file.
     * A configuration file for the [Continuous Deployment (CD)](documentation/developers-and-admins/ci-cd/continuous-deployment) repository, used to deploy database objects.
     * The **Export-DeploymentPackage.ps1** PowerShell script that creates a deployment package for the project.
     * **StorageInitializationModule.cs** class with methods that map folders containing physical files such as [content item assets](documentation/business-users/content-hub/content-item-assets) and binary files to Azure Blob storage and local storage for development. See [Azure Blob storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage).
  2. You [create a deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#create-a-deployment-package) from the project. A deployment package is a ZIP archive that contains the following: 
     * Application libraries and static files.
     * `$CDRepository` folder containing serialized database objects.
     * A specified [CD configuration](documentation/developers-and-admins/ci-cd/continuous-deployment).
     * (_Optional_) `$StorageAssets` folder for physical files.
  3. When you [upload](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#upload-a-deployment-package-to-your-xperience-portal-project) the deployment package to the SaaS environment, it is restored in the QA environment according to the specified [CD configuration](documentation/developers-and-admins/ci-cd/continuous-deployment).
  4. When you [deploy the package to another environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-the-package-to-another-deployment-environment), the same deployment package is applied to the selected environment and restored according to the [CD configuration](documentation/developers-and-admins/ci-cd/continuous-deployment). 
     * Changes in the administration interface done during testing on the previous environment will not be reflected in other environments.
     * Databases of different environments are completely independent and never copied between environments. They are only updated by new deployments or users performing action in the administration interface.
  5. When deploying a package to the production environment, the same deployment package is applied and restored according to the [CD configuration](documentation/developers-and-admins/ci-cd/continuous-deployment). 
     * See [Deploy data to the production environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-data-to-the-production-environment) to learn how to upload content to the production environment.


**Notes**
  * Currently, the **maximum size of the deployment package is 2 GB**. Binary files and media libraries are bundled in the deployment package. If you need to deploy more data during initial deployments (e.g., when [upgrading](documentation/developers-and-admins/upgrade-to-xperience-by-kentico) to Xperience by Kentico), use [custom restore](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-a-large-amount-of-data-during-initial-deployments).
  * It is essential to consider what [CD configuration](documentation/developers-and-admins/ci-cd/continuous-deployment) to be used with each deployment package. You can [exclude objects](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) from the CD repository to limit what objects are restored in the SaaS environment. You can also specify a [CD restore mode](documentation/developers-and-admins/ci-cd/continuous-deployment#cdrestoremode) used to restore the CD repository.
  * When deploying to a SaaS environment containing an existing deployment, the Xperience version that you deploy **cannot be lower** than the version in the target environment. You can only deploy the same or newer version.
  * Non-production environments (_QA_ , _UAT_ or custom) only serve to test the functionality of your project. Any data or content edited in these environments **will not** be transferred during deployment to the _Production_ environment.
  * After promoting a build to the _Production_ environment, production data can be added. Use [workflows](documentation/developers-and-admins/configuration/workflows) to prevent incomplete data from appearing publicly.
  * Only the latest deployed package can be promoted to the next environment.


### Create a deployment package
A deployment package is a ZIP archive with application libraries and static files, and a _$CDRepository_ folder with serialized database objects, and optionally a _$StorageAssets_ folder for physical files.
Follow the procedure to build your application and create a deployment package:
  1. Use [Continuous Deployment](documentation/developers-and-admins/ci-cd/continuous-deployment) to serialize database objects to the file system:
    1. Edit the **$CDRepository/repository.config** file according to your object filtering and deployment requirements. See [Exclude objects from CI/CD](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories).
You cannot adjust the location of the Continuous Deployment repository. The deployment process expects the _$CDRepository_ folder to be in the project root.
    2. Prepare [migration scripts](documentation/developers-and-admins/ci-cd/ci-cd-database-migration-scripts) for any database changes not covered by Continuous Deployment.
**Run SQL scripts on the database**  
You can use [CD migration scripts](documentation/developers-and-admins/ci-cd/ci-cd-database-migration-scripts) to perform SQL queries on the database of applications deployed in [SaaS environments](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-the-package-to-another-deployment-environment), for example, to fix database inconsistencies. However, remember to always [create a restore point](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#create-a-manual-restore-point) before modifying the database.
    3. Run the CD store command from the project’s root directory:
PS
Copy
```
dotnet run --no-build -- --kxp-cd-store --repository-path ".\$CDRepository"
```

**Escaping the ‘$’ character**
When performing this action in certain command line interfaces (e.g., PowerShell running on MacOS and the default Windows Terminal), you need to escape the dollar sign (`$`) by prefixing it with the grave accent (```). 
  2. Deploy [Media library files](guides/architecture/content-modeling/content-modeling-guide/store-files) and other unmanaged binary files used by the application to [Azure Blob storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage), as Continuous Deployment does not serialize these files.
  3. Create the deployment package with the **Export-DeploymentPackage.ps1** script.
On Windows environments, the executing account must have permissions to run PowerShell scripts from external sources:
PS
Copy
```
# Bypass all execution checks for the current user
Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
```

See [Set-ExecutionPolicy](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-executionpolicy) for other types of permission levels.
     * Refer to the list of parameters before running the script.
PS
Copy
```
.\Export-DeploymentPackage.ps1 -AssemblyName "DancingGoat"
```

       * `-AssemblyName` – the name of the main web application assembly used during project publishing.
         * The value **must be equal** to the [assembly name](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.assemblyname?view=net-6.0) of the Xperience project (by default, the assembly name is equal to the project _.csproj_ file name without an extension).
       * _(Optional)_ `-OutputPackagePath` – the full path where the deployment package will be exported. Must include the package file name and extension.
         * For example, `./DeploymentPackage.zip`.
         * When omitted, the deployment package **DeploymentPackage.zip** is exported to the directory from which the script was executed.
       * _(Optional)_ `-KeepProductVersion` – by default, the deployment package is created with a **Build number** equal to the source project’s package version, suffixed with the export date and time in format ‘ _yyyyMMddHHmm_ ’.
         * Add this parameter if you wish to exclude the date and time from the **Build number**.
         * The **Build number** is displayed in the **Deployment history** within Xperience Portal.
       * _(Optional)_ `-StorageAssetsDeploymentMode [-Create] [-CreateUpdate]` – determines whether the export preserves or overwrites the storage assets already present in [Azure Blob storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage):
         * When omitted, the `-Create` mode is used, and existing storage assets will not be overwritten and only new assets are added.
         * Use the `-CreateUpdate` parameter to allow overwriting of existing storage assets.
If your deployment package contains [media library files](documentation/business-users/media-libraries/manage-media-files), the `StorageAssetsDeploymentMode` value must match the `RestoreMode` element value in the CD configuration file. See [Reference - CI/CD object types](documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types#content-management).
       * _(Optional)_ `-ZeroDowntimeSupportEnabled` – marks the deployment package as zero-downtime ready. Include this parameter if your application code supports [read-only mode](documentation/developers-and-admins/deployment/read-only-deployments). When a zero-downtime ready package is deployed to an environment that already contains code configured to support read-only mode, the deployment will be executed as a [zero-downtime deployment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#zero-downtime-deployments).


The deployment package is now created, and you can deploy to the SaaS environment by [uploading the deployment package to your Xperience Portal project](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#upload-a-deployment-package-to-your-xperience-portal-project).
### Upload a deployment package to your Xperience Portal project
Once you have [created a deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#create-a-deployment-package), trigger the deployment to the **QA** environment by uploading a deployment package to your Xperience Portal project directly from [Xperience Portal](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#upload-a-deployment-package-from-xperience-portal) or programmatically using [Xperience Portal API](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#upload-a-deployment-package-using-xperience-portal-api).
Uploading a deployment package to Xperience Portal is unavailable during [scheduled Xperience Portal project maintenance](documentation/developers-and-admins/saas/xperience-portal#schedule-maintenance-of-xperience-portal-projects) and initial project creation.
The deployment package is currently limited by a **maximum size of 2 GB**. If you need to deploy more data during initial deployments (e.g., when [upgrading](documentation/developers-and-admins/upgrade-to-xperience-by-kentico) to Xperience by Kentico), use [custom restore](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-a-large-amount-of-data-during-initial-deployments).
#### Upload a deployment package from Xperience Portal
  1. [Access your Xperience Portal project](documentation/developers-and-admins/saas/xperience-portal#XperiencePortal-AccessPortal).
  2. Navigate to the **Deployments** application.
  3. Under the **QA** environment, select **Upload package**. 
     * Users in **DevOps Engineer** or **Tenant Administrator** [roles](documentation/developers-and-admins/saas/xperience-portal/reference-xperience-portal-user-roles) can also upload deployment packages directly to other non-production environments, or even directly to the _PROD_ environment in extraordinary circumstances. See [Deploy emergency fixes to the SaaS environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-emergency-fixes-to-the-saas-environment).
  4. Choose a valid deployment package (a ZIP archive with a maximum size of 2 GB) and select **Upload**. You will be redirected to the **Deployments** application after the upload is complete.
  5. The progress of any current deployments can be tracked in the **Deployment progress** section, where you can check the status of the deployment. After the deployment is completed, you can review it in the **Deployment history** section.


After the deployment finishes, your site is ready and running in the **QA** (or another selected) environment. Access the deployed application with the URL displayed under the selected environment section in the **Deployments** application in your Xperience Portal project.
To debug the deployment, you can download an archive with **deployment logs** under the **Deployment history** section.
#### Upload a deployment package using Xperience Portal API
You can upload the deployment package programmatically by sending a POST request authenticated with your [Personal access token (PAT)](documentation/developers-and-admins/saas/xperience-portal#XperiencePortal-PAT) to the _deployment_ Xperience Portal API endpoint:
  1. Generate a [Personal access token (PAT)](documentation/developers-and-admins/saas/xperience-portal#XperiencePortal-PAT) and store it in a local file.
  2. Get your `PROJECT_GUID` in [Xperience Portal](documentation/developers-and-admins/saas/xperience-portal) Dashboard, in the **Project info** section, or from any URL when accessing Xperience Portal, for example, _https://xperience-portal.com/ <PROJECT_GUID>/dashboard_.
  3. Send a POST request to the _deployment_ API endpoint. You can use the following PowerShell or cURL examples:
PS
**Upload using PowerShell**
Copy
```
# Due to a PowerShell issue, we recommended disabling the progress bar to boost the performance significantly.
# See https://github.com/PowerShell/PowerShell/issues/2138 for more information.
# Disable the progress bar
$ProgressPreference = 'SilentlyContinue'

# Upload the deployment package
$headers = @{ Authorization = "Bearer <PERSONAL_ACCESS_TOKEN>" }
Invoke-RestMethod -Uri https://xperience-portal.com/api/deployment/upload/<PROJECT_GUID>/qa -Method Post -InFile <FILE_PATH> -ContentType "application/zip" -Headers $headers

# Enable the progress bar
$ProgressPreference = 'Continue'
```

PS
**Upload using cURL**
Copy
```
curl -X POST -T <FILE_PATH> -H 'Content-Type: application/zip' -H 'Authorization: Bearer <PERSONAL_ACCESS_TOKEN>' https://xperience-portal.com/api/deployment/upload/<PROJECT_GUID>/qa
```

     * Users in **DevOps Engineer** or **Tenant Administrator** [roles](documentation/developers-and-admins/saas/xperience-portal/reference-xperience-portal-user-roles) can also upload deployment packages directly to other non-production environments, or even directly to the _PROD_ environment in extraordinary circumstances. See [Deploy emergency fixes to the SaaS environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-emergency-fixes-to-the-saas-environment).
  4. Navigate to the **Deployments** application.
  5. Allow a few minutes for the deployment progress to be displayed in the **Deployment history** section, where you can check the deployment progress.


After the deployment finishes, your site is ready and running in the **QA** environment. Access the deployed application with the URL displayed under the **QA** environment section in the **Deployments** application in your Xperience Portal project.
To debug the deployment, you can download an archive with **deployment logs** under the **Deployment history** section.
### Deploy data to the production environment
Keep the following in mind when performing standard deployments to production:
  * Deployment packages are intended for deploying the project only with initial data and content: 
    * Website/email/headless channels, content types representing the content model, page tree structure, basic emails, essential content items
    * Assets that are essential for the design and branding, such as a favicon, background images, graphics, icons, and company logos
  * Large binary files should be uploaded directly to the production environment: 
    * All marketing assets, images, videos, large documents
  * If your deployment package would exceed 2 GB during initial deployments, use [custom restore](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-a-large-amount-of-data-during-initial-deployments) to deploy the database and [storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) assets.
  * If you use Xperience by Kentico to manage content imported from external sources, it is recommended to import the content directly to the production or staging environment.


The [Content sync](documentation/developers-and-admins/configuration/content-sync-configuration) feature allows you to set up content staging for SaaS environments. See [Content staging best practices](documentation/developers-and-admins/configuration/content-sync-configuration#content-staging-best-practices) to learn which environments are suitable for staging.
### Zero-downtime deployments
Zero-downtime deployments update your live site without service interruptions. SaaS deployments use Kentico-managed infrastructure (deployment slots, database replication, blob versioning) that automatically switches your application to [read-only mode](documentation/developers-and-admins/deployment/read-only-deployments) to maintain data consistency.
Zero-downtime deployments require version of Xperience by Kentico 31.0.2 or higher, an updated infrastructure and [read-only mode](documentation/developers-and-admins/deployment/read-only-deployments) support in your application code. Your application must be configured to handle read-only mode before zero-downtime deployments can be used. Without proper read-only mode implementation, zero-downtime deployments will not function correctly.
See [Read-only deployments](documentation/developers-and-admins/deployment/read-only-deployments) for detailed information about read-only mode behavior, required code changes, and disabled functionality.
To enable zero-downtime deployments for your project:
  1. Update your **Export-DeploymentPackage.ps1** script to the latest version that includes the `-ZeroDowntimeSupportEnabled` parameter. If your project was created before this feature was available, replace your existing script with the latest version: [Export-DeploymentPackage.ps1](docsassets/documentation/deploy-to-the-saas-environment/Export-DeploymentPackage.ps1).
  2. Configure your application code to support [read-only mode](documentation/developers-and-admins/deployment/read-only-deployments). This is a mandatory prerequisite. Your code must: 
     * Prevent custom write operations when read-only mode is active (check [required project changes](documentation/developers-and-admins/deployment/read-only-deployments#required-project-changes) for details)
     * Handle [disabled functionality](documentation/developers-and-admins/deployment/read-only-deployments#disabled-functionality) during read-only mode (forms, activity tracking, contact management, etc.)
See [Read-only deployments](documentation/developers-and-admins/deployment/read-only-deployments) for complete implementation instructions.
  3. Mark your deployment package as zero-downtime ready when [creating the deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#create-a-deployment-package) using the **Export-DeploymentPackage.ps1** script with the `-ZeroDowntimeSupportEnabled` parameter.
  4. Deploy the package to an environment. Once an environment contains code supporting zero-downtime deployments, all subsequent deployments of packages marked as zero-downtime ready will be executed as zero-downtime deployments.


Your project is now configured for zero-downtime deployments. When you deploy packages marked with the `-ZeroDowntimeSupportEnabled` parameter to an environment containing zero-downtime ready code, the SaaS infrastructure automatically executes the deployment in read-only mode, eliminating service interruptions during updates.
**Notes**
  * The first deployment with the `-ZeroDowntimeSupportEnabled` flag enables zero-downtime infrastructure but uses standard deployment. Only subsequent deployments with the flag will be zero-downtime.
  * When you deploy a package without the `-ZeroDowntimeSupportEnabled` flag to an environment with existing zero-downtime ready code, the feature is disabled and standard deployment is used. Subsequent deployments without the flag will also use standard deployment.
  * For [private cloud](documentation/developers-and-admins/deployment/deploy-to-private-cloud) deployments, see [Read-only deployments](documentation/developers-and-admins/deployment/read-only-deployments) for manual infrastructure setup and deployment process.


## Deploy the package to another deployment environment
The following SaaS **deployment environments** are available, depending on your [service plan](documentation/developers-and-admins/saas/saas-service-plans):
  * **QA** – A QA engineer tests the deployed application in the QA environment and deploys the deployment package to the UAT ([User Acceptance Testing](https://en.wikipedia.org/wiki/Acceptance_testing#User_acceptance_testing)) environment, if available.
  * **UAT** – A project manager tests the final functionality in the UAT environment and promotes the deployment package to the production (PROD) environment. 
The UAT environment is only available for **Custom** [service plans](documentation/developers-and-admins/saas/saas-service-plans). 
  * **STG** – An environment where editors and other staff can prepare content, and then transfer it to the production (PROD) environment using [content sync](documentation/business-users/content-sync). Staging environments are designed to be nearly identical to production environments in most aspects, such as performance, availability, backup policies, and the option to set custom domains. See [Content staging best practices](documentation/developers-and-admins/configuration/content-sync-configuration#content-staging-best-practices). 
The STG environment can optionally be purchased for any [service plan](documentation/developers-and-admins/saas/saas-service-plans). 
  * **PROD** – A live production environment. Use this environment to deploy a tested iteration of your application.


After [deploying a deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-with-a-deployment-package) to the QA environment, you can deploy it to another environment in [Xperience Portal](documentation/developers-and-admins/saas/xperience-portal):
  1. Navigate to the **Deployments** application.
  2. Select **Deploy to** , located under the environment where the package is currently deployed. A drop-down menu is displayed.
  3. Select the environment where you want to deploy the package.
  4. Select whether you want to deploy the package immediately or [schedule a production deployment for a later date](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#schedule-a-production-deployment-for-a-later-date).
The SaaS environment checks if the production deployment causes downtime. Most deployments are seamless and incur no downtime, especially if the deployment package is marked as [zero-downtime ready](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#zero-downtime-deployments). However, if the system discovers downtime would occur, you can [schedule a production deployment for a later date](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#schedule-a-production-deployment-for-a-later-date). It is also recommended to define a custom [App Offline page](documentation/developers-and-admins/configuration/saas-configuration#define-a-custom-app-offline-file).
  5. Allow a minute for the deployment process to be displayed in the **Deployment progress**.
Repeating the procedure triggers a new deployment.
  6. Check the **Deployment history** section to verify the package is deployed to the environment you selected. Note that the deployment may be queued and will begin once previous SaaS processes have finished.
If you need to debug a failed deployment, see the **Logs** in the **Deployment history** section.


Your application is now deployed and running in the selected environment.
### Schedule a production deployment for a later date
Deployment to a production or staging environment can cause application downtime, impacting the customer experience. [Zero-downtime deployments](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#zero-downtime-deployments) minimize or eliminate downtime, but may not be suitable for all deployment scenarios.
When you trigger a deployment with the **Deploy to** action, the SaaS environment reports if the deployment causes downtime. You can decide whether to proceed with the deployment, schedule the deployment, or cancel the deployment.
Select the **Cancel scheduled deployment** button in the **Deployments** application to cancel the scheduled deployment.
Scheduling the production deployment blocks instant production/staging deployment and additional scheduling.
Cancel the existing scheduled deployment in the **Deployments** application to enable these actions.
If the scheduled deployment fails to start, the project’s **Tenant administrators** and the user that scheduled the deployment receive a notification email**,** and the production/staging environment is unaffected.
If the scheduled deployment starts successfully, but the deployment pipeline fails, users will not receive a notification email.
See the deployment **Logs** in the **Deployment history** section to debug the failed deployment.
## Deploy a large amount of data during initial deployments
During initial deployments (e.g., when [upgrading](documentation/developers-and-admins/upgrade-to-xperience-by-kentico) to Xperience by Kentico), you can use custom restore to deploy a large amount of data without being restricted by the 2 GB [deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#create-a-deployment-package) limit.
Custom restore enables you to transfer the database and [storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#azure-blob-storage-for-kenticos-saas) files directly to the selected environment. Custom restore does not provide a way to deploy application libraries, so it needs to be used in combination with a [deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-with-a-deployment-package).
During the process, you first upload the data to automatically created containers in Azure Storage that are separate from your SaaS environments. When you apply the custom restore, all existing data in the target environment’s database and storage is overwritten with the uploaded data, and the target environment is unavailable while the restore itself runs.
The changes introduced by a custom restore are not propagated between environments with the deployment package. Transferring them to other environments requires a custom restore for each environment.
### Perform a custom restore
Only **Tenant administrator** and **DevOps** roles can manage custom restore sessions (create and close sessions, revoke access) and restore to all environments. **Developers** can restore to non-production environments (QA, UAT) and access the restore containers.
To perform a custom restore:
  1. Back up your data.
    1. Create a [restore point](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#create-a-manual-restore-point) for the target environment before starting.
  2. Create a restore session.
    1. Navigate to **Data Management** → **Custom restore**.
    2. Select **Create session**.
[![Create restore session](docsassets/documentation/deploy-to-the-saas-environment/create_restore_session.png)](https://docs.kentico.com/docsassets/documentation/deploy-to-the-saas-environment/create_restore_session.png)
  3. Get access to restore containers.
    1. Select **Get access**.
    2. If you want to restrict access to the restore containers, specify an allowlist by entering an IP address or an IP address range and confirm with **Get access**. Otherwise, select **Get access** without entering any values.
    3. Copy both [SAS URLs](https://learn.microsoft.com/en-us/rest/api/storageservices/delegate-access-with-shared-access-signature) for the database and storage containers.
**SAS URLs**
You can’t retrieve the same SAS URLs again. However, you can get new SAS URLs with **Get access**. The previously existing SAS URLs are still valid. To revoke all current SAS URLs, select **Revoke access** under **Current session**.
Within a session, the SAS URL pairs provide access to the same containers. 
  4. Upload the database.
     * You can use [Azure Storage Explorer](https://azure.microsoft.com/en-us/products/storage/storage-explorer) to connect to the container: 
       1. In Azure Storage Explorer, connect to the restore Azure Storage (select the type _Blob container or directory_).
       2. Select the connection through _Shared access signature URL (SAS)_.
       3. Enter the SAS URL for the database container and connect to the resource. The display name does not matter.
       4. Upload the database exported as a _bacpac_ file to the database container.
The file must be named `db.bacpac`. The database to be restored must have the same version as the one currently in the environment. If these conditions are not met, the restore will not succeed. 
  5. Upload storage files.
    1. Connect to the storage container using the second SAS URL (as in the previous step).
    2. Upload the files to be stored in [Azure Blob Storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#azure-blob-storage-for-kenticos-saas).
       * Organize the files according to the Azure Blob Storage configuration of your project. You can obtain this structure by downloading a **Storage and files** [export](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) once you have deployed with this Blob storage configuration.
       * For each container used in your Blob storage mapping, upload a folder with the same name. Within that folder, include the files arranged according to your project’s storage structure. 
         * For example, if you’re using the [default configuration](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#default-kentico-managed-azure-blob-storage-configuration), the structure would be `default/assets/…`.
The names of files and folders uploaded to the storage container must be fully in **lowercase**. 
       * Note that the uploaded files count toward the file storage limit, which you can view on the **Dashboard**.
  6. Restore to an environment.
    1. Make sure you have a backup of the target environment. The custom restore process itself cannot be undone, but you can use [restore points](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) to revert the changes.
    2. Once you have uploaded both the database and the storage files to be restored, you can select **Restore to environment** under **Current session**.
    3. Select the target environment where the data should be restored. The environment will be unavailable during the restore process. The database in the target environment will be replaced with the uploaded one. All storage containers in the environment will be deleted and recreated based on the uploaded data.
    4. Select **Create** to start the custom restore process.
**Tenant administrator** and **DevOps** roles can restore to any environment. **Developers** can restore only to non-production environments (QA, UAT). 
  7. Close the session.
    1. You can see the restore in progress under **Active restores**. Once finished, it will appear under **Last completed restores**.
    2. When the restore process is complete and you no longer need to keep the data (for example, to restore it to another environment), **Close** the session.
Each session expires after a week. Once a session has expired, you can no longer upload data to the containers, but you can still restore the data you uploaded previously (up to 180 days from the session creation). Only one restore session can be open at a time. 


## Deploy emergency fixes to the SaaS environment
Emergency deployments allow users in **DevOps Engineer** or **Tenant Administrator** [roles](documentation/developers-and-admins/saas/xperience-portal/reference-xperience-portal-user-roles) to bypass the standard deployment flow and push critical fixes or version updates directly to environments other than _QA_.
The emergency deployment process is suitable for the following situations:
  * The Xperience version deployed in your _PROD_ environment is, for example, 30.0.0. In the _QA_ environment, the deployed version is 30.1.0 with some new features you are currently testing. In version 30.2.0, Kentico releases a security advisory and you want to immediately update to this version. However, your _QA_ environment is “blocked” by the tested feature.
  * You have recently released a new functionality that contains an issues that impedes the usability of your application. The fix for this issue is simple, but you need to promote it to the _PROD_ environment as soon as possible. However, your _QA_ environment is “blocked” by a new feature that is already tested in this environment.


**Handle deployments to Production with care**
Do not use emergency deployments for fixes if you are not absolutely sure that you can deploy these changes to the _PROD_ environment without first testing them on any other SaaS environment. If you have any environment other than _QA_ and _PROD_ available (e.g., _UAT_ , _STG_), it is safer to deploy directly to this environment, test the fix, and then [deploy](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-the-package-to-another-deployment-environment) to _PROD_ from there.