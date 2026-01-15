# Deploy to your private cloud
  * How-to| [ Copy page link ](guides/development/deployment/deploy-to-private-cloud#) | [Get HelpService ID](guides/development/deployment/deploy-to-private-cloud#)
Core MVC 5


[✖](guides/development/deployment/deploy-to-private-cloud# "Close page link panel") [Copy to clipboard](guides/development/deployment/deploy-to-private-cloud#)
This guide goes over the process of using _Command-line interfaces_ to deploy an Xperience by Kentico application, focusing specifically on hosting in either Azure Web Apps or IIS in your [private cloud](documentation/developers-and-admins/deployment/deploy-to-private-cloud).
Headings that are specific to either platform will be labelled accordingly.
If you want guidance on how to deploy to the Xperience SaaS environment, where you can manage your projects through the [Xperience Portal](documentation/developers-and-admins/saas/xperience-portal), see the [documentation’s instructions](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) for how to do so.
## Before you start
This guide requires the following:
  * Familiarity with [.NET Core](https://learn.microsoft.com/en-us/dotnet/), general development operations concepts, and working with [Command-line interfaces (CLIs)](https://en.wikipedia.org/wiki/Command-line_interface)
  * An instance of Xperience by Kentico to deploy. The samples in this guide use an instance running on version [29.3.0](documentation/changelog).


## Start with a working project
Let’s examine the initial deployment process, using the [Kickstart project](https://github.com/Kentico/xperience-by-kentico-kickstart) as an example.
You can still follow along with your own project, you’ll just need to account for the names of files and folders being different.
Start by following the Kickstart repository’s instructions to set up the project and database locally. Don’t forget to run the `kxp-ci-restore` command and double check that the site’s two pages render properly.
## Configure external storage
We recommend hosting assets, form files, and media files in some kind of external storage in all cases, but especially when your application is hosted in _Azure Web Apps_ or other similar environments that do not guarantee persistent storage.
Aside from preserving your files in the event of unexpected restarts, using external storage will prevent future code deployments from destroying or overwriting them.
Xperience offers two solutions out-of-the-box. The documentation contains instructions for [setting up Azure Blob Storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) and [Amazon S3](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3), along with a way to [customize file system providers](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/custom-file-system-providers) to integrate with alternative services.
If you plan to have more than one published environment, or use external storage in your development environment as well, make sure to use a separate container or bucket per environment to prevent them from overwriting one another’s files.
## Set up the database
Whether you plan to host your deployed application files in Azure or on your own server with IIS, you can theoretically host your database anywhere the application can access. You can have on-premise servers accessing Azure SQL databases, Azure Web Apps that talk to on-premise SQL servers, or any number of combinations involving other hosting providers. This guide will focus on two options: hosting your database in Azure, or in your own SQL server.
### Azure - Host your database
#### Set up a server to host the database.
If you don’t already have one, sign in to the Azure portal and create a new database server resource. For example, _SQL server (logical server)_.
Configure the server according to your organizations needs and policies. Once it is up and running, go to **Security → Networking** and make sure that your IP is allowed to access the server in the firewall rules.
#### Export the database locally
In SQL Server Management Studio, connect the object explorer to your local database server.
Find the database you want to copy to Azure, in this case the _Xperience.Kickstart_ database that you restored from the Kickstart repository.
Right-click and choose **Tasks → Export Data-tier Application** , and go through the wizard to save a **.bacpac** file.
#### Import the database to the live server
Connect the object explorer to your new Azure SQL server in SQL Server Management Studio.
Then, right-click the **Databases** folder and choose **Import Data-tier Application**.
Follow the wizard to import the database, specifying the details of the service plan and database name.
This database will serve as the live database for the deployed application.
### Host your database in your own SQL Server
If you want to host your database on a standard SQL server, connect to the local version with SQL Server Management Studio (SSMS), and create a full backup of your local _Xperience.Kickstart_ database, taking note of the folder where you save the _.bak_ file.
Then connect SSMS to the database server that will host the published site’s database, right-click on the **Databases** folder, and choose _Restore_. Choose the _.bak_ file from the previous backup, and restore it as a new database on the target server.
## Separate the app settings
In your Xperience by Kentico solution (_Kickstart.sln_), make a copy of **appsettings.json** named **appsettings.development.json**.
Leave the new _appsettings.development.json_ file as-is, but change the connection string of **_appsettings.json_** to connect to the live database.
Alternatively, you can employ things like [user secrets and environment variables](https://learn.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-8.0) to make sure different environments have the appropriate connection strings.
This way, you can test with your local kickstart database while developing, but the published version of the application will connect to the live database.
You can also use this to store settings to determine which container or bucket each environment uses if you have set up external storage.
## Create a publishable package
Navigate to the _Kickstart.Web_ folder where the main project’s _.csproj_ file lives, and open a command line.
Use the `dotnet publish` command, setting the `configuration` option to `Release`.
CMD
**Publish with the .NET CLI**
Copy
```
dotnet publish --configuration Release
```

You can use the [available options](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-publish#options) to configure aspects of the resulting application.
You can also configure the published app by adding elements to the _.csproj_ file before you publish. For example, you can set a [Runtime Identifier](https://learn.microsoft.com/en-us/dotnet/core/rid-catalog) in the project file if you want to always build the project for a specific target platform without specifying the `runtime` option of the `dotnet publish` command.
## Azure - Deploy the application to your Web App
This guide uses Azure CLI to deploy your application to a Web App in Azure.
If you don’t already have this CLI installed, use `winget` to download and install it.
CMD
**Install Azure CLI**
Copy
```
winget install -e --id Microsoft.AzureCLI
```

After running this command, close any reopen any active terminal windows so that you can use the new tool.
Run the `az login` command to initiate an authentication flow in your browser, allowing you to access your Azure subscriptions.
### Set up a web app
If you don’t already have an empty web app where you want to deploy your code, you’ll need to create one.
You can do this through the [Azure portal](https://portal.azure.com/) or with the `az webapp` command group of the Azure CLI.
Make sure that the web app can access your SQL server. For example, if your database is also hosted in Azure, you can create a firewall exception at the [database level](https://learn.microsoft.com/en-us/azure/azure-sql/database/firewall-configure?view=azuresql) [server level](https://learn.microsoft.com/en-us/azure/azure-sql/database/firewall-create-server-level-portal-quickstart?view=azuresql), or you can [set up Microsoft Entra ID](https://learn.microsoft.com/en-us/azure/azure-sql/database/authentication-aad-configure?view=azuresql&tabs=azure-portal) for your applications.
### Zip and deploy the application.
Open a command window and navigate to the **.\bin\Release\net8.0\publish** directory within the _Kickstart.Web_ folder where you previously ran `dotnet publish`.
We’re going to [deploy this folder as a zip file](https://learn.microsoft.com/en-us/azure/app-service/deploy-zip?tabs=cli), so start by putting its contents into a zip folder. You may be able to do this in the command line, depending on your environment, for example, with `Compress-Archive` in _PowerShell_ or with `zip` in _Bash_.
Now, run the `az webapp deploy` command, specifying the name of your resource group, web app, and zip.
CMD
**Deploy with the Azure CLI**
Copy
```
az webapp deploy --resource-group Kickstart --name KickstartWebApp --src-path .\KickstartDeployment.zip
```

## IIS - Deploy the application to your web server
### Install the .NET Core Hosting Bundle
In order to host .NET core apps, you’ll need to make sure to install the [.NET Core Hosting Bundle](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer) for IIS on the web server where you want your published application to live.
### Set up the web site
On the web server where you will host your application, add a new [site](https://learn.microsoft.com/en-us/iis/get-started/getting-started-with-iis/create-a-web-site) or [application](https://learn.microsoft.com/en-us/iis/configuration/system.applicationhost/sites/site/application/) in IIS, depending on your requirements. Specify a **Physical path** where it should exist in the filesystem, and take note of this location for later.
Configure any domains and certificates your application requires.
### Deploy the application
On your development machine, navigate to the **.\bin\Release\net8.0\publish** directory within the _Kickstart.Web_ folder where you previously ran `dotnet publish`.
Copy the contents of this folder to the **Physical path** location that the IIS site or application points to on the web server.
Depending on your network and security settings, this may be possible to do with the command line, for example, with `robocopy` in _cmd.exe_ , `Copy-Item` in _PowerShell_ , or `cp` in _Bash_. We recommend setting up a reusable script to run these commands along with other checks and tasks that you want to perform during deployment.
## Configure the site
Now, when you access your site, wherever it is hosted, you’ll likely see an [HTTP 500 error](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/500).
This error appears because the Kickstart repo does not contain a license key for the domain that hosts your published application.
Access the **/admin** path of your application to sign in to the Xperience admin UI. Add a license key for all necessary domains in the **License keys** application.
## Deploy new code to an existing published application
You will have to take a different approach for deploying new code to an existing published project depending on whether or not you [configured external storage](guides/development/deployment/deploy-to-private-cloud#configure-external-storage).
If you’ve configured external storage according to our recommendations, you should be able to clear the existing deployment and re-deploy with the same steps as the initial deployment- first [publish](guides/development/deployment/deploy-to-private-cloud#create-a-publishable-package) your application and then deploy the published files according to your platform.
If you host your app in an _Azure Web App_ , you can use the `--clean` parameter of the `az webapp deploy` command to clear out the deployment slot before you do anything.
If you use IIS, we recommend creating a reusable script that clears out the deployment location and copies the publishable files.
**If you do not use external storage** , you’ll need to copy _asset files_ , _media files_ , and _form submission files_ to a safe location before clearing the existing deployment, then copy them back after deployment.
Otherwise, **they may be lost permanently**.
We recommend creating a script to automate this process.
## Deploy new data to an existing published application
### Standard Continuous Deployment
In some cases, you may want to create some data in your development environment and deploy it to the published environment. For example, you might create a custom module, which contains classes and class data alongside code.
We recommend using **Continuous deployment (CD)** to keep the data in sync between your environments. You can follow the guidance in the [Continuous Deployment documentation](documentation/developers-and-admins/ci-cd/continuous-deployment).
### Asymmetric Continuous Deployment workaround
If you want to have different data between your development environment and your published environment, standard CD procedures may cause issues, overwriting objects in the production database with data that you only want to exist locally.
In this case, you can use the following workaround:
#### Set up your environments
Make sure CD is enabled locally, along with some kind of source control. Also follow the advice in the [CD documentation](documentation/developers-and-admins/ci-cd/continuous-deployment#synchronize-macro-signatures) to keep macro signatures in sync with your production environment.
#### Make your data changes
Add the data to your development instance, allowing Continuous Deployment to serialize the data in your source control repository.
#### Copy your dev environment
Make a new copy of your _Development application_ that exists outside of your source control system. We’ll refer to it as the **Copy instance** in this section. Make sure the _Copy instance_ exists outside of your source control. It should be a distinct project, not a branch.
Disable your _Published app_ , and change the connection string of the _Copy instance_ to point to the _Published database_. Then run a [CD store](documentation/developers-and-admins/ci-cd/continuous-deployment#store-objects-to-a-cd-repository) from the _Copy instance_. This serializes the data from the _Published database_ into the _Copy instance_ ’s CD repository folder.
#### Transfer the data
Using your source control history, isolate the new serialized files that were created or modified as a result of your data changes in your _Development app_ , including any dependent objects that may have been affected.
Copy these files into the corresponding folders of the _Copy instance_ ’s CD repository. Then, run a [CD restore](documentation/developers-and-admins/ci-cd/continuous-deployment#restore-the-cd-repository-to-the-database) to propagate the changes to the _Published database_.
Then you can delete the _Copy instance_ and [deploy any new code changes](guides/development/deployment/deploy-to-private-cloud#deploy-new-code-to-an-existing-published-application) to the _Published app_ before reactivating it.
## Update the Xperience version of a published deployment
At some point after deploying your application, you’ll probably want to apply a hotfix or refresh to your application. Let’s look into the update process.
Your approach can vary depending on what your environment allows, we will look into examples that are relevant to the deployment processes described in this guide.
### Upgrade directly in your published environment
In some cases, you can access your published environment directly and perform a large part of the upgrade there. See [the documentation’s update overview](documentation/developers-and-admins/installation/update-xperience-by-kentico-projects#update-process-overview) for more details.
For this approach, you need to run dotnet CLI commands directly on your published environment to update the published database.
This can be done through the [Kudu console](https://github.com/projectkudu/kudu/wiki/Kudu-console) for Azure Web Apps, or by logging in or remotely accessing your web server if you are using standard IIS.
### Update a local copy of your published site
If you prefer not to run the update in your published environment, we recommend setting up a local version of the application and database to perform the update, before re-deploying.
#### Create backups
Make sure to take backups of your development and published databases before starting this process. If you are not using source control, make a backup of your application files as well.
#### Prepare the files
Start by creating a new branch or copy of your development project. Update the Xperience NuGet packages to the version you want to target.
Fix any code errors that appear as a result of the updated libraries.
Then, take your published application offline, or institute a content freeze.
#### Copy the database to your local machine
Move a copy of the live database to your local SQL server using the same [.bacpac](guides/development/deployment/deploy-to-private-cloud#export-the-database-locally) or [.bak](guides/development/deployment/deploy-to-private-cloud#host-your-database-in-your-own-sql-server) method you used to initially copy it to the published SQL server.
Update the connection string of the development instance to point to the new copy of the live database
#### Run the update
Disable Continuous Integration (CI) and Continuous Deployment (CD). Then run the `kxp-update` command of the .NET CLI to update database data. After the update completes successfully, you can re-enable CI and/or CD.
Test your project to make sure it is in working order.
Once everything is ready, change the connection string back to its previous state.
#### Re-deploy your application
Take your published application offline if it is not already.
Copy the newly updated database to the live server using the same method you started with. You can do one of the following:
  * Delete the original live database and import the updated version with the same name.
  * Import the updated database alongside the original, then switch their names.
  * Import the updated database alongside the original, and change the published application’s connection string to point to the new database.


Then clear and deploy your updated application files, and bring the published application back online.
At this point, you can access your updated published application normally.
## What’s next?
Keep your eye out for future materials covering other deployment scenarios, and let us know if you think anything needs specific attention.