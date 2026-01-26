---
source: https://docs.kentico.com/modules/deploy-to-saas-step-by-step/saas-update-with-xperience-portal-api
scrape_date: 2026-01-26
---

Module: Deploy to SaaS step-by-step
6 of 8 Pages
# Update a QA deployment using Xperience Portal API
Let’s talk about deploying updates to your QA environment. Whether it’s a content change, class, module, or any newly developed functionality, the mechanism of updating your app deployed in Kentico SaaS is similar to the initial deployment:
You generate a new deployment package and upload it either via the Xperience Portal, as we did in [one of the previous steps](/modules/deploy-to-saas-step-by-step/saas-initial-deployment), or using the Xperience Portal API.
In this step we’ll explore the latter. Uploading updates over the Xperience Portal API is a great option if you’d like to automate this process and include it in your CI/CD pipelines.
## Build the deployment script
Instead of manually executing each step, let’s create a PowerShell script that:
  1. Serializes and stores objects supported by CD.
  2. Creates a SaaS deployment package.
  3. Deploys the package to your QA environment using the [Xperience Portal API](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#upload-a-deployment-package-using-xperience-portal-api).


You can always run all of those actions separately, but the script is reusable, and you can make it a part of your CI/CD pipeline.
Create a new _deploy.ps1_ script file in your project’s directory; in our case, inside the _DancingGoat.Web_ folder.
First, add the code to run [CD store command](/documentation/developers-and-admins/ci-cd/continuous-deployment#store-objects-to-a-cd-repository) to serialize all CD supported objects (all data except for form submissions and media libraries, which we are not using in this example).
Then, run the _Export-DeploymentPackage.ps1_ script that’s present in your project, thanks to the `--cloud` parameter we used during [installation](/modules/deploy-to-saas-step-by-step/saas-install-configure-project#install-saas-ready-project):
  * Set the `AssemblyName` to your project’s assembly name.
  * Store the path of the deployment package in a `PackagePath` variable, so we can reuse it.
  * Use the `PackagePath` variable for the `OutputPackagePath` parameter.
  * Set the `StorageAssetsDeploymentMode` to `CreateUpdate` to allow overwriting existing assets.
Read more about the `StorageAssetsDeploymentMode` parameter in [our documentation (scroll to step 3.)](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#create-a-deployment-package). 
  * In production scenarios, consider [preparing your code](/documentation/developers-and-admins/deployment/read-only-deployments#required-project-changes) for [zero-downtime deployment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#zero-downtime-deployments) and using the `ZeroDowntimeSupportEnabled` parameter.


Next, use `Invoke-RestMethod` to call the Xperience Portal’s `upload` REST endpoint:
  * Set the _Authorization_ header to use the _Bearer_ token. The value of the token needs to be your **Personal Access Token (PAT)** that you created and saved in an [earlier step of this walk-through](/modules/deploy-to-saas-step-by-step/saas-setup#create-your-personal-access-token). We’ll pass in the PAT as a script parameter.
  * Append the project GUID parameter to the REST endpoint. You can obtain the GUID from the Xperience Portal Dashboard and pass it in as a parameter as well.
  * Pass in the newly created deployment package by setting the `InFile` parameter to our `PackagePath` variable.


PS
**deploy.ps1**
Copy
```
<#
.Synopsis
    Exports and deploys a deployment package to a QA environment
#>

param (
    [Parameter(Mandatory=$true)]
    [string]$PersonalAccessToken,
    [string]$ProjectGuid
)

# Serialize CD data.
# Remember to escape the $ character with ` in PowerShell.
# If you use the optional `--no-build` parameter, make sure you always build your solution, including all desired updates before running this script.

Write-Host 'Storing CD files'
dotnet run --no-build -- --kxp-cd-store --repository-path ".\`$CDRepository"

if ($LASTEXITCODE -ne 0) {
    Set-Location -Path $originalLocation
    Write-Error "CD store failed."
    Read-Host -Prompt "Press Enter to exit"
    exit 1
}
else{
    Write-Host 'CD files stored'
}

# Export deployment package.

$PackagePath = "./DeploymentPackage.zip"

Write-Host 'Exporting deployment package'
.\Export-DeploymentPackage.ps1 -AssemblyName "DancingGoat.Web" -OutputPackagePath $PackagePath -StorageAssetsDeploymentMode "CreateUpdate"


if ($LASTEXITCODE -ne 0) {
    Set-Location -Path $originalLocation
    Write-Error "Exporting deployment package failed."
    Read-Host -Prompt "Press Enter to exit"
    exit 1
}
else{
    Write-Host 'Deployment package exported at ' $PackagePath
}

# [OPTIONAL] Disable the progress bar.
# Due to a PowerShell issue, we recommended disabling the progress bar to boost the performance significantly.
# See https://github.com/PowerShell/PowerShell/issues/2138 for more information.
$ProgressPreference = 'SilentlyContinue'

# Upload the deployment package
Write-Host 'Uploading deployment package to cloud'
# authenticate with your PAT
$Headers = @{ Authorization = "Bearer $PersonalAccessToken" }
# Append Project GUID to the Uri.
# The Uri structure is: {host}/api/deployment/upload/{projectGuid}/{environment}
# Without specifying the environment, the deployment defaults to QA.
$Uri = "https://xperience-portal.com/api/deployment/upload/$ProjectGuid"

Invoke-RestMethod -Uri $Uri -Method Post -InFile $PackagePath -ContentType "application/zip" -Headers $Headers

# Enable the progress bar
$ProgressPreference = 'Continue'
```

## Prepare and run the deployment update
### Make changes to your local project
To simulate the need for a deployment update, run your Dancing Goat project locally and make a few very simple changes:
  1. In the Dancing Goat Pages channel, change the Contacts page heading from “Roastery” to “Roastery contact. This is a code change in a view, representing project development:
cshtml
**DancingGoat.Web/Views/DancingGoatContacts/Index.cshtml**
Copy
```
@model DancingGoat.Models.ContactsIndexViewModel

<div class="contacts-page">
    <div class="col-md-12">
        <div class="col-md-6">
            <h2 class="contact-title">@HtmlLocalizer["Roastery contact"]</h2>
            <ul class="contact-info">
            ...
```

[![Changed title in the Contacts page to ‘Roastery contact’](/docsassets/guides/saas-update-with-xperience-portal-api/updated-code-localhost.jpg)](/docsassets/guides/saas-update-with-xperience-portal-api/updated-code-localhost.jpg)
  2. Replace a logo image that’s referenced by one of the emails - a media asset change.
Your browser does not support the video tag.


Stop the local application run before moving on.
In cases where you have not made any code changes, and you have only updated [supported types of content](/documentation/business-users/content-sync#supported-content), you can deploy updates between environments with Xperience’s [content sync](/documentation/business-users/content-sync) feature (_Advanced_ license required).
### Re-evaluate the CD configuration
Before creating a new deployment package, you should reevaluate whether your CD configuration in _repository.config_ is up-to-date. Are there any new data classes or modules that must be added to the config file to synchronize? Are there any objects you wish to exclude?
Feel free to experiment with excluding different objects and observe what effects it has on the deployed solution.
If you are following along, you can leave the CD configuration as is.
### Prepare migration scripts
This step is optional. Our example does not require any database migrations.
If your project contains any database changes not covered by CD, such as [creating new indexes](/documentation/developers-and-admins/ci-cd/ci-cd-database-migration-scripts#example---database-change-migration-script), follow the guidance [in our documentation](/documentation/developers-and-admins/ci-cd/ci-cd-database-migration-scripts#migration-scripts-in-a-cicd-workflow) to create the _@migrations_ folder that holds any SQL migration scripts and the _Before.txt_ and _After.txt_ files referencing the SQL scripts in the order of execution.
You may have noticed that [our deployment script](#build-the-deployment-script) does not mention or execute any migration scripts. This is because **when deploying to Kentico SaaS** , migrations are **detected in the deployment package and applied automatically** during the deployment process. All you have to do is prepare the migration files.
### Back up your data before deployment update
The Xperience portal creates an [automatic restore point for each service](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#automatic-restore-points-and-exports) before deployment, but you can also create a [manual restore point](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#create-a-manual-restore-point) or [manual export](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#create-a-manual-export) when you need to.
On top of this, Kentico creates [daily restore points of the _Staging_ and _Production_ environments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#automatic-restore-points-and-exports).
### Run the deployment script
Now is the time to run our _deploy.ps1_ script. Make sure it’s located in the project folder; if you’re following along, it is _DancingGoat.Web_.
Prepare your [Personal Access Token value from earlier](/modules/deploy-to-saas-step-by-step/saas-setup#create-your-personal-access-token).
Copy your project GUID from the Xperience Portal dashboard:
[![Project GUID value in the Xperience Portal](/docsassets/guides/saas-update-with-xperience-portal-api/project-guid.png)](/docsassets/guides/saas-update-with-xperience-portal-api/project-guid.png)
Open the project folder in PowerShell and call the script:
PS
Copy
```
./deploy.ps1 -PersonalAccessToken "<YOUR_PAT>" -ProjectGuid "<YOUR_PROJECT_GUID>"
```

If you have disabled the progress bar in PowerShell, you will not see the upload progress when the script gets to that part.
When the script finishes, after a few minutes, you should be able to observe the deployment in progress in the Xperience Portal **Deployments** screen:
[![Deployment in progress in Xperience Portal](/docsassets/guides/saas-update-with-xperience-portal-api/deployment-in-progress.png)](/docsassets/guides/saas-update-with-xperience-portal-api/deployment-in-progress.png)
## Check your progress
When your deployment has finished, you will see the deployment info in your Xperience Portal dashboard updated, including: the _deployment package ZIP file_ , _deployment finished date and time_ and _the version of the deployed XbyK instance_.
[![Deployment info in Xperience Portal dashboard](/docsassets/guides/saas-update-with-xperience-portal-api/new-deployment-info.png)](/docsassets/guides/saas-update-with-xperience-portal-api/new-deployment-info.png)
Now, navigate to your live site and check that the changes we made locally have been propagated to the QA environment: A changed heading on the Contacts page and an inverted logo in an email.
[![Updated view in cloud](/docsassets/guides/saas-update-with-xperience-portal-api/updated-code-cloud.jpg)](/docsassets/guides/saas-update-with-xperience-portal-api/updated-code-cloud.jpg) [![Updated asset in cloud](/docsassets/guides/saas-update-with-xperience-portal-api/updated-asset-cloud.png)](/docsassets/guides/saas-update-with-xperience-portal-api/updated-asset-cloud.png)
If your solution uses [Lucene search integration](https://www.nuget.org/packages/Kentico.Xperience.Lucene) and you run into an issue with the search index after deployment, you may find [this blog by Milan Lund](https://www.milanlund.com/knowledge-base/handling-search-index-rebuilds-in-xperience-by-kentico-s-saas-environment) helpful.
[ Previous page ](/modules/deploy-to-saas-step-by-step/saas-configure-channels)
6 of 8
[ Mark complete and continue ](/modules/deploy-to-saas-step-by-step/saas-deploy-to-prod)
![]()
[]()[]()
